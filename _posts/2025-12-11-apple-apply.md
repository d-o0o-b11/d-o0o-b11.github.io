---
title: iOS 출시
description: 생각보다 빠르게 끝난 iOS 심사
# author: d_o0o_b
categories: [OrbitMe]
tags: [typography]
# pin: true
math: true
mermaid: true
---


## 개요

TestFlight 테스트를 마치고
드디어 App Store 심사를 신청했습니다.

Android는 정식 출시 전
베타테스트 기간과 최소 인원 요건이 있었지만
iOS는 TestFlight로 충분히 검증되었다면
바로 심사 제출이 가능합니다.

심사 제출 전 준비 사항

- Apple 로그인 구현
- TestFlight 실기기 테스트 완료
- 앱 스크린샷 준비
- 앱 설명 및 개인정보처리방침 작성

모든 준비를 마치고
조심스럽게 심사 제출 버튼을 눌렀습니다.

<br/>

## 1차 심사: 리젝

![Image](https://github.com/user-attachments/assets/823974d3-ddbf-4c08-8292-621f2771d504?raw=true)

### 리젝 사유

**Guideline 2.3.7 - Performance - Accurate Metadata**

아이패드용 스크린샷 영역에
아이폰 스크린샷이 포함되어 있다는 이유였습니다.

### 놓치고 있었던 부분

아이패드용 스크린샷은
사실상 필수 항목이 아닙니다.

문제는 App Store Connect 설정에서
iPad가 지원 기기로 포함된 상태였다는 점이었습니다.

그 결과,

- iPad 지원 앱으로 인식
- iPad 스크린샷 요구
- iPhone 스크린샷 제출 → 메타데이터 오류

설정에서 **iPhone만 지원하도록 변경한 뒤**
iPad 관련 항목을 모두 제거했습니다.

“심사 기준을 안다고 생각했지만
설정 하나로 전혀 다른 결과가 나올 수 있구나”를 느꼈던 순간이었습니다.

<br/>


## 2차 심사: 통과

![Image](https://github.com/user-attachments/assets/03de6609-ba0e-4911-8a4f-3d4bde7a8d63?raw=true)

**"Ready for Sale"**

수정 후 재심사는
별다른 추가 요청 없이 통과되었습니다. 🎉

Apple 로그인,
개인정보 처리,
앱 완성도 관련 이슈도 모두 문제없이 넘어갔습니다.

<br/>

## Android와 iOS 심사 비교

### Google Play

- 심사 속도 빠름
- 베타테스트 필수
- 최소 테스터 인원 요구
- 출시 전 준비 단계가 길음

### App Store

- TestFlight로 사전 테스트 가능
- 심사 기준 명확하지만 엄격함
- 메타데이터와 설정에 민감함
- 한 번 통과하면 신뢰도가 높음

체감상 iOS 심사는 더 까다롭지만
그만큼 앱 전체를 다시 점검하게 만드는 과정이었습니다.

<br/>

## 양 플랫폼 출시 완료

이제 OrbitMe는
Android와 iOS 모두에서 사용할 수 있습니다.

Google Play: 출시 완료

App Store: 출시 완료

### 다운로드 링크

- [📱 Google Play](https://play.google.com/store/apps/details?id=com.chajimin.orbitme)
- [🍎 App Store](https://apps.apple.com/us/app/orbitme/id6755960465)

<br/>

## 마무리

iOS 출시는
생각보다 빠르게 끝났지만
그 과정이 가볍지는 않았습니다.

설정 하나, 스크린샷 하나까지도
모두 앱의 일부라는 것을 다시 느꼈습니다.

이제 OrbitMe는
두 플랫폼 모두에서 같은 출발선에 섰습니다.

앞으로는
기능 확장과 안정성 개선에 더 집중해보려 합니다.

각자의 궤도를 어떤 기기에서든 그릴 수 있도록! 🚀
