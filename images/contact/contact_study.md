# Contact Design Rule Study

## 1. Contact의 개념

**Contact**는 반도체 소자의 **Active Area 또는 Poly와 Metal1을 전기적으로 연결하는 수직 연결 구조**이다.

MOSFET의 Source/Drain이나 Gate에서 발생한 전기적 신호를 상부 Metal 배선으로 전달하기 위해 사용되며, 일반적으로 다음과 같은 연결을 형성한다.

- **N+ Active ↔ Metal1**
- **P+ Active ↔ Metal1**
- **Poly ↔ Metal1**

따라서 Contact는 소자 영역과 금속 배선층 사이의 전기적 연결을 담당하는 중요한 구조이다.

---

# 2. Contact와 Via의 차이

Contact와 Via는 모두 수직 방향의 전기적 연결을 담당하지만 연결하는 Layer가 다르다.

| 구분 | Contact | Via |
|---|---|---|
| 주요 역할 | Device Layer와 Metal 연결 | Metal Layer 간 연결 |
| 대표 연결 | Active ↔ Metal1 | Metal1 ↔ Metal2 |
| 대표 연결 | Poly ↔ Metal1 | Metal2 ↔ Metal3 |
| 위치 | FEOL과 배선 구조의 연결부 | BEOL Interconnect 내부 |

즉,

\[
\boxed{
\text{Contact}: \text{Device} \leftrightarrow M1
}
\]

\[
\boxed{
\text{Via}: M_k \leftrightarrow M_{k+1}
}
\]

로 구분할 수 있다.

---

# 3. Contact의 역할

MOSFET에서 Source/Drain은 Silicon 내부의 N+ 또는 P+ Active 영역에 형성된다.

하지만 실제 회로에서는 이 영역을 다른 Transistor나 전원선과 연결해야 하기 때문에 Metal 배선과 연결할 필요가 있다.

이때 Contact를 통해

\[
\text{Source/Drain}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
\]

의 전류 경로를 형성한다.

Gate 역시 Poly에서 Contact를 통해 Metal1으로 연결할 수 있다.

따라서 Contact가 안정적으로 형성되지 않으면 Contact Resistance가 증가하거나 심한 경우 Open Failure가 발생할 수 있다.

---

# 4. Minimum Contact Width

Contact에는 제조 가능한 최소 크기를 보장하기 위한 **Minimum Width Rule**이 적용된다.

Contact Hole이 지나치게 작으면 Lithography 및 Etching 과정에서 Pattern을 안정적으로 형성하기 어려우며, Contact Hole 내부를 Conductive Material로 충분히 채우는 과정에서도 문제가 발생할 수 있다.

따라서 Minimum Contact Width는

- Lithography Resolution
- Contact Etching
- Contact Filling
- Contact Resistance

등을 고려하여 결정된다.

> **Minimum Contact Width Rule은 Contact Hole을 안정적으로 형성하고 충진하여 Device와 Metal 사이의 신뢰성 있는 전기적 연결을 확보하기 위한 규칙이다.**

---

# 5. Contact Spacing

인접한 Contact 사이에는 **Minimum Contact-to-Contact Spacing**이 요구된다.

Contact 사이의 거리가 지나치게 작으면 Lithography 및 Etching 과정에서 두 Contact Hole이 서로 영향을 받거나 의도하지 않게 연결될 가능성이 증가한다.

또한 Contact Array를 구성하는 경우에는 여러 Contact가 반복적으로 배치되기 때문에 Pattern Density 및 공정 조건을 고려한 별도의 Spacing Rule이 적용될 수 있다.

따라서 Contact Spacing은

\[
\boxed{
\text{Contact Separation}
+
\text{Process Margin}
}
\]

을 확보하기 위한 규칙으로 이해할 수 있다.

---

# 6. Active Area의 Contact Enclosure

Contact를 Source/Drain에 배치할 경우 Contact 전체가 Active Area 내부에 안정적으로 위치해야 한다.

따라서 Active Area가 Contact를 일정 거리 이상 감싸도록 **Enclosure Rule**이 적용된다.

이는 Contact와 Active가 서로 다른 Mask를 이용하여 형성되기 때문에 발생할 수 있는 **Overlay Error**를 고려한 것이다.

\[
\boxed{
\text{Active Enclosure}
=
\text{Contact Region}
+
\text{Overlay Margin}
}
\]

Enclosure가 충분하지 않으면 Mask Misalignment에 의해 Contact 일부가 Active Area 밖으로 벗어날 수 있으며, 실제 유효 접촉 면적이 감소하여 Contact Resistance가 증가하거나 Contact Failure가 발생할 수 있다.

---

# 7. N+/P+ Implant의 Contact Enclosure

Contact가 N+ 또는 P+ Active에 형성될 경우 해당 Implant 영역도 Contact를 충분히 감싸도록 설계해야 한다.

이 Rule은 단순히 Overlay Error를 보상하기 위한 것뿐만 아니라 **Contact 아래에 충분한 고농도 도핑 영역을 확보한다는 전기적 의미**도 가진다.

Metal-Semiconductor Contact에서는 계면에 Potential Barrier가 존재할 수 있다.

Depletion Region의 폭은 대략

\[
W_{\mathrm{dep}}
\propto
\frac{1}{\sqrt{N}}
\]

의 관계를 가진다.

따라서 Doping Concentration \(N\)이 증가하면

\[
N\uparrow
\Rightarrow
W_{\mathrm{dep}}\downarrow
\]

가 된다.

Barrier Width가 충분히 얇아지면 Carrier의 Tunneling Probability가 증가한다.

\[
T \propto e^{-2\kappa W}
\]

따라서

\[
\boxed{
N\uparrow
\Rightarrow
W_{\mathrm{dep}}\downarrow
\Rightarrow
T_{\mathrm{tunnel}}\uparrow
\Rightarrow
R_C\downarrow
}
\]

의 관계를 얻을 수 있다.

즉, Contact 아래를 충분한 N+/P+ 고농도 Implant 영역으로 형성하면 낮은 Contact Resistance를 갖는 **Ohmic Contact에 가까운 특성**을 확보하는 데 유리하다.

---

# 8. Schottky Contact와 Ohmic Contact

Metal과 Semiconductor를 단순히 접촉시킨다고 항상 이상적인 저저항 Contact가 형성되는 것은 아니다.

Metal-Semiconductor 계면에는 재료의 Work Function 및 Interface State 등에 의해 **Schottky Barrier**가 형성될 수 있다.

이상적인 Schottky-Mott Model에서 N-type Semiconductor의 Barrier Height는

\[
\Phi_{Bn} = \Phi_m - \chi
\]

로 표현할 수 있다.

여기서

- \(\Phi_m\): Metal Work Function
- \(\chi\): Semiconductor Electron Affinity

를 의미한다.

Schottky Contact에서는 Carrier가 Barrier를 넘어 이동하는 **Thermionic Emission**이 중요한 전도 메커니즘이 될 수 있으며, 정류성 I-V 특성을 나타낸다.

반면 CMOS의 Source/Drain Contact에서는 낮은 Contact Resistance가 필요하므로 고농도 N+/P+ Doping을 이용하여 Barrier Width를 감소시키고 Carrier Tunneling을 증가시킨다.

그 결과 전압에 대해 비교적 선형적인 I-V 특성을 갖는 **Ohmic Contact**를 구현할 수 있다.

---

# 9. Doping과 Contact Resistance

Contact Resistance는 Contact의 성능을 결정하는 중요한 요소이다.

고농도 도핑 영역에서는 Metal-Semiconductor Barrier 자체가 완전히 사라지지 않더라도 Barrier Width가 매우 얇아질 수 있다.

Carrier transport mechanism은 도핑 농도가 증가함에 따라 개념적으로

\[
\text{Thermionic Emission}
\rightarrow
\text{Thermionic-Field Emission}
\rightarrow
\text{Field Emission}
\]

방향으로 변화할 수 있다.

특히 고농도 N+/P+ 영역에서는 Carrier가 얇아진 Barrier를 직접 Tunneling하는 **Field Emission**의 영향이 커질 수 있다.

따라서 Source/Drain Contact에서는 충분한 고농도 Implant 영역을 확보하는 것이 중요하다.

---

# 10. Poly Contact

Contact는 Active뿐만 아니라 **Poly와 Metal1을 연결**하는 데에도 사용된다.

Poly Gate를 Metal Interconnect와 연결하기 위해

\[
\text{Poly}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
\]

구조를 형성한다.

Poly 역시 Contact를 충분히 Enclose해야 하며, Contact가 Poly Edge에 지나치게 가까우면 Overlay Error에 의해 실제 Contact Area가 감소할 수 있다.

따라서 Poly Contact에서도 충분한 **Poly-to-Contact Enclosure**가 요구된다.

---

# 11. Metal1의 Contact Enclosure

Contact의 상부에는 Metal1이 형성되며 Metal1 역시 Contact를 충분히 감싸야 한다.

\[
\boxed{
\text{Active/Poly}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
}
\]

에서 Contact가 Metal1 Edge에 지나치게 가까우면 Overlay Error에 의해 Contact 전체가 Metal1 아래에 포함되지 않을 수 있다.

이 경우 실제 Contact Area 감소 및 Contact Resistance 증가가 발생할 수 있다.

따라서 Metal1-to-Contact Enclosure Rule을 통해 안정적인 전기적 연결을 확보한다.

---

# 12. Contact Array

큰 전류가 흐르는 Source/Drain이나 Well/Substrate Tap에서는 하나의 Contact보다 **여러 개의 Contact를 Array 형태로 배치**하는 것이 유리하다.

여러 Contact가 병렬로 연결된다고 단순화하면

\[
R_{\mathrm{eq}}
\approx
\frac{R_C}{N}
\]

으로 생각할 수 있다.

여기서 \(N\)은 병렬 Contact의 개수이다.

따라서 Contact 수를 증가시키면 전체 Contact Resistance를 감소시키고 전류를 여러 Contact로 분산시킬 수 있다.

\[
\boxed{
N_{\mathrm{Contact}}\uparrow
\Rightarrow
R_{\mathrm{Contact,eq}}\downarrow
}
\]

또한 하나의 Contact에 전류가 집중되는 것을 완화하므로 높은 전류가 흐르는 영역의 신뢰성 향상에도 유리하다.

단, 실제 저항은 Current Crowding, Metal/Active Resistance 및 Contact 간 상호작용의 영향을 받기 때문에 이상적인 \(1/N\) 관계와 정확히 일치하지 않을 수 있다.

---

# 13. Contact Design Rule의 주요 목적

Contact 관련 Width, Spacing 및 Enclosure Rule은 크게 다음과 같은 목적을 가진다.

1. **Contact Hole의 안정적인 Lithography 및 Etching**
2. **Contact Filling의 안정성 확보**
3. **Active/Poly와 Contact 사이의 Overlay Margin 확보**
4. **Contact와 Metal1 사이의 Overlay Margin 확보**
5. **Contact 아래 충분한 N+/P+ 고농도 Doping 확보**
6. **Contact Resistance 증가 방지**
7. **Open Failure 및 불완전 Contact 방지**
8. **Contact Array를 통한 전류 분산 및 신뢰성 향상**

---

# 14. Contact Design Rule에 대한 고찰

Contact Design Rule을 학습하면서 Contact는 단순히 Layout에서 Metal과 Device를 연결하는 작은 사각형이 아니라, **FEOL의 Semiconductor Device와 상부 Metal Interconnect를 연결하는 중요한 전기적 Interface**라는 점을 이해할 수 있었다.

특히 Contact 관련 Rule은 하나의 Layer만 고려해서는 이해하기 어렵다.

Contact를 안정적으로 형성하기 위해서는

\[
\text{Implant}
\rightarrow
\text{Active/Poly}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
\]

과 같이 여러 Layer의 상대적인 위치를 동시에 고려해야 한다.

Active와 Metal1이 Contact를 충분히 Enclose해야 하는 이유는 서로 다른 Mask 사이에서 발생하는 Overlay Error를 보상하기 위한 것이다.

반면 N+/P+ Implant가 Contact를 Enclose해야 하는 이유에는 Overlay Margin뿐만 아니라 **Contact 아래의 Doping Concentration을 충분히 높게 유지하여 Contact Resistance를 낮춘다는 소자 물리적인 의미**가 추가된다.

이러한 점을 통해 동일한 Enclosure Rule이라도 대상 Layer에 따라 그 물리적인 목적이 다를 수 있다는 점을 확인할 수 있었다.

또한 Metal-Semiconductor Contact의 특성을 Schottky Barrier와 연결하여 분석함으로써, 고농도 Source/Drain Doping이 단순히 MOSFET의 Source/Drain을 형성하기 위한 것뿐만 아니라 **낮은 저항의 Ohmic Contact를 구현하는 데에도 중요한 역할을 한다는 점**을 이해할 수 있었다.

---

# 15. Overall Summary

Contact는 **Active 또는 Poly와 Metal1 사이를 전기적으로 연결하는 수직 연결 구조**이며, MOSFET의 전기적 신호를 상부 Interconnect로 전달하는 역할을 한다.

Contact Design Rule은 단순히 Contact Hole의 최소 크기를 결정하는 것이 아니라,

\[
\boxed{
\text{Device Region}
\rightarrow
\text{Doping}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal Interconnect}
}
\]

전체 연결 구조의 제조 안정성과 전기적 신뢰성을 확보하기 위한 규칙이다.

특히 Contact 관련 Design Rule을 통해 **Lithography, Etching, Overlay Error와 같은 공정적인 요소와 Schottky Barrier, Tunneling, Contact Resistance와 같은 소자 물리적인 요소가 하나의 Layout Rule 안에서 서로 연결되어 있음을 확인할 수 있었다.**

Cadence Virtuoso에서 Contact의 Width, Spacing, Active/Poly Enclosure, Implant Enclosure 및 Metal1 Enclosure를 직접 구성하고 DRC를 수행함으로써, **Contact Design Rule이 실제 소자와 배선 사이에 안정적인 저저항 전기적 연결을 형성하기 위한 핵심적인 Layout 규칙이라는 점을 이해할 수 있었다.**
