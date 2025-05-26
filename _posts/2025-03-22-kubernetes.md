---
title: Kubernetes
description: 구조 + 최종 정리
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## Kubernetes 란?

컨테이너화된 애플리케이션의 배포, 확장, 관리 등을 자동화해주는 **컨테이너 오케스트레이션 플랫폼**입니다.  
수많은 컨테이너를 클러스터 형태로 묶어 관리하며 장애 조치, 로드 밸런싱, 자동 스케일링, 배포 자동화 등 다양한 기능을 제공합니다.

시스템 관리자가 수동으로 처리하던 작업(모니터링, 배포, 복구 등)을 자동화하여 **보다 안정적이고 효율적인 인프라 운영**을 가능하게 합니다.

<br/>


## 구조

기본적으로 **마스터 노드(Master Node)**와 **워커 노드(Worker Node)**로 구성된 클러스터 형태로 운영됩니다.

- **마스터 노드**는 전체 클러스터를 제어하고 관리하는 역할을 합니다.
- **워커 노드**는 실제 컨테이너가 배포되고 실행되는 노드입니다.

> 쿠버네티스에서는 하나의 컨테이너가 아닌 **Pod** 단위로 리소스를 배포하고 관리합니다.

<br/>

```mermaid
---
config:
  theme: neo
  look: handDrawn
---
flowchart LR
    subgraph Master Node
        APIServer["API Server"]
        Scheduler["Scheduler"]
        ControllerManager["Controller Manager"]
        Etcd["etcd (Key-Value Store)"]
        APIServer -->|상태 저장| Etcd
        ControllerManager -->|실행 상태 감시| Etcd
    end

    subgraph Worker Node 1
        Kubelet1["Kubelet"]
        KubeProxy1["Kube-Proxy"]
        Pod1["Pod A"]
        Pod2["Pod B"]
        Kubelet1 -->|컨테이너 실행| Pod1
        Kubelet1 -->|컨테이너 실행| Pod2
        KubeProxy1 -->|트래픽 라우팅| Pod1
        KubeProxy1 -->|트래픽 라우팅| Pod2
    end

    subgraph Worker Node 2
        Kubelet2["Kubelet"]
        KubeProxy2["Kube-Proxy"]
        Pod3["Pod C"]
        Kubelet2 -->|컨테이너 실행| Pod3
        KubeProxy2 -->|트래픽 라우팅| Pod3
    end

    APIServer -->|스케줄 요청 전달| Scheduler
    Scheduler -->|적절한 노드 선택| APIServer
    ControllerManager -->|Pod 상태 관리| APIServer
    APIServer -->|Pod 실행 명령 전달| Kubelet1
    APIServer -->|Pod 실행 명령 전달| Kubelet2

```


#### Pod 란?

- 쿠버네티스에서 배포 가능한 가장 작은 단위입니다.
- 하나의 Pod 안에는 **1개 이상의 컨테이너**가 포함될 수 있으며 이들은 같은 네트워크(IP, 포트) 및 볼륨을 공유합니다.
- 컨테이너 간 통신은 `localhost`로 가능하며 로그 수집기 등도 Pod 단위로 함께 배포할 수 있습니다.

> Pod 내부의 컨테이너들은 **하나의 유닛처럼 작동**하며 환경 공유가 가능합니다.


```mermaid
---
config:
  theme: neo
  look: handDrawn
---
flowchart TD
    subgraph Pod
        C1["Container 1"]
        C2["Container 2"]
        Volume["공유 Volume<br/>→ 로컬 파일 공유 공간"]
        Net["공유 Network (IP/Port)<br/>→ 컨테이너 간 통신 가능"]
        C1 -->|로컬 파일 공유| Volume
        C2 -->|로컬 파일 공유| Volume
        C1 -->|동일 IP/포트 사용| Net
        C2 -->|동일 IP/포트 사용| Net
    end

```

<br/>


### Master 구성 요소

마스터 노드는 클러스터 전반을 제어/관리하고 워커 노드에 명령을 내리는 역할을 합니다.  
쿠버네티스의 마스터 컴포넌트들은 대부분 **stateless**하며 모든 클러스터 상태 정보는 etcd에 저장됩니다.




#### 1. API Server

- 클러스터 내부 모든 요청의 진입 지점입니다.
- 사용자 또는 시스템의 요청을 수신하고 인증 및 검증을 거쳐 저장소(etcd)에 반영합니다.
- 요청 처리 자체는 수행하지 않으며 이를 적절한 컴포넌트에 전달합니다.

---

#### 2. Scheduler

- 새롭게 생성된 Pod를 감지하고 **적절한 워커 노드**에 할당합니다.
- 노드의 CPU, 메모리 사용량, 태그, 노드 상태 등을 고려하여 최적의 위치에 Pod를 배치합니다.

---

#### 3. Controller Manager

- 실제 클러스터 상태와 사용자가 원하는 상태 간의 차이를 지속적으로 감시하고 자동으로 조정합니다.
- ReplicaSet, Node 상태, Endpoint, Secret 등 여러 컨트롤러들이 실행됩니다.

---

#### 4. Etcd

- 클러스터의 모든 상태와 설정 데이터를 저장하는 **분산형 Key-Value 저장소**입니다.
- 쿠버네티스의 유일한 데이터 저장소로서 중요하며 백업이 매우 중요합니다.

> etcd만 보존하면 마스터 노드들은 stateless하게 언제든 재구성할 수 있습니다.
{: .prompt-info }

<br/>

## Worker Node 구성 요소

워커 노드는 실제로 컨테이너가 배포되고 실행되는 노드입니다.

Master Node와 통신하면서 Pod를 생성하고 네트워크 및 볼륨을 설정하여 애플리케이션이 정상적으로 동작하도록 합니다.


### 1. Kubelet

- 워커 노드의 핵심 에이전트입니다.
- 자신에게 할당된 Pod를 감시하고 컨테이너 실행 및 상태를 관리합니다.
- API Server와 주기적으로 통신하며 상태를 보고합니다.

---

### 2. Kube Proxy

- Pod 간의 네트워크 통신을 중계합니다.
- 클러스터 내부에서 TCP/UDP 트래픽을 포워딩하며 로드밸런싱 역할도 수행합니다.

> Kubelet은 Pod 실행 및 상태관리, Kube Proxy는 네트워크 관리 역할을 수행합니다.
{: .prompt-info }

<br/>


## Pod 생성 흐름

쿠버네티스에서는 **ReplicaSet**을 통해 Pod를 생성하고 이를 Master와 Worker가 협력하여 처리합니다.


```mermaid
---
config:
  theme: neo
  look: handDrawn
---
sequenceDiagram
    participant User as 사용자의 kubectl
    participant API as API Server
    participant ETCD as etcd
    participant CM as Controller Manager
    participant SCH as Scheduler
    participant KBL as Kubelet

    User->>API: kubectl apply 실행 (Deployment/ReplicaSet 요청)
    API->>ETCD: 요청 정보 저장
    CM->>ETCD: 원하는 상태 감시
    CM->>API: 감시 중 부족한 Pod 수만큼 생성 요청
    API->>SCH: 스케줄링 요청 전달
    SCH->>API: 적절한 워커 노드 결정
    API->>KBL: 해당 노드의 Kubelet에 실행 명령
    KBL-->>API: 실행 결과 및 상태 보고

```


### 흐름 요약

##### 1. kubectl apply
사용자가 명령어를 실행하여 ReplicaSet 또는 Deployment 정의

##### 2. API Server 요청 수신
명령을 수신하고 정보를 etcd에 저장

##### 3. ReplicaSet 감시 및 Pod 생성 요청
Controller Manager가 필요한 Pod 수를 감시하고 생성 요청

##### 4. Scheduler가 노드 선정
할당되지 않은 Pod를 감지하고 적절한 워커 노드를 선택

##### 5. Kubelet이 Pod 실행
해당 노드의 Kubelet이 실제 컨테이너를 실행하고 상태 보고

> Pod 생성은 `kubectl → API Server → Controller Manager → Scheduler → Kubelet` 순서로 흘러갑니다.
{: .prompt-info }

<br/>


## 주요 기능 4가지 

```mermaid
---
config:
  theme: neo
  look: handDrawn
---
graph TD
    A["Self-Healing"]
    A -->|Pod 장애 발생 시| A1["자동 재시작/재배포"]

    B["Rolling Update & Rollback"]
    B -->|새 버전 배포| B1["점진적 업데이트"]
    B -->|문제 발생 시| B2["즉시 롤백"]

    C["Load Balancing"]
    C -->|트래픽 자동 분산| C1["여러 Pod에 부하 분산"]
    C -->|내부 DNS 사용| C2["Service로 간편 접근"]

    D["Auto Scaling"]
    D -->|리소스 사용량 감지| D1["Pod 수 증가/감소"]
    D -->|HPA 활용| D2["수평 확장"]

```


### 1. Self-Healing

- Pod가 비정상 종료되면 자동으로 재시작하거나 다른 노드에 재배포합니다.
- 사용자는 일일이 컨테이너 상태를 감시할 필요 없이 안정적인 운영이 가능합니다.

---

### 2. Rolling Update & Rollback

- 서비스 중단 없이 새로운 버전의 애플리케이션으로 순차적 업데이트가 가능합니다.
- 문제가 발생한 경우 빠르게 이전 버전으로 롤백할 수 있습니다.

---

### 3. Load Balancing

- 동일 서비스를 제공하는 여러 Pod 간에 트래픽을 자동 분산하여 부하를 줄입니다.
- 내부 DNS와 함께 Service 객체를 통해 접근을 단순화합니다.
  - 쿠버네티스의 Service 객체는 클러스터 내 Pod들을 묶어 하나의 엔드포인트로 제공하며 DNS 이름을 할당받아 내부에서 쉽게 접근할 수 있도록 합니다.

---

### 4. Auto Scaling

- CPU/메모리 사용량에 따라 Pod 개수를 자동으로 조정합니다.
- HPA(Horizontal Pod Autoscaler)로 수평 확장 구현이 가능합니다.
