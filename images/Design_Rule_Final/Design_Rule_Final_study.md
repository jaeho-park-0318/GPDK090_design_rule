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

이를 통해 최종적으로

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

의 연결 관계를 이해하는 것을 목표로 하였다.

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

즉,

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

로 이해할 수 있다.

---

## 2.2 Width

Width Rule은 특정 Layer가 가져야 하는 최소 또는 최대 폭을 정의한다.

예를 들어 Nwell의 최소 폭이

$$
W_{Nwell} \geq 0.6\,\mu m
$$

으로 규정되어 있다면, Nwell Pattern은 어느 방향에서도 해당 최소 폭을 만족해야 한다.

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

- Lithography Pattern Collapse
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

## 2.5 Overlap

Overlap은 두 Layer가 의도적으로 일정 영역 이상 겹쳐야 하는 조건이다.

이를 통해 공정 오차가 존재하더라도 두 구조가 안정적으로 연결되거나 원하는 Device Structure를 형성할 수 있도록 한다.

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

따라서 Nwell Design Rule은 다음을 확보하기 위해 존재한다.

- Process Alignment Margin
- PN Junction Isolation
- Breakdown 특성
- Body Potential 안정화
- Latch-up Reliability

---

## 4.2 Minimum Nwell Width

Nwell이 지나치게 좁으면 Lithography 및 Well Implant 과정에서 원하는 Doping Profile을 안정적으로 형성하기 어렵다.

본 프로젝트에서는 다음 최소 폭을 구현하였다.

$$
W_{Nwell} \geq 0.6\,\mu m
$$

---

## 4.3 Nwell Spacing

### Same Potential

동일한 전위를 가지는 Nwell 사이에는

$$
S_{Nwell} \geq 0.6\,\mu m
$$

의 간격을 적용하였다.

### Different Potential

서로 다른 전위를 가지는 Nwell 사이에는

$$
S_{Nwell} \geq 1.2\,\mu m
$$

의 더 큰 간격을 적용하였다.

서로 다른 전위가 인가되는 경우 두 Well 사이의 Electric Field와 Depletion Region에 의해 Leakage 및 Breakdown 가능성이 증가할 수 있기 때문이다.

따라서

$$
S_{\text{different potential}}
>
S_{\text{same potential}}
$$

의 관계가 나타난다.

---

## 4.4 Nwell에 대한 고찰

Nwell Design Rule을 직접 Layout으로 구현하면서 Well Rule이 단순히 PMOS가 들어갈 공간을 확보하기 위한 규칙이 아니라는 점을 확인할 수 있었다.

Nwell은 PMOS의 Body Potential을 결정하며 주변 P-type 영역과 PN Junction을 형성한다.

따라서 Nwell Geometry는

- Device Isolation
- Junction Leakage
- Breakdown
- Body Effect
- Latch-up

과 연결되어 있다.

즉, Nwell Rule은 **PMOS가 형성되는 N-type Body 영역의 크기와 주변 간격을 충분히 확보하여 공정 오차를 보상하고 PN Junction Isolation, Breakdown 특성, Body Potential 및 Latch-up 신뢰성을 확보하기 위한 규칙**이라고 이해할 수 있다.

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

따라서 Active 사이에 일정한 Minimum Spacing을 확보해야 한다.

---

## 5.4 N+ / P+ Active Spacing

N+ Active와 P+ Active가 지나치게 가까우면 PN Junction의 Depletion Region 및 Leakage 문제가 발생할 수 있다.

따라서 서로 다른 Doping Type의 Active Area 사이에도 적절한 Spacing이 필요하다.

---

## 5.5 Active Area에 대한 고찰

Active Rule을 구현하면서 Active Layer가 단순히 Source/Drain을 그리는 Layer가 아니라 **STI와 함께 Device Isolation을 결정하는 핵심 Layer**라는 점을 확인할 수 있었다.

Active Geometry가 잘못되면 개별 Transistor의 문제에서 끝나는 것이 아니라 주변 Device와의 Electrical Isolation까지 영향을 받을 수 있다.

---

# 6. Thick Active Design Rule

## 6.1 Thick Active의 개념

Thick Active는 일반 Core Device보다 높은 전압을 처리하는 **Thick-Oxide Device 영역을 정의하는 Layer**이다.

일반적으로 높은 전압을 사용하는 Device에서는 Gate Oxide를 더 두껍게 형성하여 높은 Electric Field에 의한 Oxide Breakdown을 방지한다.

---

## 6.2 Thick Active Width 및 Spacing

Thick Active는 일반 Active와 다른 Process 조건을 사용하기 때문에 별도의 Width와 Spacing Rule을 가진다.

Thick Active 영역이 지나치게 좁으면 Thick Oxide Process의 경계가 불안정해질 수 있다.

또한 일반 Active와 지나치게 가까우면 Thick Oxide와 Thin Oxide Process 영역 사이의 Transition Region이 충분히 확보되지 않을 수 있다.

---

## 6.3 Thick Active Enclosure

Thick Active Layer는 실제 Thick-Oxide Device가 형성될 Active 영역을 충분히 Enclosure해야 한다.

이를 통해 Mask Misalignment가 발생하더라도 Device 전체가 의도한 Thick-Oxide Process 영역에 포함되도록 한다.

---

## 6.4 Thick Active에 대한 고찰

Thick Active Rule을 통해 동일한 CMOS Process 내부에서도 모든 Transistor가 동일한 구조를 사용하는 것이 아니라는 점을 확인하였다.

Core Device와 I/O Device는 서로 다른 Operating Voltage를 사용하며, 이에 따라 Gate Oxide Thickness와 Layout Rule 역시 달라진다.

즉,

$$
V_{\text{Operating}}\uparrow
\Rightarrow
E_{\text{Oxide}}\uparrow
\Rightarrow
\text{Breakdown Risk}\uparrow
$$

이므로 높은 전압 Device에서는 더 두꺼운 Oxide와 충분한 Process Margin이 필요하다.

---

# 7. N+/P+ Implant Design Rule

## 7.1 Implant의 개념

Implant Layer는 Silicon 내부의 Doping Type과 Doping Region을 정의한다.

대표적으로

- N+ Implant → NMOS Source/Drain, Nwell Tap
- P+ Implant → PMOS Source/Drain, P-substrate Tap

에 사용된다.

```text
NMOS

N+        Gate        N+
│          │           │
Source   Channel     Drain
└──────────┬───────────┘
       P-Substrate
```

```text
PMOS

P+        Gate        P+
│          │           │
Source   Channel     Drain
└──────────┬───────────┘
          Nwell
```

---

## 7.2 Implant와 Active Area

Implant Layer는 Active Area와 함께 사용되어 실제 Source/Drain의 Doping Type을 결정한다.

따라서 Implant가 Active Area를 충분히 포함하지 못하면 일부 Source/Drain 영역이 원하는 농도로 도핑되지 않을 수 있다.

이 때문에 Implant-to-Active Enclosure Rule이 필요하다.

---

## 7.3 Implant Spacing

서로 다른 Implant 영역이 지나치게 가까우면 Lateral Diffusion과 Process Variation으로 인해 Doping Profile이 예상과 달라질 수 있다.

특히 N+와 P+ 영역이 가까운 경우 PN Junction의 특성과 Leakage에 영향을 줄 수 있다.

---

## 7.4 Implant와 Contact

Contact 아래에는 충분한 N+ 또는 P+ Doping이 존재해야 한다.

이는 이후 Contact 부분에서 설명할 Metal–Semiconductor Contact의 Barrier Width와 Contact Resistance에 직접적인 영향을 준다.

즉,

$$
\text{High Doping}
\rightarrow
\text{Thin Barrier}
\rightarrow
\text{Tunneling Increase}
\rightarrow
R_C \downarrow
$$

의 관계를 가진다.

---

## 7.5 Implant에 대한 고찰

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

따라서 Poly Width는 MOSFET의 Gate Length와 관련될 수 있으며 Device 특성에 직접적인 영향을 준다.

---

## 8.2 Poly Width

Poly가 지나치게 좁으면 Lithography 한계로 인해 Pattern 형성이 어려워지고 Gate Length Variation이 증가할 수 있다.

Gate Length Variation은

- Threshold Voltage
- Drain Current
- Short Channel Effect
- Leakage

등에 직접적인 영향을 준다.

---

## 8.3 Poly Spacing

Poly Line 사이의 간격이 지나치게 작으면 Lithography 및 Etching 과정에서 두 Pattern이 연결될 가능성이 증가한다.

또한 Parasitic Coupling이 증가할 수 있다.

---

## 8.4 Poly와 Active

Poly가 Active Area를 가로지르면 MOSFET Gate가 형성되므로 Poly-to-Active 관계는 매우 중요하다.

Poly가 Active 경계를 충분히 넘어가도록 Extension Rule을 적용하여 Mask Alignment Error가 존재하더라도 Channel 전체가 안정적으로 형성되도록 한다.

---

## 8.5 POLY Density Ratio

Chip 전체 또는 특정 Window 내부에서 Poly가 차지하는 면적 비율을 다음과 같이 표현할 수 있다.

$$
\text{POLY Density Ratio}
=
\frac{A_{\text{POLY}}}
{A_{\text{Total}}}
\times100
$$

Poly Density가 지나치게 높거나 불균일하면 Pattern Density에 따른 공정 편차가 증가할 수 있다.

따라서 Density Rule을 통해 Chip-level Process Uniformity를 확보한다.

---

## 8.6 Poly에 대한 고찰

Poly는 MOSFET의 Gate를 직접 형성하므로 다른 일반 배선 Layer보다 Device Performance에 직접적인 영향을 준다.

또한 Gate Oxide가 매우 얇기 때문에 Poly와 연결된 구조는 Antenna Effect에도 민감하다.

따라서 Poly Rule은

- Device Dimension
- Lithography
- Gate Oxide Reliability
- Process Uniformity

를 함께 고려하는 Rule이라고 볼 수 있다.

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

Silicide가 형성되면 Source/Drain 및 Poly의 저항을 감소시킬 수 있다.

---

## 9.2 Non-Silicide의 개념

모든 영역에서 낮은 저항이 필요한 것은 아니다.

Poly Resistor와 같이 의도적으로 높은 Resistance를 사용해야 하는 영역에서는 Silicide가 형성되면 원하는 저항값을 얻을 수 없다.

따라서 특정 영역에서는 Silicide Formation을 의도적으로 차단한다.

이를 **Non-Silicide Region**이라고 볼 수 있다.

---

## 9.3 Silicide와 Non-Silicide의 차이

| 구분 | Silicide | Non-Silicide |
|---|---|---|
| 목적 | 저항 감소 | 높은 저항 유지 |
| Sheet Resistance | 낮음 | 높음 |
| Source/Drain | 주로 사용 | 일반적으로 제한적 |
| Poly Resistor | 부적합할 수 있음 | 적합 |
| Contact Resistance | 감소 | 상대적으로 증가 |

---

## 9.4 고찰

Silicide와 Non-Silicide 구조를 비교하면서 동일한 Poly 또는 Active Layer라도 목적에 따라 전기적 특성을 의도적으로 변화시킬 수 있다는 점을 확인하였다.

즉, Layout Layer는 단순히 Device Geometry만 정의하는 것이 아니라 Process 선택을 통해 최종적인 Electrical Property까지 결정할 수 있다.

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

대표적으로 Poly Resistor와 같은 구조에서 사용될 수 있다.

---

## 10.2 SIPROT Width 및 Spacing

SIPROT 영역이 지나치게 좁으면 Process Variation에 의해 의도하지 않은 Silicide가 형성될 수 있다.

따라서 Minimum Width가 필요하다.

또한 SIPROT와 Contact, Active 및 Gate 사이에는 충분한 Spacing이 필요하다.

---

## 10.3 SIPROT와 Contact

SIPROT가 Contact에 지나치게 가까우면 Contact 주변에 필요한 Silicide가 충분히 형성되지 않아 Contact Resistance가 증가할 수 있다.

따라서 SIPROT-to-Contact Spacing을 확보해야 한다.

---

## 10.4 SIPROT와 Gate

Gate 주변에서는 Silicide 형성 영역이 불균일해지지 않도록 SIPROT와 Gate 사이에 충분한 간격이 필요하다.

---

## 10.5 SIPROT에 대한 고찰

SIPROT Rule을 통해 Silicide는 단순히 존재하거나 존재하지 않는 구조가 아니라 Layout 단계에서 선택적으로 제어되는 Process임을 확인하였다.

특히 SIPROT Geometry가 잘못되면

- 원하지 않는 Silicide Formation
- 저항값 변화
- Contact Resistance 증가

등이 발생할 수 있으므로 충분한 Process Margin이 필요하다.

---

# 11. Contact Design Rule

## 11.1 Contact의 개념

Contact는 Semiconductor 또는 Poly와 Metal1을 전기적으로 연결하는 수직 Interconnect이다.

Source/Drain Contact를 단순화하면 다음과 같다.

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

Contact는 단순한 Hole이 아니라 **Metal과 Semiconductor 사이의 전기적 접촉이 실제로 형성되는 중요한 영역**이다.

---

## 11.2 Contact Width

Contact가 지나치게 작으면 Lithography 및 Etching 공정에서 Contact Hole이 완전히 열리지 않을 수 있다.

그 결과

- Open
- Contact Resistance 증가
- Contact 불량

이 발생할 수 있다.

---

## 11.3 Contact Spacing

Contact 사이의 간격이 지나치게 작으면 Lithography 또는 Etching 과정에서 두 Contact가 서로 연결될 수 있다.

따라서 Minimum Contact Spacing을 확보해야 한다.

---

## 11.4 Contact-to-Gate Spacing

Contact가 Gate에 지나치게 가까우면 공정 오차에 의해 Gate와 Contact가 Short될 수 있다.

본 프로젝트에서는 다음과 같은 Rule을 구현하였다.

$$
\text{CONT.SE.1}=0.10\,\mu m
$$

이를 통해 Gate 영역과 Contact 영역 사이에 충분한 Process Margin을 확보한다.

---

## 11.5 Implant-to-Contact Enclosure

본 프로젝트에서 Metal–Semiconductor Contact Physics와 직접 연결하여 분석한 핵심 Rule 중 하나는 다음과 같다.

$$
\boxed{
\text{CONT.E.4}=0.06\,\mu m
}
$$

즉, N+/P+ Implant가 Contact를 사방으로 최소 $0.06\,\mu m$ 이상 감싸야 한다.

이 Rule은 두 가지 관점에서 이해할 수 있다.

### Process 관점

Mask Overlay Error가 존재하더라도 Contact 전체가 N+/P+ Doped Region 내부에 위치하도록 한다.

### Electrical 관점

Contact 아래 Silicon이 충분히 Heavy Doping된 상태를 유지하여 낮은 Contact Resistance의 Ohmic Contact를 형성하도록 한다.

따라서

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

의 관계를 가진다.

---

# 12. Metal–Semiconductor Contact

## 12.1 접촉 전 상태

Metal과 Semiconductor가 접촉하기 전에는 서로 다른 Work Function과 Fermi Level을 가진다.

Metal의 Work Function은

$$
\Phi_m
$$

Semiconductor의 Work Function은

$$
\Phi_s
$$

로 나타낼 수 있다.

---

## 12.2 접촉 후 상태

Metal과 Semiconductor가 접촉하면 Thermal Equilibrium을 이루기 위해 Carrier가 이동한다.

평형 상태에서는 Fermi Level이 일정해야 하므로

$$
E_{F,m}=E_{F,s}
$$

가 된다.

이 과정에서 Semiconductor의 Energy Band가 휘어지는 **Band Bending**이 발생한다.

결과적으로 Metal–Semiconductor Interface에 Potential Barrier가 형성될 수 있다.

---

# 13. Schottky-Mott Rule

이상적인 n-type Semiconductor를 기준으로 Schottky-Mott Rule에서는 Metal과 Semiconductor의 Work Function 관계에 따라 Contact 특성을 예측할 수 있다.

$$
\boxed{
\Phi_m>\Phi_s
\quad\Rightarrow\quad
\text{Schottky Contact}
}
$$

$$
\boxed{
\Phi_s>\Phi_m
\quad\Rightarrow\quad
\text{Ohmic Contact}
}
$$

이상적인 n-type Metal–Semiconductor Junction에서 Electron에 대한 Schottky Barrier Height는

$$
\boxed{
\Phi_{Bn}=\Phi_m-\chi
}
$$

로 표현할 수 있다.

여기서

- $\Phi_m$: Metal Work Function
- $\chi$: Semiconductor Electron Affinity
- $\Phi_{Bn}$: Electron Schottky Barrier Height

이다.

이상적인 Schottky-Mott Model에서는 Barrier Height가 주로 Material Combination에 의해 결정된다.

---

# 14. Schottky Contact

## 14.1 Schottky Contact의 개념

Schottky Contact는 Metal–Semiconductor Interface에 형성된 Barrier에 의해 한 방향으로 Current가 더 쉽게 흐르는 **Rectifying Contact**이다.

Schottky Contact에서는 일반적으로 Carrier가 Barrier를 넘어 이동하는 Thermionic Emission이 중요한 전도 Mechanism이 된다.

---

## 14.2 Thermionic Emission

Schottky Contact의 Current Density는 다음과 같이 표현할 수 있다.

$$
J=
A^*T^2
\exp
\left(
-\frac{q\Phi_B}{kT}
\right)
\left[
\exp
\left(
\frac{qV}{nkT}
\right)-1
\right]
$$

여기서

- $A^*$: Effective Richardson Constant
- $T$: Absolute Temperature
- $q$: Electron Charge
- $\Phi_B$: Schottky Barrier Height
- $k$: Boltzmann Constant
- $n$: Ideality Factor

이다.

Saturation Current Density를

$$
J_S=
A^*T^2
\exp
\left(
-\frac{q\Phi_B}{kT}
\right)
$$

로 정의하면

$$
J=
J_S
\left[
\exp
\left(
\frac{qV}{nkT}
\right)-1
\right]
$$

로 나타낼 수 있다.

따라서

$$
\Phi_B\uparrow
\Rightarrow
J_S\downarrow
$$

가 된다.

---

## 14.3 Schottky Contact의 I–V 특성

Schottky Contact는 Rectifying 특성을 가진다.

Forward Bias에서는 Current가 급격하게 증가한다.

Reverse Bias에서는 작은 Reverse Saturation Current가 흐른다.

```text
Current
  ↑
  │                       /
  │                    __/
  │                 __/
  │             ____/
──┼──────────────────────→ Voltage
  │
  │ small reverse current
```

따라서 Schottky Contact의 특징은 다음과 같다.

- Non-linear I–V
- Rectifying Characteristic
- Forward Bias에서 Current 급격히 증가
- Reverse Bias에서 작은 Saturation Current
- Thermionic Emission이 중요한 전도 Mechanism

---

# 15. Ohmic Contact

## 15.1 Ohmic Contact의 개념

Ohmic Contact는 양의 Bias와 음의 Bias 모두에서 Current가 Voltage에 거의 선형적으로 비례하는 **Non-Rectifying Contact**이다.

이상적인 Ohmic Contact에서는

$$
\boxed{
I=\frac{V}{R_C}
}
$$

가 성립한다.

따라서

$$
\frac{dI}{dV}
=
\frac{1}{R_C}
$$

이다.

Contact Resistance가 작을수록 I–V Curve의 기울기가 커진다.

```text
Current
   ↑
   │        /
   │      /
   │    /
───┼──────────→ Voltage
  /│
 / │
```

MOSFET Source/Drain Contact에서는 가능한 한 낮은 Contact Resistance의 Ohmic Contact가 요구된다.

---

# 16. Heavy Doping과 Barrier Width

실제 CMOS Contact에서는 Metal과 Semiconductor 사이의 Barrier Height만큼이나 **Barrier Width**가 중요하다.

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

여기서

- $\varepsilon_s$: Semiconductor Permittivity
- $V_{bi}$: Built-in Potential
- $V$: Applied Voltage
- $q$: Electron Charge
- $N$: Doping Concentration

이다.

따라서

$$
\boxed{
W\propto\frac{1}{\sqrt{N}}
}
$$

이다.

즉,

$$
N\uparrow
\Rightarrow
W\downarrow
$$

가 된다.

Heavy Doping을 사용할수록 Metal–Semiconductor Interface의 Barrier Width가 감소한다.

---

# 17. Tunneling과 Field Emission

Barrier Width가 충분히 얇아지면 Carrier는 Barrier를 열적으로 넘어가는 대신 Quantum Mechanical Tunneling을 통해 통과할 수 있다.

Barrier 내부에서 Schrödinger Equation을 단순화하면 Wave Function은 다음과 같은 형태를 가진다.

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

따라서 Barrier Width가 $W$일 때 Tunneling Probability는 대략

$$
\boxed{
T\propto e^{-2\kappa W}
}
$$

로 표현할 수 있다.

이 식에서 중요한 것은 $W$가 Exponential Term 내부에 존재한다는 점이다.

따라서 Barrier Width가 감소하면 Tunneling Probability는 매우 빠르게 증가한다.

$$
W\downarrow
\Rightarrow
T\uparrow
$$

Heavy Doping과 연결하면

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

가 된다.

---

# 18. Carrier Transport Mechanism의 변화

Doping Concentration이 증가하면 Metal–Semiconductor Contact의 주요 Carrier Transport Mechanism은 다음과 같이 변화할 수 있다.

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

낮은 Doping에서는 Barrier가 넓기 때문에 Carrier가 Barrier를 넘어가는 Thermionic Emission이 중요하다.

반면 높은 Doping에서는 Barrier Width가 매우 얇아져 Carrier의 Tunneling이 중요해진다.

따라서 본질적으로 Barrier가 존재하더라도 Electrical Characteristic은 낮은 Contact Resistance를 가지는 Ohmic Contact에 가까워질 수 있다.

---

# 19. Surface State

Semiconductor Bulk 내부에서는 각 Silicon Atom의 Bond가 주변 Atom과 결합되어 있다.

하지만 Semiconductor Surface에서는 Crystal Structure가 끝나기 때문에 일부 Bond가 끊어진 상태로 남을 수 있다.

이를 **Dangling Bond**라고 한다.

```text
Bulk

Si — Si — Si
|    |    |
Si — Si — Si


Surface

↑    ↑    ↑
Dangling Bonds

Si — Si — Si
|    |    |
Si — Si — Si
```

이러한 Dangling Bond 등에 의해 Semiconductor Bandgap 내부에 Energy State가 형성될 수 있다.

이를 **Surface State**라고 한다.

Surface State의 원인으로는 다음이 있다.

- Dangling Bond
- Step / Edge
- Crystal Defect
- Contamination

---

# 20. Interface State

Interface State는 서로 다른 두 Material이 만나는 Boundary에서 형성되는 Energy State이다.

Metal–Semiconductor Interface에서는

- Bonding Mismatch
- Crystal Defect
- Contamination
- Oxide
- Interface Disorder

등에 의해 Bandgap 내부에 Interface State가 형성될 수 있다.

```text
Metal │ Semiconductor
      │
      │ ─ Interface State
      │ ─ Interface State
      │ ─ Interface State
```

Interface State는 Electron을 Capture하거나 Release할 수 있으므로 Interface Charge를 형성할 수 있다.

---

# 21. Fermi Level Pinning

이상적인 Schottky-Mott Model에서는 Metal Work Function을 변화시키면 Barrier Height도 이에 따라 변화해야 한다.

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

# 22. Schottky-Mott Model과 실제 Contact

이상적인 경우에는

$$
\Phi_{Bn}=\Phi_m-\chi
$$

로 Barrier Height를 예측할 수 있다.

하지만 실제 Interface에서는

$$
\text{Interface State}
\rightarrow
\text{Fermi Level Pinning}
\rightarrow
\text{Barrier Height 변화}
$$

가 발생할 수 있다.

따라서 실제 CMOS Contact에서는 다음 요소를 함께 고려해야 한다.

- Metal Work Function
- Semiconductor Electron Affinity
- Doping Concentration
- Interface State
- Fermi Level Pinning
- Silicide
- Contact Geometry

---

# 23. CONT.E.4와 Ohmic Contact의 연결

본 프로젝트에서 Contact Physics와 Layout Design Rule의 관계를 가장 직접적으로 보여주는 Rule이 `CONT.E.4`이다.

$$
\boxed{
\text{CONT.E.4}=0.06\,\mu m
}
$$

N+/P+ Implant가 Contact를 사방으로 최소 $0.06\,\mu m$ 이상 감싸야 한다.

첫 번째 이유는 **Mask Overlay Error에 대한 Process Margin**이다.

Contact Mask와 Implant Mask 사이에 Alignment Error가 발생하더라도 Contact 전체가 Doped Region 내부에 존재하도록 한다.

두 번째 이유는 **Electrical Contact 특성**이다.

Contact 아래 전체 Silicon 영역이 충분히 Heavy Doping되어 있어야 한다.

Heavy Doping이 유지되면

$$
N\uparrow
$$

이고 Depletion Width는

$$
W\propto\frac{1}{\sqrt{N}}
$$

이므로

$$
W\downarrow
$$

한다.

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

한다.

따라서 전체적인 관계는 다음과 같다.

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

즉, Contact Enclosure Rule은 단순한 Geometry Constraint가 아니라 **실제 Contact Resistance와 Metal–Semiconductor Carrier Transport 특성에 연결되는 Rule**로 이해할 수 있다.

---

# 24. Schottky Contact와 Ohmic Contact 비교

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

핵심적인 차이는 Barrier의 단순한 존재 여부가 아니라 **Carrier가 해당 Barrier를 얼마나 쉽게 통과할 수 있는가**에 있다.

---

# 25. Silicide와 Ohmic Contact

실제 CMOS Source/Drain Contact에서는 Heavy Doping뿐만 아니라 Silicide도 Contact Resistance를 감소시키는 데 중요한 역할을 한다.

구조적으로는 다음과 같이 이해할 수 있다.

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

Silicide는 Source/Drain 및 Poly의 Sheet Resistance를 낮추고 Contact Interface의 Series Resistance를 감소시키는 역할을 한다.

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

# 26. Metal1 Design Rule

## 26.1 Metal1의 역할

Metal1은 Contact를 통해 Transistor Source/Drain 또는 Poly와 연결되는 첫 번째 Metal Interconnect Layer이다.

Metal1은 신호 전달뿐만 아니라 Power Distribution에도 사용될 수 있기 때문에

- Width
- Spacing
- Current Density
- Electromigration
- Density

등을 함께 고려해야 한다.

---

## 26.2 Metal Width와 Resistance

Metal 배선의 Resistance는

$$
R=\rho\frac{L}{A}
$$

로 표현할 수 있다.

Metal 단면적은

$$
A=W\times t
$$

이므로

$$
R=
\rho
\frac{L}{Wt}
$$

가 된다.

따라서

$$
W\downarrow
\Rightarrow
R\uparrow
$$

이다.

Metal Width가 지나치게 좁으면 배선 저항이 증가하고 IR Drop 및 RC Delay가 증가할 수 있다.

---

# 27. Metal Width와 Current Density

Current Density는

$$
J=\frac{I}{A}
$$

이고 Metal의 단면적이

$$
A=W\times t
$$

이므로

$$
\boxed{
J=
\frac{I}{Wt}
}
$$

이다.

따라서 동일한 Current에서 Metal Width가 감소하면

$$
W\downarrow
\Rightarrow
J\uparrow
$$

가 된다.

Current Density 증가는 Electromigration Reliability와 직접적으로 연결된다.

---

# 28. Electromigration

Electromigration은 높은 Current Density에서 전도 Electron과 Metal Atom 사이의 Momentum Transfer에 의해 Metal Atom이 이동하는 현상이다.

이를 **Electron Wind Force**와 연결하여 이해할 수 있다.

```text
Electron Flow  ← ← ← ← ←

Metal Atom
● ● ● ● ● ● ● ●

Conventional Current → → → →
```

Metal Atom이 이동하면서 Upstream 영역에는 Vacancy가 축적될 수 있다.

Vacancy가 증가하면 **Void**가 형성될 수 있다.

```text
Metal Line

██████████    █████████
          VOID
```

Void는 배선 Resistance를 증가시키거나 Open Failure를 발생시킬 수 있다.

반대로 Atom이 축적되는 영역에서는 Hillock 또는 Whisker가 형성될 수 있다.

이는 인접 Metal Line과 Short를 발생시킬 가능성이 있다.

---

# 29. Black's Equation

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

또한 Temperature가 증가하면

$$
T\uparrow
\Rightarrow
MTTF\downarrow
$$

한다.

따라서 Metal Width Rule은 단순한 Lithography Rule이 아니라 Long-term Interconnect Reliability와 연결된다.

---

# 30. Metal Density

Metal Density는 특정 Window 내부에서 Metal이 차지하는 면적 비율이다.

$$
\text{Metal Density}
=
\frac{
A_{\text{Metal}}
}{
A_{\text{Window}}
}
\times100
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

본 프로젝트에서는 일정 크기의 Window를 이동시키면서 Metal Density를 검사하는 방식의 Rule을 학습하였다.

Metal Density Rule은 개별 배선의 문제가 아니라 **Chip-level Process Uniformity를 확보하기 위한 Rule**이다.

---

# 31. MIMCAP과 Metal

MIMCAP(Metal–Insulator–Metal Capacitor)은 두 Metal Electrode 사이에 절연막을 형성하여 구현하는 Capacitor이다.

기본적인 Capacitance는

$$
C=
\frac{
\varepsilon A
}{
d
}
$$

로 표현된다.

여기서

- $\varepsilon$: Dielectric Permittivity
- $A$: Electrode Overlap Area
- $d$: Dielectric Thickness

이다.

MIMCAP은 Metal Layer를 이용하는 구조이므로 Metal Geometry와 Density 조건을 함께 고려해야 한다.

특히 큰 MIMCAP Array는 특정 영역에서 높은 Metal Density를 만들 수 있으므로 주변 Dummy Metal 및 Density Rule과 함께 고려할 필요가 있다.

---

# 32. Via Design Rule

## 32.1 Via의 개념

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

Contact가 Semiconductor/Poly와 Metal1을 연결한다면, Via는 Metal Layer와 Metal Layer를 연결한다는 차이가 있다.

---

## 32.2 Via Enclosure

Via 주변 Metal이 충분한 Enclosure를 갖지 못하면 Mask Misalignment로 인해 Via의 일부가 Metal 밖으로 벗어날 수 있다.

그 결과

- Via Resistance 증가
- Current Crowding
- Open Failure

등이 발생할 수 있다.

따라서 Via는 상부 및 하부 Metal에 의해 충분히 Enclosure되어야 한다.

---

## 32.3 Via Stack

여러 Metal Layer를 연속으로 연결하면 Via Stack이 형성된다.

Via를 한 개만 사용할 경우 하나의 Via에 Current가 집중되므로 Current Density와 Reliability 문제가 발생할 수 있다.

반면 여러 Via를 병렬로 사용하면

$$
R_{\text{eq}}\downarrow
$$

하고 Current가 여러 Via로 분산된다.

따라서 Via Array는

- Via Resistance 감소
- Current Density 감소
- Electromigration Reliability 향상

에 도움이 된다.

---

# 33. Latch-up Rule

CMOS 구조에는 의도하지 않은 기생 PNP와 NPN BJT가 존재한다.

이들이 서로 Positive Feedback 구조를 형성하면 PNPN 구조가 Turn-on될 수 있다.

이를 **Latch-up**이라고 한다.

```text
PMOS Region                NMOS Region

P+   Nwell                 P-sub   N+
│      │                     │      │
└─ PNP ─┐                 ┌─ NPN ──┘
        └─────────────────┘
```

Latch-up이 발생하면 VDD와 GND 사이에 매우 큰 Current Path가 형성될 수 있다.

이를 방지하기 위해

- Nwell Tap
- P-substrate Tap
- Guard Ring

등을 사용한다.

Well/Substrate Tap을 Device 가까이에 배치하면 Well/Substrate Resistance를 감소시킬 수 있다.

따라서 기생 BJT가 Turn-on될 가능성을 감소시킬 수 있다.

---

# 34. Antenna Rule

## 34.1 Antenna Effect

Antenna Effect는 Plasma Etching 과정에서 Floating Metal 또는 Poly에 Charge가 축적되고, 이 Charge가 MOS Gate로 전달되면서 얇은 Gate Oxide에 높은 Electric Field가 인가되는 현상이다.

```text
Plasma
 ↓ ↓ ↓ ↓ ↓

────────────── Metal
              │
              │
             Gate
              │
         Gate Oxide
```

Gate Oxide가 매우 얇기 때문에 과도한 Charge가 축적되면 Oxide Breakdown이 발생할 수 있다.

---

## 34.2 Antenna Ratio

Antenna Effect는 기본적으로 Charge를 수집하는 Conductor Area와 Gate Area의 비율로 이해할 수 있다.

$$
AR=
\frac{
A_{\text{Antenna}}
}{
A_{\text{Gate}}
}
$$

Antenna Area가 증가하면 Plasma로부터 수집할 수 있는 Charge가 증가한다.

반대로 Gate Area가 작으면 동일한 Charge가 더 작은 Gate Oxide 영역에 집중된다.

따라서

$$
AR\uparrow
\Rightarrow
\text{Gate Oxide Damage Risk}\uparrow
$$

가 된다.

---

## 34.3 Antenna Rule의 의미

Antenna Rule은 완성된 IC의 정상 동작 상태보다 **Fabrication Process 중 발생하는 Reliability 문제를 방지하기 위한 Rule**이라는 점에서 중요하다.

즉, Layout Design Rule은 최종 Device Geometry뿐만 아니라 제조 순서와 Plasma Process까지 고려한다.

---

# 35. 전체 Design Rule과 Semiconductor Physics의 연결

본 프로젝트에서 가장 중요하게 확인한 점은 Layout Design Rule과 Semiconductor Physics가 서로 독립적으로 존재하지 않는다는 것이다.

```text
                    CMOS Design Rule
                          │
        ┌─────────────────┼─────────────────┐
        │                 │                 │
     Process          Electrical        Reliability
        │              Isolation            │
        │                 │                 │
Width / Spacing      Well / Implant      Latch-up
Enclosure            Junction            Antenna
Density              Leakage             EM / CMP
        │
        ↓
Device / Interconnect Geometry
        │
        ↓
Physical Structure
        │
        ↓
Electrical Characteristic
```

특히 Contact에서는 이러한 관계가 가장 명확하게 나타난다.

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

# 36. 프로젝트 수행 과정

본 프로젝트에서는 Design Rule Manual에 있는 규칙을 단순히 정리하는 방식이 아니라 Cadence Virtuoso에서 각 Rule을 직접 구현하였다.

전체적인 과정은 다음과 같다.

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

# 37. 프로젝트 결과

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

# 38. 고찰

이번 프로젝트를 통해 가장 중요하게 확인한 점은 **Design Rule의 각 수치에는 공정적 또는 전기적인 이유가 존재한다는 것**이다.

처음 Design Rule을 접하면 Minimum Width, Spacing, Enclosure 등의 규칙은 단순히 DRC Error를 피하기 위해 지켜야 하는 Layout 조건처럼 보일 수 있다.

그러나 실제로는 각각의 Rule이 Semiconductor Process 및 Device Reliability와 직접적으로 연결되어 있었다.

대표적으로 다음과 같이 정리할 수 있다.

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

Contact 아래에 N+/P+ Implant가 충분히 존재해야 한다는 Enclosure Rule은 단순히 Mask Alignment Margin만을 위한 것이 아니다.

Contact 전체가 Heavy Doped Region 위에 존재하도록 하여 Metal–Semiconductor Barrier Width를 감소시키고 Tunneling을 증가시키는 역할과 연결된다.

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

이를 통해 본질적으로 Schottky Barrier가 존재하더라도 Heavy Doping에 의해 Barrier가 매우 얇아지면 Field Emission/Tunneling이 지배적이 되어 낮은 Contact Resistance의 Ohmic Contact처럼 동작할 수 있음을 이해하였다.

또한 실제 Metal–Semiconductor Interface에서는 이상적인 Schottky-Mott Model만으로 Contact 특성을 완전히 설명하기 어렵다는 점을 확인하였다.

실제 Interface에는

- Dangling Bond
- Crystal Defect
- Contamination
- Oxide
- Interface State

등이 존재할 수 있다.

이러한 Interface State는 Charge를 Capture하거나 Release하면서 Fermi Level Pinning을 발생시킬 수 있다.

따라서 실제 Barrier Height는 Metal Work Function만으로 결정되는 것이 아니라 Interface Quality에도 영향을 받는다.

결과적으로 실제 CMOS Contact의 특성은

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

가 복합적으로 작용하여 결정된다고 이해할 수 있다.

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

따라서 Metal Minimum Width 역시 단순한 Lithography Constraint가 아니라 Interconnect Reliability와 연결된다고 볼 수 있다.

이러한 분석을 통해 Design Rule을 단순한 숫자의 집합으로 학습하는 것보다 **각 Rule이 실제 공정과 Device Physics에서 어떠한 문제를 방지하기 위해 존재하는지를 이해하는 것이 중요함**을 확인하였다.

---

# 39. 결론

본 프로젝트에서는 GPDK090 CMOS 공정의 Design Rule을 학습하고 **Cadence Virtuoso를 이용하여 주요 Design Rule을 직접 Layout으로 구현하고 DRC 관점에서 확인하였다.**

Nwell, Active, Thick Active, Implant, Poly, Contact, Silicide, SIPROT, Metal 및 Via와 같은 기본적인 Layout Layer뿐만 아니라 Metal Density, Latch-up 및 Antenna와 같은 Process 및 Reliability Rule까지 학습하였다.

이를 통해 Design Rule이 단순히 Layout Pattern의 최소 Width와 Spacing을 정의하는 규칙이 아니라 실제 Semiconductor Fabrication에서 발생하는 Process Variation을 고려하고 Device의 Electrical Characteristic과 Reliability를 확보하기 위한 기준임을 이해할 수 있었다.

특히 본 프로젝트에서는 전체 Design Rule 구현 이후 **Metal 및 Contact Rule을 중심으로 Metal–Semiconductor Contact Physics를 심화 분석**하였다.

Metal과 Semiconductor가 접촉하면 Fermi Level Alignment를 위해 Carrier가 이동하고 Semiconductor에서 Band Bending이 발생한다.

이상적인 Metal–Semiconductor Interface에서는 Schottky-Mott Rule에 따라 Barrier Height를 설명할 수 있다.

n-type Semiconductor를 기준으로 이상적인 Electron Barrier Height는

$$
\Phi_{Bn}
=
\Phi_m-\chi
$$

로 표현할 수 있다.

그러나 실제 Interface에서는 Surface State와 Interface State가 존재하며, 이에 의해 Fermi Level Pinning이 발생할 수 있으므로 단순한 Work Function 관계만으로 실제 Contact 특성을 완전히 설명할 수 없다.

또한 CMOS Source/Drain Contact에서는 Heavy Doping을 통해 Barrier Width를 감소시키는 것이 중요하다.

$$
W\propto\frac{1}{\sqrt{N}}
$$

이므로

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

결과적으로

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

가 된다.

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
