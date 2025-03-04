---
title: 정보 보호의 기본 원칙과 목표
description: 기밀성, 무결성, 인증, 부인 방지 등 정보 보호의 기본 원칙과 보안 목표
# author: d_o0o_b
# date: 2015-02-27 11:33:00 +0800
categories: [Dev-Notes, Theory]
tags: [typography]
pin: true
math: true
mermaid: true
# image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---

## 정보 보호(Information Security) 란?
정보의 기밀성(Confidentiality), 무결성(Integrity), 가용성(Availability)을 보장하여 인가된 사용자만 정보에 접근할 수 있도록 하고 정보가 변조되지 않으며, 필요한 순간에 사용할 수 있도록 보호하는 것

## 정보 보호의 3대 원칙 (CIA Triad)
정보 보호에서는 **기밀성(Confidentiality), 무결성(Integrity), 가용성(Availability)**을 가장 중요한 요소로 삼습니다. 이를 CIA 삼각형이라고 부르며 정보 보호의 기본 목표이자 모든 보안 정책의 핵심이 됩니다.

![Image](https://github.com/user-attachments/assets/782661b5-2142-43a4-8cb3-22cc9ecb7d69?raw=true){: w='400' }

### 1. 기밀성 (Confidentiality)
> 인가된 사용자만 정보에 접근할 수 있도록 보호한다. (암호화, 접근 제어)

기밀성이란 정보가 허가된 사람(혹은 시스템)만 접근할 수 있도록 보호하는 것을 의미합니다. 정보의 유출을 방지하는 것이 핵심 목표입니다.

<!-- 보장하는 방법
- 암호화(Encryption): 데이터를 암호화하여 허가된 사용자만 해독할 수 있도록 보호
- 접근 제어(Access Control): 사용자별 권한을 설정하여 중요 정보에 접근할 수 있는 대상을 제한
- 다중 인증(MFA, Multi-Factor Authentication): 비밀번호 외에도 OTP, 생체 인식 등을 추가로 사용하여 보안 강화
- 네트워크 보안(Firewall, VPN, IDS/IPS): 외부 공격자로부터 네트워크 트래픽을 보호 -->

ex.
- HTTPS 프로토콜을 사용한 웹사이트 보안

### 2. 무결성 (Integrity)
> 데이터가 변조되지 않도록 보호한다. (해시, 전자 서명)

무결성이란 인가되지 않은 사용자나 시스템이 데이터를 임의로 수정, 삭제, 변조하지 못하도록 보호하는 것을 의미합니다. 데이터의 정확성과 신뢰성을 유지하는 것이 핵심 목표입니다.

ex. 
- 파일 다운로드 후 제공된 해시 값(SHA-256)과 비교하여 변조 여부 확인

### 3. 가용성 (Availability)
> 인가된 사용자가 필요한 정보를 언제든지 사용할 수 있도록 보호한다. (백업, 부하 분산)

가용성이란 정보 시스템이 장애, 공격, 자연재해 등의 상황에서도 지속적으로 정상 운영될 수 있도록 보장하는 것을 의미합니다. 시스템이 안정적으로 운영되도록 유지하는 것이 핵심 목표입니다.

<br/>
<br/>

## CIA 원칙 간의 Trade-off
정보 보호에서는 기밀성, 무결성, 가용성을 동시에 최대로 유지하는 것이 이상적이지만 현실에서는 최대로 유지하는 것이 힘듭니다..
CIA 원칙을 상황에 맞게 적절히 조정하는 것이 중요하며 시스템의 목적과 사용자의 요구에 따라 보안 정책을 최적화해야 합니다.

### 기밀성 vs 가용성
보안을 강화(기밀성 증가)하면 접근이 어려워져 가용성이 떨어질 수 있음
ex.
- 은행 앱에 다중 인증(MFA)을 도입하면 보안이 강해지지만 사용자는 로그인 과정이 번거로워짐

### 무결성 vs 가용성
무결성을 강하게 유지하면 데이터를 변경하기 어렵고 시스템이 느려질 수 있음
ex. 
- 무결성을 위해 트랜잭션을 높은 수준으로 걸면 트랜잭션 속도가 느려질 수 있음

### 기밀성 vs 무결성
기밀성을 유지하려면 접근을 제한해야 하지만 무결성 검증 과정에 영향을 줄 수 있음

<br/>
<br/>


## 추가적인 보안 개념: 인증, 부인 방지, 책임 추적성
앞서 살펴본 **CIA 원칙(기밀성, 무결성, 가용성)**은 정보 보호의 기본적인 원칙이지만 실질적인 보안 환경에서는 이 원칙만으로 충분하지 않습니다.
**인증(Authentication), 부인 방지(Non-repudiation), 책임 추적성(Accountability)**과 같은 개념이 추가로 적용됩니다.

### 1. 인증 (Authentication)
> 사용자가 누구인지 확인하는 과정

인증이란 시스템이 사용자의 신원을 확인하고 올바른 권한을 가진 사용자만 정보에 접근할 수 있도록 보장하는 과정을 의미합니다.
사용자가 주장하는 신원이 정말 맞는지 검증하는 것이 핵심 목표입니다.

ex.
- 생체 인증 (지문, 얼굴 인식)


### 2. 부인 방지 (Non-repudiation) 
> 사용자가 행한 행동을 부정하지 못하도록 보장

부정 방지란 어떤 행위를 수행한 후에 사용자가 이를 부정할 수 없도록 하는 보안 기능입니다.
"나는 그 행동을 하지 않았다"라고 주장하지 못하도록 증거를 남기는 것이 핵심 목표입니다.

ex.
- 전자 서명

### 3. 책임 추적성 (Accountability)
> 누가, 언제, 어떤 행동을 했는지 추적할 수 있도록 보장

책임 추적성이란 사용자의 행위를 기록하고 필요 시 이를 확인할 수 있도록 만드는 보안 기능입니다.
"누가, 언제, 어떤 작업을 수행했는지 확인할 수 있도록 하는 것"이 핵심 목표입니다.

ex.
- 로그 기록