<!-- ---
title: Node.js 구성요소 - [3]
description: Node, V8, libuv 구성요소
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## Node.js의 구성요소
> Node.js는 C++, JavaScript 그리고 일부 Python 코드로 이루어져 있습니다. 다만, Python 코드는 빌드 및 테스트 용도로만 사용되므로 본문에서는 생략하도록 하겠습니다.
> Node.js는 여러 계층으로 구성되어 있으며 각 계층은 하위 계층의 API를 사용하는 방식으로 설계되어 있습니다.

![Image](https://github.com/user-attachments/assets/658b92c2-610f-4285-8d8e-d7ea9641ebe2?raw=true)


### 1️⃣ Node.js 애플리케이션

사용자가 작성한 JavaScript 코드로 이루어져 있습니다.
이 코드들은 Node.js에서 제공하는 다양한 API를 활용하여 실행됩니다.

### 2️⃣ Node.js API (http, fs, crypto, buffer ...)

파일 시스템(fs), 네트워크 요청(http), 암호화(crypto), 버퍼(buffer) 등 다양한 기능을 제공합니다.
이 API들은 대부분 JavaScript 코드로 작성되어 있지만 일부 기능은 C++ 코드와 바인딩되어 동작합니다.
즉, JavaScript 코드가 직접 C++ 코드와 상호작용할 수 있도록 하는 인터페이스 역할을 합니다.

### 3️⃣ C++ 애드온

Node.js에서 C++로 작성된 네이티브 모듈을 의미합니다.
Node.js의 API는 이러한 C++ 애드온을 호출하여 더 낮은 계층의 기능을 수행합니다.
이 방식은 성능이 중요한 기능(ex. 암호화, 파일 입출력)에서 특히 유용하게 활용됩니다.

### 4️⃣ V8

C++ 애드온에서 V8 엔진을 사용하여 JavaScript 코드를 실행합니다.
V8은 Google이 개발한 JavaScript 엔진으로 JIT(Just-In-Time) 컴파일러를 이용하여 JavaScript 코드를 최적화하고 빠르게 실행할 수 있도록 합니다.
JavaScript 코드는 V8을 통해 기계어 코드로 변환된 후 실행되며 코드의 실행 패턴을 분석하여 반복 실행되는 코드의 성능을 최적화하는 역할도 수행합니다.

또한, JavaScript 코드에서 C++ 코드가 필요한 경우 V8을 통해 C++ 함수가 호출되기도 합니다.
이 과정에서 C++ 종속성이 있는 코드가 실행될 수도 있습니다.


### 5️⃣ libnv

Node.js는 네트워크 및 시스템 관련 작업을 처리하기 위해 libuv라는 라이브러리를 사용합니다.
libuv는 비동기 I/O 작업을 지원하며 다양한 운영체제에서 동일한 API를 제공하여 호환성을 보장합니다.

Node.js의 주요 기능 중 하나인 이벤트 루프(Event Loop)도 libuv에 의해 구현됩니다.
이를 통해 Node.js는 논블로킹 I/O를 지원하고 동시에 여러 작업을 효율적으로 처리할 수 있습니다.

추가적으로 libuv는 아래와 같은 다양한 기능을 수행합니다.

- DNS 조회
- HTTP 요청 및 응답 처리
- OpenSSL을 이용한 암호화 기능
- 데이터 압축을 위한 zlib 지원
- 파일 시스템 및 프로세스 관련 작업


### 추가 설명

#### Node.js 바인딩
Node.js에서는 JavaScript 코드가 C/C++ 코드와 직접 상호작용할 수 있도록 바인딩 메커니즘을 제공합니다.
이를 통해 JavaScript에서 C++ 함수를 호출하고 C++에서 JavaScript로 값을 반환할 수 있습니다.
이러한 바인딩 덕분에 JavaScript에서 네이티브 성능을 요구하는 기능을 활용할 수 있습니다.


#### Node.js 표준 라이브러리
Node.js는 다양한 표준 라이브러리를 제공하며 운영체제와 관련된 여러 기능을 지원합니다.

- 파일 시스템(fs): 파일 읽기/쓰기, 디렉터리 생성 등
- 타이머(setTimeout, setInterval): 일정 시간이 지난 후 코드 실행
- 네트워크 요청(http, https): 웹 서버 및 클라이언트 기능 제공

이러한 표준 라이브러리를 통해 개발자는 복잡한 시스템 호출을 직접 작성할 필요 없이 편리하게 기능을 사용할 수 있습니다.

#### 기타 C/C++ 컴포넌트
Node.js 내부에서는 libuv 외에도 여러 가지 C/C++ 기반의 서드파티 라이브러리를 사용하고 있습니다.

- c-ares: 비동기 DNS 조회 라이브러리
- HTTP 파서: HTTP 요청과 응답을 처리하는 C++ 기반의 모듈
- penSSL: 암호화 및 보안 관련 기능 제공
- zlib: 데이터 압축 기능 제공

이러한 C/C++ 기반 라이브러리들은 성능을 높이고 네트워크 및 보안 관련 기능을 안정적으로 수행할 수 있도록 돕습니다. -->