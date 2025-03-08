---
title: HTTP , HTTPS 동작 원리 [2] - 1
description: HTTP 이해하기 <개요>
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
>  ✓ [HTTP 개요 글](https://developer.mozilla.org/ko/docs/Web/HTTP/Overview)을 보고 공부한 걸 정리한 내용입니다.
{: .prompt-info }


## HTTP
> HyperText Transfer Protocol

- HTML과 같은 하이퍼미디어 문서를 전송하기 위한 **애플리케이션 계층 프로토콜**입니다.
- 클라이언트가 요청을 하기 위해 연결을 연 다음 응답을 받을 때까지 대기하는 전통적인 클라이언트-서버 모델을 따릅니다.
- **무상태 프로토콜**이며 서버가 두 요청 간에 어떠한 데이터(상태)도 유지하지 않습니다.

<br/>

HTTP는 **애플리케이션 계층의 프로토콜**로, **신뢰 가능한 전송 프로토콜**이라면 이론상으로는 무엇이든 사용할 수 있으나 **TCP** 혹은 **암호화된 TCP 연결인 TLS**를 통해 전송됩니다.

..❓

### 애플리케이션 계층의 프로토콜
애플리케이션 계층은 우리가 직접 사용하는 웹 브라우저, 이메일, 메신저 같은 프로그램들이 데이터를 주고받는 방식(프로토콜)을 정하는 계층입니다.
웹에서 데이터를 주고받는 **규칙(프로토콜)**이며 애플리케이션 계층에서 작동합니다.

### 신뢰 가능한 전송 프로토콜 ⭐️
HTTP 자체는 **데이터를 주고받는 규칙만** 정의할 뿐, 그 데이터를 실제로 **어떻게 전송할지**는 직접 담당하지 않습니다.
데이터를 안정적으로 보내려면 **전송 계층**에서 신뢰할 수 있는 방식이 필요합니다.

예제 : 편지 보내기
```
HTTP 역할: 편지의 형식을 정하는 규칙 (ex: "받는 사람 이름, 주소를 적고 내용 작성")
전송 프로토콜 역할 (TCP): 편지를 실제로 안전하게 배달하는 과정 (ex: "우체부가 편지를 정확히 전달하고 중간에서 잃어버리지 않도록 확인")

```
HTTP는 데이터를 주고받을 때 어떤 형식으로 요청하고 응답할지를 정하는 규칙이지만 그 데이터가 네트워크를 통해 안전하게 전달되는지는 TCP 같은 전송 계층 프로토콜이 담당합니다.

### HTTP는 **대부분** TCP를 사용
**TCP(Transmission Control Protocol)**는 데이터를 순서대로, 빠짐없이, 정확하게 전달해 주는 프로토콜입니다.
그래서 HTTP는 대부분 TCP를 사용해서 데이터를 전송합니다.

HTTP 자체로는 "데이터가 제대로 도착했는지", "중간에서 손실되지 않았는지" 확인할 방법이 없습니다. 그래서 TCP 같은 신뢰할 수 있는 전송 프로토콜이 필요합니다.

**대부분**
- HTTP 자체는 TCP에 강하게 묶여 있지 않기 때문에 이론적으로는 UDP 위에서도 동작할 수 있습니다.

‣ HTTP/1.1 & HTTP/2 → TCP 기반
원래 HTTP는 TCP 위에서만 동작했습니다. (왜? → 신뢰성 때문에)
하지만 TCP는 연결을 설정하고 끊는 과정(3-way handshake, 4-way handshake) 때문에 느릴 수 있습니다.

‣ HTTP/3 → UDP 기반 (QUIC 프로토콜 사용)
구글에서 만든 QUIC(Quick UDP Internet Connections) 프로토콜을 기반으로 동작합니다.
UDP 위에서 작동하지만 TCP의 장점(신뢰성)까지 포함하여 연결 설정 속도가 빠르고 멀티플렉싱(한 번에 여러 개의 요청 처리)이 쉽습니다.
HTTP/3는 "빠르고 끊김 없이 안정적으로 통신하려고" UDP 위에서 동작하는 특별한 HTTP 버전입니다.

<br/>

**왜 HTTP/3는 UDP를 사용할까?**

‣ TCP는 안정적이지만 속도가 느릴 수 있습니다.
특히, 모바일 환경에서는 네트워크가 자주 끊기거나 변경(Wi-Fi ↔ LTE)될 수도 있는데 TCP는 이런 경우 연결이 **리셋**되어서 **다시 연결**해야 합니다.

‣ UDP 기반인 HTTP/3는 이런 문제를 개선해서 연결 설정이 더 빠르고 중간에 네트워크가 바뀌어도 끊기지 않게 동작합니다.


### HTTPS는 TLS를 추가한 것
HTTP는 기본적으로 암호화가 없어서 보안에 취약합니다.
HTTPS(= HTTP + 보안)는 **TLS(Transport Layer Security)**라는 보안 프로토콜을 추가해서 데이터를 암호화합니다.
TLS는 기본적으로 TCP 위에서 작동하니까, HTTPS는 "TLS + TCP" 조합으로 데이터를 주고 받습니다.

### HTTP는 암호화가 없어서 보안에 취약
1. 데이터가 평문으로 전송된다. - 누구든지 쉽게 읽을 수 있다.
2. 중간자 공격 - 통신 중간에 끼어서 데이터를 가로채거나 수정할 수 있다.
3. 사이트가 진짜인지 아닌지 알 수 없다.

### 무상태 프로토콜
각 요청(Request)과 응답(Response)은 서로 독립적이며 서버는 이전 요청의 정보를 기억하지 않습니다.
하지만 우리가 웹사이트를 사용할 때, 쇼핑몰 장바구니에 담은 상품이 유지되거나 로그인 상태가 유지되는 것처럼 보입니다.

**어떻게?**
HTTP 자체는 상태를 저장하지 않지만, 아래 기술을 활용하면 HTTP가 무상태 프로토콜이어도 "마치 상태를 기억하는 것처럼" 동작할 수 있습니다!

- 쿠키
<!-- 서버가 클라이언트에 데이터(세션 ID, 사용자 정보 등) 저장
이후 요청에서 해당 쿠키를 함께 전송하여 사용자 식별 -->

- 세션 & 세션 스토리지
<!-- 서버에서 세션 ID를 생성하고 이를 클라이언트의 쿠키에 저장
세션 ID를 이용해 서버가 사용자의 상태를 유지 -->

- 캐시
<!-- 자주 사용하는 데이터를 브라우저나 서버에서 저장하여 빠르게 불러올 수 있다
로그인 유지, 장바구니 기능 등에 사용 -->

- 토큰 기반 인증
<!-- 로그인 시 클라이언트에게 토큰을 발급하여 이후 요청마다 토큰을 포함시켜 사용자를 인증 -->


<br/>

...암호화가 안된다. 평문으로 간다. 그래서 위험하다. 이론적으로는 이해했는데 직접 눈으로 봐야 더 와닿을 것 같다.🧐

<br/>

## 통신 데이터를 직접 확인해보자! (보안에 취약한 이유 찾으러)
- TCP는 연결 지향형 프로토콜입니다. 두 호스트가 TCP 연결을 주고받기 전에 연결을 수립하고 통신이 끝났다면 연결을 끊는 특성을 가집니다.

1.연결 (3-way-handshake)
![Image](https://github.com/user-attachments/assets/735f8e6b-5367-4836-a1c8-7bba2a0e64cf?raw=true)

![Image](https://github.com/user-attachments/assets/9f9a084a-d8e9-4c20-bb5b-4597bd875be2?raw=true){: w='400' }



2.데이터 송수신
![Image](https://github.com/user-attachments/assets/c4c55529-9604-4d23-aa99-e57ffc0e755a?raw=true)
![Image](https://github.com/user-attachments/assets/3dc740f3-8fa4-45d9-9290-55f781739343?raw=true){: w='400' }
- 평문화로 데이터를 응답받는 모습을 볼 수 있습니다.


3.연결 종료 (4-way-handshake)
> FIN, ACK을 주고 받으며 이뤄집니다.

![Image](https://github.com/user-attachments/assets/1de15adb-4f68-4f9f-8f1b-56bcc5773bc0?raw=true)

![Image](https://github.com/user-attachments/assets/914c8c51-c9d7-4a50-81fe-63a2e6b81a4c?raw=true){: w='400' }

<br/>
<br/>

이번 블로그를 작성하면서 실제로 패킷을 직접 확인할 수 있다는 사실을 알게 되었습니다. 패킷을 분석해보니 데이터가 평문으로 전송될 경우 보안에 취약해질 수 있으며 이로 인해 사이트가 해킹 등의 위험에 노출될 가능성이 높다는 점을 깨달았습니다.