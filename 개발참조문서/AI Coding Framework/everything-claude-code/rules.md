# Everything Claude Code — 규칙(Rules) 가이드

규칙(Rules)은 Claude Code 세션에서 항상 적용되는 코딩 표준과 워크플로우 가이드라인이다. ECC는 공통(Common) 규칙과 언어별 확장 규칙으로 구성된 2단계 구조를 제공하며, 각 규칙 파일은 `~/.claude/` 또는 프로젝트의 `CLAUDE.md`에 포함하여 사용한다.

---

## 규칙 디렉토리 구조

```
rules/
├── common/          # 언어 공통 규칙 (8개)
│   ├── agents.md
│   ├── coding-style.md
│   ├── git-workflow.md
│   ├── hooks.md
│   ├── patterns.md
│   ├── performance.md
│   ├── security.md
│   └── testing.md
├── typescript/      # TypeScript/JavaScript 확장 (5개)
├── golang/          # Go 확장 (5개)
└── python/          # Python 확장 (5개)
```

---

## Common 규칙 (언어 공통)

### `agents.md` — 에이전트 사용 지침

- 13개 서브에이전트의 사용 시점과 역할 정의
- 에이전트 자동 활성화 조건 명시
- 코드 작성 후, 빌드 실패 시, 보안 검토 시 각각 어떤 에이전트를 사용할지 안내

**핵심 포인트**
- `planner` → 복잡한 기능/리팩토링 시 선행 실행
- `code-reviewer` → 모든 코드 변경 후 필수 실행
- `security-reviewer` → 커밋 전 보안 검토

---

### `coding-style.md` — 코딩 스타일 표준

- **불변성(Immutability) 필수**: 기존 객체를 변경하지 않고 항상 새 객체를 반환
- **파일 크기 제한**: 파일당 200~400줄 권장, 최대 800줄
- **작은 파일 다수 > 큰 파일 소수**: 높은 응집도, 낮은 결합도 유지

**핵심 포인트**
- 원본 데이터 직접 수정 금지 → 복사본 반환 패턴 사용
- 모듈에서 대형 유틸리티 추출하여 분리
- 코드는 작성보다 읽히는 횟수가 많으므로 가독성 최우선

---

### `git-workflow.md` — Git 워크플로우

- 커밋 메시지 형식: `<type>: <description>` (feat, fix, refactor, docs, test, chore, perf, ci)
- PR 생성 시 전체 커밋 이력 분석 필수 (`git diff [base-branch]...HEAD`)
- PR 제목은 70자 이내, 상세 내용은 본문에 작성

**핵심 포인트**
- 마지막 커밋만 보지 말고 브랜치 전체 변경 이력 기반으로 PR 작성
- 짧고 의미 있는 커밋 타입 사용

---

### `hooks.md` — 훅 설정 가이드

- PreToolUse / PostToolUse / Stop 훅의 역할과 설정법
- Auto-Accept 권한은 신중하게 사용 (신뢰된 계획에만 적용)
- `allowedTools` 설정으로 세밀한 권한 제어 권고

**핵심 포인트**
- `--dangerously-skip-permissions` 플래그는 절대 사용 금지
- 탐색적 작업에서는 Auto-Accept 비활성화
- TodoWrite로 다단계 작업 진행 상황 추적

---

### `patterns.md` — 공통 설계 패턴

- 신규 기능 구현 전 스켈레톤 프로젝트 탐색 권고
- 병렬 에이전트로 스켈레톤 후보 평가(보안, 확장성, 관련성)
- Repository 패턴: `findAll`, `findById`, `create`, `update`, `delete` 표준 인터페이스

**핵심 포인트**
- 검증된 기반 구조에서 시작하여 반복 개선
- 데이터 접근 로직은 Repository 패턴으로 캡슐화
- 컴포넌트 간 의존성 최소화

---

### `performance.md` — 성능 최적화 전략

- **모델 선택 전략**: Haiku(경량·비용 절감) → Sonnet(주요 개발) → Opus(복잡한 아키텍처)
- 컨텍스트 윈도우 관리 및 토큰 효율화 방법
- 멀티 에이전트 병렬 실행으로 처리 시간 단축

**핵심 포인트**
- 빈번히 호출되는 경량 에이전트는 Haiku 사용 (3배 비용 절감)
- 복잡한 아키텍처 결정만 Opus 사용
- 독립적인 작업은 병렬 에이전트로 분산 처리

---

### `security.md` — 보안 가이드라인

- 커밋 전 필수 보안 체크리스트 (시크릿 노출, 입력 검증, SQL Injection, XSS, CSRF, 인증/인가, 레이트 리미팅)
- 시크릿은 소스 코드에 절대 하드코딩 금지 → 환경 변수 또는 시크릿 매니저 사용
- 노출 가능성이 있는 시크릿은 즉시 로테이션

**핵심 포인트**
- 모든 사용자 입력 검증 필수
- 에러 메시지에 민감 정보 포함 금지
- 스타트업 시점에 필수 시크릿 존재 여부 검증

---

### `testing.md` — 테스팅 요구사항

- **최소 테스트 커버리지**: 80% 이상 필수
- 테스트 유형 3종 모두 필수: 단위(Unit) + 통합(Integration) + E2E
- TDD 워크플로우 강제: Red(실패 테스트 작성) → Green(최소 구현) → Refactor

**핵심 포인트**
- 구현 전 반드시 테스트 먼저 작성
- 커버리지 80% 미달 시 태스크 완료 처리 불가
- E2E 테스트는 프레임워크 선택에 따라 Playwright 사용

---

## 언어별 확장 규칙

공통 규칙을 기반으로 각 언어의 관용적 패턴과 도구를 추가한다.

### TypeScript/JavaScript (5개)

| 파일 | 주요 확장 내용 |
|------|--------------|
| `coding-style.md` | Spread 연산자로 불변 업데이트, `ApiResponse<T>` 표준 인터페이스 |
| `hooks.md` | Prettier 자동 포맷, `tsc --noEmit` 타입 체크 훅 설정 |
| `patterns.md` | `ApiResponse<T>` 타입 안전 응답, TypeScript 제네릭 패턴 |
| `security.md` | `process.env` 시크릿 관리, `zod`로 입력 검증 |
| `testing.md` | **Playwright** E2E 프레임워크 사용, 에이전트 지원 테스트 패턴 |

**핵심 포인트**
- `as any` 타입 단언 사용 금지, 정확한 타입 정의
- 모든 API 응답에 `ApiResponse<T>` 래퍼 사용
- 수정 후 Prettier + TypeScript 체크 자동 실행

---

### Go (5개)

| 파일 | 주요 확장 내용 |
|------|--------------|
| `coding-style.md` | `gofmt`/`goimports` 필수, Functional Options 패턴 |
| `hooks.md` | `gofmt`/`goimports` 자동 포맷, `go vet` 정적 분석 훅 |
| `patterns.md` | Functional Options, 인터페이스 기반 의존성 주입 |
| `security.md` | `os.Getenv()`로 시크릿 관리, 파라미터화 쿼리 필수 |
| `testing.md` | 표준 `go test` + **테이블 기반 테스트**, 레이스 탐지(`-race`) |

**핵심 포인트**
- 모든 `.go` 파일은 `gofmt` 통과 필수
- 인터페이스로 의존성 주입하여 테스트 용이성 확보
- `go test -race ./...`으로 동시성 버그 사전 탐지

---

### Python (5개)

| 파일 | 주요 확장 내용 |
|------|--------------|
| `coding-style.md` | PEP 8 준수, 모든 함수에 타입 힌트 필수 |
| `hooks.md` | `black`/`ruff` 자동 포맷, `mypy`/`pyright` 타입 체크 훅 |
| `patterns.md` | Protocol(Duck Typing), dataclass, 제네레이터 패턴 |
| `security.md` | `python-dotenv`로 시크릿 관리, 파라미터화 쿼리 |
| `testing.md` | **pytest** 프레임워크, fixtures, parametrize, 커버리지 80%+ |

**핵심 포인트**
- 타입 힌트 없는 함수 시그니처 금지
- `os.system()` 대신 `subprocess.run()` 사용 (보안)
- `pytest --cov` 로 커버리지 측정 필수
