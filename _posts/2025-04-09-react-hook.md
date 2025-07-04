---
title: 프론트 코드 정리
description: 백엔드 개발자의 프론트 삽질기
# author: d_o0o_b
categories: [Minime]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 백엔드 개발자의 프론트 삽질기

React Native를 처음 접했을 때는 일단 **작동만 되면 된다**는 생각으로 코드를 작성했습니다.  
한 파일에 `fetch`, `useEffect`, `useState`, `useRef`를 전부 넣고 스타일도 파일 하단에 쭉 작성했습니다.

> 그땐 몰랐습니다.. 이 코드가 나중에 제 발목을 잡을 줄은.. (멈춰 과거야)

처음에는 감부터 익히는 게 우선이라고 생각히여 코드 구조나 가독성은 크게 신경 쓰지 않았습니다.   
하지만 기능이 늘고 로직이 복잡해지면서 **유지보수의 어려움**이 드러나기 시작했습니다.  
MVP 개발이 어느 정도 마무리된 시점에서 리팩토링을 진행하였습니다.


<br/>


## 리팩토링 시작: 구조화

### 1. 컴포넌트 분리

초기에는 하나의 화면에 필요한 UI를 모두 한 파일에 작성했습니다.  
공통 UI 요소가 반복되면서 이를 **재사용 가능한 컴포넌트**로 분리하였습니다.

- 버튼, 모달, 카드, 리스트 등 반복되는 UI 분리
- 중복 코드 제거 및 props 기반 커스터마이징

> 중복 코드를 줄이고 재사용성을 높일 수 있었습니다.
{: .prompt-info }


---


### 2. API 로직 분리

비동기 요청은 화면 컴포넌트 내에 직접 작성되어 있었고 `try-catch` 중복 처리나 URL 하드코딩 문제도 많았습니다.

- `api.ts` 파일로 서버 통신 로직 모듈화
- 공통 에러 처리 및 응답 타입 정의
- 함수 단위로 API 명세 관리

> 이후 API 호출이 **예측 가능**하고 **중앙 집중화**되어 유지보수가 쉬워졌습니다.
{: .prompt-info }


---


### 3. 타입 정의 강화

초기에는 `any`나 암묵적 타입 추론에 의존했습니다.  
이로 인해 코드를 다시 볼 때마다 "이 변수가 어떤 값이였지?" 라는 생각을 자주 하게 되었습니다.

- 서버 응답 타입 정의
- 컴포넌트 props 명시
- 로컬 상태 타입 정의

> 이후부터는 **타입을 먼저** 정의하고 **구현을 시작**하는 방식으로 개발 흐름을 전환했습니다.  
{: .prompt-info }


---


### 4. 텍스트 컴포넌트화

앱 전반에서 유사한 `Text` 스타일이 반복되어 **디자인 시스템을 반영한 커스텀 텍스트 컴포넌트**를 만들었습니다.

먼저 디자이너와 협업하여 `fontSize`, `lineHeight`, `color`, `fontFamily` 등의 기준을 정하고 이를 기반으로 컴포넌트를 설계하였습니다.

컴포넌트에서는 `variant`와 `color` 속성으로 미리 정의된 스타일을 쉽게 적용할 수 있도록 구성했으며 필요한 경우 `style` prop을 통해 개별 커스터마이징도 가능하게 하였습니다.


```tsx
<CustomText
   variant={TextStyle.Header3SB}
   color={Colors.TextGray0}
>
나만의 만다라트를 작성하고 한 눈에 확인할 수 있어요.
</CustomText>
```

`Colors` 등의 컬러 팔레트를 상수로 정의하여 디자인 시스템 내 색상을 명확하게 관리하고 실수 없이 일관되게 사용할 수 있도록 했습니다.

> 이 구조 덕분에 텍스트 스타일의 **일관성**을 유지할 수 있었고 디자이너와의 **협업**에서도 명확한 기준이 생겨 커뮤니케이션이 원활해졌습니다.
{: .prompt-info }

<br/>


## 리팩토링 후 변화

- 코드 가독성이 눈에 띄게 향상되었습니다.
- 기능별로 책임이 분리되어 디버깅이 쉬워졌습니다.
- 재사용 가능한 코드 베이스를 확보했습니다.
- 새로운 기능을 추가할 때 부담이 줄어들었습니다.



<br/>


## 사용한 React 훅 정리

| Hook        | 용도                                 |
| ----------- | ---------------------------------- |
| `useState`  | 기본 상태 관리 <small>(입력값, 클릭 여부 등)</small>            |
| `useEffect` | 컴포넌트 생명주기 기반 사이드 이펙트 처리 <small>(API 호출 등)</small> |
| `useRef`    | 렌더링과 무관한 값 저장 및 바텀시트 제어            |


<br/>


## fetch vs axios
API 요청 방식으로 `fetch`와 `axios`중 어떤 것을 사용할지 고민하였습니다.

| 항목    | fetch             | axios                 |
| ----- | ----------------- | --------------------- |
| 기본 제공 | O <small>(브라우저 내장)</small>       | X <small>(외부 라이브러리 설치 필요)</small>    |
| 응답 파싱 | 수동 <small>(`res.json()` 직접 호출)</small> | 자동 JSON 파싱                   |
| 에러 처리 | 수동 처리            | HTTP 상태코드 기반 자동 throw |


<!-- | 응답 처리     | `res.json()` 등 직접 파싱     | 자동으로 JSON 파싱               | -->
<!-- | 에러 처리     | 200이 아니어도 에러 아님       | 상태코드 기준으로 에러 throw     | -->
<!-- | 인터셉터 등 고급 기능 | ❌ (직접 구현해야 함)       | ✅ (인터셉터, 취소 토큰 등 제공) | -->

`fetch`도 충분히 사용할 수 있었지만 `axios`의 인터셉터, 요청 취소 등의 고급 기능이 유용해 보였습니다.   
지금은 `fetch`로 일관되게 사용 중이지만 추후 상황에 따라 `axios` 기반으로 리팩토링할 여지를 열어두고 있습니다.


<br/>


## 회고: 구조는 선택이 아닌 필수였다.
"일단 감부터 잡자!"의 접근은 단기적으로는 괜찮았지만 일정 규모를 넘어서자 기술 부채로 되돌아왔습니다.

구조 없는 코드는 스스로에게 가장 큰 적이 된다는 사실을 이번에 절실히 느꼈습니다. 😭

구조를 고민하면서 얻은 건 단순히 깔끔한 코드만이 아니었습니다.
- 협업에 강한 코드
- 확장 가능한 구조
- 미래를 고려한 설계 습관

<br/>

완벽한 구조는 아니지만 계속해서 고민하고 개선해 나가도록 하겠습니다.