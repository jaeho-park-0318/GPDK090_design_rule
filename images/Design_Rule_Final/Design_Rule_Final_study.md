# GPDK090 CMOS Layout Design Rule 구현 및 Metal–Semiconductor Contact 특성 분석

## 1. 프로젝트 개요

본 프로젝트에서는 **GPDK090 CMOS 공정의 Design Rule을 학습하고, Cadence Virtuoso를 이용하여 주요 Design Rule을 직접 Layout으로 구현**하였다.

Design Rule은 단순히 Layout Pattern의 크기와 간격을 제한하는 규칙이 아니라, 실제 반도체 제조 과정에서 발생할 수 있는 **Lithography 오차, Etching 오차, Mask Misalignment, Process Variation** 등을 고려하여 원하는 구조가 Wafer 위에 안정적으로 형성되도록 하기 위한 설계 기준이다.

또한 Design Rule은 단순한 제조 가능성뿐만 아니라 다음 요소와도 밀접하게 관련된다.

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

본 프로젝트의 전체적인 학습 흐름은 다음과 같다.

**Design Rule → Layout Geometry → Fabrication Process → Physical Structure → Electrical Characteristic → Reliability**

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

Design Rule에서는 대표적으로 다음과 같은 Geometry Constraint를 정의한다.

- Minimum Width
- Minimum Spacing
- Enclosure
- Overlap
- Extension
- Parallel Length
- Density

Design Rule의 역할은 다음과 같이 정리할 수 있다.

**Design Rule = Process Margin + Electrical Isolation + Reliability**

---

## 2.2 Width

Width Rule은 특정 Layer가 가져야 하는 최소 또는 최대 폭을 정의한다.

예를 들어 Nwell의 최소 폭은 다음과 같이 표현할 수 있다.

```math
W_{Nwell} \geq 0.6\,\mu m
```

Minimum Width가 필요한 주요 이유는 Lithography와 Etching 과정에서 지나치게 좁은 Pattern이 정상적으로 형성되지 않을 수 있기 때문이다.

Metal의 경우 Width는 제조 가능성뿐만 아니라 배선 저항과 Current Density에도 영향을 준다.

---

## 2.3 Spacing

Spacing은 서로 분리되어 있는 두 Pattern의 Edge 사이에 확보해야 하는 최소 거리이다.

```text
Pattern A                  Pattern B
████████                  ████████
        ←── Spacing ──→
```

Spacing이 지나치게 작으면 다음과 같은 문제가 발생할 수 있다.

- Lithography Pattern 분리 실패
- Etching 불량
- Electrical Short
- Junction Leakage
- Capacitive Coupling 증가
- Breakdown 특성 저하

---

## 2.4 Enclosure

Enclosure는 한 Layer가 다른 Layer를 얼마나 감싸야 하는지를 나타낸다.

```text
Outer Layer
┌─────────────────────┐
│                     │
│     Inner Layer     │
│                     │
└─────────────────────┘
```

Enclosure Rule은 특히 다음과 같은 구조에서 중요하다.

- Implant → Contact
- Metal → Contact
- Metal → Via
- Nwell → Active
- SIPROT → Active

Mask Alignment Error가 발생하더라도 내부 Layer가 필요한 외부 Layer 영역을 벗어나지 않도록 Process Margin을 제공한다.

---

## 2.5 Overlap

Overlap은 두 Layer가 의도적으로 일정 거리 이상 겹치도록 요구하는 조건이다.

Overlap 역시 Mask Alignment 및 Patterning 오차가 존재하더라도 필요한 Device Structure 또는 Electrical Connection을 안정적으로 유지하기 위한 규칙이다.

---

## 2.6 Parallel Length

Parallel Length는 두 Pattern의 서로 마주보는 Edge가 일정 거리 이상 평행하게 배치되는 길이를 의미한다.

특히 Metal Design Rule에서는 Metal Width와 Parallel Length가 증가할수록 더 큰 Spacing이 요구될 수 있다.

이는 Patterning Margin뿐만 아니라 인접 배선 사이의 Coupling 및 Crosstalk와도 관련된다.

---

# 3. CMOS Layout의 기본 Layer 구조

CMOS Layout은 여러 Mask Layer가 중첩되어 하나의 MOSFET과 Interconnect 구조를 형성한다.

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
──────────────────────────
       Gate Oxide
──────────────────────────
          Silicon
```

주요 Layout Layer의 역할은 다음과 같다.

| Layer | 주요 역할 |
|---|---|
| Nwell | PMOS Body 영역 |
| Active | Device가 형성되는 Silicon 영역 |
| Thick Active | Thick-Oxide Device 영역 |
| N+/P+ Implant | Source/Drain 및 Tap의 Doping 정의 |
| Poly | MOSFET Gate 및 Poly Interconnect |
| Contact | Active/Poly와 Metal1 연결 |
| Silicide | Source/Drain 및 Poly 저항 감소 |
| SIPROT | 선택적인 Silicide 형성 방지 |
| Metal | 신호 및 전력 배선 |
| Via | 서로 다른 Metal Layer 연결 |
| STI | 인접 Active Area의 Isolation |
| ILD / IMD | Device 및 Metal Layer 사이의 절연 |

---

# 4. Nwell Design Rule

## 4.1 Nwell의 개념

Nwell은 **P-type Substrate 내부에 N-type으로 도핑하여 형성한 영역**으로, 주로 PMOS의 Body 영역으로 사용된다.

PMOS의 P+ Source/Drain은 Nwell 내부에 형성되며, Nwell은 일반적으로 N+ Well Tap을 통해 VDD에 연결하여 Body Potential을 안정적으로 유지한다.

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

Nwell의 폭, Well 사이 거리, Active와의 거리 및 Enclosure가 충분하지 않으면 Doping Profile이 서로 간섭하거나 Electrical Isolation 특성이 저하될 수 있다.

---

## 4.2 Minimum Nwell Width

Nwell이 지나치게 좁으면 Lithography 및 Well Implant 과정에서 원하는 Doping Region을 안정적으로 형성하기 어렵다.

본 프로젝트에서는 다음 최소 폭을 구현하였다.

```math
W_{Nwell} \geq 0.6\,\mu m
```

---

## 4.3 Nwell Spacing

동일한 Potential을 갖는 Nwell 사이의 최소 간격은 다음과 같다.

```math
S_{Nwell,\ same} \geq 0.6\,\mu m
```

서로 다른 Potential을 갖는 Nwell 사이에서는 더 큰 최소 간격을 요구한다.

```math
S_{Nwell,\ different} \geq 1.2\,\mu m
```

따라서 다음 관계를 갖는다.

```math
S_{different} > S_{same}
```

서로 다른 전압이 인가되는 Well 사이에서는 Electric Field와 Depletion Region의 영향으로 Leakage 가능성이 증가할 수 있으므로 더 큰 Isolation Margin이 필요하다.

---

## 4.4 Nwell에 대한 고찰

Nwell Design Rule을 직접 Layout으로 구현하면서 Well Rule이 단순히 PMOS가 들어갈 공간을 확보하기 위한 규칙이 아니라는 점을 확인하였다.

Nwell은 PMOS의 Body Potential을 결정하며 주변 P-type 영역과 PN Junction을 형성한다.

따라서 Nwell Geometry는 다음과 연결된다.

- Device Isolation
- Junction Leakage
- Breakdown
- Body Effect
- Latch-up

즉, Nwell Design Rule은 **PMOS가 형성되는 N-type Body 영역의 크기와 주변 간격을 충분히 확보하여 공정 정렬 오차를 보상하고, PN Junction Isolation, Breakdown 특성, Body Potential 및 Latch-up 신뢰성을 확보하기 위한 규칙**으로 이해할 수 있다.

---

# 5. Active Area Design Rule

## 5.1 Active Area의 개념

Active Area는 실제 MOSFET의 Source, Drain 및 Channel이 형성되는 Silicon 영역이다.

Active Area 외부는 일반적으로 STI에 의해 전기적으로 분리된다.

```text
Active          STI          Active
███████                     ███████
       └───────────────────┘
```

따라서 Active Area는 단순한 Layout 도형이 아니라 실제 Transistor가 형성되는 Device Region을 정의한다.

---

## 5.2 Active Minimum Width

Active Area가 지나치게 좁으면 Lithography 및 Etching 과정에서 원하는 Pattern이 안정적으로 형성되지 않을 수 있다.

따라서 Minimum Active Width를 통해 충분한 Device Region을 확보해야 한다.

---

## 5.3 Active-to-Active Spacing

두 Active Area가 지나치게 가까우면 두 Device 사이의 STI 폭이 감소한다.

이에 따라 다음과 같은 문제가 발생할 수 있다.

- Device Isolation 저하
- Junction Leakage 증가
- STI Process Margin 감소
- 인접 Device 간 Parasitic Interaction 증가

따라서 Active Area 사이에는 충분한 Minimum Spacing이 필요하다.

---

## 5.4 Bent Active

45°와 같이 꺾인 Active Geometry에서는 Corner Rounding 및 Pattern Distortion의 영향이 직선 Pattern보다 크게 나타날 수 있다.

따라서 Bent Active에는 일반적인 직선 Active보다 큰 Width 또는 Spacing Margin이 요구될 수 있다.

**Bent Geometry → Corner Rounding 증가 → 추가 Process Margin 필요**

---

## 5.5 Active Area에 대한 고찰

Active Rule을 구현하면서 Active Layer가 단순히 Source/Drain을 표시하는 Layer가 아니라 **STI와 함께 실제 Device Region과 Electrical Isolation을 결정하는 핵심 Layer**라는 점을 확인하였다.

Active Geometry가 잘못되면 개별 Transistor의 특성뿐만 아니라 주변 Device와의 Isolation에도 영향을 줄 수 있다.

---

# 6. Thick Active Design Rule

## 6.1 Thick Active의 개념

Thick Active는 일반 Core Device보다 높은 전압에서 동작하는 **Thick-Oxide Device 관련 영역**이다.

Gate Oxide에 형성되는 Electric Field는 개념적으로 다음과 같이 표현할 수 있다.

```math
E_{ox} \approx \frac{V_{ox}}{t_{ox}}
```

따라서 동일한 Oxide Thickness에서 전압이 증가하면 Electric Field 역시 증가한다.

```math
V_{ox}\uparrow \Rightarrow E_{ox}\uparrow
```

반대로 Oxide Thickness를 증가시키면 Electric Field를 감소시킬 수 있다.

```math
t_{ox}\uparrow \Rightarrow E_{ox}\downarrow
```

---

## 6.2 Thick Active Width 및 Spacing

Thick Active는 일반 Active와 다른 Process 조건을 사용하기 때문에 별도의 Width와 Spacing Rule을 가진다.

Thick Active 영역이 지나치게 좁거나 주변 Layer와의 간격이 부족하면 Thick-Oxide Process 영역을 안정적으로 정의하기 어려울 수 있다.

---

## 6.3 Thick Active Enclosure

Thick Active Layer는 Thick-Oxide Device가 형성될 영역을 충분히 포함하도록 배치되어야 한다.

이를 통해 Mask Misalignment가 발생하더라도 Device 전체가 의도한 Process 영역 내부에 형성되도록 한다.

---

## 6.4 Thick Active에 대한 고찰

Thick Active Rule을 통해 동일한 CMOS Process에서도 모든 MOSFET이 동일한 Gate Oxide와 Geometry를 사용하는 것은 아니라는 점을 이해할 수 있었다.

Operating Voltage가 증가하면 Oxide Electric Field와 Reliability 문제가 중요해지므로 더 두꺼운 Oxide와 추가적인 Process Margin이 필요하다.

---

# 7. N+/P+ Implant Design Rule

## 7.1 Implant의 개념

Implant Layer는 Silicon 내부에 어떠한 Doping Type을 형성할 것인지를 정의한다.

대표적으로 다음과 같이 사용된다.

- N+ Implant → NMOS Source/Drain, Nwell Tap
- P+ Implant → PMOS Source/Drain, P-substrate Tap

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

Active Area는 **소자가 형성될 위치**를 정의하고, Implant는 **그 영역의 Doping Type을 정의**한다.

즉,

**Active → Where the device is formed**

**Implant → How the region is doped**

라고 구분할 수 있다.

---

## 7.3 Implant Spacing

서로 다른 Implant 영역이 지나치게 가까우면 Process Variation 및 Lateral Diffusion에 의해 의도한 Doping Profile을 유지하기 어려울 수 있다.

특히 N+와 P+ 영역 사이에서는 Junction Leakage와 Electrical Isolation이 중요하다.

---

## 7.4 Implant와 Contact

Contact 아래에는 충분한 N+ 또는 P+ Doping이 존재해야 한다.

높은 Doping Concentration은 Metal–Semiconductor Interface의 Barrier Width를 감소시키는 데 중요한 역할을 한다.

**High Doping → Thin Barrier → Tunneling 증가 → Contact Resistance 감소**

---

## 7.5 Implant에 대한 고찰

Implant Rule을 통해 Layout에서 표시되는 N+와 P+ 영역이 단순한 색상 구분이 아니라 실제 Wafer 내부의 Carrier Concentration과 PN Junction을 결정한다는 점을 확인하였다.

특히 Contact 영역에서는 Implant Geometry가 Contact Resistance와 직접적으로 연결되므로 Device 동작뿐만 아니라 Interconnect 특성에도 중요한 영향을 준다.

---

# 8. Poly Design Rule

## 8.1 Poly의 개념

POLY는 Polysilicon Layer로, CMOS 공정에서 MOSFET의 Gate를 형성하는 핵심 Layer이다.

Active Area 위를 Poly가 가로지르는 영역에서 MOSFET Channel이 정의된다.

```text
        POLY
          │
          │
N+ ──────┼────── N+
         │
      Channel
```

---

## 8.2 Poly와 MOSFET 특성

MOSFET의 Drain Current는 Gate Width와 Length에 영향을 받는다.

```math
I_D \propto \frac{W}{L}
```

따라서 Poly Gate Geometry는 단순한 Layout Pattern이 아니라 실제 MOSFET의 Electrical Characteristic과 직접적으로 연결된다.

---

## 8.3 Poly Interconnect Resistance

Poly를 Interconnect 또는 Resistor로 사용할 경우 다음 Sheet Resistance 관계가 중요하다.

```math
R = R_{sh}\frac{L}{W}
```

따라서 동일한 길이에서 Width가 감소하면 Resistance가 증가한다.

```math
W\downarrow \Rightarrow R\uparrow
```

---

## 8.4 Poly Spacing

Poly 사이의 간격이 지나치게 작으면 Lithography와 Etching 과정에서 두 Pattern이 안정적으로 분리되지 않을 수 있다.

사용 목적에 따라 다음과 같이 별도의 Spacing Rule이 적용될 수 있다.

- Gate-to-Gate Spacing
- Poly Interconnect Spacing
- Poly Resistor Spacing

---

## 8.5 Poly Gate Extension

Poly Gate는 Active Edge에서 바로 종료되는 것이 아니라 Active 영역을 넘어 일정 거리 이상 연장되어야 한다.

이는 Poly와 Active Mask 사이의 Overlay Error를 보상하기 위한 것이다.

**Gate Extension → Overlay Margin 확보**

---

## 8.6 Bent Poly

Bent Poly에서는 Corner Rounding과 Pattern Distortion의 영향이 증가할 수 있다.

따라서 일반적인 직선 Poly보다 더 큰 Width 또는 Spacing이 요구될 수 있다.

Bent Gate나 Bent Poly Resistor가 제한되는 이유 역시 일정한 Gate Length 또는 저항 Geometry를 유지하기 위한 것이다.

---

## 8.7 Poly Density

Poly Density는 전체 Chip 또는 특정 검사 영역에서 Poly가 차지하는 면적 비율이다.

```math
D_{POLY} = \frac{A_{POLY}}{A_{Total}}\times100
```

Poly Density가 지나치게 높거나 불균일하면 Pattern Density에 따른 공정 편차가 증가할 수 있다.

따라서 Density Rule은 Chip-level Process Uniformity를 확보하기 위한 규칙이다.

---

## 8.8 Poly에 대한 고찰

Poly는 MOSFET의 Gate를 직접 정의하기 때문에 다른 일반 배선 Layer보다 Device Physics와의 연관성이 강하다.

또한 Poly는 Gate뿐만 아니라 Interconnect와 Resistor로도 사용될 수 있기 때문에 동일한 Layer에서도 사용 목적에 따라 서로 다른 Design Rule이 필요하다.

---

# 9. Silicide와 Non-Silicide

## 9.1 Silicide의 개념

Silicide는 Metal과 Silicon을 반응시켜 형성하는 저저항 Metal–Silicon Compound이다.

Source/Drain과 Poly 표면에 Silicide를 형성하면 Sheet Resistance와 Series Resistance를 감소시킬 수 있다.

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

## 9.2 Silicide의 저항 효과

Poly 또는 Diffusion의 저항은 다음 관계를 가진다.

```math
R = R_{sh}\frac{L}{W}
```

Silicide가 형성되면 Sheet Resistance가 감소한다.

```math
R_{sh}\downarrow \Rightarrow R\downarrow
```

---

## 9.3 Non-Silicide의 개념

모든 영역에서 낮은 저항이 필요한 것은 아니다.

Poly Resistor 또는 Active Resistor와 같이 의도적으로 높은 저항이 필요한 경우 Silicide를 형성하면 원하는 저항값을 얻기 어렵다.

따라서 특정 영역에서는 Silicide Formation을 의도적으로 차단하여 Non-Silicide 영역을 형성한다.

---

## 9.4 Silicide와 Non-Silicide 비교

| 구분 | Silicide | Non-Silicide |
|---|---|---|
| 주요 목적 | 저항 감소 | 높은 저항 유지 |
| Sheet Resistance | 낮음 | 높음 |
| MOSFET Source/Drain | 유리 | 일반적으로 불리 |
| Poly Resistor | 낮은 저항 | 높은 저항 구현 가능 |
| 주요 Process | Salicidation | Silicide Block |

---

## 9.5 고찰

Silicide와 Non-Silicide 구조를 비교하면서 Layout에서는 항상 저항을 낮추는 것이 최선은 아니라는 점을 확인하였다.

MOSFET에서는 낮은 Series Resistance가 유리하지만 Resistor에서는 필요한 저항값을 확보하는 것이 목적이다.

즉, 동일한 Process Layer라도 Circuit Function에 따라 요구되는 Electrical Property가 달라질 수 있다.

---

# 10. SIPROT Design Rule

## 10.1 SIPROT의 개념

SIPROT은 **특정 Active 또는 Poly 영역에서 Silicide가 형성되지 않도록 보호하는 Silicide Block Layer**이다.

```text
Without SIPROT

Poly / Active
      ↓
Silicide Formation
      ↓
Low Resistance
```

```text
With SIPROT

Poly / Active
      ↓
Silicide Blocking
      ↓
Non-Silicide
      ↓
High Resistance
```

---

## 10.2 SIPROT와 Contact

SIPROT가 Contact에 지나치게 가까우면 Contact 주변에서 필요한 Silicide Formation에 영향을 줄 수 있다.

따라서 SIPROT과 Contact 사이에는 충분한 Spacing이 필요하다.

---

## 10.3 SIPROT와 Gate

Gate 주변에서 SIPROT의 Boundary가 지나치게 가까우면 Gate Poly의 Silicide가 불균일하게 형성될 수 있다.

따라서 SIPROT-to-Gate Spacing을 확보하여 안정적인 Gate Resistance를 유지한다.

---

## 10.4 SIPROT Enclosure

Silicide를 차단해야 하는 Active 또는 Poly 영역에서는 SIPROT이 대상 Layer를 충분히 감싸야 한다.

이러한 Enclosure는 Mask Overlay Error가 발생하더라도 필요한 영역 전체에서 Silicide Formation을 안정적으로 차단하기 위한 것이다.

---

## 10.5 SIPROT에 대한 고찰

SIPROT Rule을 통해 Silicide는 단순히 공정에서 자동적으로 존재하는 구조가 아니라 Layout 단계에서 선택적으로 Formation을 제어할 수 있는 요소라는 점을 확인하였다.

SIPROT Geometry가 잘못되면 다음과 같은 문제가 발생할 수 있다.

- 원하지 않는 Silicide Formation
- Resistor 값 변화
- Contact Resistance 증가
- Gate Resistance Variation

---

# 11. Contact Design Rule

## 11.1 Contact의 개념

Contact는 Active Area 또는 Poly와 Metal1을 전기적으로 연결하는 수직 연결 구조이다.

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

Contact는 단순한 Hole이 아니라 **Metal Interconnect와 Semiconductor Device가 실제로 전기적으로 연결되는 중요한 Interface**이다.

---

## 11.2 Contact와 Via의 차이

| 구분 | Contact | Via |
|---|---|---|
| 연결 대상 | Device ↔ Metal1 | Metal ↔ Metal |
| 하부 | Active / Poly | Metal k |
| 상부 | Metal1 | Metal k+1 |
| 역할 | Device signal extraction | Multi-level routing |

---

## 11.3 Contact Width

Contact가 지나치게 작으면 Lithography 및 Etching 과정에서 Contact Hole을 안정적으로 형성하기 어렵다.

이에 따라 다음 문제가 발생할 수 있다.

- Incomplete Etching
- Filling 불량
- Contact Resistance 증가
- Open Failure

---

## 11.4 Contact Spacing

Contact 사이의 간격이 지나치게 작으면 두 Contact Hole이 독립적으로 형성되지 않거나 공정 오차에 의해 서로 연결될 수 있다.

따라서 Minimum Contact-to-Contact Spacing을 확보해야 한다.

---

## 11.5 Contact-to-Gate Spacing

Active 위의 Contact가 Gate에 지나치게 가까우면 공정 오차에 의해 Gate Damage 또는 Short가 발생할 수 있다.

본 프로젝트에서 구현한 대표적인 Rule은 다음과 같다.

```text
CONT.SE.1 = 0.10 um
```

따라서 Contact와 Gate 사이에 충분한 Process Margin을 확보한다.

---

## 11.6 Implant-to-Contact Enclosure

Metal–Semiconductor Contact Physics와 직접 연결하여 분석한 핵심 Rule은 다음과 같다.

```text
CONT.E.4 = 0.06 um
```

즉, N+/P+ Implant가 Contact를 최소 0.06 um 이상 Enclose하도록 한다.

이 Rule은 두 가지 관점에서 이해할 수 있다.

### Process 관점

Mask Overlay Error가 존재하더라도 Contact 전체가 N+/P+ Doped Region 안에 위치하도록 한다.

### Electrical 관점

Contact 아래 Silicon이 충분히 Heavy Doping된 상태를 유지하도록 하여 낮은 Contact Resistance를 확보한다.

**Implant Enclosure → Heavy Doped Region 확보 → Barrier Width 감소 → Tunneling 증가 → Contact Resistance 감소 → Ohmic Contact 형성에 유리**

---

# 12. Metal–Semiconductor Contact

## 12.1 접촉 전 상태

Metal과 Semiconductor가 접촉하기 전에는 각각 서로 다른 Work Function과 Fermi Level을 가진다.

Metal Work Function은 `Phi_m`, Semiconductor Work Function은 `Phi_s`로 표현할 수 있다.

---

## 12.2 접촉 후 상태

Metal과 Semiconductor가 접촉하면 Thermal Equilibrium을 이루기 위해 Carrier가 이동한다.

평형 상태에서는 Metal과 Semiconductor의 Fermi Level이 같아진다.

```math
E_{F,m}=E_{F,s}
```

Carrier 이동에 의해 Semiconductor 내부에는 Space Charge Region이 형성될 수 있으며 Energy Band가 휘어지는 **Band Bending**이 발생한다.

그 결과 Metal–Semiconductor Interface에 Potential Barrier가 형성될 수 있다.

---

# 13. Schottky-Mott Rule

## 13.1 n-type Semiconductor 기준

이상적인 n-type Semiconductor에서 Metal Work Function이 Semiconductor Work Function보다 큰 경우 Depletion Region 및 Schottky-type Barrier가 형성될 수 있다.

```math
\Phi_m > \Phi_s
```

반대로 Semiconductor Work Function이 더 큰 경우 이상적인 조건에서는 Electron Accumulation 방향이 되어 Ohmic Contact가 형성될 수 있다.

```math
\Phi_s > \Phi_m
```

---

## 13.2 Schottky Barrier Height

이상적인 n-type Metal–Semiconductor Junction의 Electron Barrier Height는 다음과 같이 표현된다.

```math
\Phi_{Bn} = \Phi_m - \chi
```

여기서

- `Phi_Bn`: Electron Schottky Barrier Height
- `Phi_m`: Metal Work Function
- `chi`: Semiconductor Electron Affinity

를 의미한다.

이상적인 Schottky-Mott Model에서는 Barrier Height가 주로 Metal과 Semiconductor Material Combination에 의해 결정된다.

---

# 14. Schottky Contact

## 14.1 Schottky Contact의 개념

Schottky Contact는 Metal–Semiconductor Interface의 Potential Barrier에 의해 Current가 한 방향으로 상대적으로 쉽게 흐르는 **Rectifying Contact**이다.

낮거나 중간 정도의 Doping에서 Barrier가 충분히 넓은 경우 Carrier가 Barrier를 열적으로 넘어가는 Thermionic Emission이 중요한 Carrier Transport Mechanism이 된다.

---

## 14.2 Thermionic Emission

Schottky Contact의 Current Density는 대표적으로 다음과 같이 표현할 수 있다.

```math
J=A^*T^2\exp\left(-\frac{q\Phi_B}{kT}\right)\left[\exp\left(\frac{qV}{nkT}\right)-1\right]
```

여기서

- `A*`: Effective Richardson Constant
- `T`: Absolute Temperature
- `q`: Electron Charge
- `Phi_B`: Barrier Height
- `k`: Boltzmann Constant
- `n`: Ideality Factor

이다.

Saturation Current Density는 다음과 같이 표현할 수 있다.

```math
J_S=A^*T^2\exp\left(-\frac{q\Phi_B}{kT}\right)
```

따라서 Barrier Height가 증가하면 Saturation Current가 감소한다.

```math
\Phi_B\uparrow \Rightarrow J_S\downarrow
```

---

## 14.3 Schottky Contact의 I–V 특성

Schottky Contact의 I–V Characteristic은 일반적으로 비선형적인 Rectifying 특성을 나타낸다.

Forward Bias에서는 Current가 급격하게 증가하며, Reverse Bias에서는 상대적으로 작은 Reverse Current가 흐른다.

주요 특징은 다음과 같다.

- Rectifying
- Non-linear I–V
- Forward Current의 급격한 증가
- Reverse Current 제한
- Thermionic Emission이 중요한 Transport Mechanism

---

# 15. Ohmic Contact

## 15.1 Ohmic Contact의 개념

Ohmic Contact는 Forward 및 Reverse 방향 모두에서 Current가 Voltage에 거의 선형적으로 비례하는 **Non-Rectifying Contact**이다.

이상적인 Ohmic Contact에서는 다음 관계를 사용할 수 있다.

```math
I=\frac{V}{R_C}
```

따라서 I–V Curve의 기울기는 다음과 같다.

```math
\frac{dI}{dV}=\frac{1}{R_C}
```

Contact Resistance가 작을수록 I–V Curve의 기울기가 증가한다.

MOSFET의 Source/Drain Contact에서는 낮은 Contact Resistance를 갖는 Ohmic Contact가 요구된다.

---

# 16. Heavy Doping과 Barrier Width

Metal–Semiconductor Contact에서는 Barrier Height뿐만 아니라 **Barrier Width**가 Carrier Transport를 결정하는 매우 중요한 요소이다.

Depletion Width는 단순화하면 다음과 같이 나타낼 수 있다.

```math
W=\sqrt{\frac{2\varepsilon_s(V_{bi}-V)}{qN}}
```

여기서

- `epsilon_s`: Semiconductor Permittivity
- `V_bi`: Built-in Potential
- `V`: Applied Voltage
- `q`: Electron Charge
- `N`: Doping Concentration

이다.

따라서 다음 관계가 성립한다.

```math
W\propto\frac{1}{\sqrt{N}}
```

즉, Doping Concentration이 증가하면 Depletion Width가 감소한다.

```math
N\uparrow \Rightarrow W\downarrow
```

---

# 17. Tunneling과 Field Emission

Barrier Width가 충분히 얇아지면 Carrier는 Barrier를 열적으로 넘어가는 대신 Quantum Mechanical Tunneling을 통해 통과할 수 있다.

Barrier 내부의 Wave Function은 단순화하면 다음과 같은 형태로 감소한다.

```math
\psi(x)\propto e^{-\kappa x}
```

여기서 `kappa`는 다음과 같이 표현할 수 있다.

```math
\kappa=\frac{\sqrt{2m^*(\Phi_B-E)}}{\hbar}
```

Barrier Width가 `W`인 경우 Tunneling Probability는 대략 다음 관계를 가진다.

```math
T\propto e^{-2\kappa W}
```

따라서 Barrier Width가 감소하면 Tunneling Probability가 증가한다.

```math
W\downarrow \Rightarrow T\uparrow
```

Heavy Doping과 연결하면 다음과 같이 정리할 수 있다.

**Doping 증가 → Barrier Width 감소 → Tunneling 증가 → Contact Resistance 감소**

---

# 18. Carrier Transport Mechanism의 변화

Doping Concentration이 증가하면 Metal–Semiconductor Contact에서 지배적인 Carrier Transport Mechanism이 변화할 수 있다.

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

즉, 낮은 Doping에서는 Carrier가 Barrier를 열적으로 넘어가는 과정이 중요하지만, 높은 Doping에서는 Barrier가 매우 얇아져 Tunneling의 영향이 크게 증가한다.

이 때문에 본질적으로 Schottky Barrier가 존재하더라도 Electrical Characteristic은 낮은 Contact Resistance를 갖는 Ohmic Contact처럼 나타날 수 있다.

---

# 19. Surface State

Semiconductor Bulk에서는 각 Silicon Atom의 Bond가 주변 Atom과 결합되어 있다.

하지만 Semiconductor Surface에서는 Crystal Structure가 끝나기 때문에 일부 Bond가 끊어진 상태로 남을 수 있다.

이를 **Dangling Bond**라고 한다.

Surface State는 다음과 같은 원인으로 형성될 수 있다.

- Dangling Bond
- Step / Edge
- Crystal Defect
- Contamination

이러한 구조에 의해 Semiconductor Bandgap 내부에 추가적인 Energy State가 형성될 수 있다.

---

# 20. Interface State

Interface State는 서로 다른 두 Material이 만나는 Boundary에서 형성되는 Energy State이다.

Metal–Semiconductor Interface에서는 다음과 같은 요소가 원인이 될 수 있다.

- Bonding Mismatch
- Crystal Defect
- Contamination
- Thin Oxide
- Interface Disorder

Interface State는 Electron을 Capture하거나 Release할 수 있으므로 Interface Charge를 형성할 수 있다.

---

# 21. Fermi Level Pinning

이상적인 Schottky-Mott Model에서는 Metal Work Function을 변화시키면 Barrier Height 역시 변화해야 한다.

하지만 실제 Metal–Semiconductor Interface에서는 Interface State Density가 높을 수 있다.

Interface State가 Carrier를 Capture하거나 Release하면서 계면 전하를 형성하면 Semiconductor Surface의 Fermi Level이 특정 Energy 부근에 고정되는 효과가 발생할 수 있다.

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

따라서 실제 Metal–Semiconductor Contact에서는 단순한 Work Function Combination만으로 Contact 특성을 완전히 설명하기 어렵다.

---

# 22. CONT.E.4와 Ohmic Contact의 연결

본 프로젝트에서 Layout Design Rule과 Contact Physics의 관계를 가장 직접적으로 보여주는 Rule은 `CONT.E.4`이다.

```text
CONT.E.4 = 0.06 um
```

N+/P+ Implant가 Contact를 최소 0.06 um 이상 Enclose하도록 요구한다.

첫 번째 목적은 **Overlay Error에 대한 Process Margin 확보**이다.

두 번째 목적은 **Contact 아래에 충분한 Heavy Doped Region을 유지하는 것**이다.

Heavy Doping에 의해 다음 관계가 형성된다.

```math
N\uparrow \Rightarrow W\downarrow
```

또한 Tunneling Probability는 다음과 같다.

```math
T\propto e^{-2\kappa W}
```

따라서 전체 관계를 다음과 같이 정리할 수 있다.

**Implant Enclosure → Heavy Doping 확보 → Barrier Width 감소 → Tunneling 증가 → Contact Resistance 감소 → 안정적인 Ohmic Contact 형성에 유리**

즉, `CONT.E.4`는 단순한 Layout Geometry Constraint가 아니라 Metal–Semiconductor Carrier Transport 특성과 직접적으로 연결될 수 있는 Rule이다.

---

# 23. Schottky Contact와 Ohmic Contact 비교

| 구분 | Schottky Contact | Ohmic Contact |
|---|---|---|
| 기본 특성 | Rectifying | Non-Rectifying |
| I–V | 비선형 | 거의 선형 |
| Forward Bias | Current 급격히 증가 | Voltage에 비례 |
| Reverse Bias | 상대적으로 작은 Reverse Current | 반대 방향에서도 선형 |
| Barrier | 상대적으로 넓은 Barrier | Heavy Doping으로 매우 얇게 가능 |
| 주요 Mechanism | Thermionic Emission | Field Emission / Tunneling 영향 증가 |
| Doping | Low / Moderate | Heavy Doping |
| Contact Resistance | 상대적으로 큼 | 작음 |
| CMOS S/D Contact 목표 | 일반적으로 부적합 | 적합 |

핵심적인 차이는 단순히 Barrier의 존재 여부가 아니라 **Carrier가 해당 Barrier를 얼마나 쉽게 통과할 수 있는가**에 있다.

---

# 24. Silicide와 Ohmic Contact

실제 CMOS Source/Drain Contact에서는 Heavy Doping뿐만 아니라 Silicide 역시 Series Resistance와 Contact 관련 저항을 감소시키는 데 중요한 역할을 한다.

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

따라서 실제 CMOS Contact의 낮은 저항 특성은 다음 요소가 함께 작용한 결과로 이해할 수 있다.

**Heavy Doping + Silicide + Proper Contact Geometry**

---

# 25. Metal1 Design Rule

## 25.1 Metal1의 역할

Metal1은 Contact를 통해 Transistor Source/Drain 또는 Poly와 연결되는 첫 번째 Metal Interconnect Layer이다.

Metal1은 Signal Routing뿐만 아니라 Power Distribution에도 사용될 수 있기 때문에 다음 요소를 함께 고려해야 한다.

- Width
- Spacing
- Area
- Contact Enclosure
- Via Enclosure
- Current Density
- Electromigration
- Density

---

## 25.2 Minimum Metal1 Width

본 프로젝트에서 구현한 Minimum Metal1 Width는 다음과 같다.

```text
METAL1.W.1 = 0.12 um
```

Metal1의 Width가 지나치게 좁으면 Lithography 및 Etching 안정성이 감소하며 Electrical Resistance와 Current Density가 증가할 수 있다.

---

## 25.3 Maximum Metal1 Width

본 프로젝트에서는 Maximum Metal1 Width Rule도 구현하였다.

```text
METAL1.W.2 = 12 um
```

따라서 Metal은 단순히 넓을수록 항상 좋은 것이 아니라 PDK에서 요구하는 Geometry Constraint를 함께 만족해야 한다.

---

## 25.4 Metal Resistance

Metal 배선의 Resistance는 다음과 같이 표현할 수 있다.

```math
R=\rho\frac{L}{A}
```

Metal의 단면적은 다음과 같다.

```math
A=Wt
```

따라서 다음 관계를 얻을 수 있다.

```math
R=\rho\frac{L}{Wt}
```

동일한 길이와 두께에서 Metal Width가 감소하면 Resistance가 증가한다.

```math
W\downarrow \Rightarrow R\uparrow
```

---

# 26. Metal Width와 Current Density

Current Density는 다음과 같이 정의된다.

```math
J=\frac{I}{A}
```

Metal의 단면적을 대입하면 다음과 같다.

```math
J=\frac{I}{Wt}
```

따라서 동일한 Current에서 Metal Width가 감소하면 Current Density가 증가한다.

```math
W\downarrow \Rightarrow J\uparrow
```

Current Density의 증가는 Electromigration Reliability와 직접적으로 연결된다.

---

# 27. Electromigration

Electromigration은 높은 Current Density에서 전도 Electron과 Metal Atom 사이의 Momentum Transfer에 의해 Metal Atom이 장시간에 걸쳐 이동하는 현상이다.

이를 Electron Wind Force와 연결하여 설명할 수 있다.

Metal Atom이 빠져나간 영역에서는 Vacancy가 축적되어 **Void**가 형성될 수 있다.

```text
Metal Line

██████████    █████████
          VOID
```

Void는 다음 문제를 발생시킬 수 있다.

- Local Resistance 증가
- Current Crowding
- Open Failure

반대로 Metal Atom이 축적되는 영역에서는 Hillock 또는 Extrusion이 형성될 수 있으며 인접 배선과 Short될 위험이 증가한다.

---

# 28. Black's Equation

Electromigration에 의한 Interconnect Lifetime은 Black's Equation으로 표현할 수 있다.

```math
MTTF=A J^{-n}\exp\left(\frac{E_a}{kT}\right)
```

여기서

- `MTTF`: Mean Time To Failure
- `A`: Process-dependent Constant
- `J`: Current Density
- `n`: Current Density Exponent
- `E_a`: Activation Energy
- `k`: Boltzmann Constant
- `T`: Absolute Temperature

이다.

Current Density가 증가하면 MTTF는 감소한다.

```math
J\uparrow \Rightarrow MTTF\downarrow
```

Temperature가 증가해도 일반적으로 MTTF는 감소한다.

```math
T\uparrow \Rightarrow MTTF\downarrow
```

따라서 Metal Minimum Width는 단순한 Lithography Constraint가 아니라 Electromigration에 의한 Long-term Interconnect Reliability와도 연결된다.

---

# 29. Metal Spacing

인접한 Metal1 사이에는 Minimum Spacing이 필요하다.

Metal 사이의 간격이 지나치게 작으면 Lithography 및 Etching 과정에서 두 Pattern이 안정적으로 분리되지 않거나 Short가 발생할 수 있다.

또한 인접 Metal 사이에는 Coupling Capacitance가 존재한다.

단순한 Capacitor 관계를 이용하면 다음과 같은 경향을 이해할 수 있다.

```math
C\propto\frac{\varepsilon A}{d}
```

따라서 Metal 사이의 거리 `d`가 감소하면 Coupling Capacitance가 증가한다.

```math
d\downarrow \Rightarrow C_{coupling}\uparrow
```

이는 Crosstalk와 RC Delay에 영향을 줄 수 있다.

---

# 30. Wide Metal과 Parallel Length

Metal Width 또는 Parallel Length가 증가하면 일반적인 Minimum Spacing보다 더 큰 Spacing이 요구될 수 있다.

예를 들어 특정 조건에서는 다음과 같은 Rule이 적용된다.

```text
METAL1.SP.1.5 = 1.50 um
METAL1.SP.1.6 = 2.50 um
```

따라서 Metal Spacing은 하나의 고정된 값이 아니라 Metal Width와 서로 마주보는 Parallel Length에 따라 달라질 수 있다.

---

# 31. Metal1 to Contact Enclosure

Metal1은 Contact를 충분히 포함해야 한다.

```text
┌──────────── Metal1 ────────────┐
│                                │
│             Contact            │
│               □                │
│                                │
└────────────────────────────────┘
```

Metal1과 Contact가 서로 다른 공정 단계에서 형성되기 때문에 Overlay Error가 존재할 수 있다.

따라서 충분한 Enclosure를 통해 안정적인 Electrical Contact Area를 확보한다.

---

# 32. Metal Density

Metal Density는 특정 검사 Window 내부에서 Metal이 차지하는 면적 비율이다.

```math
D_{Metal}=\frac{A_{Metal}}{A_{Window}}\times100
```

본 프로젝트에서는 Metal1 Density를 일정 크기의 Window로 검사하는 Rule을 구현하였다.

대표적인 조건은 다음과 같다.

```text
Base Window = 600 um × 600 um
Window Stepping = 300 um
Metal1 Density < 60 %
```

Metal Pattern Density가 지나치게 높거나 낮고 위치에 따라 크게 달라지면 CMP 과정에서 Surface Planarity가 불균일해질 수 있다.

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

따라서 Metal Density Rule은 개별 배선의 Geometry보다 **Chip-level 또는 Window-level Process Uniformity를 확보하기 위한 Rule**이라고 볼 수 있다.

---

# 33. Dummy Metal Fill

회로적으로 Metal이 거의 존재하지 않는 넓은 영역에서는 Metal Density를 균일하게 하기 위해 Dummy Metal Fill을 추가할 수 있다.

```text
Before

████████                     ████████
           Empty Region


After

████████   ▪ ▪ ▪ ▪ ▪ ▪     ████████
            Dummy Fill
```

Dummy Metal은 직접적인 Signal Routing보다 Process Density 균일화를 목적으로 사용된다.

다만 Dummy Fill이 신호 배선에 가까워지면 Parasitic Capacitance가 증가할 수 있으므로 Electrical Effect 역시 함께 고려해야 한다.

---

# 34. MMCAP과 Metal Layer

## 34.1 MMCAP의 기본 개념

MMCAP은 Metal Layer 사이의 Electric Field를 이용하여 Capacitance를 형성하는 Metal-based Capacitor 구조로 이해할 수 있다.

두 Conductive Plate 사이에 Dielectric이 존재하는 기본적인 Capacitor 관계는 다음과 같다.

```math
C=\frac{\varepsilon A}{d}
```

여기서

- `epsilon`: Dielectric Permittivity
- `A`: Electrode Overlap Area
- `d`: Electrode 사이의 Dielectric Thickness

이다.

따라서 Overlap Area가 증가하면 Capacitance가 증가한다.

```math
A\uparrow \Rightarrow C\uparrow
```

Dielectric Thickness가 감소해도 Capacitance는 증가한다.

```math
d\downarrow \Rightarrow C\uparrow
```

---

## 34.2 MMCAP과 Metal Geometry

일반적인 Metal Interconnect에서는 낮은 Resistance와 Routing이 중요하다.

반면 Metal-based Capacitor에서는 Metal Plate의 Overlap Area와 Dielectric Geometry가 Capacitance에 중요한 영향을 준다.

따라서 동일한 Metal Layer라도 사용 목적에 따라 중요하게 고려해야 하는 Geometry가 달라질 수 있다.

실제 GPDK090의 MMCAP Layout을 구현할 경우에는 반드시 해당 PDK에서 정의한 전용 Device Structure와 Layer Combination을 따라야 한다.

---

# 35. Via Design Rule

## 35.1 Via의 개념

Via는 서로 다른 Metal Layer를 수직 방향으로 연결한다.

```text
Metal2
════════════
     │
    Via1
     │
════════════
Metal1
```

Contact가 Active/Poly와 Metal1을 연결한다면 Via는 Metal과 Metal 사이를 연결한다.

---

## 35.2 Via Width와 Spacing

Via가 지나치게 작으면 Via Hole Etching 및 Conductive Material Filling이 불안정해질 수 있다.

또한 Via 사이의 간격이 지나치게 작으면 Pattern Separation과 Filling Uniformity에 문제가 발생할 수 있다.

특히 3×3 이상의 Via Array에서는 높은 Via Pattern Density를 고려한 별도의 Spacing Rule이 적용될 수 있다.

---

## 35.3 Via Array Spacing

본 프로젝트에서 구현한 Via Array 관련 Rule 중 하나는 다음과 같다.

```text
VIAk.SP.2 = 0.20 um
```

3×3 이상의 Via Array에서는 Via가 밀집되면서 Etching 및 Filling Uniformity가 저하될 수 있으므로 일반적인 경우보다 큰 Spacing 조건을 적용한다.

---

## 35.4 Metal-to-Via Enclosure

Via는 Metal 내부에 충분히 포함되어야 한다.

기본 Enclosure Rule과 더불어 마주보는 두 방향에서 더 큰 Enclosure를 요구하는 Rule이 존재할 수 있다.

```text
VIAk.E.1 = 0.005 um
VIAk.E.2 = 0.06 um on at least two opposite sides
```

이를 통해 Overlay Error가 존재하더라도 Via와 Metal 사이의 충분한 Electrical Contact Area를 확보한다.

---

## 35.5 Via Array의 Electrical Effect

동일한 Resistance를 갖는 Via를 여러 개 병렬로 사용한다고 단순화하면 Equivalent Resistance는 다음과 같이 감소한다.

```math
R_{eq}\approx\frac{R_{via}}{N}
```

따라서 Via 수가 증가하면 Equivalent Resistance가 감소한다.

```math
N_{via}\uparrow \Rightarrow R_{eq}\downarrow
```

또한 Current가 여러 Via로 분산되므로 각 Via가 받는 Current Stress도 감소할 수 있다.

---

# 36. Latch-up Rule

CMOS 구조에는 의도하지 않은 기생 PNP와 NPN BJT가 존재할 수 있다.

이 두 기생 BJT가 Positive Feedback 구조를 형성하면 PNPN 구조가 Turn-on되어 VDD와 GND 사이에 큰 Current가 흐르는 Latch-up이 발생할 수 있다.

```text
PMOS Region                NMOS Region

P+   Nwell                 P-sub   N+
│      │                     │      │
└─ PNP ─┐                 ┌─ NPN ──┘
        └─────────────────┘
```

Latch-up을 방지하기 위해 다음 구조가 중요하다.

- Nwell Tap
- P-substrate Tap
- Guard Ring
- 적절한 Device-to-Tap Distance

Well/Substrate Tap을 Device에 가깝게 배치하면 Well/Substrate Resistance를 감소시켜 기생 BJT가 Turn-on될 가능성을 낮출 수 있다.

---

# 37. Antenna Rule

## 37.1 Antenna Effect의 개념

Antenna Effect는 Plasma-based Process 중 Floating Metal 또는 Poly가 Charge를 수집하고, 이 Charge가 MOS Gate로 전달되어 얇은 Gate Oxide에 Electrical Stress를 발생시키는 현상이다.

```text
Plasma
 ↓ ↓ ↓ ↓ ↓

────────────── Metal / Poly
              │
             Gate
              │
          Gate Oxide
```

Gate Oxide가 매우 얇기 때문에 과도한 Charge가 축적되면 Gate Leakage 증가 또는 Oxide Damage가 발생할 수 있다.

---

## 37.2 Gate Oxide Electric Field

Gate Oxide Electric Field는 다음과 같이 생각할 수 있다.

```math
E_{ox}=\frac{V_{ox}}{t_{ox}}
```

따라서 Oxide Thickness가 매우 작으면 같은 Voltage에서도 높은 Electric Field가 형성될 수 있다.

```math
t_{ox}\downarrow \Rightarrow E_{ox}\uparrow
```

---

## 37.3 Antenna Ratio

기본적인 Antenna Ratio의 개념은 다음과 같이 나타낼 수 있다.

```math
AR=\frac{A_{Antenna}}{A_{Gate}}
```

따라서 Antenna Area가 증가하면 Antenna Risk가 증가한다.

```math
A_{Antenna}\uparrow \Rightarrow AR\uparrow
```

실제 GPDK090 Antenna Rule에서는 단순한 하나의 Ratio가 아니라 Poly, Poly Sidewall, Contact, Metal, Via 및 Cumulative Metal Area 등을 구분하여 검사한다.

---

## 37.4 주요 Antenna Rule

본 프로젝트에서 학습한 주요 Antenna Rule의 예는 다음과 같다.

| Rule | Limit | 주요 의미 |
|---|---:|---|
| ANT.1 | 275 | Poly Area 관련 Ratio |
| ANT.2 | 550 | Poly Sidewall 관련 Ratio |
| ANT.3 | 15 | Poly Contact Area / Gate Area |
| ANT.4 | 475 | Single Metal Area 관련 Ratio |
| ANT.5 | 25 | Single Via Area 관련 Ratio |
| ANT.6 | 1200 | Cumulative Multi-Level Metal Area 관련 Ratio |

Antenna Rule은 최종 IC의 정상 동작보다 **제조 중간 단계의 Plasma Charging으로부터 Gate Oxide를 보호하기 위한 Reliability Rule**이라는 점이 중요하다.

---

# 38. 전체 Design Rule과 Semiconductor Physics의 연결

본 프로젝트에서 가장 중요하게 확인한 점은 Layout Design Rule과 Semiconductor Physics가 서로 독립된 내용이 아니라는 것이다.

Contact에서는 다음과 같은 연결 관계가 나타난다.

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

Metal에서는 다음과 같이 연결할 수 있다.

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

Metal Density에서는 다음과 같은 관계가 나타난다.

```text
Metal Density Rule
        ↓
Pattern Density 균일화
        ↓
CMP Uniformity 확보
        ↓
Surface Planarity 확보
        ↓
Interconnect Process 안정성 향상
```

Antenna Rule은 다음과 같이 연결된다.

```text
Antenna Ratio 제한
        ↓
Plasma Charge 축적 제한
        ↓
Gate Oxide Electric Stress 감소
        ↓
Gate Oxide Reliability 향상
```

따라서 Design Rule은 단순한 Layout Geometry Constraint가 아니라 **원하는 Device Physics와 Reliability를 실제 Wafer에서 안정적으로 구현하기 위한 조건**이라고 볼 수 있다.

---

# 39. 프로젝트 수행 과정

본 프로젝트에서는 Design Rule Manual에 있는 규칙을 단순히 정리하는 것이 아니라 Cadence Virtuoso에서 각 Rule을 직접 구현하였다.

```text
GPDK090 Design Rule 학습
        ↓
Rule의 Geometry와 의미 분석
        ↓
Cadence Virtuoso Layout 구현
        ↓
Width / Spacing / Enclosure 측정
        ↓
DRC를 통한 Rule 확인
        ↓
Vertical Structure 분석
        ↓
공정 및 Device Physics와 연결
```

각 Rule을 Layout으로 직접 구현하면서 Design Rule Manual에 제시된 수치가 실제 Layout에서 어떠한 Geometry를 의미하는지 확인할 수 있었다.

---

# 40. 프로젝트 결과

본 프로젝트를 통해 다음 내용을 학습할 수 있었다.

## Layout 관점

- Nwell, Active, Implant, Poly의 관계
- MOSFET Layout Structure
- Contact와 Metal 연결
- Via를 이용한 Multi-Level Interconnect
- Silicide와 SIPROT 관계
- Width / Spacing / Enclosure Rule
- Metal Density
- Antenna Rule
- Latch-up Rule

## Process 관점

- Lithography Process Margin
- Etching
- Mask Overlay Error
- STI Isolation
- Ion Implantation
- Silicide Formation
- Contact / Via Formation
- CMP
- Plasma Process

## Device Physics 관점

- PN Junction
- Depletion Region
- Metal–Semiconductor Junction
- Schottky Barrier
- Thermionic Emission
- Tunneling
- Field Emission
- Surface State
- Interface State
- Fermi Level Pinning
- Ohmic Contact

## Reliability 관점

- Contact Resistance
- Electromigration
- Latch-up
- Antenna Effect
- CMP Uniformity
- Interconnect Reliability

---

# 41. 고찰

이번 프로젝트를 통해 가장 중요하게 확인한 점은 **Design Rule의 각 수치에는 공정적 또는 전기적인 이유가 존재한다는 것**이다.

처음 Design Rule을 접하면 Minimum Width, Spacing, Enclosure 등의 규칙은 단순히 DRC Error를 피하기 위해 지켜야 하는 Layout 조건처럼 보일 수 있다.

그러나 실제로 각각의 Rule은 Semiconductor Process, Device Physics 및 Reliability와 직접적으로 연결되어 있었다.

| Design Rule | 주요 물리적 의미 |
|---|---|
| Nwell Width / Spacing | Junction Isolation, Breakdown, Latch-up |
| Active Width / Spacing | STI Isolation, Device Formation |
| Implant Enclosure | Doping Region 확보 |
| Poly Rule | Gate Geometry 및 Lithography |
| Contact Rule | Contact Resistance 및 Process Margin |
| SIPROT Rule | Silicide Formation 제어 |
| Metal Width | Resistance, Current Density, Electromigration |
| Metal Spacing | Pattern Separation, Crosstalk |
| Metal Density | CMP Uniformity |
| Via Rule | Vertical Interconnect Reliability |
| Latch-up Rule | Parasitic PNPN Turn-on 방지 |
| Antenna Rule | Plasma Charging에 의한 Gate Oxide Damage 방지 |

특히 Contact Design Rule을 분석하면서 Layout Geometry와 Semiconductor Physics의 관계를 명확하게 확인할 수 있었다.

Contact 아래의 Depletion Width는 Doping Concentration에 의해 변화한다.

```math
W=\sqrt{\frac{2\varepsilon_s(V_{bi}-V)}{qN}}
```

따라서 다음 관계가 성립한다.

```math
N\uparrow \Rightarrow W\downarrow
```

Tunneling Probability는 다음과 같이 Barrier Width에 강하게 의존한다.

```math
T\propto e^{-2\kappa W}
```

따라서 다음 관계가 성립한다.

```math
W\downarrow \Rightarrow T\uparrow
```

결과적으로 다음과 같이 정리할 수 있다.

**Doping 증가 → Barrier Width 감소 → Tunneling 증가 → Contact Resistance 감소**

이를 통해 Heavy Doping에 의해 Barrier가 매우 얇아지면 Field Emission/Tunneling의 영향이 커지고 낮은 Contact Resistance의 Ohmic Contact처럼 동작할 수 있음을 이해하였다.

또한 실제 Metal–Semiconductor Interface에서는 이상적인 Schottky-Mott Model만으로 Contact 특성을 완전히 설명하기 어렵다.

실제 Interface에는 다음과 같은 요소가 존재할 수 있다.

- Dangling Bond
- Crystal Defect
- Contamination
- Oxide
- Interface State

이러한 Interface State는 Carrier를 Capture하거나 Release하여 Interface Charge를 형성하고 Fermi Level Pinning을 발생시킬 수 있다.

따라서 실제 CMOS Contact 특성은 다음 요소가 복합적으로 작용하여 결정된다고 이해할 수 있다.

**Material + Doping + Interface Quality + Silicide + Layout Geometry**

Metal Rule에서도 유사한 연결 관계를 확인할 수 있었다.

Metal의 단면적은 다음과 같다.

```math
A=Wt
```

Current Density는 다음과 같이 표현된다.

```math
J=\frac{I}{Wt}
```

따라서 Width가 감소하면 Current Density가 증가한다.

```math
W\downarrow \Rightarrow J\uparrow
```

높은 Current Density는 Electromigration을 가속할 수 있다.

Black's Equation은 다음과 같다.

```math
MTTF=A J^{-n}\exp\left(\frac{E_a}{kT}\right)
```

따라서 Current Density가 증가하면 Interconnect Lifetime은 감소한다.

```math
J\uparrow \Rightarrow MTTF\downarrow
```

이를 통해 Metal Minimum Width 역시 단순한 Lithography Constraint가 아니라 Interconnect Reliability와 연결된 Rule이라는 점을 확인할 수 있었다.

Metal Density Rule에서는 개별 Layout Shape가 모든 Width 및 Spacing Rule을 만족한다고 해서 제조 가능성이 완전히 보장되는 것은 아니라는 점을 확인하였다.

Local 또는 Global Metal Density가 지나치게 불균일하면 CMP 과정에서 Dishing과 Erosion이 발생할 수 있으므로 Chip-level Pattern Distribution까지 고려해야 한다.

Antenna Rule을 통해서는 완성된 Circuit의 정상 동작뿐만 아니라 **Wafer Fabrication 중간 단계에서의 Reliability 역시 Layout Design Rule에 포함된다는 점**을 이해할 수 있었다.

결과적으로 Design Rule은 단순한 수치 집합이 아니라 실제 Fabrication Process의 한계와 Device Physics를 Layout 단계에서 반영한 규칙이라고 볼 수 있다.

---

# 42. 결론

본 프로젝트에서는 GPDK090 CMOS 공정의 Design Rule을 학습하고 **Cadence Virtuoso를 이용하여 주요 Design Rule을 직접 Layout으로 구현하고 DRC 관점에서 확인하였다.**

Nwell, Active, Thick Active, Implant, Poly, Contact, Silicide, SIPROT, Metal 및 Via와 같은 기본적인 Layout Layer뿐만 아니라 Metal Density, Latch-up 및 Antenna와 같은 Process 및 Reliability Rule까지 분석하였다.

이를 통해 Design Rule이 단순히 Layout Pattern의 최소 Width와 Spacing을 정의하는 규칙이 아니라 실제 Semiconductor Fabrication에서 발생하는 Process Variation을 고려하고 Device의 Electrical Characteristic과 Reliability를 확보하기 위한 설계 기준임을 이해할 수 있었다.

특히 전체 Design Rule 구현 이후 **Metal 및 Contact Rule을 중심으로 Metal–Semiconductor Contact Physics를 심화 분석**하였다.

Metal과 Semiconductor가 접촉하면 Thermal Equilibrium을 이루기 위해 Carrier가 이동하고 Semiconductor에서 Band Bending이 발생한다.

이상적인 n-type Metal–Semiconductor Junction의 Electron Schottky Barrier Height는 다음과 같이 표현할 수 있다.

```math
\Phi_{Bn}=\Phi_m-\chi
```

그러나 실제 Interface에서는 Surface State와 Interface State가 존재할 수 있으며 Fermi Level Pinning에 의해 단순한 Work Function 관계만으로 실제 Barrier Height를 설명하기 어려울 수 있다.

또한 CMOS Source/Drain Contact에서는 Heavy Doping을 통해 Barrier Width를 감소시키는 것이 중요하다.

```math
W\propto\frac{1}{\sqrt{N}}
```

따라서 다음과 같다.

```math
N\uparrow \Rightarrow W\downarrow
```

Barrier Width가 감소하면 Tunneling Probability가 증가한다.

```math
T\propto e^{-2\kappa W}
```

따라서 전체적으로 다음과 같이 이해할 수 있다.

**Heavy Doping → Barrier Width 감소 → Tunneling 증가 → Contact Resistance 감소 → Ohmic Contact 형성에 유리**

이러한 관점에서 Contact 주변의 N+/P+ Implant Enclosure Rule은 단순한 Mask Geometry 조건을 넘어 **Contact 아래에 충분한 Heavy Doped Region을 확보하여 낮은 Contact Resistance를 갖는 안정적인 Electrical Contact를 구현하는 것과 연결되는 Rule**로 이해할 수 있다.

Metal Rule 역시 배선 Geometry와 Reliability가 직접적으로 연결되어 있다.

Metal Width가 감소하면 Resistance와 Current Density가 증가하고, 높은 Current Density는 Electromigration을 가속할 수 있다.

따라서 Metal Width, Spacing, Via 및 Density Rule은 단순히 DRC를 통과하기 위한 조건이 아니라 실제 Interconnect의 Electrical Performance와 Long-term Reliability를 확보하기 위한 조건이다.

최종적으로 본 프로젝트에서 확인한 전체 관계는 다음과 같다.

**Design Rule → Layout Geometry → Fabrication Process → Physical Structure → Electrical Characteristic → Device Reliability**

따라서 Full-Custom IC Layout에서 Design Rule을 올바르게 이해하기 위해서는 Rule의 수치를 단순히 암기하는 것보다 **왜 해당 Geometry가 필요한지, 해당 Geometry가 실제 Wafer 구조에 어떻게 반영되며 최종적으로 Device의 Electrical Characteristic과 Reliability에 어떠한 영향을 미치는지를 함께 이해하는 것이 중요하다.**

본 프로젝트를 통해 Cadence Virtuoso에서 Design Rule을 직접 구현하면서 **Layout Design, Semiconductor Process 및 Device Physics가 서로 분리된 개념이 아니라 실제 CMOS 설계를 구성하는 하나의 연속적인 과정**임을 확인할 수 있었다.
