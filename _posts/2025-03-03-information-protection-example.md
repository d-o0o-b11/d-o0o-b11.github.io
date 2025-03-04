---
title: 정보 보호 적용
description: 최대한 보호하자
# author: d_o0o_b
categories: [Dev-Notes, Practice]
tags: [typography]
# pin: true
math: true
mermaid: true
# image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---

## JWT 토큰 사용
> 인증(Authentication) & 책임 추적성(Accountability) 

웹 애플리케이션에서 JWT(Json Web Token)을 활용하여 사용자 인증을 구현했습니다.
로그인 성공 시 서버가 JWT 토큰을 발급하고 이후 요청마다 이 토큰을 포함하여 인증합니다.

### 적용된 보안 원칙
✔ 인증 (Authentication)

사용자가 로그인하면 JWT 토큰을 발급하여 신원을 확인합니다.
이후 요청에서 토큰을 사용해 사용자를 인증합니다. (비밀번호 재입력 없이)


✔ 책임 추적성 (Accountability)

JWT 토큰에 사용자 정보와 권한 정보가 포함됩니다.
로그를 남기면 "누가, 언제, 무엇을 요청했는지" 추적이 가능합니다.


### 보안 강화 포인트

- JWT에 서명을 추가하여 위/변조를 방지하였습니다. (HMAC 또는 RSA 사용) <-------
- 토큰 만료 시간을 설정하여 탈취가 되더라도 피해를 최소화하도록 하였습니다.
- Access Token(AT) & Refresh Token(RT) 관리
    - AT 만료 시간 짧게 설정
    - RT는 DB에 저장하여 서버에서만 알 수 있도록 관리
    - AT 블랙리스트 적용하여 재사용 방지


> 사용자의 신원을 확인하고 모든 요청을 추적 가능하도록 강화했습니다.
{: .prompt-info }

<br/>


## AWS WAF 사용
> 가용성(Availability) & 기밀성(Confidentiality)

AWS WAF(Web Application Firewall)를 활용하여 무작위 요청 및 악성 트래픽을 차단했습니다.
SQL Injection, XSS, DDoS 등의 공격을 방어하는 역할을 수행합니다.


### 적용된 보안 원칙
✔ 가용성 (Availability)

악의적인 트래픽을 차단하여 서비스가 정상적으로 운영되도록 보장합니다.
무작위 요청이나 DDoS 공격으로 인해 서버가 다운되지 않도록 합니다.

✔ 기밀성 (Confidentiality)

공격자가 불법적으로 데이터를 조회하거나 조작하지 못하도록 합니다.


### 보안 강화 포인트

- 커스텀 규칙 적용
    - 특정 국가 IP 차단
    - Admin Page 접근은 회사 내부 IP로만 제한
    - 초당 요청 수 제한
- 공격 패턴 분석 & 대응
    - 에러 발생 시 슬랙 알림을 통해 공격 패턴을 파악한 후 규칙을 수정/추가하여 무작위 요청을 완전히 차단하였습니다! 불필요한 에러 알림이 사라지고 서비스의 안정성을 확보했습니다.


> 공격자의 무작위 요청을 차단하고 서비스 안정성을 확보했습니다.
{: .prompt-info }

<br/>

## HTTP → HTTPS로 변환 (CA에서 인증한 SSL/TLS 적용)
> 기밀성(Confidentiality) & 부인 방지(Non-repudiation)

기존 HTTP 통신에서 HTTPS로 전환하여 보안을 강화했습니다.
CA(Certificate Authority)에서 인증서를 발급받아 TLS(SSL)를 적용하고 데이터 보호를 강화했습니다.


### 적용된 보안 원칙
✔ 기밀성 (Confidentiality)

HTTPS를 사용하면 통신이 암호화되어 공격자가 데이터를 가로채더라도 내용을 알 수 없습니다.
사용자의 로그인 정보, 결제 정보 등을 안전하게 보호합니다.


✔ 부인 방지 (Non-repudiation)

CA에서 발급한 디지털 인증서를 사용하여 신뢰성을 보장합니다.
클라이언트가 "이 서버가 진짜인지" 확인 가능하며 피싱(Phishing) 공격을 방지할 수 있습니다.
<!-- 서버 입장에서도 "나는 신뢰할 수 있는 인증을 받은 사이트"라고 증명 가능 -->


### 보안 강화 포인트

HSTS(HTTP Strict Transport Security) 적용하여 HTTPS 강제
<!-- TLS 버전 업그레이드 (최신 TLS 1.3 사용, 취약한 TLS 1.0/1.1 비활성화)
Let’s Encrypt 같은 자동 인증서 갱신 시스템 활용 -->

> 데이터를 암호화하여 보호하고 신뢰할 수 있는 서버임을 보장 하였습니다.
{: .prompt-info }

<br/>

## 결론
보안은 한 번 적용했다고 해서 끝나는 것이 아닙니다..
지속적인 모니터링과 개선을 통해 더욱 강력한 보안 환경을 만들어야 합니다.