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

시스템 관리자가 수행하는 작업(로깅, 모니터링 등)을 자동화하고 컨테이너를 효율적으로 관리해주는 오케스트레이션 소프트웨어입니다.

### 구조

마스터(Master)와 워커(Worker) 노드로 구성되며 이들을 포함한 구조를 클러스터(Cluster) 라고 합니다.

- 마스터 노드 <br/>
  클러스터를 관리하며 워커 노드들에게 명령 내림
- 워커 노드 <br/> 
  마스터의 명령을 받아 실제 컨테이너 실행

쿠버네티스에서는 컨테이너 대신 **Pod**라는 단위를 사용하여 배포 및 관리를 수행합니다.

#### Pod

- 쿠버네티스에서 배포 가능한 가장 작은 단위
- Pod 안에는 1개 이상의 컨테이너가 포함될 수 있음
- Pod 내부 컨테이너들은 IP 및 포트 공유, 디스크 볼륨 공유 등의 장점이 있습니다.

*컨테이너 A, B는 별도의 IP 호출 없이 localhost를 통해서 통신 가능 (Pod 내부의 컨테이너끼리 IP 및 Port를 공유함으로써)

*Pod에 로그 수집기 탑재하여 배포 시 Pod 내부 컨테이너들의 로그 모두 수집 가능 (디스크 볼륨 공유)


### Master 구성 요소

Master Node는 클러스터를 제어하고 관리하는 역할을 합니다. Worker Node와 **직접 통신하지 않고 API Server를 통해** 명령을 내리고 상태를 조회하는 방식으로 동작합니다. 즉, 사용자가 명령을 내리면 Master가 이를 처리하고 Worker Node에 전달하는 구조입니다.

Master는 크게 4가지 핵심 요소로 구성됩니다.

![Image](https://github.com/user-attachments/assets/722a44d3-cc70-4078-93c8-8c41623bceb5?raw=true)

1. API Server
  - 클러스터 내 모든 요청을 처리하는 핵심 모듈
  - 사용자가 내린 명령을 받아 저장하고 현재 상태를 조회하는 역할
  - 컨테이너 로그를 보여주거나 디버깅을 위한 명령을 실행할 수도 있음

    > 하지만 API Server는 단순히 요청을 받고 저장하는 역할만 하므로 실제 처리는 다른 구성 요소들이 담당합니다.
    {: .prompt-info }

2. Scheduler
  - 새로운 Pod가 생성될 때 적절한 워커 노드에 할당하는 역할
  - 현재 클러스터의 리소스 상황을 고려하여 최적의 워커를 선택

3. Kube-Controller
  - 클러스터 내 오브젝트들의 상태를 지속적으로 모니터링하고 관리
  - 원하는 상태와 실제 상태가 다를 경우 자동으로 조정하여 정상적인 운영 유지

4. Etcd
  - 클러스터의 모든 설정과 상태 데이터를 저장하는 분산형 Key-Value 저장소
  - 클러스터의 유일한 상태 저장소이므로 백업만 잘하면 언제든지 복구 가능

> 마스터의 나머지 모듈들은 **stateless(무상태)**하게 동작하기 때문에 etcd가 클러스터의 핵심 데이터를 안전하게 보관하는 것이 중요하다.
{: .prompt-info }


### Worker 구성 요소

Worker Node는 실제로 컨테이너가 실행되는 곳입니다. Master Node와 통신하면서 Pod를 생성하고 네트워크 및 볼륨을 설정하여 애플리케이션이 정상적으로 동작하도록 합니다.

![Image](https://github.com/user-attachments/assets/de7b8964-987f-458a-9e39-6d15440a0cad?raw=true)

1. Kubelet
  - 워커 노드에서 Pod의 생명주기를 관리하는 핵심 에이전트
  - Pod를 생성하고 내부 컨테이너의 상태를 지속적으로 체크
  - 주기적으로 마스터에 상태를 보고하여 Pod가 정상적으로 동작하도록 유지
  - API Server의 요청을 받아 컨테이너의 로그를 전달하거나 특정 명령을 실행

2. Kube Proxy
  - Pod 간 네트워크 통신을 관리하는 역할
  - TCP/UDP 트래픽을 포워딩하여 여러 Pod를 묶어 로드 밸런싱(라운드 로빈 방식) 제공
  - 클러스터 내부 및 외부 트래픽을 조정하여 원활한 서비스 제공

> Kubelet이 Pod의 상태를 관리하고 Kube Proxy가 네트워크를 관리하는 역할을 합니다.
{: .prompt-info }

### Pod 생성되는 과정

Pod는 ReplicaSet을 통해 생성되며 Master와 Worker Node가 협력하여 Pod를 원하는 상태로 맞춰갑니다.

![Image](https://github.com/user-attachments/assets/e777a9e0-4934-4de1-ae63-ecf64d908ea9?raw=true)

1. ReplicaSet 생성 (kubectl)
  - 사용자가 kubectl apply 명령어를 실행하여 ReplicaSet을 생성
  - 이 과정에서 Pod의 개수와 스펙이 정의됨

2. 생성 요청 감시
  - Master Node의 API Server가 해당 요청을 수신
  - 요청된 정보를 **etcd(쿠버네티스 저장소)**에 저장
  - Controller Manager가 ReplicaSet을 감시하며 필요한 Pod의 개수 확인

3. Pod 생성 요청
  - ReplicaSet은 현재 실행 중인 Pod 수를 확인하고 부족한 경우 Pod 생성 요청 보냄
  - API Server가 이 요청을 받아 새로운 Pod 오브젝트를 etcd에 저장

4. Pod 생성 요청 감시
  - Scheduler가 etcd를 감시하며 **아직 할당되지 않은 Pod(노언사인 Pod)**가 있는지 확인한다.

5. Node에 Pod 할당
  - Scheduler가 적절한 Worker Node를 선택하여 Pod를 할당
  - API Server는 etcd에 할당된 Node 정보를 업데이트

6. Pod 할당 감시
  - 각 워커 노드의 Kubelet은 자신에게 할당된 Pod가 있는지 감시
  - Kubelet이 할당된 Pod를 확인하면 해당 Pod를 실제로 실행하기 위한 작업 시작

7. 컨테이너 생성
  - Kubelet이 **Container RunTime(Docker, containerd 등)**을 통해 Pod 내부의 컨테이너 실행
  - 네트워크 및 볼륨을 설정한 후 컨테이너가 정상적으로 실행되는지 확인

8. Pod 상태 업데이트
  - Kubelet이 Pod의 상태 정보를 API Server에 업데이트
  - API Server는 etcd에 새로운 Pod 상태를 반영하고 사용자가 kubectl get pods 등의 명령어를 통해 상태를 조회할 수 있도록 함


> kubectl apply → API Server가 요청 저장 → ReplicaSet이 필요한 Pod 생성 요청 → Scheduler가 워커에 Pod 할당 → Kubelet이 컨테이너 실행 및 상태 업데이트
{: .prompt-info }


#### ❓ 꼭 도커만 써야 할까?

아니다!

쿠버네티스는 원래 도커를 기본 컨테이너 런타임으로 사용했지만 2020년 이후부터 containerd나 CRI-O 같은 다른 컨테이너 런타임도 지원합니다.

최근에는 containerd(도커에서 따로 분리된 경량 컨테이너 런타임)를 많이 사용합니다.

하지만 여전히 도커를 이용해서 컨테이너를 만들고 쿠버네티스에서 실행하는 게 일반적입니다.


### 주요 기능 4가지 

1. Self-Healing <br/>
  컨테이너의 상태를 감지하고 비정상적인 경우 자동으로 복구

2. Rolling Update & Rollback <br/>
  서비스 중단 없이 새로운 버전으로 업데이트 가능

3. Load Balancing <br/>
  트래픽을 자동으로 분산하여 부하 조절

4. Auto Scaling <br/>
  트래픽 증가 시 자동으로 Pod 개수를 조정