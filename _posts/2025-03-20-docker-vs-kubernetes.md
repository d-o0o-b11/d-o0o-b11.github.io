---
title: Docker vs Kubernetes 구조
description: 언제, 왜 사용해야 할까?
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요


Docker는 컨테이너 기술을 구현하는 도구이며 Kubernetes는 이를 **확장성 있게 운영**하기 위한 플랫폼입니다.  
이 글에서는 두 도구의 **구조와 실행 흐름을 중심으로** 무엇이 어떻게 다른지를 비교해보겠습니다.
<br/>

## Docker 구조
Docker는 **컨테이너 실행의 핵심 도구**로 다음과 같은 구성요소로 동작합니다.

- Docker Client: 사용자 인터페이스 (docker run, docker build 등)
- Docker Daemon: 컨테이너와 이미지의 실행, 관리 주체
- Docker Image: 실행 가능한 앱의 템플릿
- Docker Container: 실행 중인 앱 인스턴스
- Docker Registry: 이미지 저장소 (ex. Docker Hub)

```mermaid
---
config:
  theme: neo
  look: handDrawn
---
flowchart LR
    A[Docker Client]
    B[Docker Daemon]
    C[Container 1]
    D[Container 2]
    E[Docker Registry]

    A --> B
    B --> C
    B --> D
    B --> E

```

- `docker run` → Client가 Daemon에 요청
- Daemon이 이미지 Pull → 컨테이너 생성 → 실행

[Docker 구조 글 보러가기](https://d-o0o-b11.github.io/posts/docker/)

<br/>


## Kubernetes 구조
Kubernetes는 클러스터 전체에서 수많은 컨테이너를 오케스트레이션하는 시스템입니다. 주요 구성요소는 다음과 같습니다.

##### Control Plane
- API Server: 모든 요청의 진입점
- Scheduler: Pod를 실행할 노드 결정
- Controller Manager: 상태 관리 (복제, 장애 복구 등)
- etcd: 전체 클러스터 상태 저장 (key-value store)

---

##### Node(Worker)
- Kubelet: Node의 에이전트, 컨테이너 실행 지시
- Container Runtime: 실제 컨테이너 실행 (Docker, containerd 등)
- Kube Proxy: 네트워크 라우팅 및 Load Balancing

```mermaid
---
config:
  theme: neo
  look: handDrawn
---
flowchart TB
    subgraph Control Plane
        APIServer[API Server]
        Scheduler[Scheduler]
        Controller[Controller Manager]
        ETCD[etcd]
    end

    subgraph Node 1
        Kubelet1[Kubelet]
        CR1[Container Runtime]
        Pod1[Pod: Container A]
        KProxy1[Kube Proxy]
    end

    subgraph Node 2
        Kubelet2[Kubelet]
        CR2[Container Runtime]
        Pod2[Pod: Container B]
        KProxy2[Kube Proxy]
    end

    UserClient[User: kubectl] --> APIServer
    APIServer --> Scheduler
    APIServer --> Controller
    Controller --> ETCD

    Scheduler --> Kubelet1
    Scheduler --> Kubelet2

    Kubelet1 --> CR1 --> Pod1
    Kubelet2 --> CR2 --> Pod2

    APIServer --> Kubelet1
    APIServer --> Kubelet2
    KProxy1 --> Pod1
    KProxy2 --> Pod2

```

[Kubernetes 구조 글 보러가기](https://d-o0o-b11.github.io/posts/kubernetes/)

<br/>

## 구조 비교

```mermaid
---
config:
  theme: neo
  look: handDrawn
---
graph TB
  Docker["Docker"]
  K8s["Kubernetes"]

  Docker -->|컨테이너 생성 및 실행| App["애플리케이션 실행"]
  K8s -->|컨테이너 오케스트레이션| Cluster["클러스터 관리"]

  K8s -->|스케일링, 로드밸런싱, 복구| 기능들

```

| 항목    | Docker                 | Kubernetes              |
| ----- | ---------------------- | ----------------------- |
| 중심 구성 | Docker Daemon 중심 단일 구조 | Control Plane + Node 구조 |
| 실행 주체 | Daemon이 직접 컨테이너 관리     | Kubelet + Scheduler 조합  |
| 상태 저장 | 로컬 기반 / Registry 중심    | etcd (클러스터 상태 전체 저장)    |
| 확장성   | 단일 머신 기반               | 클러스터 기반 확장              |
| 장애 복구 | 없음 (수동)                | 컨트롤러 자동 복구              |


<br/>

## 컨테이너 실행 흐름 비교

### Docker
```mermaid
---
config:
  theme: neo
  look: handDrawn
---
graph LR
  A[👤 User] --> B[🐋 Docker Client]
  B --> C[🛠️ Docker Daemon]
  C --> D[📦 Container 실행]

```

---

### Kubernetes
```mermaid
---
config:
  theme: neo
  look: handDrawn
---
graph LR
  A[👤 User] --> B[kubectl CLI]
  B --> C[API Server]
  C --> D[Scheduler / Controller Manager]
  D --> E[Kubelet]
  E --> F[🛠️ Container Runtime]
  F --> G[📦 Pod 실행]

```



<br/>


## 결론
**Docker**는 단일 머신에서 컨테이너를 관리하는 데 적합하며 **Kubernetes**는 컨테이너의 집합을 클러스터 전체에서 안정적으로 운영하는 시스템입니다.

둘은 대체재가 아니라 서로를 기반으로 확장하는 관계입니다.
**Docker는 애플리케이션을 컨테이너화하고 Kubernetes는 이를 대규모로 운영하는 플랫폼입니다.**

> "Docker로 빌드하고 Kubernetes로 운영"하는 조합이 일반적입니다.
{: .prompt-tip }
