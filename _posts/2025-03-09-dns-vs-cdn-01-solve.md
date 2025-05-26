---
title: CloudFront는 왜 S3에만 써야 한다고 생각했을까?
description: DNS와 CDN 개념을 정리하며 CloudFront를 API 서버에 적용하는 가능성을 새롭게 이해하게 된 과정
categories: [Dev-Notes, Practice]
tags: [typography]
# pin: true
math: true
mermaid: true
---

[DNS와 CDN 관계 정리](https://d-o0o-b11.github.io/posts/dns-vs-cdn-0/)

## 글을 작성하며 생긴 인사이트

지금까지 제가 사용해 본 배포 구조는 다음과 같았습니다.

1. `Route 53 → ELB (HTTPS: ACM) → EC2`
2. `Route 53 → nginx (HTTPS: Let's Encrypt) → EC2`

이 구조에서는 SSL 인증서를 **ELB나 nginx 레벨**에서 적용했고 CDN은 **S3 정적 파일 제공**에만 활용해 왔습니다.

**CloudFront는 S3 전용 도구**처럼 인식하고 있었고 EC2나 API 서버 앞에 붙일 생각은 해보지 않았습니다.

<br/>

그런데 이번에 정리하면서 다음과 같은 궁금한 점이 생겼습니다.

> **"CDN은 왜 S3에만 붙였을까?"**


### 알게 된 점

CloudFront는 단순한 "S3 가속기"가 아니라 **정식 콘텐츠 전송 네트워크(CDN)**입니다.

**Origin이 S3든 EC2든 ELB든 Lambda든 모두 연결 가능**하며 **CloudFront를 API 서버 앞단에 둬도 문제없고 오히려 더 많은 장점**이 있었습니다.

<br/>

### 어떤 점이 좋을까?

1. **정적 API 캐싱 가능**
   - `/api/products`, `/api/posts`처럼 자주 변경되지 않는 데이터는 CloudFront에서 캐싱 가능합니다.
   - **서버 부하를 줄이고 응답 속도도 향상**할 수 있습니다.

2. **Edge Location 활용**
   - 한국 사용자가 미국 리전의 EC2에 직접 접근하는 것보다 **서울 엣지 서버를 통해 더 빠르게 응답 가능**합니다.

3. **보안 강화**
   - **WAF**, **DDoS 방어(AWS Shield)**, **ACM(SSL)** 등을 CloudFront에서 처리 가능합니다.
   - Origin(EC2/S3 등)을 외부 접근 차단하고 CloudFront를 통해서만 접근하도록 설정하면 보안성 강화할 수 있습니다.

4. **도메인 통일성 유지**
   - CloudFront를 끼움으로써 **브랜딩 및 관리 측면에서 유리**합니다.
     - ex. `cdn.domain.com`, `api.domain.com` 등

<br/>

###  느낀 점

CloudFront를 **정적 파일(S3)**에만 사용했던 건 일종의 **선입견**이었습니다.

이번 글을 통해,  
**EC2 + API 서버에도 CDN을 적용할 수 있다는 점**을 새롭게 이해하게 되었고  
**속도, 캐싱, 보안** 등 실질적인 이점도 많다는 걸 체감했습니다.

앞으로는 정적 리소스뿐 아니라 **API 서버나 백엔드도 CDN의 적용 대상**으로 고려하면서 설계할 수 있겠다는 확신이 생겼습니다.
