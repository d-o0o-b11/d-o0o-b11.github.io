---
title: HTTP , HTTPS 동작 원리 [2] - 0
description: HTTP 이해하기 <큰 틀>
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

## HTTP 요청이란?
HTTP(HyperText Transfer Protocol)는 **웹에서 클라이언트(브라우저)와 서버가 데이터를 주고받는 규약(프로토콜)**입니다.
브라우저 주소창에 www.example.com을 입력하면 **HTTP 요청(Request)**이 발생하고 서버에서 **응답(Response)**이 돌아오게 됩니다.

<br/>

## HTTP 요청 과정 (전체 흐름)
1. 사용자가 브라우저에 www.example.com 입력
2. DNS 조회 → 93.184.216.34 같은 IP 주소 획득
3. 브라우저가 TCP 연결 (3-way Handshake)
4. 브라우저가 HTTP 요청(Request) 전송
5. 서버가 HTTP 응답(Response) 전송
6. 브라우저가 받은 데이터를 해석하여 웹페이지 렌더링


<br/>

### 브라우저가 TCP 연결
1. 브라우저가 서버에 요청을 보낼 준비
- DNS를 통해 www.example.com의 IP 주소(93.184.216.34)를 획득한 후 브라우저는 해당 IP 주소를 이용해 서버와 연결을 맺을 준비를 합니다.

2. TCP 연결 (3-Way Handshake)
- HTTP는 TCP/IP 위에서 동작하기 때문에 브라우저는 서버와 **TCP 연결(3-Way Handshake)**을 먼저 맺어야 합니다.

**TCP 3-Way Handshake 과정**
1. SYN (클라이언트 → 서버) : "연결해도 돼?"
2. SYN-ACK (서버 → 클라이언트) : "응, 연결 가능해!"
3. ACK (클라이언트 → 서버) : "알겠어, 연결할게!"


<br/>

### HTTP 요청(Request) 전송
TCP 연결이 성립되었다면 브라우저는 서버에 HTTP 요청(Request)을 보낼 준비가 된겁니다.

```
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)
Accept: text/html,application/xhtml+xml
```

- 요청 메서드(Method): GET, POST, PUT, DELETE 등
- URL 경로(Path): /index.html
- HTTP 버전(Version): HTTP/1.1
- 호스트(Host): www.example.com
- User-Agent: 사용자의 브라우저 정보

<br/>

### 서버가 HTTP 응답(Response) 전송 
서버는 클라이언트의 요청을 받고 해당하는 데이터를 찾아서 응답을 준비합니다.
index.html 파일을 요청했다면 서버는 이를 찾아 응답해줍니다.

<br/>

### HTTP 응답(Response) 전송
서버가 요청을 처리한 후 브라우저에게 응답을 돌려줍니다.

```
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 5120
Server: Apache/2.4.41
```

- 상태 코드(Status Code): 200 OK (성공), 404 Not Found (실패)
- Content-Type: text/html, application/json 등
- Content-Length: 응답 데이터 크기
- Server: 웹 서버 정보 (Apache, Nginx 등)

이후 **응답 본문(HTML, CSS, JS 등 데이터)**이 전달됩니다.

<br/>

### 브라우저가 응답을 해석하고 웹페이지 렌더링
브라우저는 받은 데이터를 **파싱(Parsing)**하고, 웹페이지를 렌더링합니다.
1. HTML을 해석하여 DOM(Document Object Model) 생성
2. CSS 적용하여 스타일 구성
3. JS 실행하여 동적 요소 처리
4. 웹페이지 완성 후 화면에 표시

