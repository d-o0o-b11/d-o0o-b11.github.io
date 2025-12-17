---
title: 뒤로가기 핸들링, 로그아웃 에러
description: 어려운 뒤로가기,,
# author: d_o0o_b
categories: [OrbitMe]
tags: [typography]
# pin: true
math: true
mermaid: true
---


## 개요

출시 직후 발견한 심각한 UX 문제가 있었습니다.

바로 **뒤로가기 플로우**였습니다.

<br/>

## 발견한 문제들

### 1. 로그아웃 후 뒤로가기

**잘못된 플로우:**
1. 설정 화면에서 로그아웃 클릭
2. 로그인 화면으로 이동
3. 뒤로가기 버튼 누름
4. **다시 설정 화면으로 이동!** 😱

**문제:**
- 로그아웃했는데 로그인이 필요한 화면으로 돌아감
- 회원탈퇴도 동일한 문제
- 사용자 혼란 발생

### 2. 탭에서 뒤로가기

OrbitMe는 3개의 탭으로 구성되어 있습니다.

**기대하는 동작:**
- 메인 탭에서 뒤로가기 → 앱 종료 (Android) 또는 동작 없음 (iOS)

**실제 동작:**
- 이전 화면으로 이동
- 네비게이션 스택이 꼬임

<br/>

## 문제의 원인

### Navigation Stack 관리 실패

React Navigation을 사용하면서 **네비게이션 스택을 제대로 관리하지 못했습니다**.

**로그아웃 시 문제:**
<!-- ```javascript
// 잘못된 방법
navigation.navigate('Login')

// 문제: 이전 스택이 남아있음
// Stack: [Setting] → [Login]
// 뒤로가기 시 Setting으로 돌아감
``` -->

로그아웃 시 단순히 navigate만 했기 때문에
이전 화면 기록이 남아있었습니다.

<br/>

## 해결 방법: Android vs iOS

### Android: 앱 종료 구현

Android는 **BackHandler API**를 사용해서 뒤로가기를 제어할 수 있습니다.

**구현:**
<!-- ```javascript
import { BackHandler } from 'react-native'

useEffect(() => {
  const backHandler = BackHandler.addEventListener(
    'hardwareBackPress',
    () => {
      if (shouldExitApp) {
        BackHandler.exitApp()
        return true
      }
      return false
    }
  )

  return () => backHandler.remove()
}, [])
``` -->

**동작:**
- 메인 탭에서 뒤로가기 → 앱 종료
- 자연스러운 사용자 경험

### iOS: 뒤로가기 차단

하지만 iOS는 상황이 달랐습니다.

**Apple 가이드라인:**
> 앱을 프로그래밍 방식으로 종료하는 것은 금지됩니다.
> 사용자가 앱이 크래시 난 것으로 오해할 수 있기 때문입니다.

**다른 앱들은 어떻게 할까?**

여러 앱을 테스트해본 결과:
- Instagram, Twitter, 카카오톡 등
- 메인 화면에서 뒤로가기 제스처를 **차단**
- 아무 동작도 일어나지 않음

**iOS 구현:**
<!-- ```javascript
// React Navigation에서 제스처 비활성화
<Stack.Screen
  name="MainTabs"
  component={MainTabs}
  options={{
    gestureEnabled: false,
    headerLeft: () => null
  }}
/>
``` -->

**동작:**
- 메인 탭에서 뒤로 스와이프 → 무시
- Apple 가이드라인 준수

<br/>

## 로그아웃 플로우 수정

### Navigation Stack 초기화

로그아웃/회원탈퇴 시 **스택을 완전히 초기화**해야 했습니다.

<!-- **수정 전:**
```javascript
// 스택이 남아있음
navigation.navigate('Login')
```

**수정 후:**
```javascript
// 스택 초기화
navigation.reset({
  index: 0,
  routes: [{ name: 'Login' }],
})
``` -->

**결과:**
- 로그인 화면에서 뒤로가기 불가
- 더 이상 설정 화면으로 돌아가지 않음

<br/>

## 플랫폼별 뒤로가기 정책

### Android

**특징:**
- 하드웨어 뒤로가기 버튼 존재
- BackHandler로 제어 가능
- 앱 종료가 자연스러움

**구현 포인트:**
- 메인 화면에서 뒤로가기 → 앱 종료
- 서브 화면에서 뒤로가기 → 이전 화면

### iOS

**특징:**
- 제스처 기반 네비게이션
- 프로그래밍 방식 앱 종료 금지
- 뒤로가기 차단이 표준

**구현 포인트:**
- 메인 화면에서 제스처 비활성화
- 불필요한 뒤로가기 방지

<br/>

## 테스트 과정

### 다른 앱 연구

**조사한 앱들:**
- Instagram
- Twitter (X)
- 카카오톡
- 네이버
- 당근마켓

**공통점:**
- 모두 메인 화면에서 뒤로가기 차단 (iOS)
- Android는 앱 종료 구현

### 실제 테스트

**시나리오:**
1. 로그아웃 후 뒤로가기 테스트
2. 회원탈퇴 후 뒤로가기 테스트
3. 각 탭에서 뒤로가기 테스트
4. 네비게이션 스택 깊이별 테스트

**결과:**
모든 시나리오에서 의도한 대로 동작 확인!

<br/>

## 배운 점

### 1. Navigation은 생각보다 복잡하다

**착각:**
"navigate만 하면 되겠지"

**현실:**
- Stack 관리
- 플랫폼별 차이
- 제스처 제어
- 상태 초기화

모든 것을 고려해야 합니다.

### 2. 플랫폼 가이드라인 준수

iOS에서 앱을 강제 종료하면 안 된다는 것을
처음 알았습니다.

**교훈:**
각 플랫폼의 Human Interface Guidelines를
반드시 숙지해야 합니다.

### 3. 다른 앱에서 배우기

막막할 때는 **잘 만들어진 앱을 연구**하는 것이
가장 빠른 답입니다.

<br/>

## 마무리

작은 UX 문제였지만, 사용자 경험에는 큰 영향을 미치는 부분이었습니다.

뒤로가기 하나에도
- 플랫폼별 차이
- 가이드라인
- 사용자 기대치

모두 고려해야 한다는 것을 배웠습니다.

**디테일이 완성도를 만듭니다.** 🎯
