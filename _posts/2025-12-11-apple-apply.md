---
title: iOS 출시
description: 금방되는 iOS
# author: d_o0o_b
categories: [OrbitMe]
tags: [typography]
# pin: true
math: true
mermaid: true
---


## 개요

TestFlight 테스트까지 마치고 드디어 **App Store 심사**를 신청했습니다!

Android는 베타테스트 기간이 필수였지만
iOS는 TestFlight로 충분히 테스트했다면 바로 출시 심사를 받을 수 있습니다.

**준비 완료:**
- [x] Apple 로그인 구현 
- [x] TestFlight 테스트 완료 
- [x] 스크린샷 준비 
- [x] 앱 설명 작성 

긴장되는 마음으로 **심사 제출** 버튼을 눌렀습니다.

<br/>

## 1차 시도: 실패

![Image](https://github.com/user-attachments/assets/823974d3-ddbf-4c08-8292-621f2771d504?raw=true)

### 리젝 사유

**Guideline 2.3.7 - Performance - Accurate Metadata**

아이패드용 스크린샷 영역에 아이폰 스크린샷이 들어가있다는 이유였습니다.

### 혼란스러웠던 점

사실 아이패드용 이미지는 **필수가 아닙니다**.

하지만 저는 App Store Connect에서 아이패드 스크린샷을 요구해서 넣었는데,
알고보니 **설정 파일에서 iPad를 지원 기기에서 제외**해야 했습니다.


<!-- **해결:**
```
UIDeviceFamily를 iPhone only로 설정
→ iPad 스크린샷 제출 불필요
``` -->

"이런 게 있는지도 몰랐습니다..." 😅

iPad를 제외하고 iPhone만 지원하도록 설정을 변경했습니다.


<br/>

## 2차 시도: 성공!

![Image](https://github.com/user-attachments/assets/03de6609-ba0e-4911-8a4f-3d4bde7a8d63?raw=true)

**"Ready for Sale"**

드디어 성공했습니다! 🎉


이미지 문제 외에는 다른 이슈가 없었습니다.
Apple 로그인, 개인정보처리방침, 앱 완성도 모두 문제없이 통과했습니다!

<br/>

## Android vs iOS 심사 비교

### Android (Google Play)

**특징**
- 심사 빠름 (몇 시간)
- 베타테스트 필수 (2주)
- 최소 12명 테스터 필요

### iOS (App Store)

**특징**
- 베타테스트 선택적 (TestFlight 활용)
- 심사 기준 엄격
- 한 번 통과하면 신뢰도 높음

두 플랫폼 모두 장단점이 있지만,
iOS 심사를 통과하니 더 큰 성취감이 느껴졌습니다.

<br/>

## 양 플랫폼 출시 완료

**Google Play:** ✅ 출시 완료
**App Store:** ✅ 출시 완료

드디어 Android와 iOS 모두에서 OrbitMe를 만나볼 수 있게 되었습니다!

**다운로드:**
- [📱 Google Play](https://play.google.com/store/apps/details?id=com.chajimin.orbitme)
- [🍎 App Store](https://apps.apple.com/us/app/orbitme/id6755960465)

<br/>

## 마무리

3개월간의 개발, 베타테스트, 그리고 드디어 양 플랫폼 출시까지!

$129의 Apple Developer 비용이 부담스러웠지만
iOS 사용자분들도 OrbitMe를 사용하실 수 있게 되어 뿌듯합니다.

이제 시작입니다.
더 많은 기능과 안정성 개선으로 찾아뵙겠습니다!

**당신만의 궤도를 그려보세요!** 🚀✨
