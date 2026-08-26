# Contact Design Rule Study

## 1. Contact의 개념

**Contact**는 반도체 소자의 **Active Area 또는 Poly와 Metal1을 전기적으로 연결하는 수직 연결 구조**이다.

대표적으로 다음 연결에 사용된다.

- N+ Active ↔ Metal1
- P+ Active ↔ Metal1
- Poly ↔ Metal1

따라서 Contact는 Device와 상부 Metal Interconnect 사이의 전기적 연결을 담당한다.

---

## 2. Contact와 Via의 차이

| 구분 | Contact | Via |
|---|---|---|
| 주요 역할 | Device와 Metal 연결 | Metal Layer 간 연결 |
| 대표 연결 | Active ↔ Metal1 | Metal1 ↔ Metal2 |
| 대표 연결 | Poly ↔ Metal1 | Metal2 ↔ Metal3 |

```math
\text{Contact}: \text{Device} \leftrightarrow M1
```

```math
\text{Via}: M_k \leftrightarrow M_{k+1}
```

---

## 3. Contact의 역할

MOSFET의 Source/Drain은 Silicon의 N+ 또는 P+ Active 영역에 형성된다.

이를 Metal Interconnect와 연결하기 위해 Contact를 사용한다.

```math
\text{Source/Drain}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
```

Contact가 안정적으로 형성되지 않으면 Contact Resistance 증가 또는 Open Failure가 발생할 수 있다.

---

## 4. Minimum Contact Width

Contact Hole이 지나치게 작으면 다음과 같은 문제가 발생할 수 있다.

- Lithography Resolution 문제
- Contact Etching 불안정
- Contact Filling 불량
- Contact Resistance 증가

따라서 Minimum Contact Width는 **Contact Hole을 안정적으로 형성하고 충진하기 위한 최소 Geometry를 확보하는 규칙**이다.

---

## 5. Contact Spacing

인접한 Contact 사이에는 Minimum Contact-to-Contact Spacing이 요구된다.

Contact 사이의 거리가 지나치게 작으면 Lithography 및 Etching 과정에서 두 Contact Hole이 안정적으로 분리되지 않을 수 있다.

따라서 Contact Spacing Rule은 **Pattern Separation과 Process Margin을 확보하기 위한 규칙**이다.

---

## 6. Active Area의 Contact Enclosure

Active Area는 Contact를 일정 거리 이상 감싸야 한다.

이는 Active와 Contact 사이에서 발생할 수 있는 Overlay Error를 고려하기 위한 것이다.

```math
\text{Active Enclosure}
=
\text{Required Contact Region}
+
\text{Overlay Margin}
```

Enclosure가 부족하면 Contact 일부가 Active 밖으로 벗어나 유효 Contact Area가 감소할 수 있다.

---

## 7. N+/P+ Implant의 Contact Enclosure

N+/P+ Implant 역시 Contact를 충분히 감싸야 한다.

이 Rule은 Overlay Margin뿐만 아니라 **Contact 아래에 충분한 고농도 Doping을 확보한다는 의미**를 가진다.

Depletion Width는 대략 다음 관계를 가진다.

```math
W_{\text{dep}}
\propto
\frac{1}{\sqrt{N}}
```

따라서 다음과 같다.

```math
N \uparrow
\rightarrow
W_{\text{dep}} \downarrow
```

Tunneling Probability는 다음과 같이 나타낼 수 있다.

```math
T \propto e^{-2\kappa W}
```

결과적으로 다음 관계를 얻을 수 있다.

```math
N \uparrow
\rightarrow
W_{\text{dep}} \downarrow
\rightarrow
T_{\text{tunnel}} \uparrow
\rightarrow
R_C \downarrow
```

따라서 Contact 아래에 충분한 N+/P+ 고농도 Doping을 확보하면 낮은 Contact Resistance를 구현하는 데 유리하다.

---

## 8. Schottky Contact

이상적인 Schottky-Mott Model에서 N-type Semiconductor의 Schottky Barrier Height는 다음과 같이 표현할 수 있다.

```math
\Phi_{Bn} = \Phi_m - \chi
```

여기서

- $\Phi_{Bn}$ : Schottky Barrier Height
- $\Phi_m$ : Metal Work Function
- $\chi$ : Semiconductor Electron Affinity

이다.

Schottky Contact에서는 Thermionic Emission이 중요한 전도 메커니즘이 될 수 있다.

```math
J =
A^{**}T^2
\exp\left(
-\frac{q\Phi_{Bn}}{kT}
\right)
\left[
\exp\left(
\frac{qV}{nkT}
\right)-1
\right]
```

이러한 Contact는 일반적으로 Rectifying I-V Characteristic을 나타낸다.

---

## 9. Ohmic Contact와 고농도 Doping

CMOS의 Source/Drain Contact에서는 낮은 Contact Resistance가 필요하다.

Metal-Semiconductor Junction의 Depletion Width는 간략하게 다음과 같이 나타낼 수 있다.

```math
W =
\sqrt{
\frac{2\epsilon_s(V_{bi}-V)}
{qN}
}
```

따라서 Doping Concentration이 증가하면 Barrier Width가 감소한다.

```math
W \propto \frac{1}{\sqrt{N}}
```

Carrier Transport Mechanism은 도핑 농도가 증가함에 따라 개념적으로 다음과 같이 변화할 수 있다.

```math
\text{Thermionic Emission}
\rightarrow
\text{Thermionic-Field Emission}
\rightarrow
\text{Field Emission}
```

고농도 영역에서는 얇아진 Barrier를 통한 Tunneling이 증가하여 낮은 Contact Resistance와 Ohmic-like Behavior를 얻을 수 있다.

---

## 10. Poly Contact

POLY Gate 역시 Contact를 통해 Metal1과 연결할 수 있다.

```math
\text{Poly}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
```

Poly가 Contact를 충분히 Enclose하지 않으면 Overlay Error에 의해 유효 Contact Area가 감소할 수 있으므로 충분한 Enclosure가 요구된다.

---

## 11. Metal1의 Contact Enclosure

Contact 상부에서는 Metal1이 Contact를 충분히 감싸야 한다.

```math
\text{Active / Poly}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
```

Metal1 Enclosure가 부족하면 Overlay Error에 의해 Contact 일부가 Metal1 밖으로 벗어날 수 있다.

따라서 충분한 Metal1-to-Contact Enclosure를 통해 안정적인 전기적 연결을 확보한다.

---

## 12. Contact Array

큰 전류가 흐르는 Source/Drain 또는 Well/Substrate Tap에서는 여러 Contact를 Array 형태로 사용할 수 있다.

각 Contact가 동일한 저항 $R_C$를 가지며 이상적으로 병렬 연결되었다고 가정하면 다음과 같다.

```math
R_{\text{eq}}
\approx
\frac{R_C}{N}
```

따라서 Contact 수가 증가하면 다음과 같다.

```math
N_{\text{Contact}} \uparrow
\rightarrow
R_{\text{eq}} \downarrow
```

Contact Array는 Contact Resistance를 감소시키고 Current를 분산시켜 Current Handling Capability 및 Reliability를 향상시키는 데 유리하다.

---

## 13. Contact Design Rule에 대한 고찰

Contact Design Rule을 학습하면서 Contact는 단순한 Layout상의 사각형이 아니라 **Semiconductor Device와 Metal Interconnect를 연결하는 중요한 Electrical Interface**라는 점을 이해할 수 있었다.

Contact를 안정적으로 형성하기 위해서는 다음과 같이 여러 Layer의 상대적인 위치를 함께 고려해야 한다.

```math
\text{Implant}
\rightarrow
\text{Active / Poly}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal1}
```

Active와 Metal1의 Contact Enclosure는 주로 Overlay Error와 유효 Contact Area 확보라는 공정적인 의미를 가진다.

반면 N+/P+ Implant의 Contact Enclosure에는 충분한 고농도 Doping을 확보하여 Contact Resistance를 낮춘다는 소자 물리적인 의미도 포함된다.

```math
N \uparrow
\rightarrow
W_{\text{dep}} \downarrow
\rightarrow
\text{Tunneling} \uparrow
\rightarrow
R_C \downarrow
```

이를 통해 **동일한 Enclosure Rule이라도 어떤 Layer 사이의 Rule인지에 따라 그 물리적 목적이 달라질 수 있다는 점**을 이해할 수 있었다.

---

## 14. Overall Summary

Contact는 Active 또는 Poly와 Metal1을 전기적으로 연결하는 수직 연결 구조이다.

전체적인 Layer 관계는 다음과 같이 정리할 수 있다.

```math
\text{Device Region}
\rightarrow
\text{Doping}
\rightarrow
\text{Contact}
\rightarrow
\text{Metal Interconnect}
```

Cadence Virtuoso에서 Contact Width, Spacing, Active/Poly Enclosure, Implant Enclosure 및 Metal1 Enclosure를 직접 구성하고 DRC를 수행함으로써, **Contact Design Rule이 Lithography, Etching, Filling 및 Overlay와 같은 공정 요소뿐만 아니라 Doping, Schottky Barrier, Tunneling 및 Contact Resistance와 같은 소자 물리와도 연결되어 있음을 이해할 수 있었다.**
