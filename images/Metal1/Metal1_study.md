# METAL1 Design Rule Study

## 1. METAL1의 개념

**Metal1은 CMOS 소자 위에 형성되는 첫 번째 금속 배선층으로, Contact를 통해 Active Area 또는 Poly와 연결되고 Via1을 통해 Metal2와 연결되는 Interconnect Layer이다.**

전체적인 연결 구조는 다음과 같이 나타낼 수 있다.

```text
Metal2
  │
Via1
  │
Metal1
  │
Contact
  │
Active / Poly
```

즉,

```math
\text{Device}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
\rightarrow
\text{Via1}
\rightarrow
\text{Metal2}
```

Metal1은 단순히 신호를 연결하는 역할뿐만 아니라 배선의 **Resistance, RC Delay, Current Density, IR Drop 및 Electromigration Reliability**와 직접적으로 관련된다.

따라서 Metal1 Design Rule에서는 주로 다음 요소를 제한한다.

- Minimum Width
- Minimum Spacing
- Minimum Area
- Contact/Via Enclosure
- Wide Metal Spacing
- Metal Density
- 특수 구조에서의 Metal Geometry

---

## 2. Metal Interconnect의 저항

Metal 배선의 저항은 기본적으로 다음 식으로 표현할 수 있다.

```math
R = \rho \frac{L}{A}
```

Metal의 단면적은 다음과 같다.

```math
A = Wt
```

따라서,

```math
R = \rho \frac{L}{Wt}
```

여기서

- $\rho$ : Metal Resistivity
- $L$ : 배선 길이
- $W$ : Metal Width
- $t$ : Metal Thickness

이다.

또는 Sheet Resistance를 이용하면 다음과 같이 표현할 수 있다.

```math
R = R_{sh}\frac{L}{W}
```

따라서 동일한 길이와 두께에서 Metal Width가 감소하면,

```math
W\downarrow
\rightarrow
R\uparrow
```

가 된다.

이러한 관계는 Metal Minimum Width Rule을 이해하는 데 중요하다.

---

## 3. METAL1.W.1 — Minimum Metal1 Width

Metal1에는 제조 가능한 최소 Width가 존재한다.

GPDK090에서 Metal1 Minimum Width Rule은 **Metal1 배선이 지나치게 좁아지는 것을 제한**한다.

Minimum Width가 필요한 첫 번째 이유는 Lithography와 Etching이다.

Metal Pattern이 지나치게 좁으면 공정 과정에서 설계한 Width를 안정적으로 유지하기 어려워질 수 있다.

또한 Metal Width가 감소하면 배선 저항이 증가한다.

```math
R = R_{sh}\frac{L}{W}
```

따라서,

```math
W\downarrow
\rightarrow
R\uparrow
```

가 된다.

배선 저항이 증가하면 IR Drop과 RC Delay 역시 증가할 수 있다.

```math
V_{\text{drop}} = IR
```

```math
\tau \approx RC
```

따라서 Minimum Metal Width는 **제조 가능성뿐만 아니라 배선의 전기적 성능과 Reliability를 확보하기 위한 규칙**이다.

---

## 4. Metal Width와 Current Density

Metal Width는 Current Density와도 직접적인 관계를 가진다.

Current Density는 다음과 같다.

```math
J = \frac{I}{A}
```

Metal의 단면적을 이용하면,

```math
J = \frac{I}{Wt}
```

가 된다.

동일한 전류 $I$가 흐를 때 Metal Width가 감소하면,

```math
W\downarrow
\rightarrow
A\downarrow
\rightarrow
J\uparrow
```

가 된다.

따라서 지나치게 좁은 Metal 배선은 높은 Current Density를 발생시킬 수 있다.

---

## 5. Electromigration

높은 Current Density는 **Electromigration(EM)**을 가속할 수 있다.

Electromigration은 높은 전류밀도에서 전도 전자가 금속 원자에 Momentum을 전달하면서 금속 원자가 장시간에 걸쳐 이동하는 현상이다.

이를 흔히 **Electron Wind Force**와 연관하여 설명한다.

```text
Electron Flow
──────────────→

Metal Atom Migration
──────────────→
```

금속 원자가 빠져나간 영역에는 Vacancy가 축적되어 **Void**가 형성될 수 있다.

```text
Atom Depletion
     ↓
   Void
     ↓
Resistance Increase / Open Failure
```

반대로 금속 원자가 축적되는 영역에서는 **Hillock 또는 Extrusion**이 형성될 수 있으며, 심한 경우 인접 배선과 Short를 유발할 수 있다.

```text
Atom Accumulation
       ↓
Hillock / Extrusion
       ↓
Short Risk
```

따라서 Metal Width를 충분히 확보하는 것은 Electromigration Reliability와도 직접적으로 연결된다.

---

## 6. Black's Equation

Electromigration에 의한 배선의 수명은 대표적으로 **Black's Equation**을 통해 설명할 수 있다.

```math
MTTF
=
A J^{-n}
\exp\left(\frac{E_a}{kT}\right)
```

여기서

- $MTTF$ : Mean Time To Failure
- $A$ : Process-dependent Constant
- $J$ : Current Density
- $n$ : Current Density Exponent
- $E_a$ : Activation Energy
- $k$ : Boltzmann Constant
- $T$ : Absolute Temperature

이다.

따라서,

```math
J\uparrow
\rightarrow
MTTF\downarrow
```

이며 온도가 증가해도 MTTF가 감소한다.

```math
T\uparrow
\rightarrow
MTTF\downarrow
```

Metal Width와 연결하면,

```math
W\downarrow
\rightarrow
J\uparrow
\rightarrow
MTTF\downarrow
```

로 정리할 수 있다.

따라서 Minimum Metal Width는 단순한 Lithography Rule을 넘어 **Electromigration에 의한 장기 Reliability와도 관련된 Geometry Rule**이다.

---

## 7. Minimum Metal1 Spacing

서로 다른 Metal1 배선 사이에는 Minimum Spacing이 필요하다.

```text
Metal1                    Metal1
┌───────┐                ┌───────┐
│       │ ←─ Spacing ──→ │       │
└───────┘                └───────┘
```

Metal 사이의 거리가 지나치게 작으면 Lithography 및 Etching 과정에서 두 Metal Pattern을 안정적으로 분리하기 어려워질 수 있다.

또한 인접 배선 사이에는 Parasitic Capacitance가 존재한다.

단순한 Parallel-Plate 관계를 이용하면 Capacitance는 다음과 같은 경향을 가진다.

```math
C \propto \frac{\epsilon A}{d}
```

따라서 배선 사이의 거리 $d$가 감소하면,

```math
d\downarrow
\rightarrow
C_{\text{coupling}}\uparrow
```

가 된다.

이는 인접 신호선 사이의 Coupling 및 Crosstalk를 증가시키고 RC Delay에 영향을 줄 수 있다.

따라서 Metal Spacing은 **제조 가능성뿐만 아니라 배선 간 Coupling을 제어하는 데에도 중요한 요소**이다.

---

## 8. Wide Metal Spacing

Metal Width가 증가하면 일반적인 Minimum Spacing보다 더 큰 Spacing을 요구하는 Rule이 존재할 수 있다.

Wide Metal은 일반적인 Signal Line보다 큰 면적을 가지므로 Patterning 및 공정 조건이 달라질 수 있다.

또한 긴 구간에서 서로 마주보는 Wide Metal은 상대적으로 큰 Coupling Capacitance를 형성할 수 있다.

따라서 특정 Width 이상인 Metal에는 별도의 Spacing Rule이 적용될 수 있다.

```text
Normal Metal
→ Normal Spacing Rule

Wide Metal
→ Larger Spacing Rule
```

즉, Metal Spacing은 항상 하나의 고정된 값이 아니라 **Metal Width와 Geometry에 따라 달라질 수 있다.**

---

## 9. Minimum Metal1 Area

Metal1에는 Minimum Width와 별도로 **Minimum Area Rule**이 적용될 수 있다.

```math
A_{\text{Metal1}}
\geq
A_{\min}
```

Minimum Width를 만족하더라도 Metal Pattern의 길이가 지나치게 짧으면 전체 면적이 매우 작아질 수 있다.

이러한 작은 Metal Pattern은 Lithography 및 Etching 과정에서 안정적으로 형성되기 어렵다.

따라서 Minimum Area Rule은 **Minimum Width Rule만으로 검출하기 어려운 작은 Metal Island 또는 짧은 Pattern을 제한하기 위한 보완적인 규칙**이다.

---

## 10. Metal1 to Contact Enclosure

Metal1은 Contact를 충분히 감싸야 한다.

```text
┌────────── Metal1 ──────────┐
│                            │
│          ┌─────┐           │
│          │CONT │           │
│          └─────┘           │
│                            │
└────────────────────────────┘
```

Metal1과 Contact는 서로 다른 Mask로 형성되므로 Overlay Error가 발생할 수 있다.

Metal1과 Contact Edge를 정확히 일치시키면 Mask Misalignment에 의해 Contact 일부가 Metal1 밖으로 벗어날 수 있다.

따라서 Metal1을 Contact보다 충분히 크게 형성하여 안정적인 Contact Area를 확보한다.

```math
\text{Metal Enclosure}
=
\text{Required Contact Region}
+
\text{Overlay Margin}
```

---

## 11. Metal1 to Via1 Enclosure

Metal1은 Via1의 아래쪽 Metal Layer이므로 Via1 역시 충분히 Enclose해야 한다.

```text
        Metal2
          │
        Via1
          │
        Metal1
```

Via1과 Metal1 사이의 Enclosure가 부족하면 Overlay Error에 의해 Via의 유효 접촉 면적이 감소할 수 있다.

그 결과 Via Resistance 증가 또는 Open Failure가 발생할 수 있다.

따라서 Metal1-to-Via1 Enclosure Rule은 **안정적인 Metal1-Via1 Electrical Connection을 확보하기 위한 규칙**이다.

---

# 12. MMCAP의 개념

## Metal-Metal Capacitor

**MMCAP(Metal-Metal Capacitor)는 서로 다른 Metal Layer 사이에 존재하는 절연막을 Dielectric으로 이용하여 형성하는 Capacitor 구조**이다.

기본적인 구조는 다음과 같이 이해할 수 있다.

```text
========================
      Top Metal
========================

       Dielectric

========================
     Bottom Metal
========================
```

두 Metal Plate 사이에 Dielectric이 존재하므로 기본적인 Parallel-Plate Capacitor 관계를 이용할 수 있다.

```math
C
=
\frac{\epsilon A}{d}
```

여기서

- $\epsilon$ : Dielectric Permittivity
- $A$ : 두 Metal의 Overlap Area
- $d$ : Metal 사이 Dielectric Thickness

이다.

따라서,

```math
A\uparrow
\rightarrow
C\uparrow
```

이고,

```math
d\downarrow
\rightarrow
C\uparrow
```

가 된다.

---

## 13. MMCAP에서 Metal Geometry가 중요한 이유

MMCAP에서는 일반적인 Interconnect와 달리 Metal의 **Overlap Area 자체가 Capacitance를 결정하는 핵심 설계 변수**가 된다.

```math
C
\propto
A_{\text{overlap}}
```

따라서 Metal Plate의 Width 또는 Length가 증가하면 Overlap Area가 증가하여 Capacitance가 증가한다.

```math
A_{\text{overlap}} = WL
```

따라서,

```math
C
=
\frac{\epsilon WL}{d}
```

로 생각할 수 있다.

하지만 실제 IC의 Metal Capacitor에서는 Plate Edge 주변에서 **Fringing Field**가 존재하므로 단순한 Parallel-Plate 식만으로 실제 Capacitance가 완전히 결정되는 것은 아니다.

개념적으로,

```math
C_{\text{total}}
=
C_{\text{area}}
+
C_{\text{fringe}}
+
C_{\text{parasitic}}
```

로 생각할 수 있다.

따라서 MMCAP Layout에서는 단순히 Metal Area만 크게 만드는 것이 아니라 PDK에서 정의된 Geometry와 Design Rule을 따라야 한다.

---

## 14. MMCAP과 일반 Metal Interconnect의 차이

일반적인 Metal1 Interconnect의 목적은 **신호 또는 전력을 전달하는 것**이다.

```text
Metal Interconnect
→ Low Resistance
→ Signal / Power Delivery
```

반면 MMCAP에서는 Metal Layer를 **Capacitor의 Electrode 또는 Plate**로 사용한다.

```text
MMCAP
→ Metal Plate
→ Electric Field
→ Capacitance
```

따라서 동일한 Metal Layer라도 Layout 목적에 따라 중요하게 고려해야 하는 특성이 달라진다.

| 구분 | Metal Interconnect | MMCAP |
|---|---|---|
| 주요 목적 | 신호/전력 전달 | Capacitance 형성 |
| 중요 요소 | Resistance | Capacitance |
| 주요 Geometry | Width, Length | Overlap Area |
| 주요 기생 성분 | RC, Coupling | Fringe/Parasitic C |
| 전류 | 직접 흐름 | 주로 Charging/Discharging |

---

# 15. Metal Density

Metal Design Rule에서는 개별 배선의 Width와 Spacing뿐만 아니라 일정 영역 또는 Chip 전체에서 Metal이 차지하는 **Density**도 중요하다.

Metal Density는 특정 Window 또는 전체 Area에서 Metal Pattern이 차지하는 면적 비율로 정의할 수 있다.

```math
\text{Metal Density}
=
\frac{A_{\text{Metal}}}
{A_{\text{Window}}}
\times 100
```

예를 들어 특정 영역의 면적이 $100\;\mu m^2$이고 그중 Metal1이 $40\;\mu m^2$를 차지한다면,

```math
\text{Metal Density}
=
\frac{40}{100}
\times100
=
40\%
```

가 된다.

---

## 16. Metal Density Rule이 필요한 이유

Metal Density가 지나치게 높거나 낮고 위치에 따라 큰 차이가 발생하면 Wafer의 평탄화 및 Patterning Uniformity에 영향을 줄 수 있다.

특히 Multi-Level Interconnect 공정에서는 Metal과 Dielectric을 반복적으로 형성하므로 각 Layer의 표면을 평탄하게 유지하는 것이 중요하다.

이 과정에서 대표적으로 **CMP(Chemical Mechanical Planarization)**가 사용된다.

```text
Metal Pattern
      ↓
Dielectric Deposition
      ↓
CMP
      ↓
Surface Planarization
```

Metal Pattern Density가 크게 불균일하면 CMP Removal Rate 및 Local Topography가 달라질 수 있다.

대표적인 문제로 **Dishing과 Erosion**을 고려할 수 있다.

### Dishing

넓은 Metal 영역이 주변보다 과도하게 제거되어 Metal 표면이 움푹 들어가는 현상이다.

### Erosion

Metal Pattern이 밀집된 영역에서 Metal과 주변 Dielectric이 함께 과도하게 제거되어 전체 표면 높이가 낮아지는 현상이다.

따라서 Metal Density를 적절한 범위로 유지하는 것은 **CMP Uniformity와 Interconnect Thickness Control을 확보하는 데 중요하다.**

---

## 17. Metal Density와 Dummy Metal Fill

실제 Chip Layout에서는 회로적으로 Metal이 거의 존재하지 않는 영역이 발생할 수 있다.

이 경우 Metal Density를 균일하게 만들기 위해 **Dummy Metal Fill**을 추가할 수 있다.

```text
Before Dummy Fill

████████                    ████████
          Large Empty Area


After Dummy Fill

████████  ▪ ▪ ▪ ▪ ▪ ▪     ████████
          Dummy Metal
```

Dummy Metal은 주로 회로의 직접적인 신호 연결 목적이 아니라 **공정 Density를 균일하게 유지하기 위한 Pattern**이다.

```math
\text{Density Uniformity}\uparrow
\rightarrow
\text{CMP Uniformity}\uparrow
```

다만 Dummy Metal을 추가하면 주변 신호선과의 Parasitic Capacitance가 증가할 수 있으므로 전기적 영향도 함께 고려해야 한다.

---

## 18. Metal Density와 Parasitic Capacitance

Dummy Fill 또는 주변 Metal Pattern이 증가하면 신호 배선과 주변 Metal 사이의 Coupling Capacitance가 증가할 수 있다.

개념적으로,

```math
C
\propto
\frac{\epsilon A}{d}
```

이므로 주변 Metal Area가 증가하거나 배선 사이의 거리가 감소하면 Parasitic Capacitance가 증가할 수 있다.

따라서 Metal Density는 단순히 높게 만드는 것이 목적이 아니다.

```text
Too Low Density
→ CMP / Process Uniformity 문제

Too High Density
→ Process 문제 + Parasitic 증가 가능
```

즉, **PDK에서 규정하는 Density Window 내에서 균일한 Metal Distribution을 확보하는 것이 중요하다.**

---

## 19. Local Density와 Global Density

Metal Density는 크게 Local 관점과 Global 관점으로 생각할 수 있다.

### Local Density

Chip의 일정 크기 Window 안에서 Metal이 차지하는 비율이다.

```math
D_{\text{local}}
=
\frac{A_{\text{Metal, Window}}}
{A_{\text{Window}}}
```

Local Density가 지나치게 불균일하면 특정 영역의 CMP 특성이 달라질 수 있다.

### Global Density

전체 Chip 또는 넓은 영역을 기준으로 Metal이 차지하는 비율이다.

```math
D_{\text{global}}
=
\frac{A_{\text{Metal}}}
{A_{\text{Total}}}
```

따라서 Density Rule에서는 단순한 전체 평균뿐만 아니라 **특정 Window 내에서의 Local Pattern Density**도 중요하게 고려될 수 있다.

---

## 20. Metal1 Design Rule과 물리적 의미

Metal1 Design Rule을 전체적으로 정리하면 각각의 Rule은 서로 다른 물리적 목적을 가진다.

| Rule | 주요 목적 |
|---|---|
| Minimum Width | Patterning, Resistance, Current Capacity |
| Minimum Spacing | Pattern Separation, Coupling |
| Minimum Area | Small Pattern 제조 안정성 |
| Contact Enclosure | Overlay Margin, Contact Reliability |
| Via Enclosure | Overlay Margin, Via Reliability |
| Wide Metal Rule | Wide Pattern의 Process Margin |
| Metal Density | CMP 및 Process Uniformity |
| MMCAP 관련 Geometry | 안정적인 Capacitance 형성 |

따라서 Metal Design Rule은 단순히 Metal Pattern이 서로 겹치거나 가까워지는 것을 방지하는 규칙이 아니다.

**Electrical Performance와 Fabrication Reliability를 동시에 확보하기 위한 규칙**이라고 이해할 수 있다.

---

# 21. Metal1 Design Rule에 대한 고찰

Metal1 Design Rule을 학습하면서 Metal 배선의 Geometry가 단순히 회로의 연결 관계만 결정하는 것이 아니라 **Resistance, Capacitance, Current Density 및 Reliability까지 직접 결정한다는 점**을 이해할 수 있었다.

특히 Metal Width에 대해서는 다음 관계를 확인할 수 있었다.

```math
W\downarrow
\rightarrow
R\uparrow
```

동시에,

```math
W\downarrow
\rightarrow
J\uparrow
\rightarrow
MTTF\downarrow
```

이므로 좁은 Metal은 Resistance뿐만 아니라 Electromigration Reliability 측면에서도 불리할 수 있다.

Spacing Rule에서는 인접 Metal 사이의 제조 Margin뿐만 아니라 Coupling Capacitance와 Crosstalk 역시 고려할 필요가 있다는 점을 확인하였다.

또한 MMCAP을 통해 **Metal Layer가 단순한 Interconnect뿐만 아니라 Capacitor의 Electrode로도 활용될 수 있다는 점**을 학습할 수 있었다.

```math
C
\approx
\frac{\epsilon A}{d}
```

이를 통해 Metal Geometry가 Resistance를 결정하는 경우와 Capacitance를 결정하는 경우를 함께 이해할 수 있었다.

마지막으로 Metal Density Rule을 통해 개별 Pattern이 모든 Width 및 Spacing DRC를 만족하더라도 Chip 전체의 제조 가능성이 보장되는 것은 아니라는 점을 확인하였다.

```text
Individual Geometry Rule
        +
Pattern Density Rule
        ↓
Manufacturable Layout
```

특히 Multi-Level Metal 공정에서는 CMP를 통한 평탄화가 중요하므로 Local Metal Density를 균일하게 유지하고 필요에 따라 Dummy Metal Fill을 사용하는 이유를 이해할 수 있었다.

따라서 Metal1 Layout은 단순한 배선 작업이 아니라 **Resistance, RC Delay, Coupling, Electromigration, Capacitance 및 CMP Uniformity를 동시에 고려하는 과정**이라고 볼 수 있다.

---

# 22. Overall Summary

Metal1은 Contact를 통해 Device와 연결되는 첫 번째 Metal Interconnect Layer이며 Via1을 통해 상위 Metal Layer와 연결된다.

전체적인 연결 구조는 다음과 같다.

```math
\text{Active / Poly}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
\rightarrow
\text{Via1}
\rightarrow
\text{Metal2}
```

Metal1의 Width, Spacing, Area 및 Enclosure Rule은 안정적인 Patterning과 Electrical Connection을 확보한다.

또한 Metal Width는 다음과 같이 Resistance와 Electromigration Reliability에 직접적인 영향을 준다.

```math
W\downarrow
\rightarrow
R\uparrow,\quad J\uparrow
\rightarrow
\text{Performance / Reliability Degradation}
```

MMCAP에서는 Metal의 Overlap Area를 이용하여 Capacitance를 형성하며,

```math
C
\approx
\frac{\epsilon A}{d}
```

Metal Density Rule에서는 일정 영역에서 Metal이 차지하는 비율을 제한하여 CMP 및 Process Uniformity를 확보한다.

```math
\text{Metal Density}
=
\frac{A_{\text{Metal}}}{A_{\text{Window}}}
\times100
```

Cadence Virtuoso에서 Metal1의 Width, Spacing, Area, Contact/Via Enclosure, MMCAP 관련 구조 및 Metal Density Rule을 직접 Layout으로 구성하고 DRC를 수행함으로써, **Metal Design Rule이 단순한 배선 Geometry 제한이 아니라 Interconnect의 성능, Reliability, Capacitance 및 제조 공정의 Uniformity를 종합적으로 확보하기 위한 규칙이라는 점을 이해할 수 있었다.**
