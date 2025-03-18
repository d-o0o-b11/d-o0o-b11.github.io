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

웹사이트를 개발할 때는 @media 쿼리를 사용해서 PC, 태블릿, 모바일 3가지 화면 크기에 맞춰 반응형을 구현했습니다.
하지만 앱에서는 이 방식이 적절하지 않다는 생각이 들었습니다.


## 왜 웹에서 쓰던 방식이 앱에서 적절하지 않았을까?

웹에서는 @media를 활용하면 화면 크기에 따라 CSS 스타일을 다르게 적용할 수 있습니다.
하지만 React Native에서는 CSS가 아닌 JavaScript 코드로 스타일을 제어하기 때문에 @media를 직접 사용할 수 없습니다.

### 만다라트 3×3 블록 반응형

특히 가장 어려웠던 부분은 만다라트 3×3 블록을 화면 크기에 맞게 조절하는 것이었습니다.
웹에서는 `display: grid`를 활용해서 간단하게 조정할 수 있었지만,
React Native에서는 grid 시스템이 없어서 `flex`와 `width`, `height` 값을 직접 계산해야 했습니다.

## 동적인 width, height 조정

디바이스 크기에 맞춰 동적으로 스타일을 조정하는 방식으로 해결했습니다.

### Dimensions 활용

React Native에서 제공하는 Dimensions API를 사용하면 현재 기기의 width와 height 값을 가져올 수 있습니다.
이를 활용해 각 블록의 크기를 동적으로 설정했습니다.

### padding, margin 활용

단순히 width, height만 조정하는 것이 아니라 화면 크기에 따라 padding과 margin도 조정해야 했습니다.
가로/세로 비율을 계산하여 여백을 조정하는 방식으로 접근했습니다.
```
const blockSize = width * 0.3; // 화면 가로 크기의 30%를 블록 크기로 설정
const blockMargin = width * 0.02; // 화면 크기에 따라 margin도 유동적으로 조정
```

## 고민거리

1. 반응형 지원해주는 라이브러리를 활용하면 더 최적화할 수 있을까?

    반응형 지원 라이브러리를 사용하면 수동으로 width, height를 계산하는 번거로움을 줄이고 더 쉽게 반응형을 구현할 수 있습니다.
    특히 다양한 기기에서 일관된 UI를 유지하는 데 도움을 줍니다.

    추천 라이브러리
    - `react-native-responsive-screen` → 화면 크기에 맞춰 width, height, fontSize 등을 % 단위로 조정
    - `react-native-size-matters` → scale, moderateScale을 활용해 크기를 동적으로 변경

    <br/>

    >  그런데 왜 Dimensions API를 직접 사용했을까?
    > <br/>
    > 최대한 라이브러리 의존성을 줄이고 싶었습니다.
    > 또한, 라이브러리 업데이트에 따른 유지보수 부담을 줄이기 위해 직접 계산하는 방식을 선택했습니다.
    > 아직 라이브러리를 사용해보지는 않았지만, 직접 계산하면 상황에 맞게 더욱 최적화된 커스텀 로직을 적용할 수 있다는 점도 고려했습니다.
    {: .prompt-info }

<br/>

2. 폰트 크기, 여백도 기기에 맞춰 개발해야할까?
> 가능하면 맞추는 것이 좋다!

    폰트 크기와 여백도 기기에 맞춰 조정하지 않으면 다음과 같은 문제가 생길 수 있습니다.

    - 문제점

        - 작은 화면에서는 폰트가 너무 크게 보일 수 있다 <br/>
        - 큰 화면에서는 폰트가 너무 작아서 가독성이 떨어질 수 있다

    -  해결 방법

        - `react-native-responsive-screen`의 % 기반 크기 조정 활용
        - `react-native-size-matters`의 scale을 이용해 동적 폰트 크기 적용
        - `Dimensions API`로 화면 크기를 가져와 margin, padding을 동적으로 설정

## 마무리 

위 방식으로 기본적인 반응형 처리는 가능했지만 아직도 개선할 부분이 많습니다.
사용자마다 핸드폰 설정은 다르게 되어있기에 이 부분은 이후 더 공부해서 최적의 반응형 방식을 찾아볼 계획입니다.
