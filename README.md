# 🚀 Simple CI/CD Workflow Project

> **한성대학교 소프트웨어공학 (Software Engineering) 실습 과제**
> 본 프로젝트는 GitHub Actions 환경에서 실제 작동하는 지속적 통합(CI) 및 지속적 배포(CD) 자동화 파이프라인을 설계하고 구축하여, 소프트웨어 공학의 자동화 릴리스 메커니즘을 검증하기 위한 저장소입니다.

---

## 🛠️ 개발 환경 및 아키텍처 도구 (Tech Stack)

- **OS Environment:** `Ubuntu-latest` (GitHub-hosted Runners)
- **Runtime Platforms:** `Node.js 20.x` (Frontend & Backend Multi-Environment)
- **CI/CD Platform:** `GitHub Actions` (Automated Workflow Engine)
- **Security & Inspection Tools:** `npm audit`, `Trivy (Aqua Security)`, `ESLint`, `TypeScript`
- **Automation CLI:** `GitHub CLI (gh)`, `Git`

---

## 🔄 수업 사양 기반 CI/CD 파이프라인 아키텍처 (Pipeline Flow)

수업 명세에 따라 본 파이프라인은 단계별 종속성(`needs`)을 지닌 **2단계 격리형 CI Gate**와 **CD 무중단 배포 검증** 구조로 설계되었습니다.

[Local Code Commit]
│
▼ (Git Push / Pull Request to main, develop)
┌────────────────────────────────────────────────────────┐
│  ▶ STAGE 1: Static Analysis & Security Verification    │
│  ┌──────────────────────┐    ┌──────────────────────┐  │
│  │   static-analysis    │    │    security-scan     │  │
│  │ (ESLint+Type Check)  │    │ (npm audit + Trivy)  │  │
│  └──────────┬───────────┘    └──────────┬───────────┘  │
└─────────────┼───────────────────────────┼──────────────┘
└─────────────┬─────────────┘
▼ (CI Gate: Both Jobs Must Success)
┌────────────────────────────────────────────────────────┐
│  ▶ STAGE 2: Automated Quality & Logic Assurance        │
│  ┌──────────────────────┐    ┌──────────────────────┐  │
│  │      unit-test       │    │   integration-test   │  │
│  │   (Vitest & Jest)    │    │ (Coverage Target 80%)│  │
│  └──────────────────────┘    └──────────────────────┘  │
└────────────────────────────────────────────────────────┘
│
▼ (Automated CD Pipeline Triggered)
┌────────────────────────────────────────────────────────┐
│  ▶ STAGE 3: Immutable Build & Continuous Delivery      │
│  ┌──────────────────────────────────────────────────┐  │
│  │  1. Docker Build & Push (to GHCR Layer Cache)     │  │
│  │  2. Container Layer Security Scan                 │  │
│  │  3. Staging Deployment & E2E Test (Playwright)    │  │
│  │  4. Production Deployment (GitHub Pages)         │  │
│  │  5. Post-Deployment Verification (Smoke Test)     │  │
│  └──────────────────────────────────────────────────┘  │
└────────────────────────────────────────────────────────┘

---

## 📝 단계별 워크플로우 세부 작업 명세 (Job Specifications)

### 1. [CI] 정적 분석 (`static-analysis`)
- **목적:** 컴파일 전 단계에서 구문 오류 및 코드 안티 패턴을 사전 차단하여 코드 무결성 확보
- **내용:** - `actions/checkout@v4`를 통한 타겟 브랜치 소스 획득 및 `setup-node@v4` 기반 환경 초기화
  - `npm ci` 종속성 설치 시 빌드 성능 극대화를 위한 `package-lock.json` 고속 캐싱 전략 반영
  - 프론트엔드와 백엔드의 `ESLint` 문법 검사 및 `TypeScript typecheck`를 단일 Job 내에서 병렬/순차 수행

### 2. [CI] 보안 검사 (`security-scan`)
- **목적:** 의존성 패키지와 소스 코드 내에 포함된 잠재적 보안 취약점 자동 탐지
- **내용:**
  - `npm audit`을 실행하여 하위 종속성 모듈의 위험도 체크 (HIGH 레벨 이상 감지 시 빌드 실패 제어)
  - Aqua Security의 `Trivy` 파일시스템 모드(`scan-type: 'fs'`)를 통해 전체 IaC 설정 및 소스 코드 정밀 검사 진행

### 3. [CI Gate] 단위 및 통합 테스트 (`unit-test` / `integration-test`)
- **목적:** 비즈니스 로직 검증 및 컴포넌트 간 유기적인 연동 무결성 확인
- **내용:**
  - **종속성 트리거 설정:** 앞선 `static-analysis`와 `security-scan` 작업이 모두 최종 성공(`Success`) 상태일 때만 작동하도록 `needs` 구조 선언
  - **Unit Test:** Vitest(프론트엔드 컴포넌트)와 Jest(백엔드 알고리즘 및 셔플 로직)를 헤드리스 모드로 분리 구동
  - **Integration Test:** API 엔드포인트 연동 점검 및 코드 커버리지(Code Coverage) 80% 엄격한 임계값(Threshold) 검증

### 4. [CD] 도커 빌드 및 컨테이너 배포 (`docker-build` & `deployment`)
- **목적:** 불변(Immutable) 컨테이너 이미지를 통한 이관 환경 간의 실행 일관성 보장
- **내용:**
  - Docker Layer Caching 전략을 반영하여 배포 이미지 빌드 효율 극대화 및 GitHub Container Registry(GHCR) 푸시
  - Staging 환경 배포 후 Playwright 기반의 종단간(E2E) 테스트 및 GitHub Pages 최종 배포 자동화
  - 릴리스 완료 직후 서비스 생존 여부를 실시간으로 확증하는 스모크 테스트(Smoke Test) 프로토콜을 수행하여 전체 프로세스 종결

---

## 📊 결과 가시성 요약 (GitHub Step Summary)

본 파이프라인은 과제 평가 가시성을 위해 작업의 성공/실패 여부와 관계없이 항상(`if: always()`) 동작하는 요약 스크립트를 포함하고 있습니다. 파이프라인 실행이 완료되면 GitHub Actions 탭 내 요약 페이지(Summary)에 정적 분석, 보안 스캔, 단위/통합 테스트 결과표가 마크다운 테이블(Table) 형태로 실시간 출력되어 소프트웨어 품질 상태를 한눈에 모니터링할 수 있도록 지원합니다.
