# VIA Design Rule Study

## 1. VIA의 개념

**Via는 서로 다른 Metal Layer 사이를 전기적으로 연결하는 수직 Interconnect 구조**이다.

예를 들어 Via1은 일반적으로 Metal1과 Metal2 사이를 연결한다.

```text
Metal2
  │
Via1
  │
Metal1
```

일반화하면 다음과 같다.

```math
\text{Via}_k
:
\text{Metal}_k
\leftrightarrow
\text{Metal}_{k+1}
```

따라서 Via는 동일한 Metal Layer에서 이루어지는 수평 배선과 달리 **서로 다른 배선 층 사이에서 전류가 이동할 수 있는 수직 경로를 형성한다.**

---

## 2. Contact와 Via의 차이

Contact와 Via는 모두 수직 방향의 전기적 연결을 형성하지만 연결 대상이 다르다.

| 구분 | Contact | Via |
|---|---|---|
| 역할 | Device와 Metal 연결 | Metal과 Metal 연결 |
| 하부 Layer | Active / Poly | Metal k |
| 상부 Layer | Metal1 | Metal k+1 |
| 예시 | Active ↔ M1 | M1 ↔ M2 |

즉,

```text
Contact : Device ↔ Metal1

Via     : Metal k ↔ Metal k+1
```

로 구분할 수 있다.

---

## 3. VIA의 기본 구조

Via k를 형성하려면 Via의 위와 아래에 연결 대상 Metal이 존재해야 한다.

예를 들어 Via1에서는 다음과 같은 구조가 형성된다.

```text
──────────────
    Metal2
──────┬───────
     Via1
──────┴───────
    Metal1
──────────────
```

따라서 Via Design Rule에서는 Via 자체의 크기뿐만 아니라 **Via와 위·아래 Metal 사이의 Enclosure**가 매우 중요하다.

---

## 4. Minimum Via Width

Via에는 제조 가능한 최소 Width가 존재한다.

```text
      Minimum Width
        ←────→
        ┌────┐
        │Via │
        └────┘
```

Via가 지나치게 작으면 Lithography 및 Etching 과정에서 Via Hole을 안정적으로 형성하기 어려워지고 이후 Conductive Material을 안정적으로 채우는 과정에도 영향을 줄 수 있다.

따라서 Minimum Via Width Rule은 **안정적인 Via Hole 형성 및 충진을 위한 최소 Geometry를 확보하는 규칙**이다.

---

## 5. Minimum Via-to-Via Spacing

동일한 Via Layer에 존재하는 두 Via 사이에는 Minimum Spacing이 요구된다.

```text
┌─────┐                 ┌─────┐
│ Via │ ←─ Spacing ──→ │ Via │
└─────┘                 └─────┘
```

두 Via가 지나치게 가까우면 Lithography 및 Etching 과정에서 각각의 Via Hole을 안정적으로 분리하기 어려워질 수 있다.

따라서 Via-to-Via Spacing Rule은 **인접 Via의 Pattern Separation과 제조 Margin을 확보하기 위한 규칙**이다.

---

## 6. Via Array Spacing

여러 Via가 배열된 경우에는 일반적인 Via-to-Via Spacing과 별도로 **Via Array에 대한 Spacing Rule**이 적용될 수 있다.

예를 들어 한쪽 방향의 최소 Via 개수가 3개 이상인 3×3 이상의 Via Array에서는 다음과 같은 구조가 형성된다.

```text
□ □ □
□ □ □
□ □ □
```

GPDK090에서는 이러한 큰 Via Array에서 Via 사이의 간격이 특정 기준보다 작은 경우 별도의 Array Spacing Check가 적용될 수 있다.

큰 Via Array에서는 국부적으로 Via Pattern Density가 증가하므로 단일 Via 또는 소수의 Via와는 다른 공정 조건을 고려해야 한다.

따라서 Via Array Rule은 **고밀도 Via Pattern에서 안정적인 Lithography, Etching 및 Filling을 확보하기 위한 규칙**으로 이해할 수 있다.

---

## 7. Via Array를 사용하는 이유

큰 전류를 전달해야 하는 배선에서는 하나의 Via보다 여러 Via를 병렬로 사용하는 것이 유리하다.

각 Via의 저항을 $R_{\text{via}}$라고 하고 동일한 Via $N$개가 이상적으로 병렬 연결되어 있다고 가정하면,

```math
R_{\text{eq}}
\approx
\frac{R_{\text{via}}}{N}
```

따라서

```math
N_{\text{via}}\uparrow
\rightarrow
R_{\text{eq}}\downarrow
```

가 된다.

또한 전체 전류가 여러 Via로 분산되므로 개별 Via에 흐르는 전류도 감소한다.

```math
I_{\text{via}}
\approx
\frac{I_{\text{total}}}{N}
```

따라서 Via Array는 **Via Resistance 감소, Current Distribution 및 Interconnect Reliability 향상**에 유리하다.

---

## 8. Minimum Metal k to Via k Enclosure

Via는 아래쪽 Metal에 충분히 포함되어야 한다.

즉, **Metal k가 Via k를 일정 거리 이상 감싸도록 설계해야 한다.**

```text
┌──────────── Metal k ────────────┐
│                                 │
│             ┌─────┐             │
│             │ Via │             │
│             └─────┘             │
│                                 │
└─────────────────────────────────┘
```

Via와 Metal의 Edge를 정확하게 일치시키면 Overlay Error에 의해 Via 일부가 Metal 밖으로 벗어날 수 있다.

이 경우 Via와 Metal 사이의 실제 접촉 면적이 감소하여 Via Resistance 증가 또는 심한 경우 Open Failure가 발생할 수 있다.

따라서 Metal-to-Via Enclosure는 **Mask Overlay Error를 보상하고 충분한 Electrical Contact Area를 확보하기 위한 규칙**이다.

---

## 9. Minimum Metal k to Via k Enclosure on at Least Two Opposite Sides

일부 Via Rule에서는 일반적인 Enclosure와 별도로 **Via의 최소 두 개의 서로 마주보는 방향에서 더 큰 Metal Enclosure를 요구**할 수 있다.

예를 들어 좌우 방향을 선택하면 다음과 같이 구현할 수 있다.

```text
        Metal k
┌───────────────────────┐
│                       │
│    ←───┌─────┐───→    │
│        │ Via │        │
│        └─────┘        │
│                       │
└───────────────────────┘
         ↑     ↑
       좌우 Enclosure
```

또는 위·아래 방향으로 충분한 Enclosure를 확보할 수도 있다.

핵심은 **네 방향 모두 동일한 큰 Enclosure를 요구하는 것이 아니라 최소 두 개의 서로 마주보는 방향에서 지정된 Enclosure를 확보한다는 것**이다.

---

## 10. 두 Enclosure Rule의 차이

두 Rule은 다음과 같이 구분할 수 있다.

### General Enclosure

```text
Minimum Metal k to Via k Enclosure
```

Via 주변에서 요구되는 **기본적인 최소 Metal Overlap**을 정의한다.

### Opposite-Side Enclosure

```text
Minimum Metal k to Via k Enclosure
on at least two opposite sides
```

Via의 **서로 마주보는 두 방향에서 추가적인 Metal Margin**을 확보하도록 요구한다.

따라서 Layout에서는 두 조건을 동시에 만족하도록 구성해야 한다.

```text
            기본 Enclosure
                 ↓
       ┌─────────────────┐
       │                 │
       │ ←── ┌─────┐ ──→ │
       │     │ Via │     │
       │     └─────┘     │
       │                 │
       └─────────────────┘
          ↑           ↑
      Opposite-side Enclosure
```

이러한 구조는 Via와 Metal 사이에 충분한 Overlap을 확보하면서도 Metal을 네 방향 모두 불필요하게 크게 만드는 것을 방지할 수 있다.

---

## 11. Via와 IMD

Via는 서로 다른 Metal Layer 사이의 **IMD(Inter-Metal Dielectric)**를 관통하여 형성된다.

예를 들어 Metal1과 Metal2 사이에서는 다음과 같이 이해할 수 있다.

```text
========================
        Metal2
========================
           │
     IMD   │ Via1
           │
========================
        Metal1
========================
```

따라서 Via 자체가 IMD인 것은 아니다.

**IMD는 Metal Layer 사이를 절연하는 Dielectric이고, Via는 그 IMD에 형성된 Hole을 Conductive Material로 채워 Metal 간 전기적 연결을 형성한 구조**이다.

---

## 12. Via Resistance

Via도 이상적인 도체가 아니므로 일정한 저항을 가진다.

단순화하면 저항은 다음 관계로 이해할 수 있다.

```math
R
=
\rho\frac{L}{A}
```

여기서

- $\rho$ : Via Material의 Resistivity
- $L$ : Via의 수직 길이
- $A$ : Current가 흐르는 유효 단면적

이다.

따라서 Via의 유효 접촉 면적이 감소하면 Via Resistance가 증가할 수 있다.

```math
A\downarrow
\rightarrow
R_{\text{via}}\uparrow
```

이 때문에 Via 자체의 Geometry뿐만 아니라 Metal Enclosure 역시 안정적인 Via Resistance 확보에 중요하다.

---

## 13. Via와 Reliability

Via는 배선의 단면적이 급격하게 변화하고 전류가 수직 방향으로 전환되는 부분이므로 **Current Crowding**이 발생하기 쉬운 영역이다.

Via에 지나치게 큰 전류가 집중되면 국부적인 Current Density가 증가한다.

```math
J=\frac{I}{A}
```

따라서

```math
A\downarrow
\rightarrow
J\uparrow
```

가 된다.

높은 Current Density는 Via 및 Via 주변 Metal의 Electromigration Reliability에 영향을 줄 수 있다.

이러한 이유로 높은 전류가 필요한 배선에서는 하나의 Via보다 여러 Via를 병렬로 배치하는 **Via Array 또는 Multi-Via 구조**가 유리하다.

---

## 14. VIA Design Rule에 대한 고찰

Via Design Rule을 학습하면서 Via는 단순히 두 Metal Layer를 연결하는 작은 사각형이 아니라 **Interconnect Resistance와 Reliability를 결정하는 중요한 수직 연결 구조**라는 점을 이해할 수 있었다.

Via Width와 Spacing Rule은 주로 Lithography, Etching 및 Filling과 같은 제조 가능성과 관련된다.

반면 Metal-to-Via Enclosure는 서로 다른 Mask 사이의 Overlay Error가 발생하더라도 Via와 Metal 사이에 충분한 Electrical Contact Area가 유지되도록 하는 역할을 한다.

특히 일반적인 Metal Enclosure와 `at least two opposite sides` Enclosure를 비교하면서 **Enclosure Rule이 반드시 네 방향에서 동일한 Margin을 요구하는 것은 아니며, 배선 방향과 Via Geometry를 고려하여 서로 다른 조건이 적용될 수 있다는 점**을 확인할 수 있었다.

또한 Via Array를 통해 여러 Via를 병렬로 연결하면

```math
R_{\text{eq}}\downarrow
```

하고 각 Via의 Current Stress를 감소시킬 수 있으므로 Via Layout은 단순한 DRC 만족을 넘어 **Interconnect Resistance, Current Capacity 및 Electromigration Reliability**와도 직접 연결된다는 점을 이해할 수 있었다.

---

## 15. Overall Summary

Via는 **서로 다른 Metal Layer 사이의 IMD를 관통하여 수직 방향의 전기적 연결을 형성하는 Interconnect 구조**이다.

전체적인 관계는 다음과 같이 정리할 수 있다.

```math
\text{Metal}_k
\rightarrow
\text{Via}_k
\rightarrow
\text{Metal}_{k+1}
```

Via Design Rule은 Via Width, Via-to-Via Spacing, Array Spacing 및 Metal Enclosure 등을 제한하여 안정적인 Via Pattern과 충분한 Metal-to-Via Contact Area를 확보한다.

또한 Via Array를 이용하면 Via Resistance와 개별 Via의 Current Stress를 감소시킬 수 있다.

Cadence Virtuoso에서 Via의 Width, Spacing, Array 및 Metal Enclosure Rule을 직접 Layout으로 구성하고 DRC를 수행함으로써, **Via Design Rule이 단순한 Geometry 제한이 아니라 Metal Layer 사이의 안정적인 전기적 연결과 Interconnect Reliability를 확보하기 위한 공정 규칙이라는 점을 이해할 수 있었다.**
