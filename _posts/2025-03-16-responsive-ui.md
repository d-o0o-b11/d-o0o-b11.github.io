---
title: 반응형 UI
description: 두번째 고난
# author: d_o0o_b
categories: [Minime]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요

웹사이트를 개발할 때는 `@media` 쿼리를 이용해 PC, 태블릿, 모바일 화면에 맞춰 손쉽게 반응형을 구현했었습니다.  
하지만 앱에서는 다른 접근이 필요했습니다.
<!-- 하지만 앱에서는 이 방식이 적절하지 않다는 생각이 들었습니다. -->

<br/>

## 왜 웹에서 쓰던 방식이 앱에서는 적절하지 않았을까?

웹에서는 CSS를 사용하기 때문에 `@media`를 통해 화면 크기에 따라 스타일을 다르게 줄 수 있습니다.  
하지만 **React Native는 스타일을 CSS 파일이 아니라 JavaScript 객체로 정의**합니다. 그래서 `@media` 같은 CSS 기능을 쓸 수 없습니다.

> 즉, React Native는 CSS 문법을 흉내 내지만 **실제로는 CSS가 아닌, JS 객체 기반의 스타일링 시스템**을 사용합니다.
{: .prompt-info }

👉 [공식 문서 보러가기](https://reactnative.dev/docs/style)


### 만다라트 3×3 블록 반응형

저희 앱은 '만다라트 목표 설정'이라는 특성상 **3×3 격자 형태의 블록 UI**가 화면에 보여야 했습니다.  


#### 웹 
웹에서는 `display: grid`를 사용하면 행과 열을 지정해서 손쉽게 격자 구조를 만들 수 있었습니다.

```css
display: grid;
grid-template-columns: repeat(3, 1fr);
gap: 10px;
```

---

#### React Native
하지만 React Native에는 grid 시스템이 없습니다. 대신 flex 레이아웃만 지원합니다.

```js
{
  flexDirection: 'row',
  justifyContent: 'space-between'
}
```

---

#### display: grid vs flex 

| 항목              | `grid`       | `flex`                   |
| --------------- | ------------ | ------------------------ |
| 주 사용 목적         | 2차원 (행+열) 배치 | 1차원 (행 또는 열) 배치          |
| 배치 방식           | 행/열을 명시해 정렬  | 한 방향으로 정렬 후 요소를 유동적으로 배치 |
| 반응형 처리          | 쉽게 구성 가능     | 수동 계산 필요                 |
| React Native 지원 | X        | O                     |


> 위와 같이 `flex`, `width`, `height` 값을 계산해가며 UI를 구성했습니다.
{: .prompt-info }


<br/>

## 동적인 width, height 조정

디바이스 크기에 맞춰 동적으로 스타일을 조정하는 방식으로 해결했습니다.

### Dimensions API 활용

React Native에서 `Dimensions`를 사용하면 기기의 width와 height 값을 가져올 수 있습니다.
이를 활용해 각 블록의 크기를 동적으로 설정했습니다.

```js
import { Dimensions } from 'react-native';

const blockSize = width * 0.3; // 화면 가로 크기의 30%를 블록 크기로 설정
const blockMargin = width * 0.02; // 화면 크기에 따라 margin도 유동적으로 조정
```

> 화면 가로 크기에 비례해 블록의 크기와 여백을 조정해 나갔습니다.
{: .prompt-info }

<br/>

## 고민거리

### 고민 1: 반응형 라이브러리를 활용하면 더 최적화할 수 있을까?
반응형 UI를 쉽게 구현할 수 있게 도와주는 라이브러리들도 존재합니다.

그중 대표적인 두 가지는 다음과 같습니다.
- `react-native-responsive-screen` → % 단위로 width, height, fontSize 등을 계산
- `react-native-size-matters` → scale, moderateScale을 활용해 크기를 동적으로 변경

> 그런데 왜 직접 계산했을까?
> <br/>
> **최대한 라이브러리 의존성을 줄이고 싶었습니다.**
<!-- > 직접 계산하면 상황에 맞게 더욱 최적화된 커스텀 로직을 적용할 수 있다는 점도 고려했습니다. -->
> 직접 계산하면 더 유연하게 최적화할 수 있고 유지보수 시 예기치 못한 버전 충돌을 피할 수 있다고 생각했습니다.
{: .prompt-info }

---

### 고민 2: 카드 비율 vs 폰트 크기
카드 비율을 유지하자니 유저가 시스템 설정에서 폰트 크기를 키운 경우 글자가 카드 밖으로 벗어나는 문제가 발생했습니다.

이런 경우 다음 두 가지 선택지 사이에서 고민이 있었습니다.
1. 카드 비율을 유지하고 폰트 크기를 고정한다.
2. 폰트 크기를 유저 설정에 따라 유동적으로 적용하고 카드 레이아웃이 깨질 수 있음을 감수한다.

> 카드 비율이 더 중요하다고 판단하여 폰트 크기를 강제로 고정해두는 방식으로 우선 해결했습니다.
{: .prompt-info }

<br/>

## 마무리 

위 방식으로 기본적인 반응형 처리는 가능했지만 아직도 개선할 부분이 많습니다.
사용자마다 핸드폰 설정은 다르게 되어있기에 이 부분은 이후 더 공부해서 최적의 반응형 방식을 찾아볼 계획입니다.




<!-- ### 고민 2: 폰트 크기, 여백도 기기에 따라 조절해야 할까?
> 가능하면 맞추는 것이 좋다!

폰트 크기와 여백도 기기에 맞춰 조정하지 않으면 다음과 같은 문제가 생길 수 있습니다.
- 작은 화면에서는 폰트가 크게 보일 수 있습니다.
- 큰 화면에서는 폰트가 작아서 가독성이 떨어질 수 있습니다.

#### 해결 방법 
- `react-native-responsive-screen`의 % 기반 크기 조정 활용
- `react-native-size-matters`의 scale을 이용해 동적 폰트 크기 적용
- `Dimensions API`를 활용한 수동 계산 -->