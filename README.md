# GPDK090 Design Rule Study & Cadence Virtuoso Layout Practice

## Overview

본 프로젝트에서는 **GPDK090(Process Design Kit 90nm)** 환경에서 Layout Design Rule을 학습하고,
Cadence Virtuoso를 이용하여 각 Rule을 직접 구현 및 검증하였다.

단순히 DRC(Design Rule Check)를 통과하는 Layout을 만드는 것이 아니라,
각 Design Rule이 존재하는 공정적·물리적 이유(Process & Device Physics)를 함께 이해하는 것을 목표로 하였다.

---

# Objectives

- GPDK090 Design Rule 이해
- Cadence Virtuoso Layout Editor 사용
- DRC(Design Rule Check) 수행
- 다양한 Design Rule 위반 사례(Layout Error) 제작
- Rule의 공정적 의미와 Device Physics 이해

---

# Environment

- Cadence Virtuoso
- GPDK090 PDK
- Assura DRC

---

# Learning Process

## 1. GPDK090 Design Rule

먼저 GPDK090에서 제공하는 Design Rule Manual(DRM)을 기반으로
각 Layer별 Rule을 학습하였다.

주요 Rule은 다음과 같다.

- Poly Width
- Poly Spacing
- Poly Extension
- Poly Density
- Metal Width
- Metal Spacing
- Via Width
- Via Spacing
- Metal Enclosure
- Contact Enclosure
- Implant Enclosure
- Antenna Rule

각 Rule은 단순한 제조 제한사항이 아니라,

- Lithography
- Overlay Margin
- Etching
- CMP
- Contact Resistance
- Plasma Charging

등 실제 반도체 공정에서 발생하는 문제를 방지하기 위해 존재한다.

---

## 2. Cadence Virtuoso Layout

Cadence Virtuoso Layout Editor를 이용하여

- NMOS
- PMOS
- Contact
- Via
- Poly
- Metal

등을 직접 배치하였다.

각 Layer의 Width, Spacing, Enclosure를 변경하며
Design Rule이 어떻게 적용되는지 확인하였다.

---

## 3. Design Rule Check (DRC)

Layout을 작성한 후 Assura DRC를 이용하여 Rule을 검증하였다.

DRC Error가 발생하면

- Rule Number 확인
- Error Location 확인
- Layout 수정

과정을 반복하여 모든 Rule을 만족하도록 수정하였다.

이를 통해 실제 Layout 개발 과정에서 반복적인 Verification 과정이 매우 중요함을 확인하였다.

---

# Topics Studied

## Poly Rules

학습한 주요 Poly Rule

- Minimum Width
- Minimum Spacing
- Poly Extension
- Poly End Spacing
- Poly Density
- Poly Area Rule

각 Rule의 공정적 의미와 Layout 예제를 함께 분석하였다.

---

## Contact Rule

Contact Rule에서는

- Contact Size
- Contact Spacing
- Contact Enclosure

뿐만 아니라

왜 Contact 아래를 충분한 Implant가 감싸야 하는지까지 함께 학습하였다.

### Physical Background

Metal-Semiconductor Contact는 이상적으로는

- Schottky Contact
- Ohmic Contact

으로 구분된다.

실제 CMOS에서는 Interface State(Fermi Level Pinning) 때문에 대부분 Schottky Barrier가 형성된다.

고농도 N+/P+ Implant를 사용하면

```
도핑 증가
↓

Depletion Width 감소

↓

Tunneling 증가

↓

Ohmic Contact
```

가 형성되어 Contact Resistance를 크게 줄일 수 있다.

따라서 Contact Enclosure Rule은

Overlay Error가 존재하더라도

Contact 하부 전체가 고농도 Implant 영역 안에 존재하도록 하기 위한 Rule이다.

---

## Antenna Rule

Antenna Rule도 실습하였다.

Plasma Etching 과정에서 Metal이나 Poly가 안테나처럼 동작하여

Floating Gate에 전하가 축적될 수 있으며,

Gate Oxide가 Breakdown되는 문제가 발생할 수 있다.

이를 방지하기 위해

Antenna Ratio를 계산하고

Poly Contact를 증가시켜 의도적으로 Rule Violation을 발생시킨 후

DRC 결과를 확인하였다.

---

## Via Rules

Via Rule에서는

- Minimum Via Width
- Via Spacing
- Via Array Rule
- Metal Enclosure

등을 Layout으로 구현하였다.

Via가 충분히 둘러싸이지 않으면

Overlay 오차나 Etching 오차로 인해 Open 또는 높은 Via Resistance가 발생할 수 있음을 확인하였다.

---

## Metal Rule

Metal Layer에서는

- Minimum Width
- Minimum Spacing
- Metal Density

Rule을 실습하였다.

Metal Density Rule은

CMP(Chemical Mechanical Polishing) 과정에서

Dishing 및 Erosion 현상을 방지하기 위해 존재한다.

---

# Device Physics Studied

Layout Rule을 이해하기 위해 다음 Device Physics도 함께 학습하였다.

- Schottky Contact
- Ohmic Contact
- Schottky-Mott Rule
- Fermi Level Pinning
- Interface State
- Surface State
- Thermionic Emission
- Thermionic Field Emission
- Field Emission
- WKB Approximation
- Schrödinger Equation
- Contact Resistance
- Depletion Width
- Tunneling

이를 통해 단순한 Layout Rule이 아니라,

공정과 소자 물리가 함께 반영된 결과임을 이해할 수 있었다.

---

# What I Learned

이번 실습을 통해

- Layout 작성보다 Rule의 의미를 이해하는 것이 더욱 중요하다는 점
- DRC는 단순한 오류 검사가 아니라 제조 가능성을 검증하는 과정이라는 점
- Design Rule은 Lithography, Overlay, CMP, Contact Physics 등 다양한 공정 문제를 해결하기 위해 존재한다는 점
- Device Physics를 이해하면 Layout Rule의 목적을 더욱 명확하게 이해할 수 있다는 점을 배울 수 있었다.

---

# Future Work

앞으로는

- Complete Standard Cell Layout
- Analog Layout
- Matching Technique
- Guard Ring
- Latch-up Prevention
- Comparator Layout
- Two-Stage OP Amp Layout

등으로 학습 범위를 확장할 예정이다.
