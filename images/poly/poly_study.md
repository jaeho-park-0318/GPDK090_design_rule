# POLY Design Rule Study

## 1. POLY의 개념

**POLY(Polysilicon)**는 다결정 실리콘으로 이루어진 Layer로, CMOS 공정에서는 주로 **MOSFET의 Gate를 형성하는 핵심 재료**로 사용된다.

Layout에서 POLY가 Active Area를 가로지르면 두 Layer가 교차하는 영역에 MOSFET의 **Gate와 Channel**이 형성된다. 따라서 POLY의 Geometry는 MOSFET의 Channel Length와 Gate 구조를 직접 결정하며 소자의 전기적 특성에 큰 영향을 준다.

또한 POLY는 Gate뿐만 아니라 공정에서 허용하는 경우 **Interconnect 또는 Poly Resistor**로도 활용될 수 있다.

---

## 2. POLY와 MOSFET Gate

MOSFET에서 POLY Gate는 Gate Oxide 위에 형성되며, Gate Voltage를 통해 Channel의 형성과 전도 상태를 제어한다.

MOSFET의 Drain Current는 기본적으로 Gate Width $W$와 Gate Length $L$의 영향을 받으며 다음과 같은 관계를 가진다.

$$
I_D \propto \frac{W}{L}
$$

따라서 POLY Gate의 Geometry는 단순한 Layout 형상이 아니라 실제 MOSFET의 전기적 특성을 결정하는 중요한 요소이다.

특히 Gate Length가 지나치게 감소하면 Short Channel Effect의 영향이 증가할 수 있다.

---

## 3. Minimum Gate Length

GPDK090에서는 소자의 종류와 동작 전압에 따라 Minimum Gate Length가 제한된다.

Gate Length가 지나치게 짧아지면 Drain Electric Field가 Source 측 Channel Potential에 영향을 주면서 다음과 같은 문제가 발생할 수 있다.

- Threshold Voltage Roll-off
- DIBL (Drain Induced Barrier Lowering)
- Subthreshold Leakage 증가
- Punch-through 위험 증가

따라서 Minimum Gate Length Rule은 **Lithography를 통한 안정적인 Gate Pattern 형성과 MOSFET의 Channel 특성을 확보하기 위한 규칙**이다.

---

## 4. 고전압 Device와 Gate Length

높은 전압에서 동작하는 MOSFET에서는 Drain 부근에 더 큰 Electric Field가 형성될 수 있다.

높은 Electric Field에 의해 Carrier가 큰 에너지를 얻으면 **Hot Carrier Injection(HCI)**이 발생할 수 있다.

고에너지 Carrier가 Gate Oxide 또는 Si-SiO₂ Interface의 Trap에 포획되면 장기적으로 다음과 같은 열화가 발생할 수 있다.

- Interface Trap 증가
- Threshold Voltage 변화
- Transconductance 감소
- Gate Oxide Reliability 저하

따라서 고전압 Device에서는 적절한 Gate Length와 Device Geometry를 확보하여 높은 Electric Field에 대한 신뢰성을 확보하는 것이 중요하다.

---

## 5. Minimum POLY Interconnect Width

POLY를 Interconnect로 사용할 경우에도 Minimum Width Rule이 적용된다.

POLY Interconnect가 지나치게 좁으면 Lithography 및 Etching 과정에서 Pattern 형성이 불안정해질 수 있으며 전기적인 저항도 증가한다.

POLY의 저항은 Sheet Resistance를 이용하여 다음과 같이 표현할 수 있다.

$$
R = R_{sh}\frac{L}{W}
$$

따라서 동일한 길이에서 Width가 감소하면

$$
W \downarrow
\Rightarrow
R \uparrow
$$

가 된다.

Minimum POLY Width Rule은 **POLY Pattern의 제조 안정성을 확보하고 지나친 저항 증가를 방지하기 위한 규칙**이다.

---

## 6. POLY Spacing

인접한 POLY Pattern 사이에는 Minimum Spacing Rule이 적용된다.

POLY 사이의 거리가 지나치게 작으면 Lithography 및 Etching 과정에서 두 Pattern이 제대로 분리되지 않거나 Process Variation에 의해 의도하지 않은 연결이 발생할 가능성이 증가한다.

GPDK090에서는 POLY의 용도에 따라 다음과 같은 Spacing을 구분하여 고려할 수 있다.

- Gate-to-Gate Spacing
- POLY Interconnect-to-Interconnect Spacing
- POLY Resistor Spacing

같은 POLY Layer라도 **Gate, Interconnect, Resistor 등 사용 목적에 따라 요구되는 Process Margin이 다를 수 있다.**

---

## 7. POLY Gate Extension

MOSFET Layout에서는 POLY Gate가 Active Area의 경계에서 바로 끝나는 것이 아니라 Active Area를 넘어 일정 거리 이상 연장되어야 한다.

이를 **Gate Extension**이라고 한다.

Layout에서 POLY와 Active의 끝을 정확하게 맞추더라도 실제 제조에서는 두 Mask 사이에 Overlay Error가 발생할 수 있다.

따라서 Gate Extension을 확보하면 Mask Misalignment가 발생하더라도 Gate가 Active Area를 안정적으로 가로지를 수 있다.

$$
\text{Gate Extension}
\rightarrow
\text{Overlay Margin 확보}
$$

---

## 8. Active Area to Gate Enclosure

Gate 주변에서는 Source/Drain으로 사용될 Active Area가 충분히 확보되어야 한다.

Gate 바로 옆에서 Active가 끝나면 Source/Drain 영역을 안정적으로 형성하기 어렵고 Implant 및 Contact 배치를 위한 공간도 부족해질 수 있다.

따라서 Gate 주변에 충분한 Active Area를 확보하여 **Gate와 Source/Drain의 상대적인 Geometry를 안정적으로 유지**한다.

---

## 9. POLY와 Active Area의 Spacing

Gate로 사용되지 않는 POLY Interconnect가 Active Area 주변을 지나갈 때도 일정한 Spacing이 요구된다.

POLY와 Active 사이의 간격이 지나치게 작으면 Overlay 및 Patterning Variation으로 인해 의도하지 않은 Geometry가 형성될 가능성이 있다.

따라서 POLY-to-Active Spacing Rule은 **POLY Interconnect와 주변 Active Area 사이에 충분한 Process Margin을 확보하기 위한 규칙**이다.

---

## 10. Bent POLY

POLY가 45°와 같이 꺾이는 Bent Geometry에서는 일반적인 직선 POLY보다 더 큰 Width와 Spacing이 요구될 수 있다.

이는 Lithography 과정에서 Corner 부분에 **Corner Rounding 및 Pattern Distortion**이 발생할 수 있기 때문이다.

$$
\text{Bent Geometry}
\rightarrow
\text{Pattern Distortion 증가}
\rightarrow
\text{Larger Process Margin}
$$

따라서 Bent POLY에는 일반적인 직선 POLY보다 큰 Width 및 Spacing Rule이 적용될 수 있다.

---

## 11. Bent Gate와 Bent POLY Resistor

GPDK090에서는 Bent Gate와 Bent POLY Resistor를 허용하지 않는 Rule이 존재한다.

Gate가 꺾이면 위치에 따라 유효 Channel Length가 달라질 수 있으며 Corner 부분에서 Electric Field와 Lithography 특성이 불균일해질 수 있다.

POLY Resistor 역시 Corner가 존재하면 실제 Current Path와 유효 Width의 영향으로 단순한 저항식만으로 저항값을 정확하게 표현하기 어려워질 수 있다.

$$
R = R_{sh}\frac{L}{W}
$$

따라서 직선 Geometry를 이용하여 소자 및 저항 특성을 보다 균일하고 예측 가능하게 유지한다.

---

## 12. POLY Density

POLY Density는 전체 Chip Area에서 POLY가 차지하는 면적의 비율을 의미한다.

$$
\text{POLY Density Ratio}
=
\frac{A_{\text{POLY}}}{A_{\text{Total}}}
\times 100
$$

GPDK090에서는 전체 Chip에서 POLY가 차지하는 Density Ratio의 최대값을 제한한다.

Density가 지나치게 높거나 불균일하면 Pattern Density에 따른 공정 편차가 증가할 수 있으므로 Density Rule을 통해 Chip-level Process Uniformity를 확보한다.

---

## 13. Maximum Narrow POLY Segment Length

폭이 좁은 POLY Interconnect가 지나치게 길게 형성되는 것도 제한된다.

$$
R = R_{sh}\frac{L}{W}
$$

따라서

$$
L \uparrow,\quad W \downarrow
\Rightarrow
R \uparrow
$$

가 된다.

좁고 긴 POLY는 높은 Series Resistance와 RC Delay를 발생시킬 수 있으므로 Contact 사이의 최대 Segment Length를 제한하여 지나친 저항 증가를 방지한다.

---

## 14. Minimum POLY Area

POLY Pattern에는 Minimum Width뿐만 아니라 Minimum Area Rule도 적용된다.

Minimum Width를 만족하더라도 전체 면적이 지나치게 작은 POLY Pattern은 Lithography 및 Etching 과정에서 안정적으로 형성되지 않을 가능성이 있다.

$$
A_{\text{POLY}} \geq A_{\text{min}}
$$

따라서 Minimum Area Rule은 Width Rule만으로 검출하기 어려운 작은 2차원 Pattern을 제한하기 위한 보완적인 규칙이다.

---

## 15. POLY Design Rule에 대한 고찰

POLY Design Rule을 학습하면서 POLY는 단순한 Layout Layer가 아니라 **MOSFET의 전기적 특성과 공정 정확도를 직접 연결하는 중요한 Layer**라는 점을 확인할 수 있었다.

특히 Gate Length는 MOSFET의 Channel Length를 직접 결정하기 때문에 소자의 전기적 특성과 밀접한 관계를 가진다.

$$
L
\rightarrow
\text{Channel Electrostatics}
\rightarrow
I_D,\;V_{TH},\;\text{Leakage}
$$

반면 Gate Extension과 POLY-to-Active Spacing은 서로 다른 Mask 사이에서 발생할 수 있는 Overlay Error에 대한 Process Margin이라는 의미가 강하다.

Bent POLY와 Minimum Area Rule을 통해서는 단순히 Width와 Spacing만 만족한다고 모든 Geometry가 안정적으로 제조될 수 있는 것은 아니며, Corner와 작은 Pattern의 형태까지 고려해야 한다는 점을 확인하였다.

또한 POLY Interconnect와 POLY Resistor에서는 다음 관계가 중요하다.

$$
R = R_{sh}\frac{L}{W}
$$

이를 통해 동일한 POLY Layer라도 Gate, Interconnect, Resistor라는 용도에 따라 서로 다른 Design Rule이 필요한 이유를 이해할 수 있었다.

---

## 16. Overall Summary

POLY Design Rule은 크게 **Device Geometry, Patterning, Overlay, Resistance 및 Process Uniformity**를 확보하기 위한 규칙으로 정리할 수 있다.

$$
\text{POLY Layout}
\rightarrow
\text{Gate / Interconnect / Resistor}
\rightarrow
\text{Fabrication Accuracy}
\rightarrow
\text{Electrical Characteristics}
$$

Cadence Virtuoso에서 각 POLY Rule에 맞는 Layout과 Rule을 위반한 Layout을 구성하고 DRC를 수행함으로써, **POLY Design Rule의 수치가 단순한 Layout 제한이 아니라 실제 Lithography, Etching, Overlay 및 MOSFET의 전기적 특성을 고려한 제조 기준이라는 점을 이해할 수 있었다.**
