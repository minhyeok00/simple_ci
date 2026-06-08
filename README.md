# 🚀 Simple CI/CD Workflow Project

> **한성대학교 소프트웨어공학(Software Engineering)**
> 본 저장소는 소프트웨어 공학 방법론에 기반한 지속적 통합(CI) 및 지속적 배포(CD) 파이프라인의 핵심 매커니즘을 이해하고, 자동화된 워크플로우(Workflow)를 설계 및 검증하기 위한 프로젝트입니다.

---

## 🛠️ 개발 환경 및 도구 (Tech Stack)

- **Language:** `Python 3.x`
- **Framework:** `Flask` (Lightweight Web API Core)
- **CI/CD Platform:** `GitHub Actions` (Automated Workflow Engine)
- **Package Management:** `pip` (`requirements.txt`)

---

## 🔄 CI/CD 워크플로우 아키텍처 (Pipeline Stages)

본 프로젝트의 `simple_ci` 파이프라인은 소프트웨어 품질 보증 및 릴리스 안정성을 위해 **[구축] ➡️ [검사] ➡️ [테스트] ➡️ [배포]**의 4단계 표준 자동화 프로세스로 설계되었습니다.

---

## 📝 단계별 작업 명세 (Job Details)

### 1. 환경 구성 및 초기화 (`setup`)
- **목적:** 파이프라인 러너(Runner)에 독립적인 격리 환경 빌드
- **내용:** - Ubuntu 최신 환경 가상 머신 할당 및 코드 체크아웃(`actions/checkout`)
  - Python 런타임 인터프리터 구성 및 캐싱 세팅
  - `requirements.txt`에 명시된 필수 패키지 파싱 및 고속 빌드

### 2. 정적 코드 분석 (`lint`)
- **목적:** 코드 품질 무결성 확보 및 팀 소프트웨어 공학 표준 준수 검증
- **내용:** - 린팅(Linting) 도구를 활용하여 구문 오류, 미사용 변수, 코딩 컨벤션 안티 패턴 사전 필터링
  - 잠재적 버그 요소를 컴파일 전 단계에서 원천 차단하여 코드 정적 품질 점수 확보

### 3. 자동화 단위 테스트 (`test`)
- **목적:** 기능 수정 및 추가에 따른 기존 비즈니스 로직의 회귀(Regression) 방지
- **내용:**
  - 작성된 테스트 스크립트를 파이프라인 상에서 헤드리스(Headless) 모드로 자동 실행
  - 엔드포인트 라우팅 및 기능 단위의 검증 로직 통과 여부를 체크하여 빌드 성공/실패 여부 확정

### 4. 지속적 배포 검증 (`deploy`)
- **목적:** 품질이 검증된 아티팩트(Artifact)를 최종 운영 환경에 무중단 반영
- **내용:**
  - 앞선 `setup`, `lint`, `test` 작업이 모두 성공(`Success`)으로 종결되었을 때에만 트리거(Trigger) 작동
  - 타겟 인프라 환경으로 빌드 파일을 안전하게 이관하고 프로덕션 인스턴스를 무중단 재시작

---

## 📊 기대 효과 (Software Engineering Perspectives)

1. **지속적 통합(CI):** 소스 코드의 형상 관리 시스템 반영과 동시에 품질 검사가 수행되어, 코드 병합 시 발생하는 예외 비용을 최소화합니다.
2. **자동화된 배포(CD):** 수동 배포 프로세스에서 발생할 수 있는 휴먼 에러(Human Error)를 완전히 제거하고 신뢰성 높은 릴리스 사이클을 확보합니다.
