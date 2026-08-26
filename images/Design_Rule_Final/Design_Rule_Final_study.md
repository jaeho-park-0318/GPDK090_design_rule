# GPDK090 CMOS Layout Design Rule 구현 및 Metal–Semiconductor Contact 특성 분석

## 1. 프로젝트 개요

본 프로젝트에서는 **GPDK090 CMOS 공정의 Design Rule을 학습하고, Cadence Virtuoso를 이용하여 주요 Design Rule을 직접 Layout으로 구현**하였다.

Design Rule은 단순히 Layout Pattern의 크기와 간격을 제한하는 규칙이 아니라, 실제 반도체 제조 과정에서 발생할 수 있는 **Lithography 오차, Etching 오차, Mask Misalignment, 공정 변동(Process Variation)** 등을 고려하여 원하는 구조가 Wafer 위에 안정적으로 형성되도록 하기 위한 설계 기준이다.

또한 Design Rule은 단순한 제조 가능성뿐만 아니라 다음과 같은 요소와 밀접한 관계를 가진다.

- Electrical Isolation
- Junction Leakage
- Contact Resistance
- Interconnect Resistance
- Electromigration
- Latch-up
- Antenna Effect
- CMP Uniformity
- Device Reliability

본 프로젝트에서는 다음과 같은 주요 Layer 및 Reliability Rule을 대상으로 학습 및 Layout 구현을 수행하였다.

- Nwell
- Active Area
- Thick Active
- N+/P+ Implant
- Poly
- Contact
- Silicide / Non-Silicide
- SIPROT
- Metal1
- Via
- Metal Density
- Latch-up
- Antenna

전체 Design Rule을 구현한 이후에는 그중 **Contact 및 Metal 관련 Design Rule**에 초점을 맞추어 Metal–Semiconductor Contact의 물리적 특성을 추가적으로 분석하였다.

특히 다음 내용을 중심으로 심화 학습하였다.

1. Metal–Semiconductor Contact 형성
2. Schottky-Mott Rule
3. Schottky Contact
4. Ohmic Contact
5. Thermionic Emission
6. Heavy Doping에 따른 Depletion Width 감소
7. Field Emission 및 Tunneling
8. Surface State / Interface State
9. Fermi Level Pinning
10. Silicide와 Contact Resistance
11. Contact Design Rule과 Ohmic Contact의 관계

이를 통해 최종적으로 다음의 연결 관계를 이해하는 것을 목표로 하였다.

$$
\text{Design Rule}
\rightarrow
\text{Process}
\rightarrow
\text{Physical Structure}
\rightarrow
\text{Electrical Characteristic}
\rightarrow
\text{Reliability}
$$

---

# 2. Design Rule의 기본 개념

## 2.1 Design Rule이 필요한 이유

Layout에서 설계한 Pattern은 실제 Wafer 위에 완벽하게 동일한 형태로 형성되지 않는다.

실제 제조 과정에서는 다음과 같은 공정 오차가 발생할 수 있다.

```text
Layout Pattern
      ↓
Photolithography
      ↓
Etching / Deposition / Implantation
      ↓
Process Variation
      ↓
Actual Wafer Pattern
```

따라서 Layout 단계에서는 이러한 오차를 고려하여 충분한 Process Margin을 확보해야 한다.

Design Rule은 대표적으로 다음과 같은 Geometry Constraint를 정의한다.

- Minimum Width
- Minimum Spacing
- Enclosure
- Overlap
- Extension
- Parallel Run Length
- Density

즉, Design Rule의 역할은 다음과 같이 정리할 수 있다.

$$
\boxed{
\text{Design Rule}
=
\text{Process Margin}
+
\text{Electrical Isolation}
+
\text{Reliability}
}
$$

---

## 2.2 Width

Width Rule은 특정 Layer가 가져야 하는 최소 또는 최대 폭을 정의한다.

예를 들어 Nwell의 최소 폭이 다음과 같이 규정되어 있다면,

$$
W_{Nwell} \geq 0.6\,\mu m
$$

Nwell Pattern은 해당 최소 폭을 만족해야 한다.

Minimum Width가 필요한 이유는 Lithography와 Etching 과정에서 지나치게 좁은 Pattern이 정상적으로 형성되지 않을 수 있기 때문이다.

Metal의 경우 Width는 제조 가능성뿐만 아니라 배선 저항과 Current Density에도 영향을 준다.

---

## 2.3 Spacing

Spacing은 서로 분리되어 있는 두 Pattern 사이에 확보해야 하는 최소 거리이다.

```text
Pattern A        Pattern B
████████        ████████
        ← S →
```

Spacing이 지나치게 작으면 다음과 같은 문제가 발생할 수 있다.

- Lithography Pattern 불량
- Etching 불량
- Electrical Short
- Junction Leakage
- Capacitive Coupling 증가
- Breakdown 특성 저하

---

## 2.4 Enclosure

Enclosure는 한 Layer가 다른 Layer를 얼마나 감싸야 하는지를 정의한다.

```text
Outer Layer
┌──────────────────┐
│      Inner       │
│      Layer       │
└──────────────────┘
```

Enclosure Rule은 특히 다음 구조에서 중요하다.

- Implant → Contact
- Metal → Contact
- Metal → Via
- Nwell → Active
- SIPROT → Active

Mask Alignment Error가 발생하더라도 내부 Layer가 외부 Layer 밖으로 벗어나지 않도록 Process Margin을 제공한다.

---

# 3. CMOS Layout의 기본 Layer 구조

CMOS Layout은 여러 Mask Layer가 중첩되어 하나의 MOSFET 및 Interconnect 구조를 형성한다.

개념적인 수직 구조는 다음과 같다.

```text
Upper Metal
──────────────────────────

          Via
           │
──────────────────────────
         Metal1
──────────────────────────
           │
        Contact
           │
──────────────────────────
        Silicide
──────────────────────────
     N+ / P+ Active
──────────────────────────
          Silicon
```

MOSFET Gate 영역에서는 다음과 같은 구조가 형성된다.

```text
          Poly
           │
           │
──────────────────────────
       Gate Oxide
──────────────────────────
          Silicon
```

주요 Layout Layer는 다음과 같다.

| Layer | 주요 역할 |
|---|---|
| Nwell | PMOS Body 영역 |
| Active | Device가 형성되는 Silicon 영역 |
| Thick Active | Thick-Oxide Device 영역 |
| N+/P+ Implant | Source/Drain 및 Well/Substrate Contact 도핑 |
| Poly | MOSFET Gate 및 Poly Interconnect |
| Contact | Active/Poly와 Metal1 연결 |
| Silicide | Source/Drain 및 Poly 저항 감소 |
| SIPROT | 선택적인 Silicide 형성 방지 |
| Metal | 신호 및 전력 배선 |
| Via | 서로 다른 Metal Layer 연결 |

---

# 4. Nwell Design Rule

## 4.1 Nwell의 개념

Nwell은 **P-type Substrate 내부에 N-type으로 도핑하여 만든 영역**으로, 주로 PMOS의 Body 영역으로 사용된다.

PMOS의 P+ Source/Drain은 Nwell 내부에 형성되며, Nwell은 일반적으로 N+ Well Tap을 통해 VDD에 연결된다.

```text
        PMOS

P+       Gate       P+
│         │          │
Source  Channel    Drain
└─────────┬──────────┘
          │
        Nwell
          │
       N+ Well Tap
          │
         VDD
```

Nwell의 폭, Well 사이 거리, Active와의 거리 및 Enclosure가 충분하지 않으면 도핑 Profile이 서로 간섭하거나 Electrical Isolation 특성이 저하될 수 있다.

---

## 4.2 Minimum Nwell Width

본 프로젝트에서는 다음 최소 폭을 구현하였다.

$$
W_{Nwell} \geq 0.6\,\mu m
$$

Nwell이 지나치게 좁으면 Lithography 및 Well Implant 과정에서 원하는 Doping Profile을 안정적으로 형성하기 어렵다.

---

## 4.3 Nwell Spacing

### Same Potential

동일한 전위를 가지는 Nwell 사이에는 다음의 간격을 적용하였다.

$$
S_{Nwell} \geq 0.6\,\mu m
$$

### Different Potential

서로 다른 전위를 가지는 Nwell 사이에는 다음의 더 큰 간격을 적용하였다.

$$
S_{Nwell} \geq 1.2\,\mu m
$$

서로 다른 전위가 인가되는 경우 두 Well 사이의 Electric Field와 Depletion Region에 의해 Leakage 및 Breakdown 가능성이 증가할 수 있기 때문이다.

따라서 다음 관계를 갖는다.

$$
S_{\text{different potential}}
>
S_{\text{same potential}}
$$

---

## 4.4 Nwell에 대한 고찰

Nwell Design Rule을 직접 Layout으로 구현하면서 Well Rule이 단순히 PMOS가 들어갈 공간을 확보하기 위한 규칙이 아니라는 점을 확인할 수 있었다.

Nwell은 PMOS의 Body Potential을 결정하며 주변 P-type 영역과 PN Junction을 형성한다.

따라서 Nwell Geometry는 다음과 연결된다.

- Device Isolation
- Junction Leakage
- Breakdown
- Body Effect
- Latch-up

즉, Nwell Design Rule은 **PMOS가 형성되는 N-type Body 영역의 크기와 주변 간격을 충분히 확보하여 공정 정렬 오차를 보상하고, PN Junction Isolation, Breakdown 특성, Body Potential 및 Latch-up 신뢰성을 확보하기 위한 규칙**이라고 이해할 수 있다.

---

# 5. Active Area Design Rule

## 5.1 Active Area의 개념

Active Area는 실제 MOSFET의 Source, Drain 및 Channel이 형성되는 Silicon 영역이다.

Active Area 외부는 일반적으로 STI(Shallow Trench Isolation)에 의해 전기적으로 분리된다.

```text
Active        STI        Active
███████                 ███████
       └───────────────┘
```

따라서 Active Area는 단순한 Layout 도형이 아니라 실제 Transistor가 형성되는 Device Region을 정의한다.

---

## 5.2 Active Minimum Width

Active Area가 지나치게 좁으면 Lithography 및 Etching 과정에서 Pattern 변형이 발생할 수 있다.

또한 Narrow Width Effect 등에 의해 Device 특성이 변화할 수 있다.

따라서 Minimum Active Width를 통해 충분한 Device Region을 확보한다.

---

## 5.3 Active-to-Active Spacing

두 Active Area가 지나치게 가까우면 두 Device 사이의 STI 폭이 감소한다.

이는 다음과 같은 문제를 발생시킬 수 있다.

- Device Isolation 저하
- Junction Leakage 증가
- STI Process Margin 감소
- Parasitic Interaction 증가

---

## 5.4 Active Area에 대한 고찰

Active Rule을 구현하면서 Active Layer가 단순히 Source/Drain을 그리는 Layer가 아니라 **STI와 함께 Device Isolation을 결정하는 핵심 Layer**라는 점을 확인할 수 있었다.

Active Geometry가 잘못되면 개별 Transistor의 문제에서 끝나는 것이 아니라 주변 Device와의 Electrical Isolation까지 영향을 받을 수 있다.

---

# 6. Thick Active Design Rule

## 6.1 Thick Active의 개념

Thick Active는 일반 Core Device보다 높은 전압을 처리하는 **Thick-Oxide Device 영역을 정의하는 Layer**이다.

높은 전압을 사용하는 Device에서는 Gate Oxide를 더 두껍게 형성하여 높은 Electric Field에 의한 Oxide Breakdown을 방지한다.

---

## 6.2 Thick Active Width 및 Spacing

Thick Active는 일반 Active와 다른 Process 조건을 사용하기 때문에 별도의 Width와 Spacing Rule을 가진다.

Thick Active 영역이 지나치게 좁으면 Thick Oxide Process의 경계가 불안정해질 수 있다.

또한 일반 Active와 지나치게 가까우면 Thick Oxide와 Thin Oxide Process 영역 사이의 Transition Region이 충분히 확보되지 않을 수 있다.

---

## 6.3 Thick Active에 대한 고찰

Thick Active Rule을 통해 동일한 CMOS Process 내부에서도 모든 Transistor가 동일한 구조를 사용하는 것이 아니라는 점을 확인하였다.

Operating Voltage가 증가하면 Oxide에 가해지는 Electric Field 문제가 중요해진다.

$$
V_{\text{Operating}}\uparrow
\Rightarrow
E_{\text{Oxide}}\uparrow
\Rightarrow
\text{Breakdown Risk}\uparrow
$$

따라서 높은 전압 Device에서는 더 두꺼운 Oxide와 충분한 Process Margin이 필요하다.

---

# 7. N+/P+ Implant Design Rule

## 7.1 Implant의 개념

Implant Layer는 Silicon 내부의 Doping Type과 Doping Region을 정의한다.

대표적으로 다음과 같이 사용된다.

- N+ Implant → NMOS Source/Drain, Nwell Tap
- P+ Implant → PMOS Source/Drain, P-substrate Tap

Implant Layer는 Active Area와 함께 사용되어 실제 Source/Drain의 Doping Type을 결정한다.

---

## 7.2 Implant와 Contact

Contact 아래에는 충분한 N+ 또는 P+ Doping이 존재해야 한다.

이는 Contact의 Barrier Width와 Contact Resistance에 직접적인 영향을 준다.

$$
\text{High Doping}
\rightarrow
\text{Thin Barrier}
\rightarrow
\text{Tunneling Increase}
\rightarrow
R_C \downarrow
$$

---

## 7.3 Implant에 대한 고찰

Implant Rule을 통해 Layout에서 표시되는 N+와 P+ 영역이 단순한 색상 구분이 아니라 실제 Wafer 내부의 Carrier Concentration과 PN Junction을 결정한다는 점을 확인하였다.

특히 Contact 영역에서는 Implant Geometry가 Contact Resistance와 직접적으로 연결되므로 Device 동작뿐 아니라 Interconnect 특성에도 중요한 영향을 준다.

---

# 8. Poly Design Rule

## 8.1 Poly의 개념

Poly는 MOSFET의 Gate Electrode를 형성하는 핵심 Layer이다.

Active Area 위를 Poly가 가로지르는 영역에서 Channel이 형성된다.

```text
        POLY
          │
          │
N+ ──────┼────── N+
         │
      Channel
```

---

## 8.2 Poly Width

Poly가 지나치게 좁으면 Lithography 한계로 인해 Pattern 형성이 어려워지고 Gate Length Variation이 증가할 수 있다.

Gate Length Variation은 다음에 직접적인 영향을 줄 수 있다.

- Threshold Voltage
- Drain Current
- Short Channel Effect
- Leakage

---

## 8.3 POLY Density Ratio

Chip 전체 또는 특정 Window 내부에서 Poly가 차지하는 면적 비율을 다음과 같이 표현할 수 있다.

$$
\text{POLY Density Ratio}
=
\frac{A_{\text{POLY}}}{A_{\text{Total}}}
\times 100
$$

Poly Density가 지나치게 높거나 불균일하면 Pattern Density에 따른 공정 편차가 증가할 수 있다.

따라서 Density Rule을 통해 Chip-level Process Uniformity를 확보한다.

---

## 8.4 Poly에 대한 고찰

Poly는 MOSFET의 Gate를 직접 형성하므로 다른 일반 배선 Layer보다 Device Performance에 직접적인 영향을 준다.

또한 Gate Oxide가 매우 얇기 때문에 Poly와 연결된 구조는 Antenna Effect에도 민감하다.

---

# 9. Silicide와 Non-Silicide

## 9.1 Silicide의 개념

Silicide는 Silicon 또는 Poly 표면에 Metal과 Silicon의 화합물을 형성하여 Sheet Resistance와 Contact Resistance를 감소시키는 기술이다.

```text
Metal
  │
Contact
  │
Silicide
  │
N+ / P+
  │
Silicon
```

---

## 9.2 Non-Silicide의 개념

모든 영역에서 낮은 저항이 필요한 것은 아니다.

Poly Resistor와 같이 의도적으로 높은 Resistance를 사용해야 하는 영역에서는 Silicide가 형성되면 원하는 저항값을 얻을 수 없다.

따라서 특정 영역에서는 Silicide Formation을 의도적으로 차단한다.

---

## 9.3 Silicide와 Non-Silicide 비교

| 구분 | Silicide | Non-Silicide |
|---|---|---|
| 목적 | 저항 감소 | 높은 저항 유지 |
| Sheet Resistance | 낮음 | 높음 |
| Source/Drain | 주로 사용 | 제한적 |
| Poly Resistor | 부적합할 수 있음 | 적합 |
| Contact Resistance | 감소 | 상대적으로 증가 |

---

# 10. SIPROT Design Rule

## 10.1 SIPROT의 개념

SIPROT는 **Silicide가 형성되면 안 되는 영역을 보호하기 위한 Salicide Block Layer**이다.

```text
Without SIPROT

Poly
 ↓
Silicide Formation
 ↓
Resistance ↓
```

```text
With SIPROT

Poly
 ↓
Silicide Blocked
 ↓
High Resistance Maintained
```

---

## 10.2 SIPROT와 Contact

SIPROT가 Contact에 지나치게 가까우면 Contact 주변에 필요한 Silicide가 충분히 형성되지 않아 Contact Resistance가 증가할 수 있다.

따라서 SIPROT-to-Contact Spacing을 확보해야 한다.

---

## 10.3 SIPROT에 대한 고찰

SIPROT Rule을 통해 Silicide는 단순히 존재하거나 존재하지 않는 구조가 아니라 Layout 단계에서 선택적으로 제어되는 Process임을 확인하였다.

SIPROT Geometry가 잘못되면 다음과 같은 문제가 발생할 수 있다.

- 원하지 않는 Silicide Formation
- 저항값 변화
- Contact Resistance 증가

---

# 11. Contact Design Rule

## 11.1 Contact의 개념

Contact는 Semiconductor 또는 Poly와 Metal1을 전기적으로 연결하는 수직 Interconnect이다.

```text
             Metal1
══════════════════════════
               │
            Contact
               │
──────────── Silicide ─────
        Heavily Doped
          N+ or P+
══════════════════════════
          Silicon
```

---

## 11.2 Contact Width

Contact가 지나치게 작으면 Lithography 및 Etching 공정에서 Contact Hole이 완전히 열리지 않을 수 있다.

그 결과 다음과 같은 문제가 발생할 수 있다.

- Open
- Contact Resistance 증가
- Contact 불량

---

## 11.3 Contact-to-Gate Spacing

Contact가 Gate에 지나치게 가까우면 공정 오차에 의해 Gate와 Contact가 Short될 수 있다.

본 프로젝트에서는 다음 Rule을 구현하였다.

$$
\text{CONT.SE.1}=0.10\,\mu m
$$

---

## 11.4 Implant-to-Contact Enclosure

Metal–Semiconductor Contact Physics와 직접 연결하여 분석한 핵심 Rule 중 하나는 다음과 같다.

$$
\boxed{
\text{CONT.E.4}=0.06\,\mu m
}
$$

즉, N+/P+ Implant가 Contact를 사방으로 최소 $0.06\,\mu m$ 이상 감싸야 한다.

### Process 관점

Mask Overlay Error가 존재하더라도 Contact 전체가 N+/P+ Doped Region 내부에 위치하도록 한다.

### Electrical 관점

Contact 아래 Silicon이 충분히 Heavy Doping된 상태를 유지하여 낮은 Contact Resistance의 Ohmic Contact를 형성하도록 한다.

```text
Implant Enclosure
        ↓
Contact 전체가 Heavy Doped Region에 위치
        ↓
Barrier Width 감소
        ↓
Tunneling 증가
        ↓
Contact Resistance 감소
        ↓
Stable Ohmic Contact
```

---

# 12. Metal–Semiconductor Contact

## 12.1 접촉 전 상태

Metal과 Semiconductor가 접촉하기 전에는 서로 다른 Work Function과 Fermi Level을 가진다.

Metal의 Work Function은 $\Phi_m$, Semiconductor의 Work Function은 $\Phi_s$로 나타낼 수 있다.

---

## 12.2 접촉 후 상태

Metal과 Semiconductor가 접촉하면 Thermal Equilibrium을 이루기 위해 Carrier가 이동한다.

평형 상태에서는 Fermi Level이 같아진다.

$$
E_{F,m}=E_{F,s}
$$

이 과정에서 Semiconductor의 Energy Band가 휘어지는 **Band Bending**이 발생한다.

결과적으로 Metal–Semiconductor Interface에 Potential Barrier가 형성될 수 있다.

---

# 13. Schottky-Mott Rule

이상적인 n-type Semiconductor를 기준으로 다음과 같이 정리할 수 있다.

$$
\boxed{
\Phi_m>\Phi_s
\Rightarrow
\text{Schottky Contact}
}
$$

$$
\boxed{
\Phi_s>\Phi_m
\Rightarrow
\text{Ohmic Contact}
}
$$

이상적인 Electron Schottky Barrier Height는 다음과 같다.

$$
\boxed{
\Phi_{Bn}=\Phi_m-\chi
}
$$

여기서

- $\Phi_m$: Metal Work Function
- $\chi$: Semiconductor Electron Affinity
- $\Phi_{Bn}$: Electron Schottky Barrier Height

이다.

---

# 14. Schottky Contact

## 14.1 Thermionic Emission

Schottky Contact의 Current Density는 다음과 같이 표현할 수 있다.

$$
J=
A^*T^2
\exp\left(-\frac{q\Phi_B}{kT}\right)
\left[
\exp\left(\frac{qV}{nkT}\right)-1
\right]
$$

Saturation Current Density를 다음과 같이 정의할 수 있다.

$$
J_S=
A^*T^2
\exp\left(-\frac{q\Phi_B}{kT}\right)
$$

따라서

$$
J=
J_S
\left[
\exp\left(\frac{qV}{nkT}\right)-1
\right]
$$

이고,

$$
\Phi_B\uparrow
\Rightarrow
J_S\downarrow
$$

의 관계를 갖는다.

---

## 14.2 Schottky Contact의 I–V 특성

Schottky Contact는 Rectifying 특성을 가진다.

Forward Bias에서는 Current가 급격하게 증가하고 Reverse Bias에서는 작은 Reverse Saturation Current가 흐른다.

- Non-linear I–V
- Rectifying Characteristic
- Forward Bias에서 Current 급격히 증가
- Reverse Bias에서 작은 Saturation Current
- Thermionic Emission이 중요한 전도 Mechanism

---

# 15. Ohmic Contact

Ohmic Contact는 양의 Bias와 음의 Bias 모두에서 Current가 Voltage에 거의 선형적으로 비례하는 **Non-Rectifying Contact**이다.

$$
\boxed{
I=\frac{V}{R_C}
}
$$

따라서 I–V Curve의 기울기는 다음과 같다.

$$
\frac{dI}{dV}
=
\frac{1}{R_C}
$$

Contact Resistance가 작을수록 I–V Curve의 기울기가 커진다.

---

# 16. Heavy Doping과 Barrier Width

Metal–Semiconductor Junction의 Depletion Width는 단순화하면 다음과 같이 나타낼 수 있다.

$$
W=
\sqrt{
\frac{
2\varepsilon_s(V_{bi}-V)
}{
qN
}
}
$$

따라서

$$
\boxed{
W\propto\frac{1}{\sqrt{N}}
}
$$

이고,

$$
N\uparrow
\Rightarrow
W\downarrow
$$

이다.

즉, Heavy Doping을 사용할수록 Metal–Semiconductor Interface의 Barrier Width가 감소한다.

---

# 17. Tunneling과 Field Emission

Barrier 내부에서 Wave Function은 다음과 같은 형태로 감소한다.

$$
\psi(x)\propto e^{-\kappa x}
$$

여기서

$$
\kappa
=
\frac{
\sqrt{2m^*(\Phi_B-E)}
}{
\hbar
}
$$

이다.

Barrier Width가 $W$일 때 Tunneling Probability는 대략 다음과 같다.

$$
\boxed{
T\propto e^{-2\kappa W}
}
$$

따라서

$$
W\downarrow
\Rightarrow
T\uparrow
$$

이며 Heavy Doping과 연결하면 다음 관계를 얻을 수 있다.

$$
\boxed{
N\uparrow
\Rightarrow
W\downarrow
\Rightarrow
T\uparrow
\Rightarrow
R_C\downarrow
}
$$

---

# 18. Carrier Transport Mechanism의 변화

Doping Concentration이 증가하면 주요 Carrier Transport Mechanism은 다음과 같이 변화할 수 있다.

```text
Low Doping
    ↓
Thermionic Emission
    ↓
Thermionic-Field Emission
    ↓
Field Emission
    ↓
High Doping
```

즉,

$$
\text{Thermionic Emission}
\rightarrow
\text{Thermionic-Field Emission}
\rightarrow
\text{Field Emission}
$$

으로 변화한다.

높은 Doping에서는 Barrier Width가 매우 얇아져 Carrier의 Tunneling이 중요해진다.

---

# 19. Surface State와 Interface State

## 19.1 Surface State

Semiconductor Surface에서는 Crystal Structure가 끝나기 때문에 일부 Bond가 끊어진 상태로 남을 수 있다.

이를 **Dangling Bond**라고 한다.

이러한 Dangling Bond 등에 의해 Semiconductor Bandgap 내부에 Energy State가 형성될 수 있으며 이를 Surface State라고 한다.

대표적인 원인은 다음과 같다.

- Dangling Bond
- Step / Edge
- Crystal Defect
- Contamination

---

## 19.2 Interface State

Interface State는 서로 다른 두 Material이 만나는 Boundary에서 형성되는 Energy State이다.

Metal–Semiconductor Interface에서는 다음과 같은 원인에 의해 Interface State가 형성될 수 있다.

- Bonding Mismatch
- Crystal Defect
- Contamination
- Oxide
- Interface Disorder

Interface State는 Electron을 Capture하거나 Release할 수 있으므로 Interface Charge를 형성할 수 있다.

---

# 20. Fermi Level Pinning

이상적인 Schottky-Mott Model에서는 Metal Work Function을 변화시키면 Barrier Height도 이에 따라 변화한다.

하지만 실제 Interface에서는 Interface State가 존재하기 때문에 이러한 관계가 약해질 수 있다.

Interface State Density가 충분히 높으면 Interface Charge가 Fermi Level의 위치를 특정 Energy 부근에 고정시키는 효과가 발생한다.

이를 **Fermi Level Pinning**이라고 한다.

```text
Ideal Interface

Metal Work Function
        ↓
Barrier Height


Real Interface

Metal Work Function
        ↓
Interface State
        ↓
Fermi Level Pinning
        ↓
Actual Barrier Height
```

따라서 실제 Metal–Semiconductor Contact에서는 Work Function Combination만으로 Contact 특성을 완전히 설명하기 어렵다.

---

# 21. CONT.E.4와 Ohmic Contact의 연결

본 프로젝트에서 Contact Physics와 Layout Design Rule의 관계를 가장 직접적으로 보여주는 Rule은 `CONT.E.4`이다.

$$
\boxed{
\text{CONT.E.4}=0.06\,\mu m
}
$$

N+/P+ Implant가 Contact를 사방으로 최소 $0.06\,\mu m$ 이상 감싸야 한다.

첫 번째 이유는 **Mask Overlay Error에 대한 Process Margin**이다.

두 번째 이유는 **Electrical Contact 특성**이다.

Heavy Doping이 유지되면 다음과 같은 관계를 갖는다.

$$
N\uparrow
$$

$$
W\propto\frac{1}{\sqrt{N}}
$$

따라서

$$
W\downarrow
$$

이고,

$$
T\propto e^{-2\kappa W}
$$

이므로

$$
T\uparrow
$$

한다.

전체적인 관계는 다음과 같다.

$$
\boxed{
\text{Implant Enclosure}
\rightarrow
\text{Heavy Doping 확보}
\rightarrow
W\downarrow
\rightarrow
T\uparrow
\rightarrow
R_C\downarrow
\rightarrow
\text{Ohmic Contact}
}
$$

---

# 22. Schottky Contact와 Ohmic Contact 비교

| 구분 | Schottky Contact | Ohmic Contact |
|---|---|---|
| 기본 특성 | Rectifying | Non-Rectifying |
| I–V | 비선형 | 거의 선형 |
| Forward Bias | Current 급격히 증가 | Voltage에 비례 |
| Reverse Bias | 작은 Reverse Current | 반대 방향으로도 선형 |
| Barrier | 상대적으로 높거나 넓음 | 매우 얇게 형성 가능 |
| 주요 Mechanism | Thermionic Emission | Field Emission / Tunneling |
| Doping | Low / Moderate | Heavy Doping |
| Contact Resistance | 상대적으로 큼 | 작음 |
| CMOS S/D Contact | 일반적으로 부적합 | 적합 |

---

# 23. Silicide와 Ohmic Contact

실제 CMOS Source/Drain Contact에서는 Heavy Doping뿐만 아니라 Silicide도 Contact Resistance를 감소시키는 데 중요한 역할을 한다.

```text
Metal1
  │
Contact
  │
Silicide
  │
N+ / P+
  │
Silicon
```

따라서 실제 CMOS Contact는 다음 요소들의 조합으로 낮은 저항을 구현한다고 볼 수 있다.

$$
\boxed{
\text{Heavy Doping}
+
\text{Silicide}
+
\text{Proper Contact Geometry}
}
$$

---

# 24. Metal1 Design Rule

## 24.1 Metal1의 역할

Metal1은 Contact를 통해 Transistor Source/Drain 또는 Poly와 연결되는 첫 번째 Metal Interconnect Layer이다.

Metal1은 신호 전달뿐만 아니라 Power Distribution에도 사용될 수 있기 때문에 다음 요소를 함께 고려해야 한다.

- Width
- Spacing
- Current Density
- Electromigration
- Density

---

## 24.2 Metal Width와 Resistance

Metal 배선의 Resistance는 다음과 같다.

$$
R=\rho\frac{L}{A}
$$

Metal 단면적은 다음과 같다.

$$
A=W\times t
$$

따라서

$$
R=
\rho
\frac{L}{Wt}
$$

이고,

$$
W\downarrow
\Rightarrow
R\uparrow
$$

이다.

---

# 25. Metal Width와 Current Density

Current Density는 다음과 같다.

$$
J=\frac{I}{A}
$$

Metal의 단면적이

$$
A=W\times t
$$

이므로,

$$
\boxed{
J=
\frac{I}{Wt}
}
$$

이다.

따라서 동일한 Current에서

$$
W\downarrow
\Rightarrow
J\uparrow
$$

가 된다.

---

# 26. Electromigration

Electromigration은 높은 Current Density에서 전도 Electron과 Metal Atom 사이의 Momentum Transfer에 의해 Metal Atom이 이동하는 현상이다.

Metal Atom이 이동하면서 Upstream 영역에는 Vacancy가 축적되고 **Void**가 형성될 수 있다.

반대로 Atom이 축적되는 영역에서는 Hillock 또는 Whisker가 형성될 수 있다.

Void는 Resistance 증가 또는 Open Failure를 발생시킬 수 있으며, Hillock은 인접 Metal Line과 Short를 발생시킬 가능성이 있다.

---

# 27. Black's Equation

Electromigration에 의한 Interconnect Lifetime은 Black's Equation으로 표현할 수 있다.

$$
\boxed{
MTTF
=
A
J^{-n}
\exp
\left(
\frac{E_a}{kT}
\right)
}
$$

여기서

- $MTTF$: Mean Time To Failure
- $A$: Process-dependent Constant
- $J$: Current Density
- $n$: Current Density Exponent
- $E_a$: Activation Energy
- $k$: Boltzmann Constant
- $T$: Absolute Temperature

이다.

Current Density가 증가하면

$$
J\uparrow
\Rightarrow
MTTF\downarrow
$$

한다.

Temperature가 증가해도 MTTF는 감소한다.

---

# 28. Metal Density

Metal Density는 특정 Window 내부에서 Metal이 차지하는 면적 비율이다.

$$
\text{Metal Density}
=
\frac{A_{\text{Metal}}}{A_{\text{Window}}}
\times 100
$$

Metal Pattern이 특정 영역에 지나치게 집중되거나 부족하면 CMP 과정에서 Surface Planarity가 불균일해질 수 있다.

```text
Uneven Metal Density
        ↓
Non-uniform CMP
        ↓
Dishing / Erosion
        ↓
Topography Variation
        ↓
Interconnect Reliability 저하
```

따라서 Metal Density Rule은 개별 배선의 문제가 아니라 **Chip-level Process Uniformity를 확보하기 위한 Rule**이다.

---

# 29. MIMCAP과 Metal

MIMCAP(Metal–Insulator–Metal Capacitor)은 두 Metal Electrode 사이에 절연막을 형성하여 구현하는 Capacitor이다.

기본적인 Capacitance는 다음과 같다.

$$
C=
\frac{\varepsilon A}{d}
$$

여기서

- $\varepsilon$: Dielectric Permittivity
- $A$: Electrode Overlap Area
- $d$: Dielectric Thickness

이다.

MIMCAP은 Metal Layer를 이용하는 구조이므로 Metal Geometry와 Density 조건을 함께 고려해야 한다.

특히 큰 MIMCAP Array는 특정 영역에서 높은 Metal Density를 만들 수 있으므로 주변 Dummy Metal 및 Density Rule과 함께 고려할 필요가 있다.

---

# 30. Via Design Rule

## 30.1 Via의 개념

Via는 서로 다른 Metal Layer를 수직으로 연결한다.

```text
Metal2
════════════
     □
    Via1
     □
════════════
Metal1
```

Contact가 Semiconductor/Poly와 Metal1을 연결한다면, Via는 Metal Layer와 Metal Layer를 연결한다.

---

## 30.2 Via Enclosure

Via 주변 Metal이 충분한 Enclosure를 갖지 못하면 Mask Misalignment로 인해 Via의 일부가 Metal 밖으로 벗어날 수 있다.

그 결과 다음과 같은 문제가 발생할 수 있다.

- Via Resistance 증가
- Current Crowding
- Open Failure

---

## 30.3 Via Array

여러 Via를 병렬로 사용하면 Equivalent Resistance가 감소한다.

$$
R_{\text{eq}}\downarrow
$$

또한 Current가 여러 Via로 분산되므로 다음과 같은 장점이 있다.

- Via Resistance 감소
- Current Density 감소
- Electromigration Reliability 향상

---

# 31. Latch-up Rule

CMOS 구조에는 의도하지 않은 기생 PNP와 NPN BJT가 존재한다.

이들이 서로 Positive Feedback 구조를 형성하면 PNPN 구조가 Turn-on될 수 있다.

이를 **Latch-up**이라고 한다.

Latch-up을 방지하기 위해 다음과 같은 구조를 사용한다.

- Nwell Tap
- P-substrate Tap
- Guard Ring

Well/Substrate Tap을 Device 가까이에 배치하면 Well/Substrate Resistance를 감소시켜 기생 BJT가 Turn-on될 가능성을 감소시킬 수 있다.

---

# 32. Antenna Rule

## 32.1 Antenna Effect

Antenna Effect는 Plasma Etching 과정에서 Floating Metal 또는 Poly에 Charge가 축적되고, 이 Charge가 MOS Gate로 전달되면서 얇은 Gate Oxide에 높은 Electric Field가 인가되는 현상이다.

Gate Oxide가 매우 얇기 때문에 과도한 Charge가 축적되면 Oxide Breakdown이 발생할 수 있다.

---

## 32.2 Antenna Ratio

Antenna Ratio는 다음과 같이 나타낼 수 있다.

$$
AR=
\frac{A_{\text{Antenna}}}{A_{\text{Gate}}}
$$

Antenna Area가 증가하면 Plasma로부터 수집할 수 있는 Charge가 증가한다.

따라서

$$
AR\uparrow
\Rightarrow
\text{Gate Oxide Damage Risk}\uparrow
$$

가 된다.

---

# 33. 전체 Design Rule과 Semiconductor Physics의 연결

본 프로젝트에서 가장 중요하게 확인한 점은 Layout Design Rule과 Semiconductor Physics가 서로 독립적으로 존재하지 않는다는 것이다.

특히 Contact에서는 다음과 같은 관계가 나타난다.

```text
Contact Design Rule
        ↓
Implant Enclosure
        ↓
Heavy Doped Region 확보
        ↓
Barrier Width 감소
        ↓
Tunneling 증가
        ↓
Contact Resistance 감소
        ↓
Stable Ohmic Contact
```

Metal에서는 다음과 같이 연결된다.

```text
Metal Width Rule
        ↓
Metal Cross-sectional Area 확보
        ↓
Current Density 감소
        ↓
Electromigration 감소
        ↓
Interconnect Reliability 증가
```

따라서 Design Rule은 단순한 Layout Geometry Constraint가 아니라 **원하는 Device Physics와 Reliability를 실제 Wafer 위에서 안정적으로 구현하기 위한 조건**이라고 볼 수 있다.

---

# 34. 프로젝트 수행 과정

본 프로젝트에서는 Design Rule Manual에 있는 규칙을 단순히 정리하는 방식이 아니라 Cadence Virtuoso에서 각 Rule을 직접 구현하였다.

```text
GPDK090 Design Rule 학습
        ↓
Rule의 의미 분석
        ↓
Cadence Virtuoso Layout 구현
        ↓
Width / Spacing / Enclosure 측정
        ↓
DRC를 통한 Rule 확인
        ↓
Physical Structure 분석
        ↓
Semiconductor Physics와 연결
```

각 Rule을 Layout으로 직접 구현하면서 Design Rule Manual에 제시된 수치가 실제 Layout에서 어떠한 Geometry를 의미하는지 확인할 수 있었다.

---

# 35. 프로젝트 결과

본 프로젝트를 통해 다음 내용을 학습할 수 있었다.

### Layout 관점

- Nwell, Active, Implant, Poly의 관계
- MOSFET Layout 구조
- Contact와 Metal의 연결
- Via를 이용한 Multi-Level Interconnect
- Silicide와 SIPROT의 관계
- Width / Spacing / Enclosure Rule
- Metal Density
- Antenna Rule
- Latch-up Rule

### Process 관점

- Lithography Process Margin
- Mask Alignment Error
- STI Isolation
- Implantation
- Silicide Formation
- Contact/Via Formation
- CMP
- Plasma Process

### Device Physics 관점

- PN Junction
- Depletion Region
- Metal–Semiconductor Junction
- Schottky Barrier
- Thermionic Emission
- Tunneling
- Field Emission
- Interface State
- Fermi Level Pinning
- Ohmic Contact

### Reliability 관점

- Contact Resistance
- Electromigration
- Latch-up
- Antenna Effect
- CMP Uniformity

---

# 36. 고찰

이번 프로젝트를 통해 가장 중요하게 확인한 점은 **Design Rule의 각 수치에는 공정적 또는 전기적인 이유가 존재한다는 것**이다.

처음 Design Rule을 접하면 Minimum Width, Spacing, Enclosure 등의 규칙은 단순히 DRC Error를 피하기 위해 지켜야 하는 Layout 조건처럼 보일 수 있다.

그러나 실제로는 각각의 Rule이 Semiconductor Process 및 Device Reliability와 직접적으로 연결되어 있었다.

| Design Rule | 주요 물리적 의미 |
|---|---|
| Nwell Width / Spacing | Junction Isolation, Breakdown, Latch-up |
| Active Width / Spacing | STI Isolation, Device Formation |
| Implant Enclosure | Doping Region 확보 |
| Poly Rule | Gate Dimension 및 Lithography |
| Contact Rule | Contact Resistance 및 Process Margin |
| SIPROT Rule | Silicide Formation 제어 |
| Metal Width | Resistance, Current Density, EM |
| Metal Density | CMP Uniformity |
| Via Rule | Vertical Interconnect Reliability |
| Latch-up Rule | Parasitic PNPN Turn-on 방지 |
| Antenna Rule | Gate Oxide Plasma Damage 방지 |

특히 Contact Design Rule을 분석하면서 Layout Geometry와 Semiconductor Physics의 관계를 명확하게 확인할 수 있었다.

Depletion Width는

$$
W=
\sqrt{
\frac{
2\varepsilon_s(V_{bi}-V)
}{
qN
}
}
$$

이므로

$$
N\uparrow
\Rightarrow
W\downarrow
$$

이다.

Tunneling Probability는

$$
T\propto e^{-2\kappa W}
$$

이므로

$$
W\downarrow
\Rightarrow
T\uparrow
$$

이다.

따라서

$$
\boxed{
N\uparrow
\Rightarrow
W\downarrow
\Rightarrow
T\uparrow
\Rightarrow
R_C\downarrow
}
$$

의 관계를 얻을 수 있다.

이를 통해 Heavy Doping에 의해 Barrier가 매우 얇아지면 Field Emission/Tunneling이 지배적이 되어 낮은 Contact Resistance의 Ohmic Contact처럼 동작할 수 있음을 이해하였다.

또한 실제 Metal–Semiconductor Interface에서는 이상적인 Schottky-Mott Model만으로 Contact 특성을 완전히 설명하기 어렵다.

실제 Interface에는 다음과 같은 요소가 존재할 수 있다.

- Dangling Bond
- Crystal Defect
- Contamination
- Oxide
- Interface State

이러한 Interface State는 Charge를 Capture하거나 Release하면서 Fermi Level Pinning을 발생시킬 수 있다.

결과적으로 실제 CMOS Contact의 특성은 다음 요소들이 복합적으로 작용하여 결정된다고 이해할 수 있다.

$$
\boxed{
\text{Material}
+
\text{Doping}
+
\text{Interface Quality}
+
\text{Silicide}
+
\text{Layout Geometry}
}
$$

Metal Rule에서도 유사한 관계를 확인할 수 있었다.

Metal Width가 감소하면

$$
A=Wt
$$

에 따라 단면적이 감소한다.

동일한 Current가 흐를 경우

$$
J=\frac{I}{Wt}
$$

이므로 Current Density가 증가한다.

높은 Current Density는 Electromigration을 가속하며 Black's Equation

$$
MTTF
=
A
J^{-n}
\exp
\left(
\frac{E_a}{kT}
\right)
$$

에서 확인할 수 있듯 Interconnect Lifetime을 감소시킨다.

따라서 Metal Minimum Width 역시 단순한 Lithography Constraint가 아니라 Interconnect Reliability와 연결된다.

---

# 37. 결론

본 프로젝트에서는 GPDK090 CMOS 공정의 Design Rule을 학습하고 **Cadence Virtuoso를 이용하여 주요 Design Rule을 직접 Layout으로 구현하고 DRC 관점에서 확인하였다.**

Nwell, Active, Thick Active, Implant, Poly, Contact, Silicide, SIPROT, Metal 및 Via와 같은 기본적인 Layout Layer뿐만 아니라 Metal Density, Latch-up 및 Antenna와 같은 Process 및 Reliability Rule까지 학습하였다.

특히 전체 Design Rule 구현 이후 **Metal 및 Contact Rule을 중심으로 Metal–Semiconductor Contact Physics를 심화 분석**하였다.

이상적인 Metal–Semiconductor Interface에서는 Schottky-Mott Rule에 따라 Barrier Height를 설명할 수 있다.

n-type Semiconductor를 기준으로 이상적인 Electron Barrier Height는 다음과 같다.

$$
\Phi_{Bn}
=
\Phi_m-\chi
$$

그러나 실제 Interface에서는 Surface State와 Interface State가 존재하며, 이에 의해 Fermi Level Pinning이 발생할 수 있으므로 단순한 Work Function 관계만으로 실제 Contact 특성을 완전히 설명할 수 없다.

또한 CMOS Source/Drain Contact에서는 Heavy Doping을 통해 Barrier Width를 감소시키는 것이 중요하다.

$$
W\propto\frac{1}{\sqrt{N}}
$$

따라서

$$
N\uparrow
\Rightarrow
W\downarrow
$$

이다.

Barrier Width가 감소하면

$$
T\propto e^{-2\kappa W}
$$

에 따라 Tunneling Probability가 증가한다.

결과적으로 다음의 관계를 갖는다.

$$
\boxed{
N\uparrow
\Rightarrow
W\downarrow
\Rightarrow
T\uparrow
\Rightarrow
R_C\downarrow
}
$$

따라서 Contact 주변의 N+/P+ Implant Enclosure Rule은 단순한 Mask Geometry 조건을 넘어 **Contact 아래에 충분한 Heavy Doping을 확보하여 낮은 Contact Resistance의 안정적인 Ohmic Contact를 구현하는 것과 연결되는 Rule**로 이해할 수 있다.

Metal Rule 역시 배선 Geometry와 Reliability가 직접 연결되어 있다.

Metal Width가 감소하면 Current Density가 증가하고 Electromigration이 가속될 수 있다.

따라서 Metal Width, Via, Density 등의 Rule은 단순히 DRC를 통과하기 위한 조건이 아니라 실제 Interconnect의 Electrical Performance와 Long-term Reliability를 확보하기 위한 조건이다.

최종적으로 본 프로젝트를 통해 다음과 같은 연결 관계를 확인할 수 있었다.

$$
\boxed{
\text{Design Rule}
\rightarrow
\text{Layout Geometry}
\rightarrow
\text{Fabrication Process}
\rightarrow
\text{Physical Structure}
\rightarrow
\text{Electrical Characteristic}
\rightarrow
\text{Device Reliability}
}
$$

따라서 Full-Custom IC Layout에서 Design Rule을 올바르게 이해하기 위해서는 Rule의 수치를 단순히 암기하는 것보다 **왜 해당 Geometry가 필요한지, 해당 Geometry가 실제 Wafer 구조에 어떻게 반영되며 최종적으로 Device의 Electrical Characteristic과 Reliability에 어떠한 영향을 미치는지를 함께 이해하는 것이 중요하다.**

본 프로젝트를 통해 Cadence Virtuoso에서 Design Rule을 직접 구현하면서 **Layout Design, Semiconductor Process, Device Physics가 서로 분리된 개념이 아니라 실제 CMOS 설계를 구성하는 하나의 연속적인 과정**임을 확인할 수 있었다.
