# CMOS Layout Design Rule 구현 및 Metal–Semiconductor Contact 특성 분석

## 1. 프로젝트 개요

본 프로젝트에서는 CMOS 반도체 공정의 **Design Rule Manual을 기반으로 주요 Layout Design Rule을 분석하고, Cadence Virtuoso 환경에서 각 규칙을 직접 구현하여 물리적 의미와 필요성을 확인**하였다.

Design Rule은 단순히 Layout 도형의 크기와 간격을 제한하기 위한 규칙이 아니다. 실제 반도체 제조 과정에서 발생하는 Lithography 오차, Etching 오차, Mask Misalignment 등의 공정 변동을 고려하여 소자가 정상적으로 형성되도록 하고, 완성된 소자의 전기적 특성과 신뢰성을 확보하기 위한 설계 기준이다.

본 프로젝트에서는 다음과 같은 주요 Layer 및 Reliability Rule을 대상으로 분석 및 구현을 수행하였다.

- Nwell
- Active Area
- N+/P+ Implant
- Poly
- Contact
- Salicide Block (SIPROT)
- Metal
- Via
- Latch-up
- Metal Density
- Antenna

Design Rule의 기본적인 Geometry는 **Width, Spacing, Enclosure, Overlap, Parallel Length** 등의 형태로 정의된다.

전체 Design Rule을 구현한 뒤, 이 가운데 실제 소자의 **전기적 접촉 특성과 직접 연결되는 Contact 및 Metal Rule**을 중심으로 심화 분석하였다.

특히 Metal과 Semiconductor가 접촉할 때 나타나는 **Schottky Contact와 Ohmic Contact의 차이**, 그리고 실제 CMOS Source/Drain Contact가 낮은 Contact Resistance를 갖는 Ohmic Contact가 되도록 하는 **Heavy Doping, Silicide 및 Contact Layout Rule의 관계**를 분석하였다.

---

## 2. Design Rule의 목적

반도체 Layout에서 설계자가 그린 Pattern은 실제 Wafer 위에 완벽하게 동일한 형태로 형성되지 않는다.

실제 공정에서는 다음과 같은 오차가 존재한다.

```text
Layout Pattern
      ↓
Photolithography
      ↓
Etching / Deposition / Implantation
      ↓
Process Variation
      ↓
Actual Wafer Pattern
