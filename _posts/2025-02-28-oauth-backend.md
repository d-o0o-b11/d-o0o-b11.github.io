---
title: OAuth 적용 (kakao, google)
description: OAuth 적용 (w. RT를 곁들인)
# author: d_o0o_b
# date: 2015-02-27 11:33:00 +0800
categories: [First-App-Dev, Backend]
tags: [typography]
pin: true
math: true
mermaid: true
# image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---

## 개요
사이드 프로젝트를 진행할 때, 많은 기획자분들이 카카오 소셜 로그인을 추가하길 원했습니다. 그래서 카카오 소셜 로그인 코드를 보일러플레이트로 만들어 둘 정도로 유용하게 활용해 왔습니다. 기존에 사용하던 보일러플레이트가 최선이라 믿고 있었지만, 이번 앱 개발에서 프론트엔드와 백엔드를 모두 맡으면서 더 나은 방법이 있을 것 같아 기존 구조를 다시 살펴보게 되었습니다.

## 일반적인 OAuth 구조
![Image](https://github.com/user-attachments/assets/d13becae-b54d-48f8-bff2-9237cc1b502f?raw=true)

## 내가 추구했던 OAuth 구조
![Image](https://github.com/user-attachments/assets/427fabb5-3f13-462d-8af5-020756a2e3df?raw=true)

<!-- ## 내가 추구했던 OAuth 구조
제가 선택한 구조는 [일반적인 OAuth 구조 2](#일반적인-oauth-구조-2)입니다. -->

### 서버에서 처리하는 이유
보안적인 측면에 대해 고려하였습니다. API 키나 클라이언트 시크릿 같은 중요한 정보가 브라우저 환경에서 노출될 위험이 있기 때문에 서버에서 이를 관리하는 것이 보다 안전하다고 판단했습니다. 
이 방식은 이후 다른 OAuth Provider Service(Google, Facebook 등)와의 확장에도 용이한 구조였습니다.



## 위 구조로 했을 때 앱에서 겪은 문제
소셜 로그인 개발을 처음 계획할 때는 SDK를 활용한 인앱 로그인을 고려했습니다. 하지만, 인앱 로그인을 구현하려면 환경 변수를 프론트엔드에서 관리해야 했고 서버에서 idToken을 검증하는 방식으로 구현되어야 합니다.

그러나 앱 개발이 처음이었던 저에게 인앱 로그인은 러닝 커브가 높았습니다. 
게다가 출시 일정이 정해져 있었기 때문에 빠른 해결책을 찾기 위해 로그인 방식을 웹뷰로 변경했습니다. 웹뷰를 사용하면 기존의 보일러플레이트 구조를 그대로 활용할 수 있었습니다.

이 과정에서 제가 집중한 부분은 Refresh Token(RT) 관리 방식이었습니다. 1인 개발자로서 보안에 신경을 많이 쓰고 있었고, RT 반환 여부에 대한 보안측면에서 생각이 필요했습니다.

## Refresh Token(RT) 반환이 필요한가?
과거에는 AT와 RT를 모두 반환하는 것이 일반적이었습니다. AT가 만료되면 RT를 이용해 새로운 AT를 발급받아야 했고 많은 개발자들이 이 방식을 사용했기 때문에 저 역시 이것이 정답이라 생각했습니다.

그러나 최근에는 코드에 절대적인 정답이 없다는 점을 깨닫게 되었습니다. 현재 제가 고려하는 방식은 RT를 클라이언트에 반환하지 않고 서버에서 직접 관리하는 것입니다.

그 이유는 AT의 만료 시간을 짧게 설정하는 주된 이유가 보안 문제, 즉 탈취 가능성 때문이기 때문입니다. RT도 탈취될 가능성이 있으므로 보안상 위험 요소가 될 수 있습니다. RT는 AT보다 만료 기간이 긴데, 만약 RT가 탈취된다면 해당 기간 동안 악용될 수도 있습니다.

이러한 위험성을 고려했을 때, RT를 서버에서 안전하게 관리하는 것이 보안 측면에서 더 적절하다고 판단했습니다.

## AT만 반환하는 구조
![Image](https://github.com/user-attachments/assets/2bb02b9b-53d5-45d4-a9e7-489187af0a4c?raw=true)


## 결론
OAuth를 적용하면서 기존의 보일러플레이트가 최선이 아닐 수도 있다는 점을 깨달았습니다. 보안성을 강화하기 위해 RT를 반환하지 않고 서버에서 직접 관리하는 방식을 선택했습니다. 물론 이 방식 역시 정답은 아닐 수 있지만, 보안 측면에서 보다 안전하다고 판단했습니다.

개발에는 정답이 없고, 그때그때 상황에 맞는 최선의 선택을 해야 한다는 점을 다시금 깨닫는 경험이었습니다.

