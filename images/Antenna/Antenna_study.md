# Antenna Design Rule Study

## 1. Antenna Effect의 개념

**Antenna Effect는 반도체 제조 공정 중 아직 전기적으로 Floating 상태인 Metal 또는 Poly 배선에 Plasma 공정으로 전하가 축적되고, 이 전하가 MOSFET의 Gate Oxide를 통해 방전되면서 Gate Oxide가 손상되는 현상**이다.

여기서 Antenna는 RF에서 사용하는 안테나를 의미하는 것이 아니라, **공정 중 전하를 수집하는 도전성 배선 구조**를 의미한다.

```text
Plasma Process
      ↓
Metal / Poly에 Charge 축적
      ↓
Gate에 연결
      ↓
Gate Oxide에 높은 Electric Field 발생
      ↓
Gate Oxide Damage
```

따라서 Antenna Rule은 완성된 Chip의 정상 동작 상태보다 **Wafer Fabrication 과정에서 MOS Gate를 보호하기 위한 Design Rule**이다.

---

## 2. Antenna Effect가 발생하는 이유

IC 제조 과정에서는 Metal Etching과 같은 Plasma Process가 사용된다.

Plasma 내부에는 Electron과 Ion 등의 Charged Particle이 존재하며, 노출된 Conductive Pattern이 이러한 전하를 수집할 수 있다.

특히 긴 Metal 또는 넓은 Metal Pattern은 더 많은 전하를 수집할 가능성이 있다.

```text
      Plasma
 ↓  ↓  ↓  ↓  ↓  ↓  ↓

=======================
     Long Metal
=======================
           │
           │
          Gate
           │
        Gate Oxide
───────────┴───────────
        Silicon
```

이때 Metal이 Source/Drain Junction 등으로 연결되어 있지 않고 MOS Gate에만 연결되어 있다면 축적된 전하가 안전하게 빠져나갈 경로가 제한될 수 있다.

그 결과 Gate Oxide 양단에 높은 전압이 형성될 수 있다.

---

## 3. Gate Oxide와 Electric Field

Gate Oxide에 형성되는 Electric Field는 간단하게 다음과 같이 생각할 수 있다.

```math
E_{ox}
=
\frac{V_{ox}}{t_{ox}}
```

여기서

- $E_{ox}$ : Gate Oxide Electric Field
- $V_{ox}$ : Gate Oxide에 걸리는 전압
- $t_{ox}$ : Gate Oxide Thickness

이다.

Gate Oxide가 매우 얇기 때문에 비교적 작은 전압이 발생하더라도 높은 Electric Field가 형성될 수 있다.

```math
t_{ox}\downarrow
\rightarrow
E_{ox}\uparrow
```

전기장이 Oxide가 견딜 수 있는 범위를 넘어가면 Gate Oxide에 Damage가 발생할 수 있다.

---

## 4. Antenna Ratio

Antenna Effect의 위험성을 판단하기 위해 **Antenna Ratio**를 사용한다.

가장 기본적인 개념은 전하를 수집하는 Interconnect의 면적과 연결된 Gate Oxide Area의 비율을 비교하는 것이다.

```math
AR
=
\frac{A_{\text{Antenna}}}
{A_{\text{Gate}}}
```

여기서

- $AR$ : Antenna Ratio
- $A_{\text{Antenna}}$ : 공정 중 전하를 수집할 수 있는 Conductive Area
- $A_{\text{Gate}}$ : 해당 Net에 연결된 Gate Area

이다.

따라서,

```math
A_{\text{Antenna}}\uparrow
\rightarrow
AR\uparrow
\rightarrow
\text{Antenna Risk}\uparrow
```

가 된다.

반대로 연결된 Gate Area가 커지면 동일한 Antenna Area에 대한 Ratio는 감소한다.

```math
A_{\text{Gate}}\uparrow
\rightarrow
AR\downarrow
```

실제 PDK에서는 단순 면적비가 아니라 Layer별 계산 방식, Sidewall Area, 공정 순서 및 보정 계수 등이 포함될 수 있으므로 **정확한 식과 허용값은 해당 Antenna Rule Deck을 따라야 한다.**

---

## 5. 왜 긴 Metal이 문제가 되는가

Gate에 연결된 Metal이 길거나 넓어질수록 Plasma에 노출되는 Conductive Area가 증가할 수 있다.

```text
Short Metal

─────── Gate

Antenna Area : Small


Long Metal

──────────────────────────── Gate

Antenna Area : Large
```

따라서 일반적으로,

```math
A_{\text{Metal}}\uparrow
\rightarrow
Q_{\text{collected}}\uparrow
```

와 같은 경향으로 이해할 수 있다.

축적된 전하량이 증가하면 Gate Oxide에 더 큰 Electrical Stress가 발생할 가능성이 높아진다.

---

## 6. 왜 Gate가 특히 중요한가

Source와 Drain은 PN Junction을 통해 Substrate 또는 Well과 연결되는 경로가 존재할 수 있다.

반면 MOS Gate는 Gate Oxide에 의해 Silicon과 절연되어 있다.

```text
          Metal
            │
            │
           Gate
════════════════════
       Gate Oxide
════════════════════
         Silicon
```

따라서 제조 중 Gate에 연결된 배선에 전하가 축적되면 Gate Oxide가 주요 전압 Stress를 받을 수 있다.

Antenna Rule에서 **Gate Area가 중요한 기준으로 사용되는 이유**가 여기에 있다.

---

## 7. Antenna Effect와 Gate Oxide Damage

Plasma Charging에 의해 Gate Oxide에 높은 Electric Field가 장시간 또는 반복적으로 인가되면 Oxide 내부에 Defect가 생성될 수 있다.

결과적으로 다음과 같은 문제가 발생할 수 있다.

- Gate Leakage 증가
- Oxide Trap 증가
- Threshold Voltage 변화
- Gate Oxide Reliability 감소
- 심한 경우 Oxide Breakdown

따라서 Antenna Violation은 단순한 Layout Geometry 문제가 아니라 **MOSFET의 장기 Reliability와 Yield에 영향을 줄 수 있는 문제**이다.

---

## 8. Antenna Effect가 제조 중 발생한다는 의미

Antenna Effect에서 가장 중요한 개념 중 하나는 **완성된 회로의 연결 상태만 보면 안 된다는 것**이다.

최종 Chip에서는 해당 Metal Net이 다른 Device 및 Junction에 정상적으로 연결되어 있더라도 제조 과정에서는 Metal Layer가 순차적으로 형성된다.

예를 들어,

```text
Step 1
Gate + Metal1
→ Floating 가능

Step 2
Via1 + Metal2
→ 추가 연결 형성

Step 3
Upper Metal
→ 최종 Net 완성
```

과 같은 과정을 거친다.

즉, 최종 회로에서는 안전한 Net이라도 **특정 제조 단계에서는 Gate에 연결된 긴 Floating Conductor가 존재할 수 있다.**

따라서 Antenna Check는 최종 Layout의 연결 관계뿐만 아니라 **Process Sequence를 고려한다.**

---

# 9. Antenna Violation 해결 방법

Antenna Violation이 발생하면 대표적으로 다음과 같은 방법으로 해결할 수 있다.

---

## 10. 방법 1 — Metal Jumper

대표적인 Antenna Fix 방법은 **Metal Jumper**를 사용하는 것이다.

긴 Lower Metal을 중간에서 끊고 Via를 통해 Upper Metal Layer로 이동시킨 후 다시 필요한 위치에서 내려오는 방법이다.

### 수정 전

```text
Long Metal1
──────────────────────────────
                              │
                             Gate
```

Metal1이 Gate에 연결된 상태로 긴 Antenna Pattern을 형성할 수 있다.

### 수정 후

```text
Metal2
        ───────────────────
        │                 │
      Via1              Via1
        │                 │
Metal1 ──                 ── Gate
```

Lower Metal이 형성되는 공정 단계에서 Gate에 연결된 노출 Metal Area를 감소시킬 수 있다.

이를 흔히 **Metal Hopping 또는 Layer Hopping**이라고 한다.

---

## 11. 왜 Metal Jumper가 효과적인가

핵심은 최종 배선 길이 자체를 반드시 줄이는 것이 아니라 **특정 Plasma Process 단계에서 Gate에 연결되어 전하를 수집하는 Conductive Area를 줄이는 것**이다.

```text
Before

Long Lower Metal
→ Large Antenna Area


After

Short Lower Metal
   ↓
Via
   ↓
Upper Metal
→ Reduced Antenna Exposure at Critical Step
```

따라서 Metal Jumper는 Routing은 유지하면서 특정 Layer의 Antenna Ratio를 감소시킬 수 있다.

---

## 12. 방법 2 — Antenna Diode

또 다른 대표적인 방법은 Gate Net에 **Antenna Protection Diode**를 연결하는 것이다.

```text
Long Metal
───────────────┬──── Gate
               │
          Antenna Diode
               │
         Substrate / Well
```

Plasma Charging으로 전하가 축적될 때 Diode가 전하를 방전할 수 있는 경로를 제공하여 Gate Oxide에 집중되는 Electrical Stress를 줄일 수 있다.

개념적으로,

```text
Without Antenna Diode

Charge
  ↓
Gate Oxide
  ↓
Oxide Stress


With Antenna Diode

Charge
  ↓
Antenna Diode
  ↓
Discharge Path
```

로 이해할 수 있다.

---

## 13. Metal Jumper와 Antenna Diode 비교

| 방법 | Metal Jumper | Antenna Diode |
|---|---|---|
| 원리 | 공정 단계별 Antenna Area 감소 | Charge Discharge Path 제공 |
| 추가 Device | 필요 없음 | 필요 |
| Routing 변경 | 필요 | 비교적 적음 |
| Area Overhead | Routing/Via 증가 | Diode Area 증가 |
| 주요 장점 | Gate 연결 면적 직접 감소 | 긴 Net에서도 적용 가능 |
| 고려사항 | Routing 복잡도 증가 | Junction/Parasitic 증가 가능 |

따라서 Layout 상황에 따라 적절한 Antenna Fix 방법을 선택해야 한다.

---

# 14. Antenna Rule과 Metal Density Rule의 차이

Antenna Rule과 Metal Density Rule은 모두 Metal Geometry와 관련되어 있지만 목적은 완전히 다르다.

### Antenna Rule

```text
Metal Geometry
      ↓
Plasma Charge Collection
      ↓
Gate Oxide Stress
      ↓
Reliability
```

### Metal Density Rule

```text
Metal Distribution
      ↓
CMP Uniformity
      ↓
Surface Planarity
      ↓
Process Uniformity
```

따라서,

| 구분 | Antenna Rule | Metal Density Rule |
|---|---|---|
| 주요 대상 | Gate에 연결된 Conductor | 일정 Window의 Metal |
| 주요 원인 | Plasma Charging | Pattern Density |
| 주요 문제 | Gate Oxide Damage | CMP / Planarity |
| 대표 해결 | Jumper, Antenna Diode | Dummy Metal Fill |

두 Rule 모두 Metal Layout을 검사하지만 **물리적인 발생 원인과 해결 방법은 서로 다르다.**

---

# 15. Antenna Rule과 일반 Metal Rule의 차이

Minimum Width나 Minimum Spacing은 특정 Metal Pattern 자체의 Geometry를 검사하는 경우가 많다.

```text
Width Rule
→ Metal Width 검사

Spacing Rule
→ Metal 간 거리 검사
```

반면 Antenna Rule에서는 하나의 Net에 연결된 **전체 Conductive Geometry와 Gate Area 사이의 관계**가 중요하다.

```math
\text{Antenna Risk}
\sim
\frac{\text{Charge Collection Geometry}}
{\text{Gate Area}}
```

따라서 각각의 Metal Shape가 Width와 Spacing Rule을 모두 만족하더라도 **전체 Net 기준으로 Antenna Violation이 발생할 수 있다.**

---

# 16. Antenna Design Rule에 대한 고찰

Antenna Rule을 학습하면서 Design Rule은 완성된 Chip의 전기적 특성만을 고려하는 것이 아니라 **반도체가 실제로 제조되는 중간 과정의 Reliability까지 고려한다는 점**을 이해할 수 있었다.

특히 Antenna Effect는 최종 회로에서 Metal이 정상적으로 연결되어 있는지만 확인해서는 이해하기 어렵다.

Metal Layer가 순차적으로 형성되는 과정에서 특정 시점에는 Gate에 연결된 배선이 Floating 상태가 될 수 있으며, Plasma Process에서 해당 배선이 전하를 수집할 수 있다.

```text
Plasma
   ↓
Floating Interconnect
   ↓
Charge Accumulation
   ↓
Gate Oxide Stress
   ↓
Oxide Damage
```

또한 Antenna Ratio를 통해 단순히 Metal 배선이 길다는 사실보다 **전하를 수집할 수 있는 Conductive Geometry와 이를 견뎌야 하는 Gate Area의 상대적인 크기가 중요하다**는 점을 이해할 수 있었다.

```math
AR
=
\frac{A_{\text{Antenna}}}
{A_{\text{Gate}}}
```

Metal Jumper를 이용한 해결 방법에서는 최종적으로 동일한 Net을 연결하더라도 Routing Layer를 변경하면 제조 중 특정 단계에서 Gate에 연결되는 Antenna Area를 감소시킬 수 있다는 점을 확인할 수 있었다.

이를 통해 Layout Design에서는 최종적인 Electrical Connectivity뿐만 아니라 **Process Sequence까지 고려해야 한다는 점**을 학습할 수 있었다.

---

# 17. Overall Summary

Antenna Effect는 **Plasma 기반 반도체 제조 공정에서 Floating Metal 또는 Poly가 전하를 수집하고, 이 전하가 MOSFET Gate에 전달되어 얇은 Gate Oxide에 Electrical Stress를 발생시키는 현상**이다.

전체적인 발생 과정은 다음과 같다.

```text
Plasma Process
      ↓
Charge Collection
      ↓
Metal / Poly Charging
      ↓
Gate Voltage Stress
      ↓
Gate Oxide Damage
```

Antenna 위험은 기본적으로 다음과 같은 Antenna Ratio의 개념을 통해 이해할 수 있다.

```math
AR
=
\frac{A_{\text{Antenna}}}
{A_{\text{Gate}}}
```

Antenna Violation이 발생하면 Metal Jumper를 이용하여 특정 공정 단계에서 Gate에 연결되는 Conductive Area를 감소시키거나, Antenna Diode를 추가하여 축적된 전하의 방전 경로를 제공할 수 있다.

```text
Antenna Violation
       │
       ├── Metal Jumper
       │      ↓
       │  Antenna Area 감소
       │
       └── Antenna Diode
              ↓
         Discharge Path 형성
```

Cadence Virtuoso에서 Antenna Rule을 확인함으로써, **Antenna Design Rule이 단순한 Metal Geometry 제한이 아니라 Plasma 공정 중 발생할 수 있는 Charging으로부터 MOSFET의 Gate Oxide를 보호하기 위한 Process Reliability Rule이라는 점을 이해할 수 있었다.**
