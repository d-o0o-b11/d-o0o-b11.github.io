---
title: HTTPS 적용하기
description: w. AWS
# author: d_o0o_b
categories: [Dev-Notes, Practice]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요
초기 HTTP(1.0, 1.1)는 평문(Plain Text) 통신이라 보안에 취약합니다.

1. 도청(Eavesdropping) 위험 <br/>
네트워크에서 데이터를 가로채면, 로그인 정보·카드 정보 탈취 가능
2. 변조(Man-in-the-Middle Attack, MITM) 위험 <br/>
중간에서 데이터 조작 가능 (ex. 결제 정보 조작)
3. 위장(Phishing) 위험 <br/>
가짜 사이트가 진짜 사이트처럼 속여서 사용자 정보 탈취
<br/>
HTTPS는 **암호화 + 인증 + 무결성**을 제공하며 보안 문제 해결합니다.


## CA 인증서의 역할

CA 인증서는 공개 키 암호화와 디지털 서명을 통해 서버의 신원을 보장하고 안전한 통신을 가능하게 합니다. 서버가 자신의 인증서를 클라이언트에 제공하면 클라이언트는 이 인증서가 신뢰할 수 있는 인증 기관(CA)에서 발급된 것인지 확인합니다. 이 과정을 통해 서버가 속이지 않고 실제 소유자와 연결되어 있음을 보장할 수 있습니다.

## AWS를 이용하여 SSL/TLS 인증서 발급

### Route 53

![Image](https://github.com/user-attachments/assets/e415122d-931b-47ac-b90a-eab5d97173f2?raw=true){: w='400' }

### Application Load Balancer

![Image](https://github.com/user-attachments/assets/de0f94d7-39b5-4a06-ad2c-7c7997e885b7?raw=true){: w='400' }


### AWS Certificate Manager

ACM을 사용하면 간편하게 SSL/TLS 인증서를 발급받을 수 있습니다. 인증서는 DNS 인증 또는 이메일 인증을 통해 도메인의 소유권을 확인한 후 발급됩니다. DNS 인증은 도메인의 DNS 레코드에 특정 값을 추가하는 방식이며 이메일 인증은 인증서 신청 시 입력한 이메일 주소로 확인 메일을 보내는 방식입니다.

![Image](https://github.com/user-attachments/assets/16f00dc8-f5d6-4b4e-abcb-ddf34dd333c0?raw=true){: w='500' }

### 클라이언트-서버 SSL/TLS Handshake 과정

![Image](https://github.com/user-attachments/assets/d8df9e94-c864-42e8-8e16-bdb000d6f755?raw=true)


## 궁금증

### 브라우저에 CA 인증서 목록이 있다고 했는데 직접 확인해보고 싶다.
> 주소창에 `chrome://settings/certificates` 입력

크롬에서 인증서 관리자 페이지를 이동하면 크롬이 기본적으로 신뢰하는 인증서들이 저장되어 있습니다.

크롬은 신뢰할 수 있는 인증서를 기반으로 HTTPS 연결을 통해 데이터를 암호화하고 안전하게 전송합니다.
크롬이 신뢰하는 인증서는 CA(Certificate Authority)라는 인증 기관에서 발급한 인증서입니다.
이 CA 인증서는 Chrome Root Store라는 목록에 포함되어야만 크롬에서 신뢰하는 인증서로 인식되며 사용자에게 경고 없이 HTTPS 연결을 허용합니다.

**if.**
<br/> 크롬이 신뢰하지 않는 CA 인증서를 사용한다면 사용자는 "보안 연결이 안전하지 않다"는 경고 메시지를 받게 됩니다. 이는 사이트가 악성 사이트일 수도 있다는 신호로 사용자가 해당 사이트를 신뢰하지 않을 수 있습니다.


### SSL/TLS의 차이점

SSL/TLS는 동일한 목적을 가진 보안 프로토콜이지만 기술적으로는 다릅니다. 
- SSL은 보안 소켓 계층(Secure Sockets Layer)을 의미하고 
- TLS는 전송 계층 보안(Transport Layer Security)을 의미합니다.
<br/>
현재는 TLS가 SSL을 대체하고 있으며 HTTPS에서 사용되는 프로토콜은 사실 TLS입니다.

> 다음 글에서 자세히 다루겠습니다. 
{: .prompt-info }



## 경험
HTTPS는 배포하면 자연스레 붙는 줄 알았던 과거의 나 ..
<br/>
CORS(Cross-Origin Resource Sharing) 오류가 발생하고 부터 HTTPS로 배포하기 위해서 인증서 발급받는 방법에 대해 알아 보았습니다.
CORS의 원인은 프론트는 HTTPS로 배포되었고 서버는 HTTP로 배포되었기 때문입니다.
프론트와 서버가 서로 다른 프로토콜(HTTP vs HTTPS)을 사용할 때 문제가 발생합니다.
<br/>
<br/>
CORS는 웹 브라우저가 다른 출처에서 자원을 요청할 때 보안을 위해 발생할 수 있는 문제를 해결하는 메커니즘입니다. 기본적으로 다른 도메인에서 자원을 요청하는 것을 브라우저가 보안상 제한합니다. 그런데 HTTPS 페이지에서 HTTP 리소스를 요청하면 브라우저가 이를 **Mixed Content**로 인식하고 차단합니다.
<br/>
그 이유는 HTTPS는 데이터를 암호화하여 안전하게 전송하는 방식이지만 HTTP는 암호화되지 않은 평문 데이터를 사용하기 때문입니다. 브라우저는 HTTPS로 요청한 데이터를 HTTP로 응답받으면 보안상의 위험이 있다고 판단하고 이를 차단합니다. 이로 인해 CORS 오류가 발생하는 것입니다. 🥹
<br/>
