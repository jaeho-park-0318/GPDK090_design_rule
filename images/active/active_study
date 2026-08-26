# Active, Thick Active, Silicide & Non-Silicide Design Rule Study

본 학습에서는 **GPDK090 Design Rule**을 기반으로 Active Area와 Thick Active Area의 개념 및 관련 Design Rule을 분석하고, Cadence Virtuoso에서 직접 Layout을 구성하여 DRC를 통해 규칙의 적용 여부를 확인하였다. 또한 Silicide와 Non-Silicide 구조를 비교하여 저항 소자의 구현 방식과 Silicide Block의 필요성을 학습하였다.

단순히 Design Rule의 수치를 확인하는 것에서 그치지 않고, **각 Rule이 실제 반도체 공정 및 소자 특성과 어떠한 관계를 가지는지**를 함께 고찰하였다.

---

# 1. Active Area

## 1.1 Active Area의 개념

**Active Area**는 STI(Shallow Trench Isolation)에 의해 주변 영역과 격리된 실리콘 영역으로, MOSFET의 **Source/Drain 및 Channel이 형성되는 영역**이다.

MOSFET Layout에서는 Poly Gate가 Active Area를 가로지르면서 Channel 영역을 정의하고, Gate 양쪽 Active Area에 N+ 또는 P+ Implant가 형성되어 Source/Drain이 구성된다.

따라서 Active Area는 실제 트랜지스터가 형성되는 핵심 영역이며, Active 이외의 영역은 STI를 통해 인접 소자와 전기적으로 격리된다.

---

## 1.2 Active Width Rule

Active Area에는 안정적인 패턴 형성을 위해 **Minimum Width Rule**이 적용된다.

Active의 폭이 지나치게 좁으면 Lithography와 Etching 과정에서 원하는 Critical Dimension을 안정적으로 구현하기 어려워지며, STI 공정 편차의 영향도 상대적으로 증가할 수 있다.

특히 MOSFET에서는 Active와 Poly가 교차하는 부분이 Channel을 형성하므로 Active Width는 소자의 유효 Gate Width와도 관련된다.

MOSFET의 Drain Current는 간략하게

$$
I_D \propto \frac{W}{L}
$$

의 관계를 가지므로, Active Geometry는 단순한 제조 가능성뿐만 아니라 소자의 전기적 특성과도 밀접하게 연관된다.

---

## 1.3 Active Spacing Rule

서로 다른 Active Area 사이에는 일정한 최소 간격이 요구된다.

이는 인접한 Active 사이에 충분한 **STI Isolation 영역**을 확보하기 위한 것이다.

Active 사이의 거리가 지나치게 작으면 Lithography 및 Etching 공정 편차에 의해 Isolation Margin이 감소할 수 있으며, 인접 Diffusion 영역 간의 전기적 분리에도 영향을 줄 수 있다.

따라서 N+ Active와 N+ Active, P+ Active와 P+ Active뿐만 아니라 서로 다른 N+/P+ Active 사이에도 Minimum Spacing Rule이 적용된다.

---

## 1.4 Bent Active Rule

45°와 같이 Active Area가 꺾이는 부분에는 일반적인 직선 Active보다 더 큰 Width 및 Spacing이 요구될 수 있다.

이는 Corner 부분에서 발생할 수 있는 **Corner Rounding 및 Pattern Distortion** 때문이다.

Lithography 과정에서는 설계된 Layout의 날카로운 모서리가 Wafer 위에 완벽하게 전달되지 않으며, 실제 패턴에서는 모서리가 둥글게 형성될 수 있다.

따라서 Bent Active에서는 일반 Active보다 추가적인 Process Margin을 확보하여 실제 제조 후에도 필요한 유효 폭과 간격을 유지하도록 한다.

---

## 1.5 Active Rule에 대한 고찰

Active Design Rule을 학습하면서 Active가 단순히 Source/Drain을 표시하기 위한 Layout Layer가 아니라, **실제 실리콘에서 트랜지스터가 형성되는 영역을 결정하는 중요한 요소**라는 점을 확인할 수 있었다.

특히 Width와 Spacing Rule은 각각 독립적인 규칙처럼 보이지만 실제로는

* Lithography Resolution
* Etching Variation
* STI Isolation
* Overlay Error
* Device Geometry

등 여러 공정 조건을 동시에 고려한 결과라고 볼 수 있다.

또한 Bent Active에서 일반 Active보다 큰 Design Margin이 요구되는 것을 통해 **동일한 Layer에서도 Layout Geometry에 따라 제조 난이도가 달라질 수 있음**을 확인하였다.

결과적으로 Active Design Rule은 단순히 최소 치수를 제한하는 규칙이 아니라, 실제 Wafer에서 의도한 Transistor Geometry와 소자 간 Isolation을 안정적으로 구현하기 위한 규칙이라고 이해할 수 있었다.

---

# 2. Thick Active Area

## 2.1 Thick Active의 개념

GPDK090에서는 일반적인 저전압 소자와 더 높은 전압에서 동작하는 소자를 구분하며, **Thick Active Area는 Thick-Oxide Device 영역과 관련된 Layout 영역**이다.

높은 전압이 Gate Oxide에 인가되면 Oxide 내부의 Electric Field가 증가한다.

이를 단순화하면

$$
E_{ox} \approx \frac{V_{ox}}{t_{ox}}
$$

로 나타낼 수 있다.

따라서 동일한 Oxide Thickness에서

$$
V_{ox}\uparrow
\Rightarrow
E_{ox}\uparrow
$$

가 된다.

반대로 Gate Oxide Thickness를 증가시키면

$$
t_{ox}\uparrow
\Rightarrow
E_{ox}\downarrow
$$

가 되어 높은 동작 전압에서도 Gate Oxide의 전기적 Stress를 감소시킬 수 있다.

따라서 GPDK090에서는 일반적인 저전압 소자와 Thick-Oxide 소자를 구분하고, 관련 영역에 별도의 Design Rule을 적용한다.

---

## 2.2 Thick Active Width 및 Spacing

Thick Active에도 일반 Active와 마찬가지로 Minimum Width와 Minimum Spacing Rule이 존재한다.

Thick Active 영역이 지나치게 작으면 별도의 공정 영역을 안정적으로 정의하기 어려우며, Mask Alignment 및 Pattern Variation의 영향을 크게 받을 수 있다.

또한 인접한 Thick Active 영역 사이에는 충분한 간격을 확보하여 서로 다른 소자 영역이 공정 편차에 의해 간섭하지 않도록 해야 한다.

특히 45°로 꺾이는 영역에서는 일반적인 직선 영역보다 더 큰 Spacing이 요구되는데, 이는 Active Rule과 마찬가지로 Corner Rounding 및 Lithography Pattern Distortion에 대한 Margin을 확보하기 위한 것으로 이해할 수 있다.

---

## 2.3 Thick Active Enclosure

Thick Active와 관련된 중요한 Rule 중 하나는 **Enclosure Rule**이다.

Thick Active 영역은 관련 Active Area 및 Gate 주변을 충분히 감싸도록 설계되어야 한다.

이는 Layout에서 두 Layer가 정확히 정렬되어 있더라도 실제 제조 과정에서는 Mask Overlay Error가 존재하기 때문이다.

따라서

$$
\text{Required Region}
+
\text{Overlay Margin}
=
\text{Enclosure}
$$

의 개념으로 이해할 수 있다.

충분한 Enclosure를 확보하면 공정 중 Mask Misalignment가 발생하더라도 고전압 소자의 중요한 영역이 의도한 Thick-Oxide Device 영역 안에 유지될 가능성을 높일 수 있다.

---

## 2.4 일반 Active와 Thick Active의 차이

| 구분         | Active                               | Thick Active                              |
| ---------- | ------------------------------------ | ----------------------------------------- |
| 주요 용도      | 일반 MOSFET 형성 영역                      | Thick-Oxide Device 관련 영역                  |
| 주요 소자      | 저전압/Core Device                      | 상대적으로 높은 전압의 Device                       |
| Gate Oxide | 상대적으로 얇음                             | 상대적으로 두꺼움                                 |
| 주요 목적      | Source/Drain, Channel 및 Isolation 정의 | 고전압 소자 영역의 안정적인 정의                        |
| 주요 Rule    | Width, Spacing, Bent Geometry        | Width, Spacing, Enclosure, Gate 관련 Margin |
| 중요 요소      | STI Isolation 및 Pattern Fidelity     | Overlay Margin 및 Oxide Reliability        |

---

## 2.5 Thick Active Rule에 대한 고찰

Thick Active Rule을 통해 **동일한 CMOS 공정에서도 모든 MOSFET이 동일한 구조와 Design Rule을 사용하는 것은 아니라는 점**을 확인할 수 있었다.

동작 전압이 증가하면 Gate Oxide에 인가되는 Electric Field와 소자의 신뢰성 조건이 달라지므로, 이를 고려하여 별도의 Device Region과 더 큰 Process Margin이 필요하다.

특히 일반 Active보다 큰 Width, Spacing 및 Enclosure가 요구되는 것은 단순히 고전압 소자의 크기를 크게 만들기 위한 것이 아니라, **공정 오차가 존재하더라도 Thick-Oxide Device의 핵심 영역이 의도한 공정 조건 안에 안정적으로 형성되도록 하기 위한 것**으로 이해할 수 있었다.

이를 통해 Design Rule의 수치는 단순한 Layout 제약이 아니라 **동작 전압, 공정 오차 및 소자 신뢰성을 함께 고려하여 결정된 값**이라는 점을 확인할 수 있었다.

---

# 3. Silicide & Non-Silicide

## 3.1 Silicide의 개념

**Silicide**는 금속과 Silicon을 반응시켜 형성하는 저저항의 Metal-Silicon Compound이다.

MOSFET에서는 일반적으로 Poly Gate 및 Source/Drain 표면에 Silicide를 형성하여 해당 영역의 **Sheet Resistance 및 Contact Resistance를 감소**시키는 데 사용한다.

개념적으로

$$
\text{Metal}+\text{Si}
\rightarrow
\text{Metal Silicide}
$$

로 나타낼 수 있다.

Silicide가 형성되면 저저항 전류 경로가 추가되므로

$$
R_{sh,\;silicided}
<
R_{sh,\;non-silicided}
$$

의 관계를 가진다.

따라서 일반적인 MOSFET에서는 Silicide를 통해 저항 성분을 감소시키고 소자의 성능을 향상시킬 수 있다.

---

## 3.2 Non-Silicide의 개념

**Non-Silicide 또는 Unsilicided Structure**는 특정 Poly 또는 Diffusion 영역에 Silicide가 형성되지 않도록 의도적으로 차단한 구조이다.

일반적인 MOSFET에서는 낮은 저항이 유리하지만, **저항 소자를 구현하는 경우에는 높은 Sheet Resistance가 필요할 수 있다.**

저항값은

$$
R=R_{sh}\frac{L}{W}
$$

로 표현할 수 있다.

따라서

$$
R_{sh}\uparrow
\Rightarrow
R\uparrow
$$

이며, 높은 Sheet Resistance를 이용하면 동일한 목표 저항을 더 작은 \(L/W\)로 구현할 수 있다.

이러한 이유로 저항 소자의 Body 영역에서는 Silicide 형성을 차단하여 Poly 또는 Diffusion 자체의 높은 Sheet Resistance를 이용할 수 있다.

---

## 3.3 Silicide Block

Non-Silicide Resistor를 구현하기 위해서는 저항으로 사용할 영역에서 Silicide 형성을 막아야 한다.

이를 위해 **Silicide Block과 관련된 공정 영역**을 사용한다.

결과적으로 저항 Body에서는

$$
\text{Silicide Block}
\rightarrow
\text{Silicide Formation Suppression}
\rightarrow
R_{sh}\uparrow
$$

의 관계를 얻을 수 있다.

반면 저항의 Terminal 부분에서는 낮은 Contact Resistance가 중요하기 때문에, 실제 구조에서는 Resistor Body와 Terminal의 역할을 구분하여 설계할 수 있다.

즉,

$$
\boxed{
\text{Resistor Body}
\rightarrow
\text{High Resistance}
}
$$

$$
\boxed{
\text{Terminal}
\rightarrow
\text{Low Contact Resistance}
}
$$

라는 서로 다른 요구조건을 동시에 만족시키는 것이 중요하다.

---

## 3.4 Silicide와 Non-Silicide 비교

| 구분                        | Silicide      | Non-Silicide |
| ------------------------- | ------------- | ------------ |
| Silicide Layer            | 형성            | 형성 억제        |
| Sheet Resistance          | 낮음            | 높음           |
| Contact/Series Resistance | 상대적으로 낮음      | 상대적으로 높음     |
| 주요 목적                     | 저항 감소 및 성능 향상 | 의도적인 고저항 구현  |
| 일반 MOSFET                 | 유리            | 일반적으로 불리     |
| Resistor                  | 낮은 저항 구현      | 높은 저항 구현에 유리 |

---

## 3.5 Silicide / Non-Silicide에 대한 고찰

Silicide와 Non-Silicide 구조를 비교하면서 **Layout 설계에서는 항상 저항을 최소화하는 것이 최선은 아니라는 점**을 확인할 수 있었다.

MOSFET의 Source/Drain이나 Gate에서는 Parasitic Resistance를 줄이는 것이 성능 향상에 유리하므로 Silicide가 필요하다.

반면 저항 소자에서는 저항 자체가 회로의 기능이므로, 동일한 Silicide 형성은 오히려 원하는 저항값을 구현하는 데 방해가 될 수 있다.

따라서 동일한 공정에서도

$$
\text{MOSFET}
\Rightarrow
\text{Resistance 최소화}
$$

와

$$
\text{Resistor}
\Rightarrow
\text{필요한 Resistance 확보}
$$

라는 서로 다른 설계 목표가 존재한다.

특히

$$
R=R_{sh}\frac{L}{W}
$$

관계를 통해 Non-Silicide 구조로 \(R_{sh}\)를 증가시키면 같은 목표 저항을 더 작은 \(L/W\)로 구현할 수 있음을 이해하였다.

이는 **공정 Layer를 선택하는 것 자체가 회로의 전기적 특성을 결정하는 중요한 Layout Design 요소**라는 것을 보여준다.

---

# 4. Overall Discussion

Active, Thick Active, Silicide 및 Non-Silicide 구조를 학습하고 Cadence Virtuoso에서 관련 Layout을 직접 구성하면서, Design Rule을 단순한 최소 Width나 Spacing 값으로 이해하는 것보다 **각 Layer가 실제 공정에서 어떤 구조를 정의하고 어떤 전기적 특성에 영향을 주는지 이해하는 것이 중요함**을 확인하였다.

Active Rule을 통해서는 **MOSFET이 형성되는 실리콘 영역과 STI Isolation의 관계**를 이해하였고, Thick Active Rule을 통해서는 **소자의 동작 전압과 Gate Oxide Reliability가 Layout Rule에 영향을 미칠 수 있음**을 확인하였다.

또한 Silicide와 Non-Silicide 구조를 통해서는 동일한 Poly 또는 Diffusion 영역도 후속 공정의 차이에 따라 Sheet Resistance가 크게 달라질 수 있으며, 이러한 특성을 이용하여 MOSFET의 Parasitic Resistance를 줄이거나 반대로 의도적인 저항 소자를 구현할 수 있음을 학습하였다.

결과적으로 다음과 같은 흐름으로 Design Rule을 이해할 수 있었다.

$$
\boxed{
\text{Device Requirement}
\rightarrow
\text{Process Structure}
\rightarrow
\text{Layout Layer}
\rightarrow
\text{Design Rule}
\rightarrow
\text{DRC Verification}
}
$$

Cadence Virtuoso에서 각 Rule에 맞는 Layout과 의도적으로 Rule을 위반한 Layout을 구성하고 DRC 결과를 비교함으로써, **Design Rule이 단순한 EDA Tool의 제한 조건이 아니라 실제 Wafer에서 설계한 구조를 안정적으로 제조하기 위한 공정·소자적 제약 조건**이라는 점을 이해할 수 있었다.

