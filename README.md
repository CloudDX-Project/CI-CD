# AI Travel Planner CI/CD

AI Travel Planner Backend의 CI/CD 및 GitOps 배포 구성을 관리하는 Repository입니다.

Backend 코드의 빌드·테스트·품질 검증부터 Docker Image 생성, Amazon ECR 저장,
GitOps Repository 변경, Argo CD를 통한 Amazon EKS 배포까지의 흐름을 구성하고
실제 배포를 통해 검증했습니다.

---

# CI/CD Architecture

```text
Developer
   │
   ▼
GitHub Backend Repository
   │
   ▼
GitHub Actions
   │
   ├─ Gradle Build
   ├─ Test
   ├─ JaCoCo Coverage
   └─ SonarQube Cloud
   │
   ▼
Docker Build
   │
   ▼
GitHub OIDC
   │
   ▼
AWS IAM Role
   │
   ▼
Amazon ECR
   │
   ├─ Commit SHA Image
   └─ Image Vulnerability Scan
   │
   ▼
GitOps Repository
   │
   ▼
Argo CD
   │
   ▼
Amazon EKS
   │
   ├─ travel-api (2 replicas)
   └─ travel-worker (1 replica)
