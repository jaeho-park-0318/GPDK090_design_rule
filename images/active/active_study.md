# Active Design Rule Study

## 1. Active Area의 개념

**Active Area**는 STI(Shallow Trench Isolation)에 의해 주변 영역과 격리된 Silicon 영역으로, MOSFET의 **Source/Drain 및 Channel이 형성되는 영역**이다.

MOSFET Layout에서는 POLY Gate가 Active Area를 가로지르면서 Channel 영역을 정의하고, Gate 양쪽의 Active Area에 N+ 또는 P+ Implant가 형성되어 Source/Drain이 구성된다.

따라서 Active Area는 실제 Transistor가 형성되는 핵심 영역이며, Active 이외의 영역은 주로 STI를 통해 인접 소자와 전기적으로 격리된다.

---

## 2. Active Area와 STI

STI는 **Shallow Trench Isolation**의 약자로, Silicon에 Trench를 형성한 후 절연 물질로 채워 인접한 Active Area를 전기적으로 분리하는 구조이다.

Active Area는 STI가 형성되지 않고 Silicon 표면이 소자 형성에 사용되는 영역으로 이해할 수 있다.

```math
\text{Active Area}
\rightarrow
\text{Device Formation Region}
```

```math
\text{STI}
\rightarrow
\text{Device Isolation Region}
```

따라서 Active Design Rule은 단순히 Active Pattern 자체의 제조 가능성뿐만 아니라 인접 소자 사이에 충분한 Isolation을 확보하는 것과도 관련된다.

---

## 3. Active Area와 MOSFET

MOSFET에서는 POLY가 Active Area를 가로지르는 부분에 Gate와 Channel이 형성된다.

Gate 양쪽의 Active Area에는 Implant 공정을 통해 Source/Drain이 형성된다.

NMOS의 경우 개념적으로 다음과 같이 나타낼 수 있다.

```math
\text{Active}
+
\text{POLY}
+
\text{N-Implant}
\rightarrow
\text{NMOS}
```

PMOS에서는 N-Well 내부의 Active Area와 P-type Implant를 이용하여 P+ Source/Drain을 형성한다.

따라서 Active Area의 Geometry는 MOSFET의 Source/Drain 및 Channel 구조와 직접적인 관계를 가진다.

---

## 4. Minimum Active Width

Active Area에는 안정적인 Pattern 형성을 위한 **Minimum Width Rule**이 적용된다.

Active Width가 지나치게 작으면 Lithography 및 Etching 과정에서 설계한 Geometry를 안정적으로 구현하기 어려워질 수 있다.

또한 주변 STI 공정의 영향을 상대적으로 크게 받을 수 있으므로 충분한 Active Width를 확보해야 한다.

MOSFET의 Drain Current는 기본적으로 Gate Width $W$와 Gate Length $L$에 영향을 받는다.

```math
I_D \propto \frac{W}{L}
```

따라서 Active Geometry는 제조 가능성뿐만 아니라 MOSFET의 전기적 특성과도 연결된다.

---

## 5. Minimum Active Spacing

서로 다른 Active Area 사이에는 일정한 Minimum Spacing이 요구된다.

Active 사이의 간격은 인접 소자 사이에 충분한 STI Isolation 영역을 확보하는 데 중요하다.

Active Area 사이의 거리가 지나치게 작으면 Lithography 및 Etching 공정 편차에 대한 Margin이 감소하고, 인접 소자 사이의 Isolation 특성에도 영향을 줄 수 있다.

따라서 다음과 같은 관계로 이해할 수 있다.

```math
\text{Active Spacing}
\rightarrow
\text{STI Isolation Margin}
\rightarrow
\text{Device Isolation}
```

즉, Active Spacing Rule은 **인접 소자 사이의 충분한 전기적 Isolation과 제조 Margin을 확보하기 위한 규칙**이다.

---

## 6. Active Enclosure

Active와 다른 Layer가 서로 중첩되어 소자를 형성하는 경우 충분한 Enclosure가 요구될 수 있다.

Layout에서 두 Layer의 경계를 정확하게 맞추더라도 실제 제조에서는 서로 다른 Mask 사이에 Overlay Error가 발생할 수 있다.

따라서 필요한 영역보다 Layer를 일정 거리 더 크게 설계하여 Process Margin을 확보한다.

```math
\text{Enclosure}
=
\text{Required Region}
+
\text{Overlay Margin}
```

이를 통해 Mask Misalignment가 발생하더라도 필요한 Device Region이 안정적으로 유지될 수 있도록 한다.

---

## 7. Bent Active

Active Area가 45° 등으로 꺾이는 Bent Geometry에서는 일반적인 직선 Active보다 더 큰 Width 또는 Spacing이 요구될 수 있다.

이는 Lithography 과정에서 Corner 부분이 설계된 형태 그대로 형성되지 않고 **Corner Rounding 및 Pattern Distortion**이 발생할 수 있기 때문이다.

```math
\text{Bent Geometry}
\rightarrow
\text{Corner Rounding}
\rightarrow
\text{Larger Process Margin}
```

따라서 Bent Active에는 일반적인 직선 Active보다 추가적인 Design Margin이 필요할 수 있다.

---

## 8. Active Design Rule에 대한 고찰

Active Design Rule을 학습하면서 Active는 단순히 Source/Drain을 표시하기 위한 Layout Layer가 아니라 **실제 Silicon에서 Transistor가 형성되는 영역을 결정하는 핵심적인 Layer**라는 점을 이해할 수 있었다.

특히 Active Width와 Spacing Rule은 단순한 Geometry 제한이 아니라 다음과 같은 여러 공정 요소와 관련되어 있다.

- Lithography Resolution
- Etching Variation
- STI Isolation
- Overlay Error
- Device Geometry

또한 Bent Active Rule을 통해 동일한 Layer에서도 Geometry의 형태에 따라 제조 난이도가 달라질 수 있다는 점을 확인하였다.

즉, 직선 Pattern보다 Corner를 포함한 Pattern이 Lithography 및 Etching 공정에 더 민감할 수 있으므로 추가적인 Process Margin이 필요하다.

---

## 9. Overall Summary

Active Area는 STI에 의해 주변 영역과 격리되며 MOSFET의 **Source/Drain 및 Channel이 형성되는 Silicon 영역**이다.

Active Design Rule은 Width, Spacing 및 Enclosure 등을 제한하여 실제 Wafer에서 필요한 Active Geometry와 소자 간 Isolation을 안정적으로 구현하도록 한다.

전체적인 관계는 다음과 같이 정리할 수 있다.

```math
\text{Active Layout}
\rightarrow
\text{Device Region}
\rightarrow
\text{STI Isolation}
\rightarrow
\text{MOSFET Geometry}
\rightarrow
\text{Electrical Characteristics}
```

Cadence Virtuoso에서 Active 관련 Design Rule을 직접 Layout으로 구성하고 DRC를 수행함으로써, **Active Design Rule이 단순한 Layout 치수 제한이 아니라 실제 MOSFET 구조와 STI Isolation을 안정적으로 형성하기 위한 제조 규칙이라는 점을 이해할 수 있었다.**
