---
title: HTTP
description: 책을 읽고 다시 한 번 정리
# author: d_o0o_b
categories: [Reading, CS]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요

이전 글에도 HTTP 내용을 많이 다뤘습니다.

네트워크 공부를 하면서 http 파트가 있길래 책 내용을 다시 한 번 정리해보고자 합니다.


<br/>


## HTTP

애플리케이션 계층으로서 웹 서비스 통신에 사용된다.

HTTP/1.0부터 시작해서 발전을 거듭하여 현재는 HTTP/3 까지 발전해왔다.

<br/>

### HTTP/1.0

기본적으로 한 연결당 하나의 요청을 처리하도록 설계되었다.
이는 RTT 증가를 불러오게 되었다.

![Image](https://github.com/user-attachments/assets/02c5a833-ea9c-4b9f-9cd2-604611ea0401?raw=true)

> 서버로부터 파일을 가져올 때마다 TCP의 **3-way-handshake**를 계속해서 열어야 하기 때문에 RTT가 증가한다는 단점이 있다.

**RTT**
- 패킷이 목적지에 도달하고 나서 다시 출발지로 돌아오기까지 걸리는 시간이며 패킷 왕복 시간이다.

<br/>

#### RTT의 증가를 해결하기 위한 방법
매번 연결할 때마다 RTT가 증가하니 서버에 부담이 많이 가고 사용자 응답 시간이 길어졌습니다.
이를 해결하기 위해 이미지 스플리팅, 코드 압축, 이미지 Base64 인코딩을 사용하곤 했습니다.

<br/>

##### 이미지 스플리팅
많은 이미지를 다운로드받게 되면 과부하가 걸리기 때문에 많은 이미지가 합쳐 있는 하나의 이미지를 다운받고 `background-position`을 이용해서 이미지를 표기하는 방법이다.

<br/>

##### 코드 압축
코드를 압축해서 개행 문자, 빈칸을 없애서 코드의 크기를 최소화하는 방법이다.
코드 용량이 줄어듭니다.

<br/>

##### 이미지 Base64 인코딩
이미지 파일을 64진법으로 이루어진 문자열로 인코딩하는 방법이다.
서버와의 연결을 열고 이미지에 대해 서버에 HTTP 요청을 할 필요가 없다는 장점이 있다.
But, Base64 문자열로 변환할 경우 37% 정도 크기가 더 커지는 단점이 있다.

**인코딩**
- 정보의 형태나 형식을 표준화, 보안, 처리 속도 향상, 저장 공간 절약 등을 위해 다른 형태나 형식으로 변환하는 처리 방식이다.


<br/>

### HTTP/1.1
HTTP/1.0에서 발전한 것이 바로 HTTP/1.1이다.
매번 TCP 연결을 하는 것이 아니라 한 번 TCP 초기화를 한 이후에 keep-alive라는 옵션으로 여러 개의 파일을 송수신할 수 있게 바뀌었다.

*HTTP/1.0에서도 keep-alive가 있었지만 표준화가 되어 있지 않았고 HTTP/1.1부터 표준화가 되어 기본 옵션으로 설정되었다.

![Image](https://github.com/user-attachments/assets/8e3824f3-a6ae-476f-9ecc-9ab8b2399682?raw=true)

한 번 TCP-way-handshake가 발생하면 그 다음부터는 발생하지 않는다.
But, 문서 안에 포함된 다수의 리소스(이미지, 동영상, css파일, js 파일 등)를 처리하려면 요청할 리소스 개수에 비례해서 대기 시간이 길어진다.

<br/>

#### HOL Blocking (Head Of Line Blocking)
네트워크에서 같은 큐에 있는 패킷이 그 첫 번째 패킷에 의해 지연될 때 발생하는 성능 저하 현상이다.

<br/>

#### 무거운 헤더 구조
HTTP/1.1의 헤더에는 쿠키 등 많은 메타데이터가 들어 있고 압축이 되지 않아 무겁다.


<br/>

### HTTP/2
SPDY 프로토콜에서 파생된 HTTP/1.x 보다 지연 시간을 줄이고 응답 시간을 더 빠르게 할 수 있으며 멀티플렉싱, 헤더 압축, 서버 푸시, 요청의 우선순위 처리를 지원하는 프로토콜이다.

<br/>

#### 멀티플렉싱
여러 개의 스트림을 사용하여 송수신한다는 것이다.
특정 스트림의 패킷이 손실되어도 해당 스트림에만 영향을 미치고 나머지 스트림은 정상 동작한다.

**스트림(stream)**
- 시간이 지남에 따라 사용할 수 있게 되는 일련의 데이터 요소를 가리키는 데이터 흐름

이를 통해 단일 연결을 사용하여 병렬로 여러 요청을 받을 수 있고 응답을 줄 수 있다.
HTTP/1.x에서 발생하는 문제인 HOL Blocking을 해결할 수 있다.

<br/>

#### 헤더 압축
HTTP/1.x에는 크기가 큰 헤더라는 문제가 있었다.

HTTP/2에서는 헤더 압축을 사용해서 해결한다. 허프만 코딩 압축 알고리즘을 사용하는 HPACK 압축 형식을 가진다.

**허프만 코딩(huffman coding)**
문자열을 문자 단위로 쪼개 빈도수를 세어 빈도가 높은 정보는 적은 비트 수를 사용하여 표현하고 빈도가 낮은 정보는 비트 수를 많이 사용하여 표현해서 전체 데이터의 표현에 필요한 비트양을 줄이는 원리이다.

<br/>

#### 서버 푸시
HTTP/1.1에서는 클라이언트가 서버에 요청을 해야 파일을 다운로드 받을 수 있었다.
HTTP/2는 클라이언트 요청 없이 서버가 바로 리소스를 푸시할 수 있다.

Html에서는 css,js 파일이 포함될 수 있는데, Html 읽으면서 그 안에 들어 있는 css 파일을 서버에서 푸시하여 클라이언트에 먼저 줄 수 있다.

<br/>


### HTTPS
HTTP/2는 HTTPS 위에서 동작한다.
HTTPS는 애플리케이션 계층과 전송 계층 사이에 신뢰 계층인 SSL/TLS 계층을 넣은 신뢰할 수 있는 HTTP 요청을 뜻한다. 이를 통해 '통신을 암호화'한다.

<br/>

#### SSL/TLS (Secure Socket Layer/Transport Layer Security Protocol)
버전이 올라가면서 TLS로 명칭이 변경되었으나 SSL/TLS로 많이 부른다.
전송 계층에서 보안을 제공하는 프로토콜이다.
클라이언트와 서버가 통신할 때 SSL/TLS를 통해 제3자가 메시지를 도청하거나 변조하지 못하도록 한다. 인터셉터를 방지할 수 있다.

보안 세션을 기반으로 데이터를 암호화하며 보안 세션이 만들어질 때 인증 메커니즘, 키 교환 암호화 알고리즘, 해싱 알고리즘이 사용된다.

<br/>

##### 보안 세션
보안이 시작되고 끝나는 동안 유지되는 세션을 뜻한다.
SSl/TLS는 handshake를 통해 보안 세션을 생성하고 이를 기반으로 상태 정보 등을 공유한다.

**세션**
- 운영체제가 어떠한 사용자로부터 자신의 자산 이용을 허락하는 일정한 기간을 뜻한다.
- 즉, 사용자는 일정 시간 동안 응용 프로그램, 자원 등을 사용할 수 있다.

<br/>

##### 인증 매커니즘
CA(Certificate Authorities)에서 발급한 인증서를 기반으로 이루어진다.
CA에서 발급한 인증서는 안전한 연결을 시작하는 데 있어 필요한 공개키를 클라이언트에 제공하고 사용자가 접속한 서버가 신뢰할 수 있는 서버임을 보장한다.

인증서는 서비스 정보, 공개키, 지문, 디지털 서명 등으로 이루어져 있다.

<br/>

##### 암호화 알고리즘
키 교환 암호화 알고리즘으로는 대수곡선 기반의 ECDHE 또는 모듈식 기반의 DHE를 사용한다.
둘 다 디피-헬만(Diffie-Hellman) 방식을 근간으로 만들어졌다.

<br/>

##### 해싱 알고리즘
데이터를 추정하기 힘든 더 작고 섞여 있는 조각으로 만드는 알고리즘이다. SSL/TLS는 해싱 알고리즘으로 SHA-256 알고리즘과 SHA-384 알고리즘을 쓴다.

**SHA-256 알고리즘**
- 해시 함수으 결괏값이 256비트인 알고리즘이다.
- 해싱을 해야 할 메시지에 1을 추가하는 등 전처리를 하고 전처리된 메시지를 기반으로 해시를 반환한다.

**해시**
- 다양한 길이를 가진 데이터를 고정된 길이를 가진 데이터로 매핑한 값

**해싱**
임의의 데이터를 해시로 바꿔주는 일이며 해시 함수가 이를 담당한다.

**해시 함수**
임의의 데이터를 입력으로 받아 일정한 길이의 데이터로 바꿔주는 함수








<br/>

#### SEO에도 도움이 되는 HTTPS
구글은 SSL 인증서를 강조해왔고 사이트 내 모든 요소가 동일하다면 HTTPS 서비스를 하는 사이트가 그렇지 않은 사이트보다 SEO 순위가 높을 것이라고 공식적으로 밝혔다.

**SEO(Search Engine Optimization)**
- 검색엔진 최적화를 뜻한다.
- 구글, 네이버 같은 검색엔진으로 웹 사이트를 검색했을 때 그 결과를 페이지 상단에 노출시켜 많은 사람이 볼 수 있도록 최적화하는 방법이다.

<br/>

#### HTTPS 구축 방법
크게 3가지입니다. 
1. 직접 CA에서 구매한 인증키를 기반으로 HTTPS 서비스를 구축한다.
2. 서버 앞단의 HTTPS를 제공하는 로드밸런서를 둔다.
3. 서버 앞단에 HTTPS를 제공하는 CDN을 둬서 구축한다.

<br/>

### HTTP/3
HTTP/1.1 및 HTTP/2와 함께 World Wide Web에서 정보를 교환하는 데 사용되는 HTTP의 3번째 버전입니다.
TCP 위에서 돌아가는 HTTP/2와는 달리 HTTP/3은 QUIC 계층 위에서 돌아가며 TCP 기반이 아닌 UDP 기반으로 돌아간다.

![Image](https://github.com/user-attachments/assets/2638c2d4-c874-4cb6-a931-ef5b0a99b32f?raw=true)

HTTP/2에서 장점이었던 멀티플렉싱을 가지고 있으며 초기 연결 설정 시 지연 시간 감소라는 장점이 있다.

<br/>

#### 초기 연결 설정 시 지연 시간 감소
QUIC은 TCP를 사용하지 않기 때문에 통신을 시작할 때 번거로운 3-way-handshake 과정을 거치지 않아도 된다.
QUIC은 첫 연결 설정에 1-RTT만 소요된다.

QUIC은 순방향 오류 수정 매커니즘(FEC, Forward Error Correction)이 적용되었다.
이는 전송한 패킷이 손실되었다면 손실되었다면 수신 측에서 에러를 검출하고 수정하는 방식이며 열악한 네트워크 환경에서도 낮은 패킷 손실률을 자랑한다.