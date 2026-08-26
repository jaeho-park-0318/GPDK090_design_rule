# SIPROT Design Rule Study

## 1. SIPROT의 개념

**SIPROT은 특정 Active Area 또는 Poly 영역에서 Silicide 형성을 차단하기 위해 사용하는 Silicide Protection Layer**이다.

일반적인 CMOS 공정에서는 Source/Drain 및 Poly 표면에 Silicide를 형성하여 저항을 감소시킨다.

Silicide는 Silicon 또는 Polysilicon보다 낮은 저항을 가지므로 Source/Drain의 Series Resistance와 Poly의 Sheet Resistance를 감소시키는 역할을 한다.

반대로 **SIPROT이 적용된 영역에서는 Silicide 형성이 차단되어 Non-Silicide 영역이 형성된다.**

따라서 다음과 같이 구분할 수 있다.

```text
SIPROT 없음 → Silicide 형성 → Low Resistance

SIPROT 있음 → Silicide Block → Non-Silicide → High Resistance
```

SIPROT은 특히 **Non-Silicide Poly Resistor**와 같이 의도적으로 높은 저항이 필요한 구조를 구현할 때 중요한 역할을 한다.

---

## 2. Silicide와 Non-Silicide

Silicide는 Metal과 Silicon을 반응시켜 형성하는 저저항 물질이다.

Silicide가 형성된 Poly에서는 Sheet Resistance가 감소하므로 동일한 Geometry에서도 전체 저항이 감소한다.

Poly Resistor의 저항은 다음과 같이 표현할 수 있다.

```math
R = R_{sh}\frac{L}{W}
```

여기서

- $R_{sh}$ : Sheet Resistance
- $L$ : Resistor Length
- $W$ : Resistor Width

이다.

Silicide가 형성되면 Sheet Resistance가 감소한다.

```math
R_{sh}\downarrow
\rightarrow
R\downarrow
```

반대로 SIPROT을 이용하여 Silicide 형성을 차단하면 높은 Sheet Resistance를 갖는 Non-Silicide 영역을 형성할 수 있다.

```math
\text{SIPROT}
\rightarrow
\text{Silicide Block}
\rightarrow
R_{sh}\uparrow
\rightarrow
R\uparrow
```

따라서 SIPROT은 **Silicide가 형성될 영역과 형성되지 않을 영역을 선택적으로 구분하여 저항 특성을 제어하기 위한 Layer**로 이해할 수 있다.

---

## 3. SIPROT.W.1 — Minimum Silicide Block Width

**SIPROT.W.1은 Silicide Block의 최소 폭을 0.44 um 이상 확보하도록 하는 규칙이다.**

```text
Minimum Silicide Block Width = 0.44 um
```

SIPROT Width가 지나치게 좁으면 Lithography 및 Patterning 과정에서 Silicide Block Pattern을 안정적으로 형성하기 어렵다.

특히 Silicide를 차단해야 하는 영역이 너무 좁으면 Process Variation에 의해 일부 영역에서 의도하지 않은 Silicide가 형성될 가능성이 증가한다.

따라서 **안정적인 Non-Silicide 영역을 형성할 수 있도록 최소 SIPROT Width를 확보하는 규칙**이다.

---

## 4. SIPROT.SP.1 — Minimum Silicide Block Spacing

**SIPROT.SP.1은 서로 다른 Silicide Block 사이의 최소 간격을 0.44 um 이상 확보하도록 하는 규칙이다.**

```text
Minimum Silicide Block Spacing = 0.44 um
```

두 SIPROT Pattern이 지나치게 가까우면 Lithography 및 Patterning 과정에서 두 Block 영역을 독립적으로 구분하기 어려워질 수 있다.

따라서 충분한 Spacing을 확보하여 **Silicide 영역과 Non-Silicide 영역의 경계를 안정적으로 형성하기 위한 규칙**이다.

---

## 5. SIPROT.SE.1 — Silicide Block to Contact Spacing

**SIPROT.SE.1은 SIPROT과 Contact 사이에 최소 0.24 um의 간격을 확보하도록 하는 규칙이다.**

```text
SIPROT to Contact Spacing = 0.24 um
```

Contact 주변은 Device와 Metal 사이에 안정적인 전기적 연결을 형성해야 하는 영역이다.

SIPROT이 Contact에 지나치게 가까워지면 Contact 주변의 Silicide 형성 영역에 영향을 줄 수 있으므로 충분한 간격을 확보한다.

따라서 이 Rule은 **Contact 주변의 안정적인 저저항 연결 영역을 확보하기 위한 규칙**으로 이해할 수 있다.

---

## 6. SIPROT.SE.2 — SIPROT to Unrelated Active Area Spacing

**SIPROT.SE.2는 SIPROT과 관계없는 Active Area 사이에 최소 0.24 um의 간격을 확보하도록 하는 규칙이다.**

```text
SIPROT to Unrelated Active Area Spacing = 0.24 um
```

SIPROT이 Silicide를 차단하려는 대상이 아닌 Active Area에 지나치게 가까우면 Process Variation 또는 Overlay Error에 의해 해당 Active의 Silicide 형성에 영향을 줄 가능성이 있다.

따라서 **의도하지 않은 Active Area에서 Silicide가 차단되는 것을 방지하기 위한 Spacing Rule**이다.

---

## 7. SIPROT.SE.3 — SIPROT to Gate Spacing

**SIPROT.SE.3은 SIPROT과 Gate 사이에 최소 0.44 um의 간격을 확보하도록 하는 규칙이다.**

```text
SIPROT to Gate Spacing = 0.44 um
```

일반적인 MOSFET에서는 Gate Poly 표면에도 Silicide를 형성하여 Gate Resistance를 감소시킬 수 있다.

SIPROT이 의도하지 않은 Gate에 지나치게 가까워지면 Process Variation에 의해 Gate의 Silicide 형성에 영향을 줄 수 있다.

따라서 **일반 Gate의 정상적인 Silicide 형성과 저저항 특성을 보호하기 위해 충분한 Spacing을 확보하는 규칙**이다.

---

## 8. SIPROT.E.1 — SIPROT to Active Area Enclosure

**SIPROT.E.1은 Silicide를 차단하려는 Active Area를 SIPROT이 최소 0.25 um 이상 감싸도록 하는 규칙이다.**

```text
SIPROT to Active Area Enclosure = 0.25 um
```

SIPROT과 Active Area는 서로 다른 Mask로 정의되므로 실제 제조 과정에서 Overlay Error가 발생할 수 있다.

SIPROT과 Active의 Edge를 정확히 일치시키면 Mask Misalignment에 의해 Active 일부가 SIPROT 밖으로 노출될 수 있다.

이 경우 노출된 Active 영역에 의도하지 않은 Silicide가 형성될 수 있다.

따라서 SIPROT을 Active보다 충분히 크게 형성하여 Process Margin을 확보한다.

```math
\text{SIPROT Enclosure}
=
\text{Required Block Region}
+
\text{Overlay Margin}
```

즉, **Active Area 전체에서 안정적으로 Silicide 형성을 차단하기 위한 Enclosure Rule**이다.

---

## 9. SIPROT.E.2 — Active Area to SIPROT Enclosure

**SIPROT.E.2는 Active Area가 SIPROT을 최소 0.24 um 이상 감싸도록 하는 규칙이다.**

```text
Active Area to SIPROT Enclosure = 0.24 um
```

SIPROT.E.1과 SIPROT.E.2는 Enclosure 방향이 서로 다르다는 점이 중요하다.

```text
SIPROT.E.1

SIPROT
└── Active Area를 Enclose


SIPROT.E.2

Active Area
└── SIPROT을 Enclose
```

따라서 Layout에서 Enclosure Rule을 확인할 때는 **어느 Layer가 바깥쪽에 위치하고 어느 Layer가 안쪽에 위치하는지**를 정확하게 확인해야 한다.

---

## 10. SIPROT.E.3 — SIPROT to Poly Enclosure

**SIPROT.E.3은 Field 영역에 위치한 Poly를 SIPROT이 최소 0.28 um 이상 감싸도록 하는 규칙이다.**

```text
SIPROT to Poly (on field) Enclosure = 0.28 um
```

이 Rule은 특히 **Non-Silicide Poly Resistor**를 구현할 때 중요하다.

Poly의 특정 구간에서 Silicide 형성을 차단하면 해당 영역의 Sheet Resistance를 증가시킬 수 있다.

```math
\text{SIPROT}
\rightarrow
\text{Non-Silicide Poly}
\rightarrow
R_{sh}\uparrow
```

SIPROT이 Poly를 충분히 Enclose하지 않으면 Overlay Error에 의해 Poly 일부에 Silicide가 형성될 수 있다.

이 경우 Resistor의 실제 저항값이 설계값과 달라질 수 있다.

따라서 SIPROT.E.3은 **Non-Silicide Poly 영역을 안정적으로 형성하여 원하는 저항값을 확보하기 위한 Process Margin**이다.

---

## 11. SIPROT.EA.1 — Minimum Enclosed Area

**SIPROT.EA.1은 SIPROT으로 둘러싸인 Donut 형태의 Hole에 대해 최소 Enclosed Area를 1.2 um² 이상 확보하도록 하는 규칙이다.**

```text
Minimum SIPROT Enclosed Area = 1.2 um²
```

SIPROT이 Ring 또는 Donut 형태로 형성되면 내부에 Hole이 만들어질 수 있다.

이 Hole이 지나치게 작으면 Lithography 및 Patterning 과정에서 설계된 형태를 안정적으로 구현하기 어려울 수 있다.

따라서 Minimum Enclosed Area를 제한하여 지나치게 작은 Hole Pattern이 형성되는 것을 방지한다.

이는 Minimum Width만으로 검출하기 어려운 **2차원 Geometry의 제조 가능성을 보완하는 Area Rule**이라고 이해할 수 있다.

---

## 12. SIPROT Layout 구현 방법

Cadence Virtuoso에서는 각각의 SIPROT Design Rule을 독립적인 Test Pattern으로 구성하여 DRC를 통해 확인할 수 있다.

### SIPROT Width

SIPROT Rectangle을 생성하고 최소 폭을 0.44 um로 설정한다.

```text
     0.44 um
   ←────────→

   ┌────────┐
   │ SIPROT │
   └────────┘
```

### SIPROT Spacing

두 SIPROT Pattern 사이의 Edge-to-Edge 거리를 0.44 um로 설정한다.

```text
┌────────┐       0.44 um       ┌────────┐
│ SIPROT │ ←─────────────────→ │ SIPROT │
└────────┘                      └────────┘
```

### SIPROT to Contact Spacing

Contact와 SIPROT 사이의 Edge-to-Edge 거리를 0.24 um로 설정한다.

```text
Contact  ←──── 0.24 um ────→  SIPROT
```

### SIPROT to Active Enclosure

Active Area를 SIPROT 내부에 배치하고 Active Edge에서 SIPROT Edge까지 필요한 Enclosure를 확보한다.

```text
┌────────────── SIPROT ──────────────┐
│                                    │
│    ← 0.25 → ┌──── Active ────┐     │
│             │                │     │
│             └────────────────┘     │
│                                    │
└────────────────────────────────────┘
```

### SIPROT to Poly Enclosure

Non-Silicide로 만들 Poly 영역을 SIPROT 내부에 배치하고 SIPROT이 Poly를 최소 0.28 um 이상 Enclose하도록 구성한다.

```text
┌──────────────── SIPROT ────────────────┐
│                                        │
│       ← 0.28 → ┌──── POLY ────┐        │
│                │              │        │
│                └──────────────┘        │
│                                        │
└────────────────────────────────────────┘
```

이와 같이 각 Rule의 기준값과 위반값을 각각 구성하고 DRC 결과를 비교하면 Design Rule의 의미를 보다 명확하게 확인할 수 있다.

---

## 13. SIPROT Design Rule에 대한 고찰

SIPROT Design Rule을 학습하면서 Silicide는 단순히 공정 과정에서 자동으로 형성되는 구조가 아니라 **Layout을 통해 특정 영역에서 선택적으로 형성을 차단할 수 있는 요소**라는 점을 이해할 수 있었다.

일반적인 MOSFET에서는 Silicide를 이용하여 Gate와 Source/Drain의 저항을 낮추는 것이 유리하다.

```math
R_{\text{series}}\downarrow
\rightarrow
\text{Voltage Drop}\downarrow
```

반면 Poly Resistor와 같이 높은 저항이 필요한 구조에서는 Silicide가 형성되면 Sheet Resistance가 감소하여 원하는 저항값을 얻기 어려울 수 있다.

따라서 SIPROT을 이용하여 특정 Poly 또는 Active 영역에서 Silicide 형성을 차단한다.

```math
\text{SIPROT}
\rightarrow
\text{Silicide Block}
\rightarrow
\text{Non-Silicide Region}
\rightarrow
R_{sh}\uparrow
```

특히 SIPROT의 Width와 Spacing Rule을 통해 SIPROT 자체도 Lithography 및 Patterning의 영향을 받는 하나의 Mask Pattern이라는 점을 확인할 수 있었다.

또한 Active 및 Poly에 대한 Enclosure Rule을 통해 SIPROT Mask와 다른 Layer 사이에서 발생할 수 있는 Overlay Error를 고려해야 한다는 점을 이해할 수 있었다.

Contact 및 Gate 주변에 별도의 Spacing Rule이 존재한다는 점에서는 **SIPROT의 배치가 단순히 Non-Silicide 영역의 형성뿐만 아니라 주변 소자의 저저항 Contact 및 Gate 특성을 유지하는 것과도 관련되어 있음**을 확인할 수 있었다.

---

## 14. Overall Summary

SIPROT은 **특정 Active Area 또는 Poly 영역에서 Silicide 형성을 차단하여 Non-Silicide 영역을 정의하기 위한 Layer**이다.

전체적인 관계는 다음과 같이 정리할 수 있다.

```math
\text{SIPROT Layout}
\rightarrow
\text{Silicide Blocking}
\rightarrow
\text{Non-Silicide Region}
\rightarrow
R_{sh}\uparrow
\rightarrow
R\uparrow
```

Cadence Virtuoso에서 SIPROT의 Width, Spacing, Contact/Gate Spacing, Active/Poly Enclosure 및 Enclosed Area Rule을 직접 Layout으로 구성하고 DRC를 수행함으로써, **SIPROT Design Rule이 Silicide와 Non-Silicide 영역을 정확하게 구분하고 원하는 전기적 특성을 안정적으로 구현하기 위한 공정 규칙이라는 점을 이해할 수 있었다.**
