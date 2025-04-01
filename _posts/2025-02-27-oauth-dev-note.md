---
title: OAuth (kakao, google)
description: OAuth란?
categories: [Dev-Notes, Theory]
tags: [typography]
math: true
mermaid: true
---

## OAuth 란
Open Authorization
리소스 소유자가 사용자 자격 증명을 공유하지 않고도 타사 애플리케이션 리소스에 액세스할 수 있는 권한을 부여하는 액세스 위임에 사용되는 프로토콜입니다.

OAuth(Open Authorization)는 인증 및 권한 부여를 위한 표준화된 프레임워크입니다.
사용자가 자격 증명을 직접 공유하지 않고도 타사 애플리케이션이 특정 리소스에 접근할 수 있도록 안전하게 권한을 위임하는 방식입니다.

OAuth는 공식적으로 RFC 문서에서 **"Authorization Framework"**로 정의되지만, 프로토콜의 성격을 가진다고도 볼 수 있습니다.

그 이유는 OAuth가 **액세스 권한을 부여하는 과정(인증 흐름)**을 표준화된 규칙으로 정의하며,
이를 기반으로 여러 서비스가 일관된 방식으로 구현할 수 있도록 하기 때문입니다.

즉, OAuth는 엄격한 프로토콜만큼 강제적이지는 않지만, 인증/인가에 대한 가이드라인을 제공하여 다양한 시스템에서 표준처럼 활용됩니다.

## OAuth 생긴 유래
사용자 자격 증명을 공유하지 않아도 서비스에 접근할 수 있는 방법을 고안하다가 탄생하게 되었습니다.

## OAuth 1.0
![Image](https://github.com/user-attachments/assets/147bae6e-a516-4cfc-a4b3-f1930d96b8dc?raw=true)



### 단점
1. 복잡한 인증 과정
  - Request Token → Approved Request Token → Access Token

2. 보안 이슈
  - Access Token이 평문(암호화X)으로 전달돼서 보안에 취약

<br/>
<br/>

## OAuth 2.0
![Image](https://github.com/user-attachments/assets/4ba2d337-7531-4787-ac32-58df4bee9042?raw=true)

### 장점
1. 간단하고 안전적
  - OAuth 1.0보다 간단한 흐름
  - HTTPS를 통해 더 안전한 방식으로 토큰 처리

2. Refresh Token 사용
  - Access Token이 만료되면 Refresh Token을 통해 새로 발급 가능

<br/>

### OAuth2.0 흐름에서 중요한 포인트
1. Authorization Code는 한 번만 사용하고 Access Token은 일정 기간만 유효합니다.
2. Access Token이 만료되면 Refresh Token을 사용해 새로운 Access Token을 발급 가능합니다.
3. Client Secret은 애플리케이션 서버와 OAuth 서버 사이에서 비밀 정보로 외부에 노출되면 안됩니다.

<br/>
<br/>

## OAuth1.0 vs OAuth2.0 비교

| OAuth 1.0                    |
| :--------------------------- |
| 3단계 토큰 (Request Token → Authorization Code → Access Token)  | 
| 인증 절차가 복잡하고 보안 문제 존재  | 


| OAuth2.0                   | 
| :--------------------------- |
| 더 간단한 2단계 흐름 (Authorization Code → Access Token)  |
| Refresh Token을 사용하여 토큰 갱신 가능  | 


