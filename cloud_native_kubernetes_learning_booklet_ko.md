# Cloud Native · Docker · Kubernetes · GitOps 학습 소책자

## 부제
Front-end 개발자 관점에서 이해하는 현대 클라우드 인프라 아키텍처

---

# 1. 왜 이 기술들이 등장했는가?

현대 소프트웨어는 더 이상 단일 서버 위에서 동작하지 않는다.

과거 웹 서비스 구조는 보통 다음과 같았다.

```text
웹 서버 1대
DB 서버 1대
```

하지만 서비스 규모가 커지면서 다음 문제가 발생했다.

- 서버 장애 시 전체 서비스 중단
- 트래픽 증가 대응 어려움
- 배포 시 다운타임 발생
- 서버 환경 차이 문제
- 운영 자동화 부재
- 서비스 간 복잡한 의존성 증가

이 문제를 해결하기 위해 등장한 흐름이 바로:

```text
Container
→ Orchestration
→ Cloud Native
→ GitOps
```

이다.

---

# 2. Docker

## 2-1. Docker의 본질

Docker는 단순한 “가상머신”이 아니다.

Docker의 핵심은:

```text
애플리케이션 실행 환경 자체를 패키징
```

하는 것이다.

즉:

- OS 환경
- Runtime
- 라이브러리
- 애플리케이션

을 하나의 단위로 묶는다.

---

## 2-2. Docker Image

Docker Image는:

```text
실행 가능한 템플릿
```

이다.

예:

```text
Alpine Linux
 + Node.js
 + npm package
 + Next.js build
```

전체를 이미지로 저장 가능.

---

## 2-3. Docker Container

Image를 실제 실행하면:

```text
Container
```

가 된다.

즉:

- Image = 설계도
- Container = 실행 중 인스턴스

관계.

---

## 2-4. 왜 Docker가 혁명적이었는가?

예전에는:

```text
내 PC에서는 되는데 서버에서는 안됨
```

문제가 매우 흔했다.

Docker는 실행 환경 자체를 패키징하면서:

```text
환경 차이 문제
```

를 크게 줄였다.

---

# 3. Docker Registry

## 3-1. Registry란?

Registry는:

```text
Docker Image 저장소
```

이다.

대표적인 Registry:

- Docker Hub
- GHCR(GitHub Container Registry)
- Amazon ECR
- Google Artifact Registry
- Azure Container Registry

---

## 3-2. npm과의 차이

npm package:

```text
JavaScript 코드 배포
```

Docker Registry:

```text
실행 환경 전체 배포
```

이다.

---

## 3-3. 실제 운영 흐름

```text
git push
  ↓
GitHub Actions
  ↓
Docker Build
  ↓
GHCR Push
  ↓
Kubernetes Pull
  ↓
Container 실행
```

---

# 4. Docker Compose

## 4-1. 왜 필요한가?

현대 서비스는 하나의 컨테이너만으로 구성되지 않는다.

예:

```text
Frontend
Backend
Redis
PostgreSQL
Nginx
```

여러 컨테이너 필요.

---

## 4-2. Compose의 역할

Docker Compose는:

```text
여러 컨테이너를 함께 orchestration
```

하는 도구.

---

## 4-3. Kubernetes와의 관계

Compose는:

```text
로컬 mini orchestration
```

느낌에 가깝다.

다만 Kubernetes Pod와 완전히 동일한 개념은 아니다.

Compose의 service들은 보통:

```text
서로 다른 Container
```

이며,

Kubernetes Pod는:

```text
강하게 결합된 Container 그룹
```

이다.

---

# 5. Kubernetes

## 5-1. Kubernetes란?

Kubernetes는:

```text
Container Orchestration Platform
```

이다.

즉:

- 컨테이너 배포
- 복구
- 스케일링
- 네트워크
- 운영 자동화

를 관리.

---

# 6. Cluster

## 6-1. Cluster의 정의

Cluster는:

```text
여러 서버를 하나처럼 묶은 시스템
```

이다.

구조:

```text
Cluster
 ├─ Control Plane
 └─ Worker Nodes
```

---

## 6-2. Control Plane

Control Plane은 Kubernetes의 두뇌.

역할:

- 스케줄링
- 상태 감시
- 배포 관리
- Desired State 유지

---

## 6-3. Worker Node

실제 Pod가 실행되는 서버.

---

# 7. Pod

## 7-1. Pod란?

Pod는:

```text
Kubernetes의 최소 배포 단위
```

이다.

구조:

```text
Pod
 └─ Containers
```

---

## 7-2. Multi Container Pod

Pod 안에는 여러 Container 포함 가능.

예:

```text
Pod
 ├─ app container
 └─ sidecar container
```

---

## 7-3. Pod의 특징

같은 Pod 안 Container들은:

- localhost 공유
- network namespace 공유
- volume 공유

한다.

---

# 8. Sidecar Pattern

## 8-1. Sidecar란?

메인 애플리케이션을 보조하는 Container.

예:

```text
Pod
 ├─ App
 └─ Log Collector
```

---

## 8-2. Sidecar가 하는 일

- Logging
- Metrics
- Tracing
- Proxy
- TLS

등 공통 기능 담당.

---

## 8-3. Istio와 Sidecar

Istio는 각 Pod에 Envoy Proxy를 Sidecar로 주입.

즉:

```text
App
 ↓
Envoy Proxy
 ↓
Network
```

구조.

---

# 9. Deployment

## 9-1. Deployment란?

Deployment는:

```text
Pod 관리자
```

이다.

역할:

- Pod 생성
- Replica 유지
- Rolling Update
- Self-healing

---

## 9-2. Replica

예:

```yaml
replicas: 3
```

의 의미:

```text
동일 Pod 3개 유지
```

---

## 9-3. Self Healing

Pod 하나 죽으면:

```text
새 Pod 자동 생성
```

한다.

Kubernetes 핵심 철학:

```text
Container는 죽는다.
자동 복구하면 된다.
```

---

## 9-4. Rolling Update

새 버전 배포 시:

```text
Pod 하나씩 교체
```

하며 무중단 배포 가능.

---

# 10. Service

## 10-1. 왜 Service가 필요한가?

Pod는:

- 계속 생성/삭제
- IP 변경

된다.

따라서:

```text
Pod IP 직접 접근 불가
```

---

## 10-2. Service의 역할

Service는:

```text
Pod들의 Stable Endpoint
```

이다.

즉:

```text
backend-service
```

같은 이름으로 접근 가능.

---

## 10-3. Service Discovery

Kubernetes는 내부 DNS(CoreDNS)를 제공.

따라서:

```text
http://backend-service
```

처럼 이름 기반 통신 가능.

---

## 10-4. Load Balancing

Service는 여러 Pod로 트래픽 분산 가능.

```text
Service
 ├─ Pod A
 ├─ Pod B
 └─ Pod C
```

---

# 11. Ingress

## 11-1. Ingress란?

Ingress는:

```text
외부 HTTP 요청 라우터
```

이다.

---

## 11-2. 역할

- URL Routing
- TLS 처리
- Reverse Proxy
- Load Balancing

---

## 11-3. 실제 구조

```text
Internet
 ↓
Ingress
 ↓
Service
 ↓
Pods
```

---

# 12. Reverse Proxy

## 12-1. Forward Proxy vs Reverse Proxy

### Forward Proxy

```text
Client 측 대리인
```

### Reverse Proxy

```text
Server 측 대리인
```

---

## 12-2. Reverse Proxy 역할

- TLS 종료
- Routing
- Load Balancing
- Authentication
- Caching

---

# 13. NAT

## 13-1. NAT란?

```text
사설 IP ↔ 공인 IP 변환
```

기술.

---

## 13-2. Kubernetes와 NAT

Docker는 NAT 기반 networking 사용.

반면 Kubernetes는 가능하면:

```text
Pod 간 직접 통신(flat network)
```

지향.

---

# 14. TLS

## 14-1. TLS란?

TLS는:

```text
암호화 통신 프로토콜
```

이다.

즉:

```text
HTTPS = HTTP + TLS
```

---

## 14-2. TLS 역할

- 암호화
- 인증서 검증
- 데이터 위변조 방지

---

## 14-3. mTLS

Mutual TLS.

서비스 간 양방향 인증.

Service Mesh에서 매우 중요.

---

# 15. Stateless vs Stateful

## 15-1. Stateless

Stateless는:

```text
상태를 외부 저장소(DB)에 저장
```

하는 구조.

즉 App Pod 자체는 상태 유지 안함.

예:

- Frontend
- API Server
- Worker

---

## 15-2. Stateful

Stateful은:

```text
데이터/identity 유지 필요
```

한 시스템.

예:

- PostgreSQL
- Kafka
- Redis Cluster

---

# 16. Volume

## 16-1. 왜 필요한가?

Container filesystem은:

```text
Ephemeral
```

즉 휘발성.

Pod 죽으면 데이터 사라질 수 있음.

---

## 16-2. Volume 역할

```text
Pod 외부 저장소 연결
```

---

# 17. PV / PVC

## 17-1. PV (Persistent Volume)

실제 저장소 자원.

예:

- AWS EBS
- NFS
- Ceph

---

## 17-2. PVC (Persistent Volume Claim)

스토리지 요청 객체.

즉:

```text
"10GB 디스크 주세요"
```

요청.

---

## 17-3. 흐름

```text
Pod
 ↓
PVC
 ↓
PV
 ↓
Real Storage
```

---

# 18. StatefulSet

## 18-1. StatefulSet이 필요한 이유

Deployment는:

- Pod 이름 랜덤
- 순서 없음
- identity 유지 없음

---

## 18-2. StatefulSet 특징

### 고정 이름

```text
mysql-0
mysql-1
```

---

### 고정 Storage

```text
mysql-0 → 전용 디스크
```

---

### 순서 보장

```text
0 생성
→ 1 생성
→ 2 생성
```

---

# 19. Kafka

## 19-1. Kafka란?

Kafka는:

```text
Distributed Event Streaming Platform
```

이다.

쉽게 말하면:

```text
초고성능 분산 메시지 시스템
```

---

## 19-2. 왜 Kafka가 필요한가?

현대 시스템은 이벤트 기반 구조 사용.

예:

```text
회원가입 발생
 ↓
이메일
포인트
추천시스템
로그
```

동시 처리.

---

## 19-3. Kafka 구조

```text
Producer
 ↓
Kafka
 ↓
Consumers
```

---

## 19-4. Kafka 특징

- 매우 빠름
- Durable
- Replication 지원
- Consumer Group 지원
- Event Streaming 가능

---

# 20. gRPC

## 20-1. gRPC란?

Google이 만든:

```text
고성능 RPC 통신 프로토콜
```

---

## 20-2. 특징

- HTTP/2 기반
- Binary Protocol
- Type-safe
- Streaming 지원
- 매우 빠름

---

## 20-3. 사용처

Microservice 내부 통신.

예:

```text
API Service
 ↔ Payment Service
```

---

# 21. API Gateway

## 21-1. API Gateway란?

```text
외부 요청의 중앙 진입점
```

---

## 21-2. 역할

- Authentication
- TLS
- Routing
- Rate Limit
- Logging

---

# 22. Service Mesh

## 22-1. Service Mesh란?

```text
서비스 간 네트워크를 관리하는 계층
```

---

## 22-2. 핵심 철학

네트워크 기능을:

```text
애플리케이션 코드에서 제거
```

---

## 22-3. Sidecar Proxy 기반

```text
App
 ↓
Envoy Proxy
 ↓
Network
```

---

## 22-4. 제공 기능

- Retry
- Timeout
- mTLS
- Tracing
- Metrics
- Circuit Breaker

---

# 23. Observability

## 23-1. Observability란?

```text
시스템 내부 상태를 분석 가능한 능력
```

---

## 23-2. 3대 축

### Metrics

숫자 데이터.

예:

- CPU
- Latency
- Error Rate

---

### Logs

이벤트 기록.

---

### Traces

요청 흐름 추적.

---

# 24. Prometheus / Grafana

## Prometheus

Metrics 수집 시스템.

## Grafana

시각화 플랫폼.

---

# 25. OpenTelemetry

현대 observability 표준.

Metrics / Logs / Traces 통합.

---

# 26. CI/CD

## 26-1. CI

Continuous Integration.

자동:

- Build
- Lint
- Test

실행.

---

## 26-2. CD

Continuous Delivery / Deployment.

자동 배포.

---

# 27. GitHub Actions

GitHub 기반 CI/CD 플랫폼.

예:

```text
git push
 ↓
GitHub Actions
 ↓
Docker Build
 ↓
Registry Push
```

---

# 28. GitOps

## 28-1. GitOps란?

```text
운영 상태를 Git으로 관리
```

하는 방식.

---

## 28-2. 핵심 철학

```text
Git = Source of Truth
```

---

# 29. ArgoCD

## 29-1. ArgoCD란?

Git 상태와 Kubernetes 상태를 자동 동기화하는 플랫폼.

---

## 29-2. 흐름

```text
Git Repo
 ↓
ArgoCD
 ↓
Kubernetes Cluster
```

---

## 29-3. 장점

- Drift 방지
- 자동 동기화
- Rollback 용이
- 운영 이력 추적

---

# 30. Helm

## 30-1. Helm이란?

```text
Kubernetes Package Manager
```

---

## 30-2. 왜 필요한가?

Kubernetes YAML이 매우 많기 때문.

Helm은 템플릿 기반으로 재사용 가능.

---

# 31. 현대 Cloud Native 전체 흐름

```text
Developer
 ↓
Git Push
 ↓
GitHub Actions
 ↓
Docker Build
 ↓
GHCR
 ↓
ArgoCD
 ↓
Kubernetes
 ↓
Pods
```

---

# 32. 현대 플랫폼 엔지니어링의 핵심

현재 Cloud Native 시스템은 단순 서버 운영이 아니다.

핵심은:

- Automation
- Scalability
- Self Healing
- Observability
- Immutable Infrastructure
- Declarative System

이다.

---

# 33. 최종 핵심 철학 정리

## Docker

```text
실행 환경 패키징
```

---

## Kubernetes

```text
Container 운영 자동화
```

---

## Service

```text
Stable Network Endpoint
```

---

## Ingress

```text
외부 HTTP 진입점
```

---

## Deployment

```text
Desired State 유지 관리자
```

---

## StatefulSet

```text
상태를 가진 Pod 관리자
```

---

## GitOps

```text
운영 상태를 Git으로 관리
```

---

## Observability

```text
시스템 내부 상태를 추론 가능한 능력
```

---

# 34. 학습 다음 단계 추천

현재 이해 수준 기준 다음 추천 순서:

1. 실제 Kubernetes Cluster 구축
2. Minikube / Kind 실습
3. Helm Chart 작성
4. ArgoCD 설치
5. Prometheus + Grafana 구축
6. OpenTelemetry 실습
7. Kafka 실습