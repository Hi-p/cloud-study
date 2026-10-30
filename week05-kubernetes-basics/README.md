# ☸️ Week 05 - Kubernetes Basics

> Docker Desktop을 이용한 Kubernetes 기초 실습

2026년 10월 29일 진행한 클라우드 스터디 5회차 실습 기록입니다.

스터디에서 제공한 「쿠버네티스 기초 실습 가이드 (5회차)」의 STEP 0~7을 기준으로 정리했습니다.

## 📚 Contents

- [Environment](#-environment)
- [STEP 0 - Cluster Setup](#step-0---cluster-setup)
- [STEP 1 - Create Pod](#step-1---create-pod)
- [STEP 2 - Delete Pod](#step-2---delete-pod)
- [STEP 3 - Deployment](#step-3---deployment)
- [STEP 4 - Scaling](#step-4---scaling)
- [STEP 5 - Service](#step-5---service)
- [STEP 6 - Update and Rollback](#step-6---update-and-rollback)
- [STEP 7 - Cleanup](#step-7---cleanup)
- [Troubleshooting](#-troubleshooting)
- [Key Takeaways](#-key-takeaways)

---

## 💻 Environment

| Item | Description |
|---|---|
| OS | macOS |
| Container Platform | Docker Desktop |
| Kubernetes | v1.36.1 |
| Cluster | docker-desktop |
| Provisioning | kind |
| Nodes | 1 |
| Namespace | default |
| Image | nginx:1.27 / nginx:1.28 |

이번 실습은 학교의 원격 서버가 아닌 로컬 Kubernetes 클러스터에서 진행했습니다.

---

## STEP 0 - Cluster Setup

Docker Desktop의 Settings → Kubernetes에서 Kubernetes를 활성화했습니다.

```bash
kubectl get nodes
kubectl get pods
```

노드 확인 결과:

```text
NAME                    STATUS   ROLES           VERSION
desktop-control-plane   Ready    control-plane   v1.36.1
```

초기 Pod 조회 결과:

```text
No resources found in default namespace.
```

**핵심:** Kubernetes 클러스터가 정상 실행 중이고, 아직 일반 애플리케이션 Pod가 없는 상태입니다.

---

## STEP 1 - Create Pod

nginx:1.27 이미지를 이용하여 Pod를 생성했습니다.

```bash
kubectl run web --image=nginx:1.27
kubectl get pods
kubectl describe pod web
```

생성 결과:

```text
pod/web created
```

생성 직후에는 다음 상태를 확인했습니다.

```text
NAME   READY   STATUS              RESTARTS
web    0/1     ContainerCreating   0
```

이미지를 준비하고 컨테이너를 생성한 뒤 정상적으로 실행되면 `Running` 상태가 됩니다.

`kubectl describe pod web` 명령어로 Pod 정보와 Events를 확인할 수 있습니다.

---

## STEP 2 - Delete Pod

```bash
kubectl delete pod web
kubectl get pods
```

일반 Pod를 삭제하면 자동으로 다시 생성되지 않습니다.

```text
Pod 생성
   ↓
Pod 실행
   ↓
Pod 삭제
   ↓
자동 복구 없음
```

---

## STEP 3 - Deployment

Deployment를 이용해 Pod 2개를 생성했습니다.

```bash
kubectl create deployment web --image=nginx:1.27 --replicas=2
kubectl get pods
```

두 번째 터미널에서 Pod 상태를 실시간으로 관찰했습니다.

```bash
kubectl get pods -w
```

첫 번째 터미널에서 Pod 하나를 삭제했습니다.

```bash
kubectl get pods

# 실제 조회된 Pod 이름으로 변경
kubectl delete pod <POD_NAME>
```

Pod 하나가 삭제되더라도 Deployment가 원하는 Replica 수를 유지하도록 새 Pod가 생성됩니다.

```text
Deployment
    ↓
ReplicaSet
    ↓
Pod 1 + Pod 2
    ↓
Pod 1 삭제
    ↓
새 Pod 생성
    ↓
Pod 2개 유지
```

**핵심:** Deployment는 선언된 Pod 개수를 지속적으로 유지합니다.

---

## STEP 4 - Scaling

Deployment의 Replica 개수를 2개에서 3개로 확장했습니다.

```bash
kubectl scale deployment web --replicas=3
kubectl get pods
```

```text
Before
2 Pods
   ↓
Scale-out
   ↓
After
3 Pods
```

`replicas` 값을 변경하면 Deployment가 필요한 Pod를 생성하거나 제거합니다.

---

## STEP 5 - Service

Deployment를 Service로 노출했습니다.

```bash
kubectl expose deployment web --port=80
```

Port Forwarding을 통해 로컬 브라우저에서 접속했습니다.

```bash
kubectl port-forward svc/web 8080:80
```

접속 주소:

http://localhost:8080

정상적으로 접속하면 nginx 환영 페이지가 표시됩니다.

```text
Browser
   ↓
localhost:8080
   ↓
Port Forward
   ↓
Service
   ↓
nginx Pod
```

접속 확인 후 Ctrl+C로 Port Forwarding을 종료했습니다.

---

## STEP 6 - Update and Rollback

nginx 이미지 버전을 1.27에서 1.28로 업데이트했습니다.

```bash
kubectl set image deployment/web nginx=nginx:1.28
kubectl rollout status deployment/web
```

업데이트가 완료되면 다음 메시지가 표시됩니다.

```text
successfully rolled out
```

이후 이전 버전으로 되돌렸습니다.

```bash
kubectl rollout undo deployment/web
```

**Rolling Update:** 기존 Pod를 점진적으로 새 버전으로 교체하는 방식입니다.

**Rollback:** 이전 Deployment Revision으로 복귀하는 기능입니다.

---

## STEP 7 - Cleanup

실습에서 생성한 리소스를 정리했습니다.

```bash
kubectl delete deployment,service web
kubectl get all
```

실습용 Deployment와 Service를 삭제하고 상태를 확인했습니다.

기본 Kubernetes Service는 남아 있을 수 있습니다.

---

## 🔧 Troubleshooting

실습 가이드에 정리된 주요 오류와 해결 방법입니다.

| Error | Possible Cause | Solution |
|---|---|---|
| Pending | 리소스 부족 | Pod Events 확인, Replica 수 조정 |
| ImagePullBackOff | 이미지 이름 또는 태그 오류 | 이미지 주소 확인 |
| connection refused | 클러스터 실행 또는 연결 문제 | Docker Desktop 상태 확인 |
| AlreadyExists | 동일한 이름의 리소스 존재 | 기존 리소스 확인 후 정리 |

Pod의 상세 정보는 다음 명령어로 확인합니다.

```bash
kubectl describe pod <POD_NAME>
```

---

## 🧠 Key Takeaways

| Concept | Description |
|---|---|
| Pod | 컨테이너를 실행하는 최소 배포 단위 |
| Deployment | Pod 개수와 업데이트 관리 |
| ReplicaSet | 지정된 Pod 개수 유지 |
| Service | Pod 집합에 대한 안정적인 네트워크 접근 |
| Scaling | Pod 복제본 수 조정 |
| Rolling Update | 점진적인 버전 변경 |
| Rollback | 이전 버전으로 복귀 |

### Docker와 Kubernetes의 차이

```text
Docker
컨테이너 실행과 관리

Kubernetes
여러 컨테이너의 원하는 상태를 지속적으로 유지
```

### 이번 실습의 핵심

일반 Pod는 삭제하면 다시 생성되지 않지만,
Deployment를 이용하면 Kubernetes가 원하는 Replica 수를 유지하기 위해 새로운 Pod를 생성합니다.

---

## ✅ Completion

- [x] Kubernetes 클러스터 실행
- [x] Pod 생성
- [x] Pod 삭제
- [x] Deployment 생성
- [x] Pod 자동 복구
- [x] Scaling
- [x] Service 생성
- [x] Port Forwarding
- [x] Rolling Update
- [x] Rollback
- [x] 리소스 정리

**Week 05 - Completed**
