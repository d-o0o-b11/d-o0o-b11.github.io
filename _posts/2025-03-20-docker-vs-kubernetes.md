---
title: Docker vs Kubernetes
description: 언제, 왜 사용해야 할까?
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요

Docker와 Kubernetes는 컨테이너 기반 애플리케이션 운영에서 핵심적인 역할을 하는 도구들입니다. 하지만 이 둘은 같은 기능을 제공하는 것이 아니라 서로 보완적인 관계입니다. 이번 글에서는 Docker와 Kubernetes의 차이점을 이해하고 어떤 상황에서 각각을 사용해야 하는지를 정리해보겠습니다.

## Docker vs Kubernetes 개념 비교

| 개념 | Docker  | Kubernetes |
| :--------------------------- |:---------------------------|:---------------------------|
| 역할  | 컨테이너 실행 및 관리 | 컨테이너 오케스트레이션 (대규모 관리) | 
| 범위  | 단일 컨테이너 또는 `docker-compose`를 통한 다중 컨테이너 관리 | 여러 대의 서버(클러스터)에서 컨테이너 자동 배포, 스케일링, 로드 밸런싱| 
| 실행 방식  | `docker run` 명령어로 컨테이너 실행 | `kubectl apply -f` 명령어로 배포 설정 적용 |
| 확장성  | 기본적으로 단일 서버에서 실행 | 클러스터 기반으로 확장 가능 |
| 복구 기능  | 컨테이너가 종료되면 수동으로 재시작해야 함 | 컨테이너 장애 발생 시 자동 복구(Self-Healing) |

- Docker는 컨테이너 실행 도구
- Kubernetes는 컨테이너 오케스트레이션 도구입니다. 

즉, Docker는 컨테이너를 개별적으로 실행하고 관리하는 역할을 하며, Kubernetes는 컨테이너를 여러 개의 서버에서 효율적으로 운영하도록 관리하는 역할을 합니다.

## 언제 Docker만 써도 되는가?
> Docker만 사용해도 충분한 경우

### 개발 및 테스트 환경

- 로컬에서 컨테이너를 실행해 개발할 때
- `docker-compose`를 사용하여 여러 개의 컨테이너를 쉽게 실행할 때

### 소규모 애플리케이션

- 하나의 서버에서 몇 개의 컨테이너만 실행하면 되는 경우
    - 개인 프로젝트나 스타트업 초기 단계에서는 Docker Compose만으로도 충분

### 단순한 배포 환경

- 서비스 규모가 작고 별도의 부하 분산이나 자동 복구가 필요하지 않을 때
- `docker-compose.yml`을 활용하여 웹 서버, 데이터베이스 등을 구성 가능

#### docker-compose 예제 (단일 서버에서 다중 컨테이너 실행)

```
db:
  image: postgres:latest
  restart: always
  environment:
    POSTGRES_USER: user
    POSTGRES_PASSWORD: password

web:
  image: my-web-app:latest
  ports:
    - "8080:80"
  depends_on:
    - db
```

위와 같이 `docker-compose up -d` 명령어를 실행하면 데이터베이스와 웹 애플리케이션이 동시에 실행됩니다.

## 언제 Kubernetes가 필요할까?
> Kubernetes를 고려해야 하는 주요 상황

### 트래픽이 증가하고 확장이 필요한 경우

- 여러 대의 서버에서 컨테이너를 실행해야 할 때
- 사용량이 많아지면 자동으로 컨테이너를 추가하여 부하를 분산

### 자동 복구(Self-Healing)가 필요한 경우

- 특정 컨테이너가 장애가 발생해도 자동으로 재시작하도록 설정 가능

### 멀티 클러스터 및 마이크로서비스 환경

- 여러 개의 독립적인 서비스(ex. API, DB, 캐시 서버 등)를 운영할 때
- 각 서비스를 컨테이너로 분리하고 K8s가 관리하도록 설정

### 배포 자동화가 필요한 경우

- `kubectl apply -f`를 통해 배포 및 업데이트를 자동화
- 롤링 업데이트(서비스 중단 없이 배포) 가능

#### Kubernetes Deployment 예제

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
deployment:
  replicas: 3  # 컨테이너 3개 실행
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-web-app:latest
        ports:
        - containerPort: 80
```

위 설정을 적용하면 my-app 컨테이너가 3개 자동으로 실행되고 장애 발생 시 새로운 컨테이너가 생성됩니다.

## Docker Compose vs Kubernetes 비교

| 비교 항목 | Docker Compose  | Kubernetes |
| :--------------------------- |:---------------------------|:---------------------------|
| 사용 범위  | 단일 서버 내에서 컨테이너 실행 | 다중 서버(클러스터) 환경에서 컨테이너 관리 | 
| 배포 방식  | `docker-compose up` | `kubectl apply -f` | 
| 확장성  | 제한적 (단일 노드) | 무한 확장 가능 (클러스터 기반) |
| 장애 복구  | 없음 (컨테이너 종료 시 직접 재시작 필요) | 자동 복구 (Self-Healing) 지원 |
| 네트워크 관리  | 단순한 내부 네트워크 연결 제공 | 서비스 디스커버리 및 로드 밸런싱 가능 |

- Docker Compose는 개발 및 테스트 환경에서 간단한 다중 컨테이너 애플리케이션을 실행하는 데 적합하지만
- 운영 환경에서 확장성과 안정성을 보장하려면 Kubernetes가 필요합니다.

## 적합한 상황

| 상황 | Docker  | Kubernetes |
| :--------------------------- |:---------------------------|:---------------------------|
| 로컬 개발 환경  | ✔️ |  | 
| 단일 서버 애플리케이션  | ✔️ | | 
| 대규모 서비스 운영  | | ✔️ |
| 자동 확장(Auto Scaling) 필요  |  | ✔️ |
| 장애 발생 시 자동 복구 필요  |  | ✔️ |
| 멀티 클러스터 운영  | | ✔️ |

