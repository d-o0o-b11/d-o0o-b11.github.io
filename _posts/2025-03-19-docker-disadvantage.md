---
title: 도커 단점
description: 장점이 있다면 단점도 있다
# author: d_o0o_b
categories: [Dev-Notes, Theory]
tags: [typography]
# pin: true
math: true
mermaid: true
---

## 개요

이전 글에서는 **가상 머신(VM)의 단점**인 환경 불일치, 배포의 복잡성, 리소스 사용량 등의 문제를 해결하기 위해 **Docker가 등장**하게 되었음을 설명하였습니다.  
Docker는 컨테이너 기반 가상화 기술을 활용하여 보다 경량화된 애플리케이션 배포 및 관리를 가능하게 하였으며, 이를 통해 개발 및 운영(DevOps) 환경에서 널리 사용되고 있습니다.  

하지만 Docker도 완벽한 솔루션은 아닙니다. VM의 단점을 보완했지만 반대로 새로운 단점들이 존재합니다.  
이번 글에서는 **Docker의 단점과 그 해결 방법**에 대해 알아보겠습니다.  

## 보안 이슈

### 문제점

- Docker 컨테이너는 **Host OS의 커널을 공유**하기 때문에 한 컨테이너에서 커널 취약점이 발견될 경우 다른 컨테이너나 Host OS도 영향을 받을 가능성이 있습니다.  
- 권한이 높은 컨테이너(예: `--privileged` 옵션 사용)는 Host OS의 중요한 설정을 변경할 수 있어 보안 위협이 될 수 있습니다.  
- 기본적으로 **컨테이너 간 완벽한 격리가 이루어지지 않으므로** VM보다 보안성이 낮을 수 있습니다.  

### 해결 방법

- **최소 권한 원칙 적용** → 컨테이너 실행 시 `--privileged` 옵션을 피하고 필요한 최소한의 권한만 부여하도록 설정합니다. (`--cap-drop` 옵션 활용)
- **네임스페이스 및 cgroups 활용** → 컨테이너 간 프로세스 격리를 강화하여 보안성을 높일 수 있습니다.  
- **Docker 이미지 신뢰성 확보** → 공식 이미지 또는 검증된 레지스트리에서만 다운로드하고 `docker scan` 명령어를 통해 취약점을 검사합니다.  
- **Host OS 보안 강화** → SELinux/AppArmor 같은 보안 모듈을 활성화하여 컨테이너의 권한을 제한합니다.  


## 네트워크 성능 저하 (Overhead)

### 문제점

- Docker는 기본적으로 NAT(Network Address Translation)를 사용하여 컨테이너 간 네트워크를 제공합니다.  
- 이로 인해 **VM보다 네트워크 성능이 저하될 가능성이 있으며** 컨테이너 간 통신이 많은 경우 지연이 발생할 수 있습니다.  
- Docker의 기본 브리지 네트워크(bridge network)는 여러 개의 컨테이너가 연결될 때 성능 저하가 발생할 수 있습니다.  

### 해결 방법

- **Host 네트워크 모드 사용** → `--network host` 옵션을 사용하면 NAT 없이 직접 Host의 네트워크를 사용할 수 있습니다. (단, 포트 충돌 가능성 존재)  
- **Macvlan 네트워크 활용** → 컨테이너가 독립적인 IP 주소를 갖도록 설정하여 성능을 향상할 수 있습니다.  
- **Service Mesh 사용** → Istio, Linkerd 같은 Service Mesh 도구를 사용하여 네트워크 성능 및 보안을 강화할 수 있습니다.  



## 데이터 관리 이슈 (Persistent Storage Issues)

### 문제점

- 컨테이너는 기본적으로 **휘발성(Stateless)** 이므로 컨테이너가 종료되면 내부 데이터도 함께 삭제됩니다.  
- 따라서 중요한 데이터를 컨테이너 내부에 저장할 경우 **데이터 손실 위험**이 있습니다.  
- 볼륨을 사용하지 않으면 컨테이너를 재시작할 때 데이터가 유지되지 않습니다.  

### 해결 방법

- **Docker Volume 사용** → `docker volume create`를 활용하여 데이터를 별도 저장소에 보관하여 컨테이너 종료 후에도 데이터가 유지되도록 설정합니다.  
- **바인드 마운트(Bind Mount) 활용** → 호스트 디렉토리를 컨테이너 내부에 마운트하여 지속적인 데이터 유지합니다. (`-v /host/path:/container/path`) 
- **분산 스토리지 활용** → 클라우드 환경에서는 AWS EFS, Google Cloud Filestore, Azure Files 같은 네트워크 스토리지를 사용하여 데이터를 안전하게 보관합니다.  


## OS 및 커널 의존성 (OS & Kernel Dependency)

### 문제점

- Docker는 Host OS의 커널을 공유하기 때문에 **컨테이너 내부에서 실행되는 프로세스가 Host OS의 커널과 호환되지 않을 경우 실행이 불가능**합니다.  
- Windows에서 Linux 컨테이너를 실행하려면 추가적인 가상화 계층(WSL2 또는 Hyper-V)이 필요합니다.  
- 특정 커널 기능이 필요한 애플리케이션(예: 특수한 파일 시스템, 커널 모듈 사용)이 정상적으로 동작하지 않을 수 있습니다.  

### 해결 방법

- **멀티플랫폼 지원** → Docker는 `docker buildx` 기능을 제공하여 여러 플랫폼(AMD64, ARM 등)에서 실행되는 컨테이너 이미지를 빌드 가능합니다.  
- **Windows에서 Linux 컨테이너 실행 시 WSL2 활용** → 성능이 더 우수하며 Hyper-V보다 가벼운 방식입니다.
- **커널에 의존하는 애플리케이션은 VM 사용 고려** → 특정 커널 기능이 필요한 경우에는 가상 머신이 더 적합할 수 있습니다.  



## 컨테이너 오케스트레이션 복잡성 (Orchestration Complexity)

### 문제점

- Docker는 컨테이너 실행만을 담당하며 **대규모 운영 환경에서 컨테이너를 관리하기 어렵습니다.**  
- 수십~수백 개의 컨테이너를 실행하는 경우 컨테이너 스케줄링, 로드 밸런싱, 상태 관리 등의 작업이 필요합니다.  
- Docker Compose는 단일 서버에서만 동작하므로 **다중 서버 환경에서는 Kubernetes 같은 추가 도구가 필요합니다.**  

### 해결 방법

- **Kubernetes(K8s) 사용** → 대규모 컨테이너 운영 환경에서 필수적인 오케스트레이션 도구입니다.  
- **Docker Swarm 활용** → Kubernetes보다 간단한 오케스트레이션 도구로 사용 가능합니다. (단, 기능이 제한적) 
- **자동화 도구 연계** → Terraform, Ansible 등을 활용하여 컨테이너 배포 및 관리를 자동화합니다.  


##  이미지 크기 및 관리 (Image Size & Management)

### 문제점

- Docker 이미지는 애플리케이션 실행에 필요한 모든 라이브러리를 포함하므로 **이미지 크기가 커질 가능성이 있습니다.**  
- 불필요한 패키지가 포함되면 빌드 시간이 길어지고 네트워크 전송 속도가 저하될 수 있습니다.  

### 해결 방법

- **경량 베이스 이미지 사용** → `alpine`, `distroless` 같은 최소한의 패키지만 포함한 이미지를 사용하여 최적화합니다.  
- **멀티스테이지 빌드 활용** → `Dockerfile`에서 빌드 과정과 실행 환경을 분리하여 불필요한 파일 포함을 방지합니다.  
- **이미지 캐싱 활용** → `docker build --cache-from` 옵션을 사용하여 중복된 빌드를 최소화합니다.  



## 결론

Docker는 개발 및 배포를 혁신적으로 개선한 도구이지만,**보안, 네트워크 성능, 데이터 관리, OS 의존성, 오케스트레이션 복잡성, 이미지 관리 문제** 등의 단점이 존재합니다.  
그러나 적절한 설정과 추가 도구 활용을 통해 이러한 문제들을 해결할 수 있습니다.  


> Docker를 사용할 때 단점과 해결 방법을 고려하여 최적화된 환경을 구축하는 것이 중요합니다! 
{: .prompt-tip }


## 왜?

### Docker 컨테이너는 Host OS의 커널을 공유한다?

#### ❓ 날씨 서비스와 투두 서비스가 독립적이지 못하다는 뜻인가?

아니요! 날씨 서비스와 투두 서비스가 각각 다른 컨테이너에서 실행되는 것은 맞습니다.
하지만 중요한 점은 모든 컨테이너가 Host OS의 동일한 커널을 공유한다는 것입니다.

날씨 서비스 컨테이너 (컨테이너 A)
투두 서비스 컨테이너 (컨테이너 B)

→ 두 컨테이너는 각각 독립적으로 동작하지만 같은 Host OS 커널을 사용합니다.
즉, 만약 컨테이너 A에서 커널 취약점이 발견되면, 컨테이너 B도 영향을 받을 가능성이 있습니다.

#### VM과 비교하면?

VM은 각각의 게스트 OS를 사용하여 커널까지 완전히 분리됩니다.

Docker 컨테이너는 커널을 공유하므로 격리 수준이 낮습니다.

#### ❓ 권한이 높은 컨테이너란? (--privileged 옵션)

기본적으로 Docker 컨테이너는 제한된 권한으로 실행됩니다.
그러나 `--privileged` 옵션을 사용하면 Host OS의 거의 모든 리소스에 접근할 수 있는 권한을 부여합니다.

```
docker run --privileged -d ubuntu
```
이렇게 실행하면 컨테이너 내부에서 Host OS의 중요한 설정을 변경할 수 있어 보안에 취약할 수 있습니다.
(ex. 컨테이너 내부에서 호스트의 네트워크 설정을 변경할 수도 있습니다.)

#### ❓ 컨테이너 간 격리가 완벽하지 않다는 뜻은?

- Docker 이미지는 컨테이너를 생성하기 위한 "설계도"
- Docker 컨테이너는 실행 중인 인스턴스

즉, 날씨 서비스와 투두 서비스는 각각 독립적인 컨테이너에서 실행되지만 완벽한 격리는 아니다라는 의미입니다.

Docker 컨테이너 간에는 PID 네임스페이스나 네트워크 네임스페이스 등을 통해 격리가 이루어지지만 완벽하게 VM 수준으로 분리되지 않기 때문에 특정한 경우 한 컨테이너가 다른 컨테이너의 정보를 엿볼 수도 있습니다.

#### ❓ docker scan, SELinux/AppArmor 예시
- `docker scan`: Docker Hub에서 제공하는 이미지 취약점 검사 도구

```
docker scan my-image
```

- SELinux/AppArmor 설정 예시 (`/etc/apparmor.d/`에서 설정)

```
apt install apparmor
apparmor_parser -r /etc/apparmor.d/docker
```

위 설정을 하면 컨테이너가 지정된 권한 외의 작업을 하지 못하도록 제한할 수 있습니다.

### Docker의 네트워크 성능 저하?

#### ❓ NAT이 왜 성능을 떨어뜨리는가?

Docker 컨테이너는 기본적으로 NAT(Network Address Translation) 방식으로 네트워킹을 제공합니다.

이게 성능을 저하시키는 이유는 다음과 같습니다.

- Docker 컨테이너는 기본적으로 가상의 브리지 네트워크(bridge network)를 사용합니다.
- 컨테이너가 네트워크 요청을 하면 NAT를 통해 Host OS에서 변환 후 외부로 전송합니다.
- 변환 과정에서 추가적인 오버헤드 발생합니다.

#### ❓ 컨테이너 간 통신 예제
날씨 서비스(containerA)와 투두 서비스(containerB)가 통신한다고 가정해봅시다.

```
docker network create my_network
docker run -d --name containerA --network my_network my_image_A
docker run -d --name containerB --network my_network my_image_B
```

이 경우, containerA에서 containerB로 직접 통신하면 브리지 네트워크를 통해 NAT 변환 과정을 거치므로 성능이 저하될 수 있습니다.

#### 해결 방법?

`--network host` 사용하여 NAT 없이 실행 가능합니다.

```
docker run --network host -d my_image
```

### 마운트(Mount)란?

"마운트"는 외부 저장소(호스트 디렉토리, 볼륨 등)를 컨테이너 내부에서 사용할 수 있도록 연결하는 것입니다.

#### 예제

```
docker run -v /host/path:/container/path my_image
```

위 명령어를 실행하면
- `host/path` → Host OS의 디렉토리
- `container/path` → 컨테이너 내부에서 접근 가능한 디렉토리

즉, 컨테이너가 종료되더라도 데이터가 사라지지 않도록 유지할 수 있습니다.

### Host OS의 커널을 공유하기 때문에 발생하는 문제?

#### Windows에서 Linux 컨테이너 실행이 어려운 이유

Linux 컨테이너는 Linux 커널이 필요합니다.
Windows에서 실행하려면 가상화 계층(WSL2, Hyper-V)이 필요합니다.

```
# WSL2 설정
wsl --set-version Ubuntu 2
```

즉, Windows에서 Docker를 실행할 때 추가적인 가상화 계층을 거쳐야 하므로 성능이 저하될 수 있습니다.

#### ❓ 특정 커널 기능을 사용하는 애플리케이션이 실행되지 않는 이유

예를 들어, **특정한 파일 시스템(Btrfs)**을 사용하는 애플리케이션이 있다고 가정해봅시다.

```
mkfs.btrfs /dev/sdb1
```

이런 경우, Host OS가 Btrfs를 지원하지 않으면 Docker 컨테이너 내부에서도 사용할 수 없습니다.

즉, 컨테이너 내부의 프로세스가 Host OS 커널 기능을 그대로 사용하기 때문입니다.

### Docker 이미지 캐싱이란?
#### ❓ `docker build --cache-from`을 사용하면 빌드 속도가 빨라지는 이유?

Docker는 Dockerfile을 빌드할 때 이전 단계에서 캐싱된 결과물을 재사용할 수 있습니다.

Dockerfile
```
FROM node:18
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
RUN npm run build
```

이 경우, `COPY package.json .` 이후 `RUN npm instal`l이 실행됩니다.
하지만 package.json이 변경되지 않았다면, npm install 단계는 캐싱된 결과를 재사용합니다.

```
docker build --cache-from=my_previous_image -t my_new_image .
```
- `npm install` 같은 변경되지 않은 단계는 캐싱을 활용하여 더 빠르게 빌드 가능합니다.
- `COPY . .` 이후 `RUN npm run build` 단계만 새로 빌드됩니다.

즉, 코드를 변경했을 때도 일부만 빌드하여 속도를 최적화할 수 있습니다.