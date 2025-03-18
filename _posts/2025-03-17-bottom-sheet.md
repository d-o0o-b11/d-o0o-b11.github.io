---
title: 바텀 시트
description: 세번째 고난
# author: d_o0o_b
categories: [Minime]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요

React Native에서 바텀 시트를 구현하는 과정에서 두 가지 주요 문제를 겪었습니다.

1. 바텀 시트가 탭 위로 올라와야 한다.
2. 바텀 시트에서 TextInput을 사용할 수 있어야 한다.

이번 글에서는 이 문제를 해결하는 과정과 고민했던 부분을 정리해보겠습니다.



## 구현해야하는 화면

![Image](https://github.com/user-attachments/assets/0ed578e9-b307-4010-8eb1-9061d7e609f7?raw=true){: w='400' }


## 문제 1. 바텀 시트가 탭을 가려야 한다.

바텀 시트 라이브러리인 @gorhom/bottom-sheet을 사용하여 구현하려 했습니다.

```
@gorhom/bottom-sheet
```

하지만 기본적으로 바텀 시트가 탭보다 아래에서 나타나며 원하는 UI처럼 탭을 가리지 않았습니다.
공식 문서의 예제 코드로는 해결할 수 없었고 여러 시도 끝에 해당 라이브러리를 활용한 강의를 찾아 해결할 수 있었습니다.

### 해결 방법

`<BottomSheetModalProvider>`의 위치가 중요합니다. 이를 올바르게 배치해야 바텀 시트가 탭을 가릴 수 있습니다.

[참고 유튜브](https://youtu.be/oIEykI5oagI?si=917g4yysij1ZbBU1)



## 문제 2. 바텀 시트 높이 동적 조정

바텀 시트 높이 지정 자체는 어렵지 않습니다. 하지만 TextInput을 포함해야 하는 UI에서 문제가 발생했습니다.

### 문제 발생 원인

1. 키보드보다 바텀 시트의 높이가 낮다면 키보드가 올라오지 못합니다.
2. 반대로 바텀 시트 높이를 너무 크게 설정하면 화면의 반 이상이 여백입니다.

### 해결 방법

핸드폰 화면 높이를 가져와 키보드가 올라온 경우와 그렇지 않은 경우를 나누어 바텀 시트의 높이를 동적으로 설정했습니다.

```
const screenHeight = Dimensions.get('window').height;
const keyboardHeight = useKeyboardHeight();
const sheetHeight = keyboardHeight > 0 ? screenHeight * 0.6 : screenHeight * 0.4;
```

이 방식으로 어느 정도 조정은 가능하지만 가끔 바텀 시트의 높이 계산이 씹히거나 키보드가 정상적으로 올라오지 않는 문제가 발생합니다.

![Image](https://github.com/user-attachments/assets/d7a0e207-d333-40bc-bb36-648efa9a2f04?raw=true){: w='300' }


## 고민거리

1. 바텀 시트 라이브러리를 계속 사용할 것인가?

    - 원하는 대로 동작하지 않을 때마다 해결하는 데 시간이 걸린다.
    - 직접 구현하면 더 유연하게 컨트롤 가능하지만, 개발 및 유지보수 비용 증가

2. 바텀 시트의 높이 조정 방식 최적화

    - 현재 방식이 완벽하지 않음 → 더 안정적인 계산 방식 필요
    - 다른 라이즈러리 찾아보기


## 마무리 

현재 구현된 방식은 기본적인 동작을 수행하지만 완벽하지 않기에 추가적인 개선이 필요합니다. 

바텀 시트를 직접 구현하는 것이 더 나을지, 라이브러리를 계속 활용하면서 최적화할지를 고민 중입니다. 앞으로도 지속적으로 테스트하고 최적의 해결책을 찾아볼 계획입니다.