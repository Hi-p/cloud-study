# Week 3 - Image Optimization & Registry

## 학습 내용

- Docker Layer / Layer Cache
- Multi-stage Build
- Go Web Server
- CGO
- Docker Hub Registry
- Image Tag & Push
- amd64 / arm64
- Multi-architecture Image

## Layer Cache

잘 변경되지 않는 명령은 Dockerfile 위쪽에,
자주 변경되는 소스 코드는 아래쪽에 배치하면 Layer Cache를 효율적으로 사용할 수 있다.

## Multi-stage Build

실습 결과:

- hello-go:single → 약 1.3GB
- hello-go:multi → 약 24MB

빌드 환경과 실행 환경을 분리하여 최종 Image 크기를 크게 줄였다.

## CGO

CGO가 활성화된 바이너리를 Alpine에서 실행할 경우 libc 차이로 실행 문제가 발생할 수 있다.

해결:

CGO_ENABLED=0 go build -o server .

## Docker Hub

docker tag hello-go:multi kwakjoonoh/hello-go:v1

docker push kwakjoonoh/hello-go:v1

## Multi-architecture

docker buildx build \
  --platform linux/amd64,linux/arm64 \
  -f Dockerfile.multiarch \
  -t kwakjoonoh/hello-go:v2 \
  --push .

지원 플랫폼:

- linux/amd64
- linux/arm64

kwakjoonoh/hello-go:v2는 다음 Kubernetes 실습에서 사용한다.
