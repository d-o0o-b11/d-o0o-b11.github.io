---
title: HTTP , HTTPS 동작 원리 [3]
description: HTTP 캐싱
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

## HTTP 캐싱
불필요한 네트워크 요청을 줄여 성능을 최적화하는 기술입니다.

### 캐싱 관련 헤더

| 헤더    | 설명 |
| :--------------------------- |:---------------------------|
| Cache-Control  | 캐싱 정책을 정의 (no-cache, max-age=3600, public, private) | 
| ETag | 리소스의 버전을 나타내는 해시값, 변경 시 새로운 값 제공 | 
| Last-Modified  | 리소스의 마지막 수정 시간 표시 | 

### 브라우저 캐싱 vs 서버 캐싱

브라우저 캐싱: 클라이언트에서 캐싱된 데이터를 재사용 (ex: Cache-Control)

서버 캐싱: CDN, 프록시 서버 등을 활용해 응답을 캐싱

- 브라우저 캐싱 
    - 클라이언트에서 리소스를 저장하여 동일한 요청 시 캐시된 데이터를 재사용합니다.
    - ex. Cache-Control 헤더를 사용하여 브라우저가 캐싱 정책을 따르도록 설정

<br/>

- 서버 캐싱
    - CDN(Content Delivery Network) 또는 프록시 서버를 활용하여 서버 측에서 응답을 캐싱합니다.
    - ex. Cloudflare, Varnish 같은 캐시 서버 사용
    - ex. Redis, Memcached 등을 사용해 DB 조회 결과를 캐싱하여 응답 속도 개선

### CDN, 프록시 서버 vs Redis
- CDN/프록시 서버 (예: Cloudflare, Varnish)
    - HTTP 응답을 캐싱해서 서버 부하를 줄임
    - ex. 정적 리소스(이미지, CSS, JS 파일)를 캐싱하여 속도 향상

<br/>

- Redis/Memcached
    - DB 조회 결과를 캐싱해서 애플리케이션의 데이터 조회 성능을 향상
    - ex. MySQL에서 자주 조회하는 데이터를 Redis에 저장하여 빠르게 제공

### 브라우저 캐싱 vs 서버 캐싱 목적과 효율성 측면
1. 브라우저 캐싱 (Client-side Caching)
브라우저 캐싱은 클라이언트 측에서 수행됩니다. 사용자가 웹사이트에 방문하면 웹 페이지를 구성하는 이미지, CSS 파일, 자바스크립트 파일 등을 브라우저가 저장해두고 같은 자원이 다시 필요할 때 서버에 요청하지 않고 로컬에서 바로 불러옵니다.

**어떤 것을 브라우저 캐싱할까?**
- 정적 파일: 이미지, 스타일시트(CSS), 자바스크립트 파일 등 변경이 잘 일어나지 않는 리소스
- 주요 페이지: 페이지 구조나 콘텐츠가 자주 바뀌지 않는 경우
- API 응답 데이터: 자주 변하지 않는 데이터나 결과를 JSON 형식으로 반환하는 API 응답

**장점**
- 빠른 응답: 로컬에서 파일을 바로 불러오기 때문에 페이지 로드 속도가 빨라집니다.
- 서버 부하 감소: 사용자가 같은 자원을 여러 번 요청할 필요가 없어 서버의 부담이 줄어듭니다. (서버 자원 절약)
- 네트워크 대역폭 절약: 이미 로컬에 저장된 데이터를 사용하므로 네트워크 요청을 줄일 수 있습니다. (트래픽 절감)
- 비용 절감: 서버 요청이 줄어들면, 서버나 네트워크 인프라에 드는 비용을 절감할 수 있습니다.

**단점**
- 캐시 관리 문제: 브라우저 캐시는 캐시 만료 기간이나 버전 관리가 잘못 설정되면 오래된 데이터가 사용될 수 있습니다. 이로 인해 사용자가 업데이트된 콘텐츠를 보지 못할 수 있습니다.
- 보안 문제: 민감한 데이터가 캐시될 경우 다른 사람이 같은 컴퓨터를 사용하면서 접근할 수 있게 될 수 있습니다.
- 동적 콘텐츠에 부적합: 사용자 맞춤형 데이터나 자주 변경되는 콘텐츠는 브라우저 캐시로 적합하지 않으며 캐시를 제대로 관리하지 않으면 잘못된 데이터가 로드될 수 있습니다.

<br/>

2. 서버 캐싱 (Server-side Caching)
서버 캐싱은 서버에서 데이터를 저장하고 같은 요청이 들어올 때마다 빠르게 반환하는 방식입니다. Redis는 대표적인 서버 캐시 시스템으로 자주 요청되는 데이터나 계산 비용이 큰 데이터를 메모리에 캐싱하여 빠르게 응답할 수 있게 합니다.

**어떤 것을 서버 캐싱할까?**
- 동적인 데이터: 자주 바뀌거나 사용자의 요청에 따라 변하는 데이터 (예: DB 조회 결과)
- 복잡한 계산: 계산에 시간이 오래 걸리는 데이터를 미리 계산하여 캐싱
- 빈번한 API 호출: API 요청에 대해 응답 속도를 빠르게 하기 위해 데이터를 미리 캐싱

**장점**
- 빠른 응답: 서버가 자주 요청되는 데이터를 캐싱하면 그 데이터를 다시 계산하거나 DB에서 조회할 필요가 없어져서 빠른 응답을 제공합니다.
- 서버 부하 감소: 동일한 요청에 대해 매번 DB나 복잡한 처리를 반복하지 않아서 서버 부하가 줄어듭니다.
- 비용 절감: 자주 요청되는 데이터는 캐시에서 바로 반환되므로 데이터베이스나 다른 리소스의 사용을 줄일 수 있습니다.
- 유연한 캐싱 전략: 서버 캐싱은 데이터의 변동성에 맞춰 다양한 전략(예: TTL, LRU 등)을 설정할 수 있어 동적이고 변화가 자주 일어나는 데이터에도 유용합니다.

**단점**
- 메모리 사용: 서버 캐시(ex. Redis)는 메모리를 많이 사용하므로 캐시할 데이터가 많아지면 서버의 메모리 사용량이 증가하고 비용 증가로 이어질 수 있습니다.
- 복잡한 관리: 서버 캐싱을 제대로 관리하지 않으면 데이터의 일관성 문제가 발생할 수 있습니다. 예를 들어, 데이터가 갱신되었을 때 캐시를 무효화해야 하는데 이를 제대로 관리하지 않으면 오래된 데이터가 반환될 수 있습니다.
- 비용 문제: Redis와 같은 메모리 기반 캐시 시스템은 고용량의 데이터를 캐시하려면 많은 메모리가 필요합니다. 메모리 비용이 증가할 수 있으며 캐시 데이터를 너무 많이 저장하면 서버의 성능 저하나 다운타임을 초래할 수 있습니다.


3. 비용 측면에서의 고려사항
Redis는 메모리 기반 캐시 시스템이기 때문에 데이터가 많아질수록 비용이 증가할 수 있습니다. 하지만 이를 잘 관리하면 자주 변하지 않는 데이터를 캐싱해서 서버 부하와 DB 비용을 절감할 수 있습니다.
브라우저 캐싱은 비용이 들지 않기 때문에, 정적 파일이나 변경이 자주 일어나지 않는 데이터는 브라우저 캐싱으로 처리하는 것이 더 경제적입니다.


### 결론
정적 파일은 브라우저 캐싱으로 처리하면 좋습니다. 로컬에 저장되어 재사용되므로 서버와 네트워크 부하를 줄일 수 있습니다.
동적 데이터나 자주 변경되는 데이터는 서버 캐싱 (Redis 등)을 활용해 응답 속도를 높이고, DB 부담을 줄여야 합니다.
브라우저 캐싱과 서버 캐싱은 각각 장단점이 있으며 두 가지를 적절하게 혼합하여 사용하는 것이 최적의 성능을 얻는 방법입니다.



> 최신 데이터가 중요하다면 DB → Redis → 클라이언트 흐름을 잘 설계하는 게 핵심!
{: .prompt-info }

<br/><br/>

> 서버 캐싱만 사용해봤어서, 캐싱은 1개만 있는 줄알았는데,,브라우저 캐싱이란게 있었다. 반환할때 header에 캐싱 옵션 추가해주면 별도의 redis 없이 캐싱이 가능하다! 
