# Week 2 - Dockerizing

## 학습 내용

- Ubuntu 22.04 Container 실행
- nginx 수동 설치
- Port Mapping
- Dockerfile 작성
- Docker Image Build
- 여러 Container 동시 실행
- Container 재현성 확인

## 실행 예시

docker run -it --name manual-test -p 8080:80 ubuntu:22.04 bash

docker build -t my-nginx .

docker run -d -p 8081:80 my-nginx

curl localhost:8081

## 핵심 흐름

Dockerfile
→ docker build
→ Image
→ docker run
→ Container

동일한 Image 하나를 사용하여 여러 개의 Container를 생성할 수 있다.
