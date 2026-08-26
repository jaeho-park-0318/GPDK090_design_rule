# POLY Design Rule Study

## 1. POLY의 개념

**POLY(Polysilicon)**는 다결정 실리콘으로 이루어진 Layer로, CMOS 공정에서는 대표적으로 **MOSFET의 Gate를 형성하는 핵심 재료**로 사용된다.

Layout에서 POLY가 Active Area를 가로지르면 두 Layer가 교차하는 영역에 MOSFET의 **Gate와 Channel**이 형성된다. 따라서 POLY의 형상은 MOSFET의 Channel Length와 Gate 구조를 직접 결정하며, 소자의 전기적 특성에 큰 영향을 준다.

또한 POLY는 Gate뿐만 아니라 공정에서 허용하는 경우 **Local Interconnect 또는 Poly Resistor**로도 활용될 수 있다.

---

## 2. POLY와 MOSFET Gate

MOSFET에서 POLY Gate는 Gate Oxide 위에 형성되며, Gate Voltage를 이용하여 Channel의 형성과 전도 상태를 제어한다.

Layout 관점에서는 POLY와 Active Area가 교차하는 부분이 매우 중요하다.

- **POLY가 Active를 가로지르는 방향의 치수** → Gate Length \(L\)
- **Active를 따라 형성되는 교차 영역의 폭** → Gate Width \(W\)

MOSFET의 전류 구동 능력은 기본적으로

\[
I_D \propto \frac{W}{L}
\]

의 영향을 받기 때문에 POLY Gate의 치수는 단순한 Layout Geometry가 아니라 실제 MOSFET의 성능과 직접 연결된다.

특히 Gate Length가 지나치게 짧아지면 Short Channel Effect가 증가할 수 있고, 공정 편차에 대한 민감도 역시 증가한다.

---

# 3. Minimum Gate Length

GPDK090에서는 동작 전압 및 소자 종류에 따라 N-channel과 P-channel Gate에 대한 최소 Gate Length를 제한한다.

Minimum Gate Length Rule은 Lithography 공정에서 Gate Pattern을 안정적으로 형성하는 동시에 MOSFET의 최소 Channel Length를 제한하는 역할을 한다.

Gate Length가 지나치게 짧아지면 Drain 전기장이 Source 쪽 Channel Potential에 영향을 주면서 다음과 같은 Short Channel Effect가 증가할 수 있다.

- Threshold Voltage Roll-off
- DIBL (Drain Induced Barrier Lowering)
- Subthreshold Leakage 증가
- Punch-through 위험 증가

따라서 Minimum Gate Length는 **공정의 Patterning 한계와 소자의 전기적 특성을 동시에 고려한 중요한 Rule**이다.

---

## 4. 고전압 소자의 Gate Length

높은 전압에서 동작하는 MOSFET은 일반적인 저전압 Core Device보다 더 큰 전기적 Stress를 받는다.

특히 Drain 부근에서 높은 Electric Field가 형성되면 Carrier가 큰 에너지를 얻을 수 있으며, 이를 통해 **Hot Carrier Injection(HCI)**이 발생할 수 있다.

고에너지 Carrier가 Gate Oxide 또는 Si-SiO₂ Interface에 포획되면 장기적으로

- Interface Trap 증가
- Threshold Voltage 변화
- Transconductance 감소
- Gate Oxide 열화

등이 발생할 수 있다.

따라서 고전압 Device에서는 충분한 Gate Length와 별도의 Device Geometry를 확보하여 높은 Electric Field에 대한 신뢰성을 높이는 것이 중요하다.

---

# 5. Minimum POLY Interconnect Width

POLY를 Gate 이외의 Interconnect로 사용할 경우에도 최소 Width가 요구된다.

POLY Interconnect가 지나치게 좁으면 Lithography 및 Etching 과정에서 Pattern이 불안정해질 수 있으며, 단면적 감소로 인해 저항도 증가한다.

저항은 대략

\[
R = \rho \frac{L}{A}
\]

또는 Sheet Resistance를 이용하면

\[
R = R_{sh}\frac{L}{W}
\]

로 나타낼 수 있다.

따라서

\[
W\downarrow
\Rightarrow
R\uparrow
\]

의 관계를 가진다.

Minimum POLY Width Rule은 **공정 후에도 POLY Pattern이 안정적으로 유지되도록 하면서 과도한 저항 증가를 방지하기 위한 규칙**으로 이해할 수 있다.

---

# 6. POLY Spacing

인접한 POLY Pattern 사이에는 Minimum Spacing Rule이 적용된다.

POLY 사이의 거리가 지나치게 작으면 Lithography 및 Etching 과정에서 두 Pattern이 제대로 분리되지 않거나, 공정 편차에 의해 의도하지 않은 연결이 발생할 가능성이 증가한다.

따라서 POLY Spacing Rule은 인접 Pattern 사이에 충분한 제조 Margin을 확보하기 위해 필요하다.

GPDK090에서는 용도에 따라 서로 다른 Spacing Rule이 적용될 수 있다.

- Gate-to-Gate Spacing
- POLY Interconnect-to-Interconnect Spacing
- POLY Resistor Spacing

같은 POLY Layer라도 **Gate, Interconnect, Resistor 등 사용 목적에 따라 요구되는 Process Margin이 다르기 때문에 서로 다른 Rule이 적용될 수 있다.**

---

# 7. Gate Spacing

인접한 두 MOSFET의 Gate 사이에는 Minimum Gate Spacing이 요구된다.

Gate Pattern은 MOSFET의 Channel 위치를 직접 결정하기 때문에 Pattern 간 간격이 지나치게 작으면 Lithography 및 Etching 공정 편차에 더욱 민감해질 수 있다.

따라서 Gate Spacing Rule은 인접한 MOSFET Gate가 안정적으로 분리되어 형성되도록 최소 거리를 확보한다.

---

# 8. POLY Resistor Spacing

POLY는 저항 소자로도 사용할 수 있으며, POLY Resistor에는 일반적인 Gate 또는 Interconnect와 다른 Spacing Rule이 적용될 수 있다.

저항은

\[
R = R_{sh}\frac{L}{W}
\]

로 결정되므로 POLY Resistor에서는 실제 제조된 \(L\)과 \(W\)의 정확성이 저항값에 직접 영향을 준다.

따라서 주변 Pattern과 충분한 거리를 확보하여 Lithography 및 Etching 과정에서 Resistor Geometry가 영향을 받지 않도록 하는 것이 중요하다.

---

# 9. POLY Gate Extension

MOSFET Layout에서는 POLY Gate가 Active Area의 경계에서 바로 끝나는 것이 아니라 **Active Area를 넘어 일정 거리 이상 연장**되어야 한다.

이를 Gate Extension이라고 한다.

Gate가 Active 끝부분까지만 정확하게 그려져 있다면 Layout상으로는 문제가 없어 보일 수 있지만, 실제 제조에서는 POLY와 Active Mask 사이에 Overlay Error가 존재한다.

이 경우 Gate가 Active 전체를 완전히 가로지르지 못할 가능성이 있다.

따라서

\[
\text{Gate Extension}
\Rightarrow
\text{Overlay Margin}
\]

을 확보함으로써 Mask Misalignment가 발생하더라도 Gate가 Active Area를 안정적으로 가로지르도록 한다.

N-channel과 P-channel 모두 동일한 기본 목적을 가진다.

---

# 10. Active Area to Gate Enclosure

Gate 주변에서는 Source/Drain으로 사용될 Active Area가 충분히 확보되어야 한다.

Gate 바로 옆에서 Active Area가 지나치게 짧게 끝나면 Source/Drain 영역을 안정적으로 형성하기 어렵고, Implant 및 Contact 배치를 위한 공간 역시 부족해질 수 있다.

따라서 Gate를 기준으로 Source/Drain 방향에 일정 크기 이상의 Active Area를 확보하도록 Enclosure Rule이 적용된다.

이를 통해 **Gate와 Source/Drain의 상대적인 Geometry를 안정적으로 유지**할 수 있다.

---

# 11. POLY와 Unrelated / Related Active Area Spacing

POLY Interconnect가 Active Area 주변을 지나가는 경우에도 최소 Spacing이 요구된다.

여기서는 POLY가 해당 Active와 전기적으로 관련된 구조인지, 아니면 단순히 주변을 지나가는 **Unrelated POLY**인지에 따라 Rule이 구분될 수 있다.

POLY와 Active 사이의 간격이 지나치게 작으면 Overlay 및 Patterning Variation으로 인해 의도하지 않은 Geometry가 만들어질 가능성이 있다.

따라서 POLY-to-Active Spacing Rule을 통해 **Gate로 사용되지 않는 POLY가 Active와 의도하지 않은 방식으로 간섭하는 것을 방지**한다.

---

# 12. Bent POLY

POLY가 45° 등으로 꺾이는 Bent Geometry에서는 일반적인 직선 POLY보다 더 큰 Width와 Spacing이 요구될 수 있다.

이는 Lithography에서 Corner 부분이 설계된 형태 그대로 형성되지 않고 **Corner Rounding**이 발생할 수 있기 때문이다.

따라서

\[
\text{Bent Geometry}
\Rightarrow
\text{Pattern Distortion 증가}
\Rightarrow
\text{Larger Design Margin}
\]

의 관계로 이해할 수 있다.

GPDK090에서는 이러한 문제를 고려하여 Bent POLY에 별도의 Minimum Width와 Minimum Spacing을 적용한다.

---

# 13. Bent Gate Prohibition

POLY Interconnect는 특정 조건에서 Bent Geometry가 허용될 수 있지만, **MOSFET Gate 자체를 꺾어서 형성하는 것은 허용되지 않는다.**

Gate가 꺾이면 Channel Length가 위치에 따라 달라질 수 있으며, Corner 부분에서 Electric Field와 Lithography 특성이 불균일해질 수 있다.

결과적으로 하나의 MOSFET 내부에서 Channel 특성이 균일하지 않게 형성될 가능성이 있다.

따라서 Gate는 일정한 Channel Length를 갖는 직선 구조로 형성하는 것이 중요하다.

---

# 14. Bent POLY Resistor Prohibition

POLY Resistor 역시 Bent Geometry가 제한될 수 있다.

저항은

\[
R = R_{sh}\frac{L}{W}
\]

에 의해 결정되는데, Corner가 존재하면 해당 영역의 실제 Current Path와 유효 Width를 단순한 \(L/W\)만으로 정확하게 표현하기 어려워진다.

또한 Corner Rounding 및 Etching Bias가 저항값 Variation을 증가시킬 수 있다.

따라서 정확하고 재현성 있는 저항값을 확보하기 위해 Bent POLY Resistor를 제한할 수 있다.

---

# 15. POLY Density

GPDK090에서는 전체 Chip에서 POLY가 차지하는 비율, 즉 **POLY Density Ratio**에 대한 제한이 존재한다.

Density는 개념적으로

\[
\text{POLY Density Ratio}
=
\frac{A_{\mathrm{POLY}}}{A_{\mathrm{Total}}}
\times 100
\]

으로 표현할 수 있다.

POLY Density가 지나치게 높거나 불균일하면 공정 단계에서 Pattern Density에 따른 공정 편차가 증가할 수 있다.

따라서 Density Rule은 개별 MOSFET 하나의 동작보다는 **Chip 전체의 제조 균일성과 공정 안정성**을 확보하기 위한 Rule이라는 점에서 Width나 Spacing Rule과 성격이 다르다.

---

# 16. Maximum Narrow POLY Segment Length

폭이 좁은 POLY Interconnect가 지나치게 길게 형성되는 것을 제한하는 Rule도 존재한다.

POLY는 Metal에 비해 높은 Sheet Resistance를 가지므로

\[
R = R_{sh}\frac{L}{W}
\]

에서

\[
L\uparrow,\quad W\downarrow
\Rightarrow
R\uparrow
\]

가 된다.

따라서 좁고 긴 POLY Interconnect는 큰 Series Resistance를 형성할 수 있다.

특히 두 Contact 사이의 POLY Segment가 지나치게 길어지면 신호 전달 특성과 RC Delay에 불리할 수 있으므로 최대 길이를 제한한다.

---

# 17. Minimum POLY Area

POLY Pattern에는 Width뿐만 아니라 Minimum Area Rule도 적용될 수 있다.

어떤 POLY Pattern이 최소 Width를 만족하더라도 전체 면적이 지나치게 작으면 Lithography 및 Etching 과정에서 Pattern이 안정적으로 형성되지 않거나 소실될 가능성이 있다.

따라서

\[
A_{\mathrm{POLY}}\ge A_{\mathrm{min}}
\]

조건을 추가하여 너무 작은 POLY Island가 생성되는 것을 방지한다.

이는 **Minimum Width만으로 검출하기 어려운 작은 2차원 Pattern을 제한하기 위한 보완적인 Rule**이라고 볼 수 있다.

---

# 18. POLY Design Rule의 분류

GPDK090의 POLY Rule을 기능에 따라 정리하면 다음과 같이 분류할 수 있다.

| Rule Type | 주요 목적 |
|---|---|
| Gate Length | Channel Length 및 소자 특성 확보 |
| Width | 안정적인 POLY Pattern 형성 |
| Spacing | 인접 POLY Pattern 분리 |
| Extension | Active-POLY Overlay Margin 확보 |
| Enclosure | Gate 주변 Source/Drain 영역 확보 |
| POLY-to-Active Spacing | 의도하지 않은 Active-POLY 간섭 방지 |
| Bent Width/Spacing | Corner Patterning Margin 확보 |
| Bent Gate Restriction | 균일한 Channel Geometry 확보 |
| Resistor Rule | 저항값의 정확성 및 재현성 확보 |
| Density | Chip-level 공정 균일성 확보 |
| Segment Length | POLY Interconnect 저항 증가 제한 |
| Minimum Area | 작은 POLY Pattern의 제조 안정성 확보 |

---

# 19. POLY Design Rule에 대한 고찰

POLY Design Rule을 학습하면서 POLY가 단순한 하나의 Layout Layer가 아니라 **MOSFET의 전기적 특성과 공정 정확도를 직접 연결하는 중요한 Layer**라는 점을 확인할 수 있었다.

특히 Gate Length Rule은 다른 일반적인 Width Rule과 달리 단순히 Pattern이 제조 가능한지를 판단하는 것을 넘어 **MOSFET의 Channel Length 자체를 결정**한다.

따라서 Gate Length의 변화는

\[
L
\rightarrow
\text{Channel Electrostatics}
\rightarrow
I_D,\;V_{TH},\;\text{Leakage}
\]

와 같이 실제 소자 특성 변화로 이어질 수 있다.

반면 Gate Extension과 POLY-to-Active Spacing은 **서로 다른 Mask 사이에서 발생하는 Overlay Error에 대한 Margin**이라는 공정적인 의미가 강하다.

Bent POLY와 Minimum Area Rule을 통해서는 단순히 Width와 Spacing만 만족한다고 모든 Layout Geometry가 제조 가능한 것은 아니며, **Corner와 작은 Pattern의 형태까지 고려해야 한다는 점**을 확인할 수 있었다.

또한 POLY Resistor와 POLY Interconnect를 학습하면서 동일한 POLY Layer라도 사용 목적에 따라 중요하게 고려해야 하는 특성이 달라진다는 점을 알 수 있었다.

Gate에서는 Channel Geometry가 중요하지만, Interconnect와 Resistor에서는

\[
R=R_{sh}\frac{L}{W}
\]

에 따른 Sheet Resistance와 Geometry의 영향이 중요하다.

즉, 하나의 POLY Layer에 여러 종류의 Design Rule이 존재하는 이유는 POLY가 **Gate, Interconnect, Resistor 등 서로 다른 기능으로 사용될 수 있기 때문**이라고 이해할 수 있었다.

---

# 20. Overall Summary

POLY Design Rule은 크게 **Device Geometry, Patterning, Overlay, Resistance 및 Process Uniformity**를 확보하기 위한 규칙으로 정리할 수 있다.

전체적인 관계는 다음과 같다.

\[
\boxed{
\text{POLY Layout}
\rightarrow
\text{Gate / Interconnect / Resistor Geometry}
\rightarrow
\text{Fabrication Accuracy}
\rightarrow
\text{Electrical Characteristics}
\rightarrow
\text{Device Reliability}
}
\]

Cadence Virtuoso에서 각 POLY Rule에 맞는 Layout과 의도적으로 Rule을 위반한 Layout을 직접 구성하고 DRC를 수행함으로써, **Design Rule의 수치가 단순한 Layout 제한이 아니라 실제 Lithography, Etching, Overlay 및 MOSFET의 전기적 특성을 고려하여 설정된 제조 기준이라는 점을 이해할 수 있었다.**

특히 POLY는 Active Area와 교차하여 MOSFET의 Gate와 Channel을 직접 정의하기 때문에, 다른 배선 Layer보다 **Layout Geometry와 Device Physics 사이의 연관성이 매우 강한 Layer**라는 점을 확인할 수 있었다.
