# Cloud Study

학교에서 매주 목요일 진행하는 클라우드 스터디 실습 기록입니다.

Docker의 기본 개념부터 이미지 최적화, Registry, Multi-architecture를 학습하고 이후 Kubernetes까지 단계적으로 학습합니다.

## Environment

- MacBook Pro 14
- Apple M4
- macOS
- Docker Desktop
- Architecture: arm64

## Study Roadmap

### Week 1 - VM vs Container
- VM과 Container 구조 비교
- Docker 기본 개념
- Docker Desktop 환경 구축

### Week 2 - Dockerizing
- Ubuntu 22.04 Container
- nginx 수동 설치
- Port Mapping
- Dockerfile
- Docker Image Build
- 동일 Image로 여러 Container 실행
- 재현성 실습

### Week 3 - Image Optimization & Registry
- Docker Layer
- Layer Cache
- Multi-stage Build
- Go Web Server
- CGO / glibc / musl
- Docker Hub Registry
- Image Tag & Push
- amd64 / arm64
- Multi-architecture Build

### Week 4 - Kubernetes
- 예정

## Docker Hub

Multi-architecture image:

kwakjoonoh/hello-go:v2

Supported platforms:

- linux/amd64
- linux/arm64
