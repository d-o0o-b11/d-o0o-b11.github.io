---
title: 코드 리팩토링 (프로젝트 1차)
description: 프로젝트 구조 개선 중심의 리팩토링 기록
# author: d_o0o_b
categories: [Dev-Notes, Practice]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요
약 1년 반 전에 개발했던 프로젝트를 리팩토링하고자 합니다.  
이번 1차 리팩토링에서는 전체를 한 번에 고치기보다는 **구조 개선과 도메인 중심 설계**에 중점을 두고 진행했습니다.


<br/>

## Entity 설계 검토
리팩토링에 앞서 기존 Entity 설계를 다시 살펴보았습니다.

가장 먼저 들었던 생각은 **"정말 엉망이다..."** 였습니다. 😭  
**확장성 없는 구조**, **불필요한 컬럼**, **중복되는 테이블**, 그로 인해 발생한 **불필요한 연관관계** 등의 문제가 있었습니다.

당시에는 데이터 구분에 대한 명확한 기준 없이 설계가 이루어졌고 이로 인해 현재와 같은 비효율적인 구조가 만들어졌던 것 같습니다.

### 1. 확장성을 고려하지 않은 설계
해당 서비스에는 카카오 소셜 로그인 기능이 구현되어 있으며 이를 위해 별도의 카카오 유저 테이블을 구성했습니다.  
그러나 추후 다른 소셜 로그인(구글, 네이버 등)을 추가하게 된다면 각 로그인 방식마다 별도의 테이블과 컬럼을 새로 정의해야 하는 구조였습니다. 이는 유지보수와 확장성 측면에서 매우 취약합니다.



#### 개선 방향
- `auth` 테이블: 소셜 로그인 방식에 관계없이 **외부 유저 ID, 로그인 플랫폼 코드 등 인증 관련 정보**를 통합 관리  
- `user` 테이블: 닉네임, 프로필 이미지 등 **내부 서비스 사용자 정보**를 담당

이러한 구조로 개선하면 소셜 로그인 방식이 추가되더라도 테이블 구조에는 변화가 없으며 **인증 수단의 확장에 유연하게 대응**할 수 있어 유지보수가 수월해집니다.



---

### 2. 중복된 테이블 구조
서비스의 모든 사용자는 기본적으로 6개의 폴더(업무, 영화, 독서, 여행, 버킷리스트, 기타)를 보유하게 되어 있습니다. 
기존에는 **기본 폴더 데이터를 저장하는 테이블**과 **사용자가 직접 생성한 폴더를 저장하는 테이블**을 분리하여 구성했습니다.

이로 인해 연관된 위시카드(FK)의 참조 대상이 중복되고 조회 및 연관관계가 복잡해지는 문제가 발생했습니다.


#### 개선 방향
폴더 테이블을 단일 테이블로 통합하고 회원가입 마지막 단계에서 기본 폴더를 자동 생성하도록 처리합니다.  
이렇게 하면 위시카드가 속한 폴더를 단일 FK로 관리할 수 있고 비즈니스 로직도 명확해져 유지보수성이 크게 향상됩니다.


<br/>

## 아키텍처 리팩토링

기존 구조는 전통적인 **3-Layer 아키텍처 (Controller - Service - Repository)**를 따르고 있었으나,  
이번 리팩토링에서는 **[Vertical Slice Architecture](https://d-o0o-b11.github.io/posts/vertical-slice-architecture/)**를 기반으로 구조를 개선했습니다.

Vertical Slice 아키텍처는 각 기능 단위로 책임을 분리해 **가시성과 응집도가 높으며** 기능 단위로 배포 및 테스트가 용이합니다.  
여기에 **DDD** 요소를 접목하여 **도메인 중심의 계층 분리**를 적용했습니다.

<br/>

## 테스트 전략

리팩토링을 진행하기 전에 통합 테스트 코드를 작성해두고 하는 편이지만, 이번 리팩토링에서는 **도메인 모델의 변경이 많았기 때문에 단위 테스트 위주로 진행**했습니다.


> 도메인 로직 검증을 위한 유닛 테스트를 우선적으로 작성하고 전체 시스템 안정화 이후 통합 테스트를 진행할 예정입니다.
{: .prompt-info }


<br/>

## 회고

단순 CRUD 기반의 프로젝트이기에 리팩토링이 어렵지 않을 것으로 예상했지만 **초기 설계의 문제점**으로 인해 생각보다 많은 리팩토링 비용이 들었습니다.

- Swagger 반환 타입 누락
- 광범위한 `any` 타입 남용
- 필요 없는 API까지 명세 작성

이러한 경험을 통해 다시금 깨달았습니다.

> 좋은 코드는 현재를 위한 것이 아니라 변화에 유연한 미래를 위한 것입니다.
{: .prompt-info }

기능만 동작하게 구현하는 것이 아니라 **변경이 발생했을 때 영향 범위가 좁고** **새로운 기능이 쉽게 추가**될 수 있으며 **구조를 빠르게 이해하고 유지보수**할 수 있는 구조를 갖추는 것이 생산성과 품질을 동시에 지키는 핵심임을 확인할 수 있었습니다.

이번 리팩토링을 계기로 앞으로의 프로젝트에서도 **"구조를 우선하고 로직은 그 위에 쌓는다"**는 원칙을 일관되게 실천할 수 있는 기반이 마련되었다고 생각합니다.

---

> 📌 관련 GitHub Repository: [github.com/d-o0o-b11/306_potenday](https://github.com/d-o0o-b11/306_potenday)
