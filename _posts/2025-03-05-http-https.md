---
title: HTTP , HTTPS 동작 원리 [1]
description: DNS 이해하기
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
# image:
#   path: /commons/devices-mockup.png
#   lqip: data:image/webp;base64,UklGRpoAAABXRUJQVlA4WAoAAAAQAAAADwAABwAAQUxQSDIAAAARL0AmbZurmr57yyIiqE8oiG0bejIYEQTgqiDA9vqnsUSI6H+oAERp2HZ65qP/VIAWAFZQOCBCAAAA8AEAnQEqEAAIAAVAfCWkAALp8sF8rgRgAP7o9FDvMCkMde9PK7euH5M1m6VWoDXf2FkP3BqV0ZYbO6NA/VFIAAAA
#   alt: Responsive rendering of Chirpy theme on multiple devices.
---

## 도메인 vs IP 주소
> 웹사이트는 어떻게 찾아갈까?

<br/>

### IP 주소란?
인터넷에 연결된 모든 기기는 **고유한 주소(IP 주소)**를 가지고 있습니다.
이 IP 주소는 우리가 집을 찾을 때 사용하는 집 주소와 같은 역할을 합니다.

```
IPv4: 192.168.1.1, 8.8.8.8
IPv6: 2001:db8::ff00:42:8329
```

웹사이트도 결국 인터넷에 연결된 서버에 존재하니까 이 서버도 IP 주소를 가지고 있습니다.
우리가 자주 방문하는 Google의 IP 주소는 142.250.190.78 같은 형태로 고유한 주소를 가지고 있습니다.

<br/>

### 왜 우리는 IP 주소로 웹사이트를 방문하지 않을까?
IP 주소는 숫자로 되어 있어서 기억하기 어렵습니다.
구글을 방문하려면 142.250.190.78 대신 **www.google.com**을 입력하는 게 훨씬 기억하기 편합니다.

이처럼 **IP 주소를 쉽게 기억하고 사용할 수 있도록 문자 형태로 만든 것이 "도메인(Domain)"**입니다.

<br/>

### 도메인과 IP 주소의 관계
웹사이트는 도메인 이름을 IP 주소로 변환하는 과정이 필요합니다. 
이 과정을 DNS라고 부릅니다.
www.google.com을 입력하면 컴퓨터는 먼저 이 도메인의 IP 주소를 찾고 해당 서버에 요청을 보냅니다.

<br/>

### 정리
1. 	IP 주소
- 인터넷에서 기기의 고유 주소
- 142.250.190.78
- 네트워크 간 통신을 위해 사용

2. 도메인
- 사람이 쉽게 기억할 수 있도록 만든 이름
- www.google.com
- 사용자가 쉽게 웹사이트에 접근 가능

<br/>

## DNS란?
DNS(도메인 네임 시스템, Domain Name System)는 도메인 이름을 IP 주소로 변환해주는 시스템입니다.
"인터넷 전화번호부" 같은 역할을 합니다.

www.google.com을 입력하면 DNS는 142.250.190.78 같은 IP 주소를 찾아서 브라우저가 해당 서버에 접근할 수 있도록 도와줍니다.
마치 휴대폰 연락처에서 "OO"라고 저장된 번호를 자동으로 찾아 전화하는 것과 유사합니다.

<br/>

### DNS 동작 과정
웹사이트에 접속할 때 도메인 → IP 주소 변환을 하기 위해 여러 과정을 거칩니다.

www.example.com에 접속할 때의 DNS 동작 순서

1. 사용자가 브라우저에 www.example.com 입력
- 브라우저는 이 도메인의 IP 주소를 아는지 확인

2. 로컬 DNS 캐시 확인
- 내 컴퓨터(또는 브라우저)에서 이전에 방문한 기록이 있다면 → DNS 조회 없이 바로 사용
- 기록이 없다면 → ISP(인터넷 제공 업체)의 DNS 서버에 요청

3. ISP의 DNS 서버에서 확인 (재귀적 질의, Recursive Query)
- ISP의 DNS 서버(로컬 DNS 서버)가 www.example.com의 IP 주소를 알고 있다면 → 즉시 반환
- 모르면 → 상위 DNS 서버(루트 서버)로 요청 전달

4. 루트 네임서버(Root Name Server) 응답
- 루트 서버는 "나는 정확한 IP 주소는 모르지만 .com을 담당하는 서버를 알려줄게!" 라고 응답

5. TLD 네임서버 응답
- .com을 담당하는 TLD(Top-Level Domain) 네임서버는 "나는 example.com의 정보를 담당하는 네임서버를 알려줄게!"

6. 도메인 네임서버(NS) 응답
- example.com의 최종 네임서버가 정확한 IP 주소(93.184.216.34) 반환

7. 브라우저가 해당 IP 주소로 HTTP 요청 전송
- 이제 브라우저는 93.184.216.34로 요청을 보내고 웹사이트가 표시 됨

<br/>

### DNS 서버 종류
> 여러 계층으로 이루어져 있습니다!

| DNS 서버 종류    | 역할 |
| :--------------------------- |:---------------------------|
| 로컬 캐시  | 브라우저나 운영체제에 저장된 최근 조회 결과 | 
| ISP의 DNS 서버  | 인터넷 서비스 제공업체(ISP)가 관리하는 DNS 서버 | 
| 루트 네임서버  | 전 세계 13개만 존재하는 최상위 DNS 서버 | 
| TLD 네임서버  | .com, .net, .org 등 각 최상위 도메인(TLD) 담당 | 
| 도메인 네임서버(NS)  | 개별 도메인의 최종 DNS 서버 (웹사이트가 직접 관리) | 

<br/>

### DNS가 없다면?
웹사이트를 방문할 때 IP 주소를 직접 입력해야 합니다.
www.google.com 대신 142.250.190.78을 외워야 합니다.





