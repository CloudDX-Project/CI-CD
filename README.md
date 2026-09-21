# CI/CD

AI Travel Planner Backend의 CI/CD 구성입니다.

---

# CI

## Status

Implemented

GitHub Actions 기반 Backend CI Pipeline을 구성했습니다.

## Architecture

```text
Developer
   ↓
GitHub
   ↓
GitHub Actions
   ↓
Gradle Build / Test
   ↓
JaCoCo
   ↓
SonarQube Cloud
   ↓
Quality Gate
   ↓
Docker Build
   ↓
GitHub OIDC
   ↓
AWS IAM Role
   ↓
Amazon ECR
   ↓
Commit SHA Image
   ↓
ECR Basic Image Scan
```

## Implemented

- GitHub Actions
- Java 21
- Gradle Build
- JUnit Test
- JaCoCo Test Coverage
- SonarQube Cloud
- SonarQube Quality Gate 연동
- Docker Image Build
- GitHub OIDC
- AWS IAM Role Assume
- Amazon ECR Push
- Git Commit SHA 기반 Image Tag
- ECR Basic Image Scan
- HIGH / CRITICAL Vulnerability Check

---

## AWS Authentication

장기 AWS Access Key를 GitHub Repository에 저장하지 않습니다.

```text
GitHub Actions
      ↓
GitHub OIDC
      ↓
AWS IAM Role
      ↓
Amazon ECR
```

AWS 인증은 GitHub OIDC와 IAM Role을 사용합니다.

---

## Image Tag Strategy

Docker Image는 Git Commit SHA를 Tag로 사용합니다.

```text
ai-travel-backend:<commit-sha>
```

예:

```text
ai-travel-backend:a13f8c2...
```

이를 통해 Source Commit과 Container Image를 추적할 수 있습니다.

---

## Current CI Limitation

CI Pipeline 구성은 완료되어 있지만,
Backend Code의 SonarQube Quality Gate 결과에 따라
최종 Pipeline 성공 여부가 결정됩니다.

Backend 안정화 이후 전체 Pipeline을 최종 검증합니다.

---

# CD

## Status

Planned / WIP

Argo CD 기반 GitOps CD는 아직 실제 구축 전입니다.

Backend 안정화 이후 구성할 예정입니다.

## Target Architecture

```text
CI

GitHub
 ↓
GitHub Actions
 ↓
Amazon ECR
 ↓
ai-travel-backend:<commit-sha>


CD

GitOps Repository
       ↓
    Argo CD
       ↓
   Amazon EKS
```

---

## Current Deployment

현재 EKS Deployment Image:

```text
ai-travel-backend:latest
```

## Target Deployment

향후:

```text
ai-travel-backend:<commit-sha>
```

형태로 변경합니다.

목적:

- 배포 Version 추적
- Git Commit과 Image 연결
- Rollback 대상 명확화

---

## Planned CD Tasks

- Kubernetes Manifest 정리
- GitOps Repository 구성
- Argo CD 설치
- Argo CD Application 생성
- Manual Sync 검증
- Commit SHA 기반 Deployment
- Auto Sync 검증
- Rolling Update 검증
- Rollback 검증
- Final E2E Test

---

## Database Configuration

Backend Database 설정은 Kubernetes Secret을 통해 관리합니다.

```text
SPRING_DATASOURCE_URL
```

최종 배포 전에:

```text
Secret 내부 DB Name
        =
실제 RDS Database Name
```

정합성을 확인한 후 배포합니다.

---

# Final Target

```text
Developer
   ↓
GitHub
   ↓
GitHub Actions
   │
   ├── Build
   ├── Test
   ├── JaCoCo
   ├── SonarQube
   ├── Docker Build
   ├── OIDC
   ├── ECR Push
   └── Image Scan
          ↓
    Amazon ECR
          ↓
   GitOps Manifest
          ↓
       Argo CD
          ↓
      Amazon EKS
```
