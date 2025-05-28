<!-- ---
title: Node.js 구성요소 - [4]
description: Node, V8, libuv 구성요소
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## V8 엔진
V8 엔진은 C++로 개발된 오픈 소스 JavaScript 실행 엔진입니다. Google에서 개발하였으며, Chrome 브라우저와 Node.js에서 사용되고 있습니다. V8 엔진은 인터프리터 역할을 하는 **이그니션(Ignition)**과 컴파일러 역할을 하는 **터보팬(Turbofan)**을 사용하여 JavaScript 코드를 실행합니다. 이를 통해 JavaScript 코드를 빠르고 효율적으로 실행할 수 있도록 최적화하고 있습니다.

### V8 엔진의 JavaScript 코드 컴파일 과정

![Image](https://github.com/user-attachments/assets/0c99adcc-eb90-4948-ab30-983577c00ac2?raw=true)



### 1️⃣ JavaScript 코드 → 파서(Parser)
JavaScript 코드는 먼저 파서에 의해 분석됩니다. 파서는 코드를 읽고 문법적으로 올바른지 검사한 후 **추상 구문 트리(Abstract Syntax Tree, AST)**를 생성합니다.

### 2️⃣ 파서 → 추상 구문 트리(AST)
파서가 생성한 AST는 JavaScript 코드의 구조를 나타내는 트리 형태의 데이터입니다. 이 트리는 코드의 실행을 위해 다음 단계로 전달됩니다.

### 3️⃣ 추상 구문 트리 → 이그니션(Ignition)
AST는 이그니션 인터프리터로 전달되며 이그니션은 이를 바탕으로 **바이트코드(Bytecode)**를 생성합니다.

### 4️⃣ 이그니션 → 바이트코드(Bytecode)
바이트코드는 CPU가 직접 실행할 수 없는 형태이므로 이그니션 인터프리터가 해석하여 실행합니다. 이를 통해 빠르게 실행할 수 있도록 돕습니다.

### 5️⃣ 이그니션 → 터보팬(Turbofan)
V8 엔진은 성능을 최적화하기 위해 특정 코드가 반복적으로 실행되는지 분석합니다. 자주 실행되거나 성능 병목이 발생하는 코드(핫스팟 코드)는 터보팬 최적화 컴파일러로 전달됩니다.

### 6️⃣ 터보팬 → 바이너리 코드(Binary Code)
터보팬은 바이트코드를 최적화하여 **바이너리 코드(기계어)**로 변환합니다. 기계어는 CPU에서 직접 실행할 수 있으므로 성능이 크게 향상됩니다.

### 7️⃣ 바이너리 코드 → 바이트코드 (필요 시 최적화 해제)
최적화된 코드가 예상과 다르게 실행될 경우 V8 엔진은 최적화를 해제하고 다시 이그니션 인터프리터에서 실행합니다. 이를 통해 안정성을 유지하면서도 높은 성능을 제공합니다.

### 추가 설명

1. JIT(Just-In-Time) 컴파일러
    - V8 엔진은 인터프리터와 컴파일러의 장점을 동시에 갖춘 JIT 컴파일러를 사용합니다.
    - 인터프리터는 빠르게 실행할 수 있는 장점이 있으며 컴파일러는 최적화된 기계어 코드를 생성하여 성능을 높이는 역할을 합니다.
    - JIT 컴파일러를 사용하면 적절한 시점에 코드가 최적화되므로 성능이 향상되지만, 메모리를 많이 사용할 수 있습니다.

2. 핫스팟 최적화(Hotspot Optimization)
    - V8 엔진은 실행 중인 JavaScript 코드의 성능을 실시간으로 분석합니다.
    - 특정 코드가 반복적으로 실행되면 터보팬을 통해 최적화하여 실행 속도를 향상시킵니다.
    - 하지만 예상과 다르게 동작하는 경우에는 최적화를 되돌리는(Deoptimization) 작업을 수행하여 안정성을 보장합니다.

이와 같은 과정을 통해 V8 엔진은 JavaScript 코드를 효율적으로 실행하며, Node.js에서도 빠르고 안정적인 환경을 제공합니다.

<br/><br/>

## libuv

libuv는 Node.js에서 비동기 I/O 기능을 지원하는 라이브러리입니다. 이벤트 루프, 운영체제의 비동기 API 및 Thread Pool을 지원하여 Node.js에서 HTTP 요청, 파일 시스템, 소켓 통신 등과 같은 JavaScript 자체에는 없는 기능들을 구현할 수 있도록 도와줍니다.

libuv는 JavaScript 코드에서 C++ 코드를 실행할 수 있도록 해주는 역할도 하며 C++ 바인딩을 통해 JavaScript에서 C++로 작성된 기능을 호출할 수 있도록 지원합니다.


### libuv 아키텍처

![Image](https://github.com/user-attachments/assets/84270006-bee7-4c04-90fe-3c08ed6454a3?raw=true)

1. User Code
    - JavaScript 개발자가 작성하는 코드입니다.
2. Network I/O 
    - TCP, UDP, 파이프 등의 네트워크 입력과 출력을 처리합니다.
3. File I/O
    - 파일 시스템과의 입출력을 담당합니다.
4. DNS 옵션
    - 도메인 네임 시스템(DNS) 요청을 비동기적으로 처리합니다.
5. uv_io_t
    - libuv에서 I/O 작업을 추적하는 구조체로, 다양한 입출력 이벤트를 처리하는 역할을 합니다.
6. IOCP (I/O Completion Port)
    - Windows 운영체제에서 비동기 I/O를 처리하기 위한 모델입니다.
7. Thread Pool
    - 파일 시스템, DNS 조회 등 CPU 바운드 작업을 비동기적으로 실행할 수 있도록 지원하는 스레드 풀입니다.
8. epoll, kqueue, 이벤트 포트
    - 각각 Linux, macOS, Solaris 등의 운영체제에서 효율적인 이벤트 처리를 지원하는 기술입니다.


libuv는 이러한 구조를 기반으로 이벤트 루프를 운영하며, Node.js에서 비동기 프로그래밍을 가능하게 합니다. 이를 통해 높은 성능과 확장성을 제공하며, 많은 동시 연결을 처리할 수 있도록 도와줍니다.
 -->
