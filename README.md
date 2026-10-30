<div align="center">

# ☁️ Cloud Study

**Docker부터 Kubernetes까지 단계적으로 학습하는 클라우드 스터디 기록**

학교에서 매주 목요일 진행하는 클라우드 스터디의  
실습 코드, Dockerfile, 학습 내용과 결과를 정리한 저장소입니다.

<br>

![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![Go](https://img.shields.io/badge/Go-00ADD8?style=for-the-badge&logo=go&logoColor=white)
![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)

<br>

![Study Records](https://img.shields.io/badge/Study_Records-4%2F4-22c55e?style=flat-square)
![Architecture](https://img.shields.io/badge/Architecture-arm64-orange?style=flat-square)
![Docker Hub](https://img.shields.io/badge/Docker_Hub-hello--go:v2-2496ED?style=flat-square&logo=docker&logoColor=white)

</div>

---

## 📚 Table of Contents

- [Study Progress](#-study-progress)
- [Environment](#-environment)
- [Repository Structure](#-repository-structure)
- [Week 1 - VM vs Container](#-week-1---vm-vs-container)
- [Week 2 - Dockerizing](#-week-2---dockerizing)
- [Week 3 - Image Optimization & Registry](#-week-3---image-optimization--registry)
- [Week 5 - Kubernetes Basics](#-week-5---kubernetes-basics)
- [Key Results](#-key-results)
- [Docker Hub](#-docker-hub)
- [Next Step](#-next-step)

---

## 📈 Study Progress

**실제 진행한 4개 회차 모두 GitHub 기록 완료 (4/4 · 100%)**

| Week | Topic | Status |
|:---:|---|:---:|
| 01 | VM vs Container | ✅ Completed |
| 02 | Dockerizing | ✅ Completed |
| 03 | Image Optimization & Registry | ✅ Completed |
| 04 | 시험 기간 휴강 | ⏸️ No Session |
| 05 | Kubernetes Basics | ✅ Completed |

> 진행률 100%는 전체 교육과정의 완료율이 아니라 지금까지 실제 진행한 회차의 GitHub 기록률입니다.

---

## 💻 Environment

| Item | Environment |
|---|---|
| Device | MacBook Pro 14 |
| Chip | Apple M4 |
| OS | macOS |
| Container Runtime | Docker Desktop |
| Architecture | `arm64` |
| Registry | Docker Hub |

---

## 📁 Repository Structure

```text
cloud-study/
│
├── week01-vm-container/
│   └── README.md
│
├── week02-dockerizing/
│   ├── Dockerfile
│   ├── Dockerfile.bad
│   ├── index.html
│   └── README.md
│
├── week03-image-optimization/
│   ├── Dockerfile
│   ├── Dockerfile.single
│   ├── Dockerfile.cgo
│   ├── Dockerfile.multiarch
│   ├── go.mod
│   ├── main.go
│   └── README.md
│
├── week05-kubernetes-basics/
│   └── README.md
│
├── .gitignore
└── README.md
```

---

## 🖥️ Week 1 - VM vs Container

> Virtual Machine과 Container의 구조적 차이를 이해하고 Docker 환경을 구축했습니다.

📂 [Week 1 실습 기록](./week01-vm-container)

### 학습 내용

- Virtual Machine과 Container 구조 비교
- Hypervisor와 Container Runtime
- Docker Image와 Container의 차이
- Docker Desktop 설치 및 환경 구축
- Docker 기본 명령어 실습

### 핵심 개념

```text
Virtual Machine

Hardware
   ↓
Hypervisor
   ↓
Guest OS
   ↓
Application
```

```text
Container

Hardware
   ↓
Host OS
   ↓
Container Runtime
   ↓
Container
```

---

## 🐳 Week 2 - Dockerizing

> 수동으로 구성한 nginx 환경을 Dockerfile을 사용해 이미지로 자동화했습니다.

📂 [Week 2 실습 코드](./week02-dockerizing)

### 학습 내용

- Ubuntu 22.04 Container 실행
- nginx 수동 설치
- Port Mapping
- Dockerfile 작성
- Docker Image Build
- 동일한 Image로 여러 Container 실행
- Container 재현성 확인

### Docker 기본 흐름

```text
Dockerfile
    ↓
docker build
    ↓
Docker Image
    ↓
docker run
    ↓
Docker Container
```

### 주요 명령어

```bash
docker build -t my-nginx .

docker run -d -p 8080:80 my-nginx

docker ps

curl localhost:8080
```

---

## 🚀 Week 3 - Image Optimization & Registry

> Docker Image 최적화부터 Registry와 Multi-architecture Build까지 실습했습니다.

📂 [Week 3 실습 코드](./week03-image-optimization)

### 학습 내용

- Docker Layer
- Layer Cache
- Dockerfile 명령 순서 최적화
- Go Web Server Dockerizing
- Single-stage Build
- Multi-stage Build
- CGO / glibc / musl
- Docker Hub Registry
- Docker Image Tag & Push
- `amd64` / `arm64`
- Docker Buildx
- Multi-architecture Image

---

### 🧱 Layer Cache

Dockerfile의 각 명령은 Layer를 생성합니다.

잘 변경되지 않는 작업을 위쪽에 두고  
자주 변경되는 소스 코드를 아래쪽에 배치하면 Cache를 효율적으로 사용할 수 있습니다.

#### Good

```dockerfile
FROM ubuntu:22.04

RUN apt update && apt install -y nginx

COPY index.html /var/www/html/index.html
```

#### Bad

```dockerfile
FROM ubuntu:22.04

COPY index.html /var/www/html/index.html

RUN apt update && apt install -y nginx
```

실습에서는 `index.html`만 변경했을 때:

```text
Good Dockerfile
→ nginx 설치 Layer 재사용
→ 약 1.6초

Bad Dockerfile
→ nginx 설치 다시 실행
→ 약 15초 이상
```

의 차이를 확인했습니다.

---

### 🏗️ Multi-stage Build

Go 애플리케이션을 Single-stage와 Multi-stage 방식으로 각각 빌드했습니다.

| Build | Image Size |
|---|---:|
| `hello-go:single` | 약 **1.3 GB** |
| `hello-go:multi` | 약 **24 MB** |

약 **98% 수준의 이미지 크기 감소**를 확인했습니다.

### Single-stage

```text
golang image
├── Go Compiler
├── Build Tools
├── Libraries
├── Source Code
└── Server Binary
```

### Multi-stage

```text
Builder Stage
golang:1.22
    ↓
Go Build
    ↓
server binary
    ↓
COPY --from=builder
    ↓
Runtime Stage
alpine:3.20
    ↓
server binary only
```

---

### ⚙️ CGO

Go 바이너리를 Alpine에서 실행하는 과정에서  
`glibc`와 `musl libc`의 차이로 다음 오류를 확인했습니다.

```text
exec /server: no such file or directory
```

정적 바이너리로 빌드하여 해결했습니다.

```dockerfile
RUN CGO_ENABLED=0 go build -o server .
```

---

### 🌐 Multi-architecture Build

Apple Silicon 환경은 `arm64`를 사용하지만  
일반적인 x86 서버 환경은 `amd64`를 사용하는 경우가 많습니다.

Docker Buildx를 사용해 두 Architecture를 모두 지원하는 이미지를 생성했습니다.

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -f Dockerfile.multiarch \
  -t kwakjoonoh/hello-go:v2 \
  --push .
```

지원 플랫폼:

```text
linux/amd64
linux/arm64
```

---

## ☸️ Week 5 - Kubernetes Basics

> Docker Desktop에서 Kubernetes 기초 실습을 진행했습니다.

📂 [Week 5 실습 기록](./week05-kubernetes-basics)

### 실습 내용

- Kubernetes 클러스터 실행 및 상태 확인
- nginx Pod 생성과 삭제
- Deployment를 통한 Pod 자동 복구
- Replica 2개에서 3개로 Scaling
- Service 생성 및 Port Forwarding
- nginx 1.27 → 1.28 Rolling Update
- Rollback
- 실습 리소스 정리

### 핵심 실습 흐름

```text
Docker Desktop
      ↓
Kubernetes Cluster
      ↓
Pod 생성 / 삭제
      ↓
Deployment (replicas=2)
      ↓
Pod 자동 복구
      ↓
Scale to 3 Pods
      ↓
Service / Port Forward
      ↓
Rolling Update / Rollback
      ↓
Cleanup
```

✅ **Week 5 Completed**

---

## 🏆 Key Results

### Docker Layer Cache

```text
Good Dockerfile  → 약 1.6s
Bad Dockerfile   → 약 15s+
```

### Multi-stage Build

```text
Single-stage → 약 1.3 GB
Multi-stage  → 약 24 MB
```

### Registry

```text
Local Image
     ↓
docker tag
     ↓
Docker Hub
     ↓
docker pull
     ↓
Another Machine
```

### Multi-architecture

```text
                 kwakjoonoh/hello-go:v2
                          │
               ┌──────────┴──────────┐
               ↓                     ↓
          linux/amd64           linux/arm64
          x86 Server            Apple Silicon
```

---

## 🐳 Docker Hub

### Image

```text
kwakjoonoh/hello-go:v2
```

### Supported Platforms

```text
linux/amd64
linux/arm64
```

### Pull

```bash
docker pull kwakjoonoh/hello-go:v2
```

### Run

```bash
docker run -p 8080:8080 kwakjoonoh/hello-go:v2
```

Docker가 실행 환경의 CPU Architecture에 맞는 이미지를 자동으로 선택합니다.

---

## ☸️ Next Step

다음 회차에서는 Kubernetes의 선언형 설정과 배포를 더 깊이 학습할 예정입니다.

- Kubernetes YAML Manifest
- Deployment / Service 설정
- ConfigMap / Secret
- 컨테이너 이미지 배포

> 4회차는 시험 기간으로 스터디를 진행하지 않았습니다.

---

<div align="center">

### ☁️ Cloud Study

**Docker → Registry → Kubernetes**

계속 업데이트 중입니다.

</div>
