# Everything Claude Code — 커맨드(Commands) 가이드

커맨드(Commands)는 Claude Code에서 `/커맨드명`으로 실행하는 슬래시 명령어다. 각 커맨드는 특정 에이전트를 호출하거나 정해진 워크플로우를 실행하며, ECC의 에이전트·스킬·훅 시스템과 연동하여 반복적인 개발 작업을 자동화한다.

---

## 카테고리별 커맨드 목록 (총 30개)

### 개발 & 빌드

#### `/build-fix`
**역할**: TypeScript/JavaScript 빌드 에러 점진적 수정

- `build-error-resolver` 에이전트를 내부적으로 호출
- 에러를 파일별로 그룹화하고 심각도 순으로 정렬하여 처리
- 최소한의 변경으로 빌드 통과에 집중 (아키텍처 변경 없음)

---

#### `/go-build`
**역할**: Go 빌드 에러, `go vet` 경고, 린터 이슈 점진적 수정

- `go-build-resolver` 에이전트 호출
- `go build ./...` → `go vet ./...` → `staticcheck` 순서로 진행
- 외과적(Surgical) 수정으로 최소 diff 보장

---

#### `/plan`
**역할**: 구현 계획 수립 — 코드 수정 전 사용자 확인 대기

- `planner` 에이전트 호출
- 요구사항 재확인, 리스크 평가, 단계별 구현 계획 생성
- **사용자가 CONFIRM하기 전까지 코드 미수정** (안전한 사전 계획)

---

#### `/tdd`
**역할**: TDD 워크플로우 강제 적용 (테스트 먼저 작성)

- `tdd-guide` 에이전트 호출
- 인터페이스 스캐폴딩 → 테스트 생성(Red) → 최소 구현(Green) 순서 강제
- 80%+ 테스트 커버리지 보장

---

### 코드 품질

#### `/code-review`
**역할**: 미커밋 변경 사항의 종합적 보안·품질 리뷰

- `code-reviewer` 에이전트 호출
- `git diff --name-only HEAD`로 변경 파일 목록 추출 후 순서대로 검토
- Critical(필수 수정) / Warning(권장 수정) / Suggestion(개선 고려) 분류 출력

---

#### `/go-review`
**역할**: Go 코드 전문 리뷰 (관용 패턴, 동시성, 보안)

- `go-reviewer` 에이전트 호출
- `git diff -- '*.go'` 및 `go vet`, `staticcheck` 실행
- 동시성 안전성, 에러 처리, SQL Injection 등 Go 특화 항목 집중 검토

---

#### `/python-review`
**역할**: Python 코드 전문 리뷰 (PEP 8, 타입 힌트, 보안)

- `python-reviewer` 에이전트 호출
- `ruff`, `mypy`, `pylint`, `black --check` 실행
- Pythonic 코드 스타일, 타입 힌트, SQL/커맨드 인젝션 보안 검토

---

#### `/verify`
**역할**: 현재 코드베이스 상태 종합 검증

- 빌드 → 타입 체크 → 린팅 → 테스트 순서로 검증 실행
- 각 단계 실패 시 이후 단계 중단
- PR 생성 전 또는 주요 변경 후 실행 권장

---

#### `/test-coverage`
**역할**: 테스트 커버리지 분석 및 미흡한 테스트 생성

- `npm test --coverage` 또는 `pnpm test --coverage` 실행
- `coverage/coverage-summary.json` 분석으로 80% 미달 파일 식별
- 미달 파일에 대한 추가 테스트 자동 생성

---

### E2E 테스팅

#### `/e2e`
**역할**: E2E 테스트 생성·실행 및 아티팩트 관리

- `e2e-runner` 에이전트 호출
- Vercel Agent Browser(선호) 또는 Playwright로 테스트 실행
- 스크린샷, 비디오, 트레이스 수집 및 아티팩트 업로드

---

#### `/go-test`
**역할**: Go TDD 워크플로우 강제 — 테이블 기반 테스트 먼저 작성

- 테이블 기반 테스트 작성 → 구현 → `go test -cover`로 커버리지 확인
- 80%+ 커버리지 목표 강제
- `go test -race ./...`으로 동시성 버그 탐지

---

### 리팩토링

#### `/refactor-clean`
**역할**: 데드 코드를 안전하게 식별하고 테스트 검증 후 제거

- `refactor-cleaner` 에이전트 호출
- `knip`, `depcheck`, `ts-prune`으로 분석 → `.reports/dead-code-analysis.md` 생성
- 제거 전 테스트 실행으로 안전성 확인 후 `DELETION_LOG.md`에 기록

---

### 문서화

#### `/update-docs`
**역할**: 소스에서 문서를 동기화 (package.json, .env.example 기반)

- `doc-updater` 에이전트 호출
- `package.json scripts` → 스크립트 레퍼런스 테이블 자동 생성
- `.env.example` → 환경 변수 문서 자동 생성

---

#### `/update-codemaps`
**역할**: 코드베이스 구조 분석 및 아키텍처 문서 업데이트

- 모든 소스 파일의 imports, exports, 의존성 스캔
- `codemaps/architecture.md`, `backend.md`, `frontend.md`, `data.md` 자동 생성
- 토큰 효율적인 형식으로 아키텍처 문서화

---

### 학습 & 진화 시스템

#### `/learn`
**역할**: 현재 세션에서 재사용 가능한 패턴 추출

- 세션에서 비자명적(Non-trivial) 문제 해결 패턴 탐지
- 패턴을 스킬 파일로 저장하여 미래 세션에서 재활용
- 세션 중 의미 있는 문제를 해결한 시점에 실행

---

#### `/skill-create`
**역할**: 로컬 git 히스토리를 분석해 팀 코딩 패턴으로 SKILL.md 생성

- 저장소의 커밋 히스토리에서 반복되는 패턴 추출
- Skill Creator GitHub App의 로컬 버전
- 팀 관행을 Claude에게 가르치는 커스텀 스킬 생성

---

#### `/eval`
**역할**: Eval 기반 개발(EDD) 워크플로우 관리

- `eval-harness` 스킬과 연동
- `/eval define <feature>`: 기대 동작 정의
- `/eval check <feature>`: 현재 구현 검증
- `/eval report`: 전체 eval 결과 보고서 생성
- `/eval list`: 정의된 eval 목록 출력

---

#### `/evolve`
**역할**: 관련 Instinct들을 스킬, 커맨드, 에이전트로 클러스터링

- `continuous-learning-v2`와 연동
- 축적된 Instinct를 분석하여 재사용 가능한 아티팩트로 승격
- 신뢰도가 높은 패턴을 공식 스킬/커맨드/에이전트로 진화

---

#### `/instinct-status`
**역할**: 학습된 모든 Instinct와 신뢰도 점수 조회

- 도메인별로 그룹화된 Instinct 목록 출력
- 각 Instinct의 신뢰도 점수(Confidence Score) 표시
- 진화 준비가 된 Instinct 식별

---

#### `/instinct-export`
**역할**: Instinct를 팀원 또는 다른 프로젝트와 공유 가능한 형식으로 내보내기

- 선택적 Instinct 내보내기 또는 전체 내보내기
- 팀 공유, 백업, 다른 프로젝트 이식에 활용

---

#### `/instinct-import`
**역할**: 팀원 또는 Skill Creator에서 Instinct 가져오기

- 외부 Instinct 파일 가져오기
- 팀 간 학습 내용 공유 및 동기화

---

### 워크플로우 & 오케스트레이션

#### `/orchestrate`
**역할**: 복잡한 작업을 위한 순차적 에이전트 워크플로우

- 워크플로우 타입별로 에이전트 실행 순서 정의
- 단계별 에이전트 결과를 다음 단계에 전달하는 파이프라인 구성

---

#### `/workflow`
**역할**: PRD 또는 기능 요구사항에서 구조화된 구현 워크플로우 생성

- 멀티 모델 협업 개발 워크플로우 (Research → Ideation → Plan → Execute → Optimize → Review)
- Frontend → Gemini, Backend → Codex 등 역할별 모델 라우팅
- MCP 서비스와 품질 게이트 통합

---

#### `/checkpoint`
**역할**: 워크플로우 내 체크포인트 생성 및 검증

- `/checkpoint create <name>`: 현재 상태 저장
- `/checkpoint verify <name>`: 저장된 상태 검증
- `/checkpoint list`: 전체 체크포인트 목록 조회
- 장시간 작업의 진행 상태 추적 및 복구 지점 확보

---

#### `/sessions`
**역할**: Claude Code 세션 히스토리 관리 (`~/.claude/sessions/`)

- `/sessions list`: 저장된 세션 목록 조회
- `/sessions load <id>`: 특정 세션 이전 컨텍스트 로드
- `/sessions alias <id> <name>`: 세션에 별칭 부여
- `/sessions info <id>`: 세션 상세 정보 조회

---

### 인프라 & 설정

#### `/pm2`
**역할**: 프로젝트를 자동 분석하여 PM2 서비스 명령어 생성

- 프로젝트 구조 분석 후 PM2 ecosystem 설정 자동 생성
- 멀티 서비스 환경의 프로세스 관리 설정 간소화

---

#### `/setup-pm`
**역할**: 선호하는 패키지 매니저 설정 (npm/pnpm/yarn/bun)

- 프로젝트 레벨 또는 글로벌 패키지 매니저 설정
- `disable-model-invocation: true`로 모델 호출 없이 즉시 실행
- ECC 설치 및 초기 환경 구성 시 가장 먼저 실행

---

## 커맨드 요약표

| 커맨드 | 카테고리 | 호출 에이전트 | 주요 목적 |
|--------|----------|--------------|-----------|
| `/build-fix` | 빌드 | `build-error-resolver` | TS/JS 빌드 에러 수정 |
| `/go-build` | 빌드 | `go-build-resolver` | Go 빌드 에러 수정 |
| `/plan` | 개발 | `planner` | 코드 전 계획 수립 |
| `/tdd` | 개발 | `tdd-guide` | TDD 워크플로우 강제 |
| `/code-review` | 품질 | `code-reviewer` | 범용 코드 리뷰 |
| `/go-review` | 품질 | `go-reviewer` | Go 코드 리뷰 |
| `/python-review` | 품질 | `python-reviewer` | Python 코드 리뷰 |
| `/verify` | 품질 | — | 빌드·테스트 종합 검증 |
| `/test-coverage` | 품질 | — | 커버리지 분석·생성 |
| `/e2e` | 테스팅 | `e2e-runner` | E2E 테스트 실행 |
| `/go-test` | 테스팅 | — | Go TDD + 커버리지 |
| `/refactor-clean` | 리팩토링 | `refactor-cleaner` | 데드 코드 제거 |
| `/update-docs` | 문서화 | `doc-updater` | 문서 동기화 |
| `/update-codemaps` | 문서화 | `doc-updater` | 코드맵 업데이트 |
| `/learn` | 학습 | — | 패턴 추출·저장 |
| `/skill-create` | 학습 | — | 팀 패턴 → 스킬 생성 |
| `/eval` | 학습 | `eval-harness` | Eval 기반 개발 |
| `/evolve` | 학습 | — | Instinct → 아티팩트 진화 |
| `/instinct-status` | 학습 | — | Instinct 목록 조회 |
| `/instinct-export` | 학습 | — | Instinct 내보내기 |
| `/instinct-import` | 학습 | — | Instinct 가져오기 |
| `/orchestrate` | 워크플로우 | — | 순차적 에이전트 파이프라인 |
| `/workflow` | 워크플로우 | — | 멀티 모델 협업 개발 |
| `/checkpoint` | 워크플로우 | — | 진행 상태 저장·복구 |
| `/sessions` | 워크플로우 | — | 세션 히스토리 관리 |
| `/pm2` | 인프라 | — | PM2 설정 자동 생성 |
| `/setup-pm` | 설정 | — | 패키지 매니저 설정 |
