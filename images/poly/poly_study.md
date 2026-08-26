# POLY Design Rule Study

## 1. POLY의 개념

**POLY(Polysilicon)**는 다결정 Silicon으로 이루어진 Layer로, CMOS 공정에서는 주로 **MOSFET의 Gate를 형성하는 핵심 재료**로 사용된다.

Layout에서 POLY가 Active Area를 가로지르면 두 Layer가 교차하는 영역에 MOSFET의 **Gate와 Channel**이 형성된다.

따라서 POLY의 Geometry는 MOSFET의 Channel Length와 Gate 구조를 직접 결정하며 소자의 전기적 특성에 큰 영향을 준다.

또한 POLY는 공정에서 허용하는 경우 Interconnect 또는 Poly Resistor로도 활용될 수 있다.

---

## 2. POLY와 MOSFET Gate

MOSFET에서 POLY Gate는 Gate Oxide 위에 형성되며, Gate Voltage를 통해 Channel의 형성과 전도 상태를 제어한다.

MOSFET의 Drain Current는 기본적으로 Gate Width $W$와 Gate Length $L$의 영향을 받는다.

```math
I_D \propto \frac{W}{L}
```

따라서 POLY Gate의 Geometry는 단순한 Layout 형상이 아니라 MOSFET의 전기적 특성을 결정하는 중요한 요소이다.

---

## 3. Minimum Gate Length

GPDK090에서는 소자의 종류와 동작 전압에 따라 Minimum Gate Length가 제한된다.

Gate Length가 지나치게 짧아지면 다음과 같은 Short Channel Effect가 증가할 수 있다.

- Threshold Voltage Roll-off
- DIBL
- Subthreshold Leakage 증가
- Punch-through 위험 증가

따라서 Minimum Gate Length Rule은 **안정적인 Gate Pattern 형성과 MOSFET Channel 특성을 확보하기 위한 규칙**이다.

---

## 4. Minimum POLY Interconnect Width

POLY를 Interconnect로 사용하는 경우에도 Minimum Width가 요구된다.

POLY의 저항은 Sheet Resistance를 이용하여 다음과 같이 표현할 수 있다.

```math
R = R_{sh}\frac{L}{W}
```

따라서 동일한 길이에서 POLY Width가 감소하면 다음과 같다.

```math
W \downarrow
\rightarrow
R \uparrow
```

POLY가 지나치게 좁으면 Patterning 안정성이 감소하고 저항이 증가할 수 있으므로 Minimum Width를 제한한다.

---

## 5. POLY Spacing

인접한 POLY Pattern 사이에는 Minimum Spacing이 요구된다.

POLY 사이의 거리가 지나치게 작으면 Lithography 및 Etching 과정에서 Pattern을 안정적으로 분리하기 어려워질 수 있다.

GPDK090에서는 용도에 따라 다음과 같은 Spacing Rule이 존재할 수 있다.

- Gate-to-Gate Spacing
- POLY Interconnect Spacing
- POLY Resistor Spacing

따라서 동일한 POLY Layer라도 사용 목적에 따라 서로 다른 Process Margin이 요구될 수 있다.

---

## 6. POLY Gate Extension

POLY Gate는 Active Area를 가로지른 후 Active Edge를 넘어 일정 거리 이상 연장되어야 한다.

이는 POLY와 Active Mask 사이에서 발생할 수 있는 Overlay Error를 고려하기 위한 것이다.

```math
\text{Gate Extension}
\rightarrow
\text{Overlay Margin}
```

충분한 Gate Extension을 확보하면 Mask Misalignment가 발생하더라도 POLY가 Active Area를 완전히 가로지를 수 있다.

---

## 7. Active Area to Gate Enclosure

Gate 주변에는 Source/Drain으로 사용될 충분한 Active Area가 필요하다.

Gate 바로 옆에서 Active가 끝나면 Source/Drain 형성 및 Contact 배치를 위한 영역이 부족해질 수 있다.

따라서 Gate를 기준으로 Source/Drain 방향에 충분한 Active Area를 확보하여 안정적인 Device Geometry를 형성한다.

---

## 8. POLY와 Active Area의 Spacing

Gate가 아닌 POLY Interconnect가 Active Area 주변을 지나가는 경우에도 Minimum Spacing이 요구된다.

POLY와 Active가 지나치게 가까우면 Overlay 및 Patterning Variation에 의해 의도하지 않은 Geometry가 형성될 수 있다.

따라서 POLY-to-Active Spacing Rule을 통해 충분한 Process Margin을 확보한다.

---

## 9. Bent POLY

Bent POLY에서는 Corner Rounding과 Pattern Distortion의 영향이 증가할 수 있다.

따라서 일반적인 직선 POLY보다 더 큰 Width 또는 Spacing이 요구될 수 있다.

```math
\text{Bent Geometry}
\rightarrow
\text{Pattern Distortion}
\rightarrow
\text{Larger Process Margin}
```

---

## 10. Bent Gate와 Bent POLY Resistor

Bent Gate는 위치에 따라 유효 Channel Length가 달라질 수 있고 Corner 부분에서 전기장 및 Patterning 특성이 불균일해질 수 있으므로 허용되지 않는다.

POLY Resistor 역시 Bent Geometry를 사용하면 Corner에서 Current Path가 복잡해져 단순한 저항 관계에서 벗어날 수 있다.

```math
R = R_{sh}\frac{L}{W}
```

따라서 직선 Geometry를 이용하여 Gate와 Resistor의 특성을 보다 균일하게 유지한다.

---

## 11. POLY Density

POLY Density는 전체 Chip Area에서 POLY가 차지하는 면적의 비율을 의미한다.

**POLY Density Ratio**

```math
\text{POLY Density Ratio}
=
\frac{A_{\text{POLY}}}{A_{\text{Total}}}
\times 100
```

GPDK090에서는 전체 Chip에서 POLY가 차지하는 Density Ratio의 최대값을 제한한다.

Density가 지나치게 높거나 불균일하면 Pattern Density에 따른 공정 편차가 증가할 수 있으므로 Density Rule을 통해 Chip-level Process Uniformity를 확보한다.

---

## 12. Maximum Narrow POLY Segment Length

폭이 좁은 POLY Interconnect가 지나치게 길게 형성되는 것도 제한된다.

```math
R = R_{sh}\frac{L}{W}
```

따라서 다음과 같은 관계를 가진다.

```math
L \uparrow,\quad W \downarrow
\rightarrow
R \uparrow
```

좁고 긴 POLY는 Series Resistance와 RC Delay를 증가시킬 수 있으므로 Contact 사이의 최대 Segment Length를 제한한다.

---

## 13. Minimum POLY Area

Minimum Width를 만족하더라도 전체 POLY 면적이 지나치게 작으면 Lithography 및 Etching 과정에서 Pattern이 안정적으로 형성되지 않을 수 있다.

```math
A_{\text{POLY}} \geq A_{\text{min}}
```

따라서 Minimum Area Rule은 Width Rule만으로 검출하기 어려운 작은 POLY Pattern을 제한하기 위한 보완적인 규칙이다.

---

## 14. POLY Design Rule에 대한 고찰

POLY Design Rule을 학습하면서 POLY는 단순한 Layout Layer가 아니라 **MOSFET의 전기적 특성과 공정 정확도를 직접 연결하는 Layer**라는 점을 이해할 수 있었다.

특히 Gate Length는 MOSFET의 Channel Length를 직접 결정한다.

```math
L
\rightarrow
\text{Channel Electrostatics}
\rightarrow
I_D,\;V_{TH},\;\text{Leakage}
```

반면 Gate Extension과 POLY-to-Active Spacing은 Overlay Error에 대한 Process Margin이라는 의미가 강하다.

또한 POLY Interconnect와 POLY Resistor에서는 다음 관계가 중요하다.

```math
R = R_{sh}\frac{L}{W}
```

이를 통해 동일한 POLY Layer라도 Gate, Interconnect, Resistor라는 용도에 따라 서로 다른 Design Rule이 필요한 이유를 이해할 수 있었다.

---

## 15. Overall Summary

POLY Design Rule은 **Device Geometry, Patterning, Overlay, Resistance 및 Process Uniformity**를 확보하기 위한 규칙이다.

```math
\text{POLY Layout}
\rightarrow
\text{Gate / Interconnect / Resistor}
\rightarrow
\text{Fabrication Accuracy}
\rightarrow
\text{Electrical Characteristics}
```

Cadence Virtuoso에서 POLY 관련 Layout을 직접 구성하고 DRC를 수행함으로써, **POLY Design Rule이 실제 Lithography, Etching, Overlay 및 MOSFET의 전기적 특성과 연결되어 있음을 이해할 수 있었다.**
