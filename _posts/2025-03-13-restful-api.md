---
title: REST API 원칙
description: REST API의 개요와 설계 원칙
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## REST API 개요

REST(Representational State Transfer) API는 웹 서비스의 아키텍처 스타일 중 하나로 HTTP를 기반으로 클라이언트와 서버 간의 통신을 수행하는 방식입니다. 웹 아키텍처에서 "자원(Resource)"을 중심으로 한 설계 방식으로 정의되었습니다.

## REST의 특징

- 클라이언트-서버 구조
<br/>
    클라이언트와 서버가 서로 독립적이고 요청과 응답만으로 상호작용합니다. 서버는 클라이언트의 요청에 따라 자원을 제공하며 클라이언트는 서버에 요청을 보내고 응답을 받습니다.

- 무상태성(Stateless)
<br/>
    각 요청은 독립적이어야 하며 서버는 요청 간에 클라이언트의 상태를 저장하지 않습니다. 클라이언트의 상태는 클라이언트가 유지하고 서버는 매 요청마다 완전한 정보가 포함되어야 합니다.

- 자원 기반
<br/>
    자원(Resource)은 특정 엔드포인트에서 제공하는 데이터 단위이며, URI(Uniform Resource Identifier)로 식별됩니다. 예를 들어 users는 사용자 목록을 나타내는 자원이 될 수 있습니다.

- 표현
<br/>
    자원은 다양한 형태로 표현될 수 있습니다. 예를 들어 JSON, XML 등이 사용됩니다.

- HTTP 메소드
<br/>
    REST는 HTTP 메소드(GET, POST, PUT, DELETE 등)를 통해 자원을 조작합니다.

## REST API의 등장 이유

REST API가 등장하게 된 이유는 웹에서 시스템 간의 효율적이고 유연한 통신을 위한 필요성 때문입니다. <br/>
초기 웹 서비스에서는 RPC(Remote Procedure Call)나 SOAP(Simple Object Access Protocol) 같은 복잡하고 무거운 프로토콜들이 사용되었습니다. 하지만 이러한 프로토콜은 구현이 복잡하고 시스템 간의 호환성 문제를 일으켰습니다.

REST는 이러한 문제를 해결하기 위해 HTTP 프로토콜을 최대한 활용하면서도 간결하고 효율적인 통신을 가능하게 만들었습니다. 
<br/>
REST API는 특히 다음과 같은 이유로 인기를 끌었습니다.

1. 단순함
    <br/>
    REST는 HTTP를 기반으로 하며 GET, POST, PUT, DELETE와 같은 기본적인 HTTP 메소드를 사용합니다. 이로 인해 구현이 간단하고 직관적입니다.
2. 확장성
    <br/>
    REST는 클라이언트와 서버의 독립성을 보장하기 때문에 서버가 변경되어도 클라이언트에 미치는 영향을 최소화합니다.
3. 호환성
    <br/>
    REST는 HTTP를 사용하므로 기존의 웹 인프라와 쉽게 통합할 수 있습니다.
4. 효율성
    <br/>
    REST는 상태 정보를 서버에 저장하지 않기 때문에 서버는 더 적은 자원으로 더 많은 요청을 처리할 수 있습니다.

## RESTful API 설계 원칙

RESTful API를 설계할 때 지켜야 할 기본적인 원칙들은 REST의 이점을 극대화하고 API의 일관성, 확장성, 가독성을 높이기 위한 가이드라인입니다. 

### 자원 기반 설계

REST는 자원 기반의 설계를 따릅니다. 모든 객체나 데이터는 자원으로 간주되며 자원은 URI(Uniform Resource Identifier)로 식별됩니다.

```
/users - 사용자 목록
/users/{id} - 특정 사용자
/posts - 게시물 목록
/posts/{id} - 특정 게시물
```

### HTTP 메소드의 적절한 사용

RESTful API에서는 HTTP 메소드를 사용하여 자원을 조작합니다. 각 HTTP 메소드는 특정 작업을 의미합니다.
- GET: 자원의 조회
- POST: 자원의 생성
- PUT: 자원의 전체 수정
- PATCH: 자원의 부분 수정
- DELETE: 자원의 삭제

### 무상태성(Stateless)

REST API는 요청 간에 서버가 클라이언트의 상태를 저장하지 않는 무상태성을 유지해야 합니다. 각 요청은 독립적이어야 하며 요청에 필요한 모든 정보는 요청 자체에 포함되어야 합니다.

- 클라이언트는 매 요청마다 인증 정보를 포함시켜야 하며 서버는 해당 정보를 저장하지 않습니다.
- 요청이 완전한 정보를 포함하고 있어야 하므로, 클라이언트는 서버 상태를 추적할 필요가 없습니다.

### 계층적이고 일관된 URI 설계

URI는 자원을 명확하고 직관적으로 표현해야 합니다. REST API에서는 자원에 대해 일관된 URL 패턴을 사용해야 하며 가독성과 유지보수성을 고려해야 합니다.
<br/>
리소스(Resource)를 URI로 표현할 때 계층적인 구조를 유지해야 합니다. 이를 통해 API 사용자가 쉽게 이해할 수 있고 REST의 직관적인 설계를 따를 수 있습니다.

#### URI 설계 원칙
1. 명사 기반 리소스 사용

    REST에서는 **명사(Noun)**를 사용하여 리소스를 식별합니다.
    트리 구조처럼 계층적인 관계 표현
    - GET /getUserList (X)
    - GET /users (O)

2. 특정 리소스의 하위 리소스를 표현할 때 폴더 구조처럼 계층적으로 설계합니다.
    - GET /users/{userId}/boards/{boardId}/comments (O)
    - GET /shops/{shopId}/products/{productId} (O)

3. 소문자와 하이픈(-) 사용을 권장하며 카멜케이스나 언더스코어는 지양

    URI에서 단어 구분 시 하이픈(-)을 사용하고, 언더스코어(_)는 사용하지 않는 것이 권장됩니다.
    - GET /user_list (X)
    - GET /user-list (O)

4. 동작(Verb) 대신 HTTP 메서드 사용

    URI에 get, update, delete 같은 동사를 사용하지 말고 HTTP 메서드(GET, POST, PUT, DELETE)를 활용합니다.
    - GET /getUser/1 (X)
    - GET /users/1 (O)

5. 컬렉션(복수형)과 단수형 사용 규칙

    컬렉션을 나타낼 때는 복수형, 특정 리소스를 나타낼 때는 단수형을 사용합니다.
    - GET /users → 전체 사용자 조회 (O)
    - GET /users/{userId} → 특정 사용자 조회 (O)


### 상태 코드(Status Codes) 사용

HTTP 상태 코드를 통해 클라이언트에게 요청 처리 결과를 알려줍니다. 성공적인 요청과 오류를 명확히 구분하기 위해 적절한 상태 코드를 사용해야 합니다.

```
200 OK: 요청 성공
201 Created: 자원 생성 성공
400 Bad Request: 클라이언트 요청 오류
404 Not Found: 자원 미발견
500 Internal Server Error: 서버 오류
```

### 버전 관리

REST API는 시간이 지나면서 변경될 수 있습니다. 버전 관리는 이를 해결하기 위한 중요한 방식입니다. API 버전 관리는 URL에 포함하거나 HTTP 헤더를 통해 전달할 수 있습니다.


## 요약

REST API는 HTTP를 기반으로 자원(Resource)을 중심으로 설계된 웹 서비스 아키텍처 스타일입니다. RESTful API를 설계할 때는 자원의 표현, HTTP 메소드의 적절한 사용, 무상태성 유지, 일관된 URI 설계 등을 지켜야 합니다. 이러한 원칙들을 따르면 효율적이고 확장 가능한 API를 만들 수 있습니다. RESTful API는 현대 웹 서비스에서 널리 사용되며, HTTP 프로토콜을 최대한 활용한 간결하고 직관적인 방식으로 애플리케이션을 구현하는 데 유용합니다.



## 추가 설명

### 웹 서비스 아키텍처 스타일 종류

#### REST (Representational State Transfer)

특징: HTTP 프로토콜을 기반으로 자원(데이터)을 URI로 식별하고, HTTP 메소드(GET, POST, PUT, DELETE 등)를 통해 자원을 조작하는 아키텍처 스타일입니다.
장점: 간결하고 직관적인 설계, 클라이언트와 서버의 독립성, 확장성.
단점: 복잡한 상태 저장 기능이나 실시간 처리에 적합하지 않을 수 있음.

#### SOAP (Simple Object Access Protocol)

특징: XML을 기반으로 한 메시지 전송 프로토콜로, 메시지 구조와 전송 방식에 대해 엄격한 규약을 따릅니다. 주로 HTTP 외에도 SMTP, FTP 등을 사용할 수 있습니다.
장점: 보안, 트랜잭션 관리, ACID(Atomicity, Consistency, Isolation, Durability)와 같은 고급 기능을 지원.
단점: 구현이 복잡하고, XML을 사용하여 데이터 크기가 커질 수 있으며, 성능에 부담이 있을 수 있음.

#### GraphQL

특징: 클라이언트가 요청하는 데이터의 구조를 명시적으로 지정할 수 있는 API 쿼리 언어입니다. 하나의 엔드포인트에서 클라이언트가 원하는 데이터만 선택적으로 요청할 수 있습니다.
장점: 클라이언트가 필요한 데이터만 가져올 수 있어 효율적인 데이터 처리 가능, 복잡한 관계를 쉽게 다룰 수 있음.
단점: 서버에서 복잡한 쿼리를 처리하는 데 시간이 걸릴 수 있고, 초기 설정이 복잡할 수 있음.

#### gRPC (Google Remote Procedure Call)

특징: 구글이 개발한 고성능 원격 프로시저 호출(RPC) 시스템으로, Protobuf(Protocol Buffers)를 사용하여 데이터를 직렬화하고, HTTP/2를 기반으로 높은 성능을 제공합니다.
장점: 빠르고 효율적인 데이터 직렬화, 양방향 스트리밍 지원, 다양한 언어 지원.
단점: REST에 비해 학습 곡선이 가파르고, HTTP/2를 지원하는 환경이 필요함.

### Query Parameter vs Path Parameter 

<!-- 구분	언제 사용?	예시 -->
- Path Parameter	
    - 리소스를 고유하게 식별할 때	
    - GET /users/{userId} → 특정 사용자 조회
- Query Parameter	
    - 검색, 필터링, 정렬 등에 사용	
    - GET /users?age=25&sort=desc → 25세 유저를 나이 내림차순으로 조회
<br/>

```
GET /users/123 → 특정 사용자(123)의 정보 조회 (Path Parameter)
GET /users?age=25&city=seoul → 나이가 25세이고, 서울에 거주하는 사용자 검색 (Query Parameter)
```

Path Parameter는 리소스를 식별하는데 사용하고 Query Parameter는 검색이나 정렬 등에 사용하는 것이 원칙입니다.

### 카멜케이스는 왜 지양 해야할까?

RESTful API에서는 모든 글자를 소문자로 쓰고 단어 구분은 하이픈(-)을 사용하는 것이 표준적인 스타일입니다.

- 카멜케이스 사용 예시 (비추천)
```
GET /userList
GET /userProfileImage
```

- 하이픈(-) 사용 예시 (추천)
```
GET /user-list
GET /user-profile-image
```

<br/>
- 일관성 유지 → 대부분의 RESTful API에서 하이픈(-)을 표준으로 사용
- 가독성 향상 → URL은 대소문자 구분이 가능하므로 소문자로만 구성하는 것이 더 직관적
- 웹 표준 준수 → W3C(웹 표준 기구)에서도 URL에서는 소문자와 하이픈을 권장

그래서 RESTful API에서는 userList 대신 user-list 형태로 쓰는 게 가장 깔끔하고 표준적입니다.
<br/>

음,,가독성 측면인거면 카멜케이스 써도 괜찮을 것 같다. 😋