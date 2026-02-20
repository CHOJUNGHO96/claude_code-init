# Everything Claude Code — 서브에이전트 가이드

서브에이전트(Sub-agent)는 특정 도메인에 특화된 AI 전문가 역할을 수행하는 독립 에이전트다. Claude Code의 Task 도구를 통해 호출되며, 각자 전문 영역에서 최적의 분석·수정·검토를 수행한다.

---

## 카테고리별 에이전트 목록

### 설계 & 기획

#### `architect` — 소프트웨어 아키텍처 전문가

> 신규 기능 계획, 대규모 리팩토링, 아키텍처 결정 시 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Grep, Glob |

**주요 기능**
- 신규 기능의 시스템 아키텍처 설계
- 기술 트레이드오프 평가 및 패턴 추천
- 확장성 병목 식별 및 성장 계획 수립
- 코드베이스 전반의 일관성 검토

**사용 시점**
- 새로운 기능 또는 서비스를 설계할 때
- 대규모 리팩토링 전 구조적 검토가 필요할 때
- 기술 스택 또는 아키텍처 패턴 결정이 필요할 때

---

#### `planner` — 구현 계획 전문가

> 복잡한 기능 구현, 아키텍처 변경, 대규모 리팩토링 요청 시 **자동 활성화**

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Grep, Glob |

**주요 기능**
- 요구사항 분석 및 상세 구현 계획 수립
- 복잡한 기능을 관리 가능한 단계로 분해
- 의존성 및 잠재적 리스크 식별
- 엣지 케이스 및 에러 시나리오 고려

**사용 시점**
- 코드 작성 전 구체적인 실행 계획이 필요할 때
- `/plan` 커맨드 실행 시 내부적으로 호출
- 복잡한 다단계 구현 작업을 시작하기 전

---

### 코드 리뷰

#### `code-reviewer` — 범용 코드 리뷰 전문가

> 코드 작성 또는 수정 직후 **반드시** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Grep, Glob, Bash |

**주요 기능**
- 코드 품질, 가독성, 중복 여부 검토
- 보안 취약점(시크릿 노출, 입력 검증 등) 탐지
- 에러 처리 및 테스트 커버리지 확인
- Critical / Warning / Suggestion 우선순위로 피드백 제공

**사용 시점**
- 코드를 작성하거나 수정한 직후
- `/code-review` 커맨드 실행 시

---

#### `go-reviewer` — Go 코드 리뷰 전문가

> Go 프로젝트의 모든 코드 변경 시 **반드시** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Grep, Glob, Bash |

**주요 기능**
- 관용적 Go 패턴 및 컨벤션 검토
- 동시성(goroutine, channel) 안전성 분석
- SQL Injection, 커맨드 Injection 등 보안 취약점 탐지
- `go vet`, `staticcheck` 실행 및 결과 분석

**사용 시점**
- Go 파일(`.go`)을 수정한 후
- `/go-review` 커맨드 실행 시

---

#### `python-reviewer` — Python 코드 리뷰 전문가

> Python 프로젝트의 모든 코드 변경 시 **반드시** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Grep, Glob, Bash |

**주요 기능**
- PEP 8 준수 및 Pythonic 코드 스타일 검토
- 타입 힌트 누락 및 타입 오류 탐지
- SQL Injection, subprocess 커맨드 인젝션 보안 검토
- `ruff`, `mypy`, `pylint` 등 정적 분석 실행

**사용 시점**
- Python 파일(`.py`)을 수정한 후
- `/python-review` 커맨드 실행 시

---

### 빌드 에러 해결

#### `build-error-resolver` — TypeScript/빌드 에러 해결 전문가

> 빌드 실패 또는 타입 에러 발생 시 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep, Glob |

**주요 기능**
- TypeScript 타입 에러, 제네릭 제약 조건 문제 해결
- 모듈 해석 실패, 의존성 충돌 수정
- `tsconfig.json`, Webpack, Next.js 설정 오류 해결
- 최소한의 변경으로 빌드를 통과시키는 것에 집중 (아키텍처 변경 금지)

**사용 시점**
- `npm run build` 또는 `tsc` 실행 시 에러가 발생할 때
- `/build-fix` 커맨드 실행 시

---

#### `go-build-resolver` — Go 빌드 에러 해결 전문가

> Go 빌드 실패 시 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep, Glob |

**주요 기능**
- Go 컴파일 에러 및 `go vet` 경고 수정
- `staticcheck` / `golangci-lint` 이슈 해결
- 모듈 의존성 문제(`go.mod`) 처리
- 타입 에러 및 인터페이스 불일치 수정

**사용 시점**
- `go build ./...` 실행 시 에러가 발생할 때
- `/go-build` 커맨드 실행 시

---

### 테스팅

#### `e2e-runner` — E2E 테스트 전문가

> E2E 테스트 생성, 유지보수, 실행 시 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep, Glob |

**주요 기능**
- Vercel Agent Browser(선호) 또는 Playwright로 E2E 테스트 작성
- 테스트 여정(Test Journey) 관리 및 불안정 테스트(Flaky Test) 격리
- 스크린샷, 비디오, 트레이스 아티팩트 수집·업로드
- 핵심 사용자 플로우의 정상 동작 보장

**사용 시점**
- 중요 사용자 시나리오에 대한 E2E 테스트가 필요할 때
- `/e2e` 커맨드 실행 시

---

#### `tdd-guide` — TDD 전문가

> 새 기능 작성, 버그 수정, 리팩토링 시 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep |

**주요 기능**
- 테스트 먼저(Test-First) 방법론 강제 적용
- Red-Green-Refactor 사이클 가이드
- 80% 이상 테스트 커버리지 보장
- 단위/통합/E2E 테스트 스위트 작성

**사용 시점**
- 새 기능을 TDD 방식으로 개발할 때
- `/tdd` 커맨드 실행 시

---

### 유지보수 & 보안

#### `refactor-cleaner` — 데드 코드 정리 전문가

> 미사용 코드 제거, 중복 정리, 리팩토링 시 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep, Glob |

**주요 기능**
- `knip`, `depcheck`, `ts-prune`으로 데드 코드 탐지
- 미사용 exports, dependencies, imports 제거
- 중복 코드 식별 및 통합
- 변경 사항을 `DELETION_LOG.md`에 기록

**사용 시점**
- 코드베이스 정리 및 기술 부채 감소가 필요할 때
- `/refactor-clean` 커맨드 실행 시

---

#### `security-reviewer` — 보안 취약점 탐지 전문가

> 사용자 입력 처리, 인증, API 엔드포인트, 민감 데이터 관련 코드 작성 후 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep, Glob |

**주요 기능**
- OWASP Top 10 취약점 탐지
- 하드코딩된 시크릿(API 키, 비밀번호, 토큰) 발견
- 입력 검증, XSS, CSRF, SQL Injection 방지 검토
- `npm audit`, `semgrep`, `git-secrets` 등 보안 도구 실행

**사용 시점**
- 인증/인가 코드 또는 민감 데이터를 다루는 코드를 작성한 후
- 커밋 전 보안 검토가 필요할 때

---

#### `database-reviewer` — PostgreSQL/데이터베이스 전문가

> SQL 작성, 마이그레이션 생성, 스키마 설계, DB 성능 문제 발생 시 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep, Glob |

**주요 기능**
- 쿼리 성능 최적화 및 적절한 인덱스 추가
- Row Level Security(RLS) 및 최소 권한 원칙 적용
- 커넥션 풀링, 타임아웃, 동시성 문제 해결
- Supabase 모범 사례 기반 설계

**사용 시점**
- SQL 쿼리 또는 마이그레이션 코드를 작성할 때
- 데이터베이스 성능 문제를 진단할 때

---

#### `doc-updater` — 문서화 & 코드맵 전문가

> 코드맵 업데이트 및 문서화 작업 시 **선제적으로** 사용

| 항목 | 내용 |
|------|------|
| 모델 | Claude Opus |
| 도구 | Read, Write, Edit, Bash, Grep, Glob |

**주요 기능**
- 코드베이스 구조를 분석해 아키텍처 코드맵(`docs/CODEMAPS/*`) 생성
- README 및 가이드 문서를 실제 코드에 맞게 최신화
- TypeScript AST 분석으로 의존성 매핑
- `/update-codemaps`, `/update-docs` 커맨드 실행 시 내부 호출

**사용 시점**
- 코드 변경 후 문서가 최신 상태인지 확인해야 할 때
- `/update-codemaps` 또는 `/update-docs` 커맨드 실행 시

---

## 에이전트 요약표

| 에이전트 | 카테고리 | 주요 역할 | 관련 커맨드 |
|----------|----------|-----------|------------|
| `architect` | 설계 | 시스템 아키텍처 설계 | — |
| `planner` | 설계 | 구현 계획 수립 | `/plan` |
| `code-reviewer` | 코드 리뷰 | 범용 코드 품질 검토 | `/code-review` |
| `go-reviewer` | 코드 리뷰 | Go 코드 전문 리뷰 | `/go-review` |
| `python-reviewer` | 코드 리뷰 | Python 코드 전문 리뷰 | `/python-review` |
| `build-error-resolver` | 빌드 에러 | TS/JS 빌드 에러 수정 | `/build-fix` |
| `go-build-resolver` | 빌드 에러 | Go 빌드 에러 수정 | `/go-build` |
| `e2e-runner` | 테스팅 | E2E 테스트 생성·실행 | `/e2e` |
| `tdd-guide` | 테스팅 | TDD 방법론 강제 적용 | `/tdd` |
| `refactor-cleaner` | 유지보수 | 데드 코드 정리 | `/refactor-clean` |
| `security-reviewer` | 보안 | 보안 취약점 탐지 | — |
| `database-reviewer` | DB | PostgreSQL 최적화 | — |
| `doc-updater` | 문서화 | 코드맵·문서 업데이트 | `/update-codemaps`, `/update-docs` |
