# N-Implant Design Rule Study

## 1. N-Implant의 개념

**N-Implant**는 Silicon에 Donor 불순물을 주입하여 **N-type 영역을 형성하기 위한 Implant Layer**이다.

대표적으로 다음 영역을 형성하는 데 사용된다.

- NMOS의 N+ Source/Drain
- N-Well의 N+ Well Tap
- 고농도 N-type Contact 영역

대표적인 Donor 불순물에는 Arsenic(As), Phosphorus(P) 등이 있다.

Layout에서 N-Implant는 **어떤 Silicon 영역을 N-type으로 도핑할 것인지를 지정하는 공정 Layer**로 이해할 수 있다.

---

## 2. N-Implant와 Active Area

Active와 Implant는 서로 다른 역할을 한다.

- **Active Area** : 소자가 형성될 Silicon 영역 정의
- **N-Implant** : 해당 영역을 N-type으로 도핑할 영역 정의

```math
\text{Active Area}
+
\text{N-Implant}
\rightarrow
\text{N-type Active Region}
```

따라서 Active Area만 존재한다고 자동으로 N+ Source/Drain이 형성되는 것은 아니다.

---

## 3. NMOS와 N-Implant

NMOS에서는 P-type Body 내부에 N-type Source/Drain 영역을 형성한다.

Layout 관점에서는 다음과 같이 이해할 수 있다.

```math
\text{Active}
+
\text{POLY}
+
\text{N-Implant}
\rightarrow
\text{NMOS}
```

따라서 N-Implant가 필요한 Active 영역을 충분히 포함하도록 Layout을 구성하는 것이 중요하다.

---

## 4. N-Well Tap과 N-Implant

N-Implant는 N-Well Tap을 형성하는 데에도 사용된다.

```math
\text{N+ Well Tap}
\rightarrow
\text{N-Well}
\rightarrow
V_{DD}
```

고농도 N+ 영역을 이용하면 N-Well과 낮은 저항으로 연결할 수 있으며 이를 통해 Body Potential을 안정적으로 유지할 수 있다.

---

## 5. Minimum N-Implant Width

N-Implant 영역이 지나치게 좁으면 Lithography 및 Ion Implantation 과정에서 원하는 Doping Region을 안정적으로 정의하기 어려울 수 있다.

따라서 Minimum Width Rule을 통해 Process Variation에도 필요한 N-type 영역이 안정적으로 형성되도록 한다.

---

## 6. N-Implant Spacing

서로 다른 N-Implant 영역 사이에는 충분한 Spacing이 필요하다.

```math
\text{Implant Spacing}
\rightarrow
\text{Doping Region Separation}
```

Spacing Rule은 실제 Wafer에서 서로 다른 Doping Region을 안정적으로 구분하기 위한 Process Margin으로 이해할 수 있다.

---

## 7. N-Implant Enclosure

N-type으로 형성해야 하는 Active보다 N-Implant를 일정 거리 더 크게 형성하여 Active를 충분히 감싸도록 한다.

이는 Mask 간 Overlay Error를 고려하기 위한 것이다.

```math
\text{N-Implant Enclosure}
=
\text{Required Doping Region}
+
\text{Overlay Margin}
```

이를 통해 Mask Misalignment가 발생하더라도 필요한 Active 영역이 N-type Doping Region 안에 유지되도록 한다.

---

## 8. N-Implant와 Contact

Contact 아래에는 충분한 고농도 N-type Doping을 확보하는 것이 중요하다.

Metal-Semiconductor Junction의 Depletion Width는 도핑 농도와 대략 다음 관계를 가진다.

```math
W_{\text{dep}}
\propto
\frac{1}{\sqrt{N_D}}
```

따라서 다음과 같다.

```math
N_D \uparrow
\rightarrow
W_{\text{dep}} \downarrow
```

Barrier Width가 감소하면 Tunneling Probability가 증가한다.

```math
T \propto e^{-2\kappa W}
```

결과적으로 다음과 같은 관계를 가진다.

```math
N_D \uparrow
\rightarrow
W_{\text{dep}} \downarrow
\rightarrow
T_{\text{tunnel}} \uparrow
\rightarrow
R_C \downarrow
```

따라서 Contact 주변의 N-Implant Enclosure는 **Overlay Margin뿐만 아니라 낮은 Contact Resistance를 확보하는 것과도 관련된다.**

---

## 9. N-Implant와 N-Well의 차이

| 구분 | N-Well | N-Implant |
|---|---|---|
| 주요 역할 | PMOS Body 형성 | 고농도 N-type 영역 형성 |
| 대표적 사용 | PMOS Body | NMOS S/D, N-Well Tap |
| 영역 특성 | 비교적 넓고 깊음 | 국부적인 고농도 영역 |
| 주요 목적 | Body 및 Isolation | Source/Drain 및 Contact |

N-Well과 N-Implant는 모두 N-type Doping과 관련되어 있지만 **깊이, 농도, 공정 목적 및 Layout 역할이 서로 다르다.**

---

## 10. N-Implant Design Rule에 대한 고찰

N-Implant Design Rule을 통해 Implant Layer는 단순한 Layout Pattern이 아니라 **Silicon 내부의 Doping Profile과 직접 연결되는 Layer**라는 점을 이해할 수 있었다.

```math
\text{Active}
\rightarrow
\text{소자가 형성될 영역}
```

```math
\text{Implant}
\rightarrow
\text{해당 영역의 Doping Type}
```

특히 Enclosure Rule은 Overlay Error가 존재하더라도 필요한 Active Area 전체에 충분한 Doping이 형성되도록 하기 위한 Process Margin이라는 점을 확인하였다.

또한 Contact 영역에서는 다음 관계가 중요하다.

```math
N_D \uparrow
\rightarrow
W_{\text{dep}} \downarrow
\rightarrow
\text{Tunneling} \uparrow
\rightarrow
R_C \downarrow
```

이를 통해 고농도 N-type Doping이 낮은 Contact Resistance를 확보하는 데에도 중요한 역할을 한다는 점을 이해할 수 있었다.

---

## 11. Overall Summary

N-Implant는 Silicon에 Donor 불순물을 주입하여 N-type 영역을 형성하기 위한 공정 Layer이다.

```math
\text{N-Implant Layout}
\rightarrow
\text{Ion Implantation}
\rightarrow
\text{N-type Doping Profile}
\rightarrow
\text{Source/Drain \& Well Tap}
\rightarrow
\text{Electrical Characteristics}
```

Cadence Virtuoso에서 N-Implant의 Width, Spacing 및 Enclosure를 직접 구성하고 DRC를 수행함으로써, **Implant Design Rule이 실제 Silicon 내부의 Doping Profile과 소자의 전기적 특성을 안정적으로 구현하기 위한 공정 규칙이라는 점을 이해할 수 있었다.**
