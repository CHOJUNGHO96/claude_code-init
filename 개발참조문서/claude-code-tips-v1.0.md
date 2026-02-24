# Claude Code 핵심 팁 20가지 v1.0 🚀

> Claude Code를 효율적으로 활용하기 위한 essential tips
> 생산성 10배↑ | 비용 50%↓ | 컨텍스트 최적화

**작성자**: 조정호
**소속**: 개발2팀
**최종 업데이트**: 2026-02-24
**출처**: Claude Code 공식 문서 + 실무 노하우 종합

---

## 📋 빠른 목차 (카테고리별)

| 카테고리 | 팁 번호 | 주요 내용 |
|----------|---------|---------|
| **기초 설정** | 1-4 | CLAUDE.md, Auto Memory, Skills, 팀 설정 |
| **계획 & 설계** | 5-7 | Plan Mode, 모델 선택, 인터뷰 기법 |
| **자동화** | 8-10 | Hooks, 알림, 포맷팅 |
| **확장성** | 11-13 | MCP 서버, Skills, 커스텀 명령어 |
| **컨텍스트** | 14-17 | HANDOFF.md, 모델 스위칭, ! 접두사 |
| **생산성** | 18-20 | 서브에이전트, Undo, Git Worktree |

---

## 📌 섹션 1: 기초 설정 ⚙️

### Tip 1. CLAUDE.md로 프로젝트 컨텍스트 구축 ⭐⭐⭐

**핵심 한 줄**: 새 프로젝트마다 `/init`으로 시작하여 AI가 코드베이스를 분석한 후 자동으로 `CLAUDE.md` 생성

**방법**:
1. 프로젝트 루트에서 `/init` 명령어 실행
2. Claude가 코드베이스를 분석하고 초안 생성
3. 팀원들이 공유할 핵심 정보만 유지

**예시**:
```bash
/init
# Claude가 자동으로 생성한 CLAUDE.md:
# - 프로젝트 구조
# - 빌드 & 실행 명령어
# - 코딩 규칙
# - 아키텍처 설명
```

**주의**:
- 처음 1-2회 생성된 내용을 검토하고 수정
- 민감한 정보(API 키, 내부 URL)는 제거
- 공개 정보와 개인 설정 분리 필요

---

### Tip 2. CLAUDE.md에서 역할 분리하기 (500줄 이하) ⭐⭐⭐

**핵심 한 줄**: `CLAUDE.md`는 프로젝트 특정 정보만, 일반적인 패턴은 Skills로 분리 → 토큰 절약

**방법**:
```
CLAUDE.md (500줄 이하):
  - 프로젝트 개요
  - 빌드/실행 명령어
  - 핵심 아키텍처
  - 외부 API 의존성

.claude/skills/:
  - 코딩 패턴 (design-patterns.md)
  - 스타일 가이드 (coding-style.md)
  - 테스트 전략 (testing.md)
```

**예시**:
```yaml
# CLAUDE.md - 프로젝트만 담기
---
title: CVE-INFO Backend
---

## Setup
uv sync && uv run alembic upgrade head

## Architecture
Modular Monolith + DDD (4-layer structure)

## 외부 API
- NVD v2: API Key required
- GitHub: Token 필요 (30 req/min)
```

**장점**:
- 매 세션 기본 컨텍스트 토큰 30-40% 절약
- 파일 기반 관리로 버전 관리 용이
- Skills는 필요할 때만 로드 (on-demand)

---

### Tip 3. 팀 설정을 Git에 커밋하기 ⭐⭐

**핵심 한 줄**: `.claude/` 폴더를 Git에 커밋하여 모든 팀원이 동일한 설정과 커스텀 명령어 공유

**방법**:
```bash
# .gitignore에서 제외
git add .claude/
git commit -m "chore: Claude Code 팀 설정 추가"
```

**포함되어야 할 파일**:
```
.claude/
├── rules/              # 코딩 규칙 (YAML)
├── skills/             # 커스텀 Skills (MD)
└── agents/             # 커스텀 Sub-agents (JSON)
```

**제외되어야 할 파일**:
```
.claude/
├── memories/           # 개인 메모 (×)
└── .claude.local.md    # 개인 설정 (×, 자동 .gitignore)
```

**이점**:
- 팀 전체가 동일한 개발 환경
- PR 검토 시 일관된 가이드라인 적용
- 새 팀원 온보딩 시간 단축

---

### Tip 4. CLAUDE.local.md로 개인 설정 분리 ⭐

**핵심 한 줄**: 공유하지 않을 개인 설정(샌드박스 URL, 개인 API 키 등)은 `CLAUDE.local.md`에 저장 → 자동 gitignore

**방법**:
```markdown
# CLAUDE.local.md (자동으로 .gitignore)

## 개인 환경 변수
OPENAI_API_KEY=sk-...
DATABASE_URL=postgresql://user:pass@localhost/db

## 샌드박스 설정
SANDBOX_URL=http://localhost:5173

## 개인 Rules (override)
[추가 규칙]
```

**특징**:
- 자동으로 `.gitignore` 적용
- `CLAUDE.md` 다음으로 로드됨 (덮어쓰기 가능)
- 로컬 환경에서만 활성화

---

## 🎯 섹션 2: 계획 & 설계

### Tip 5. Plan Mode를 항상 먼저 사용하기 ⭐⭐⭐

**핵심 한 줄**: 복잡한 작업은 `Shift+Tab` 2회로 Plan Mode 진입 → 아키텍처 검증 후 실행 (성공률 90%↑)

**방법**:
1. **계획 요청**: "이 기능을 구현해줘" 입력
2. **Plan Mode 진입**: `Shift+Tab` `Shift+Tab` (또는 UI에서 "Plan" 버튼)
3. **계획 작성**: Claude가 단계별 계획 제시
4. **검토 & 승인**: 계획 검토 후 "proceed" 또는 "수정해줘"
5. **자동 실행**: 승인 후 자동 구현

**예시 계획 결과**:
```markdown
## 계획: N-day PoC 탐지 API 구현

### Phase 1: 도메인 설계 (30분)
- ExploitIntel Entity 정의
- Repository Interface 작성
- DDD Repository Pattern 적용

### Phase 2: 인프라 구현 (45분)
- SQLAlchemy ORM 모델
- Repository 구현체
- Unit Test

### Phase 3: API 레이어 (30분)
- FastAPI 라우터
- Pydantic 스키마
- Error Handling

### 위험 요소
- GitHub API Rate Limit (대응: Retry Logic)
- 성능 (대응: Indexing Strategy)
```

**이점**:
- 잘못된 방향 조기 감지
- 팀과 아키텍처 공유 가능
- 코드 작성 전 검증

---

### Tip 6. Plan Mode에서 모델 전환하기 ⭐⭐⭐

**핵심 한 줄**: 계획은 **Opus**(깊은 추론), 구현은 **Sonnet**(비용 효율) → 토큰 50% 절약

**방법**:
```yaml
Plan Mode (아키텍처):
  model: opus  # 깊은 사고, 장기적 설계
  thinking: enabled

Implementation (코딩):
  model: sonnet  # 실용적, 빠른 구현
  thinking: auto

검색/간단 작업:
  model: haiku  # 매우 저렴
```

**전환 방법**:
```
1. Plan Mode에서 계획 완료
2. "구현 단계에서 Sonnet으로 전환해줘" 요청
3. 또는 UI에서 우측 상단 모델 선택
```

**비용 비교** (1M 토큰 기준):
| 모델 | 입력 비용 | 사용 사례 |
|------|----------|---------|
| Haiku | $0.8 | 검색, 간단한 코드 생성 |
| Sonnet | $3 | 대부분의 개발 작업 |
| Opus | $15 | 복잡한 설계, 리팩토링 |

**전략**:
- 초기 Plan: Opus
- 구현 대부분: Sonnet
- 단순 검색/문서화: Haiku

---

### Tip 7. "인터뷰"로 요구사항 명확히 하기 ⭐⭐

**핵심 한 줄**: 구현 전 "먼저 나에게 인터뷰해줘"로 질문 받으면 예외 처리, 기술 스택이 더 정확함

**방법**:
```
사용자: "사용자 인증 시스템을 구현해줘"

Claude:
  1. 인증 방식? (JWT, Session, OAuth)
  2. DB? (PostgreSQL, MongoDB)
  3. Rate Limit 필요?
  4. 다중 로그인 지원?
  5. 비밀번호 정책?

[인터뷰 완료 후 정확한 구현 시작]
```

**효과**:
- 재작업(rework) 50% 감소
- 엣지 케이스 사전 예측
- 팀과의 커뮤니케이션 향상

---

## ⚙️ 섹션 3: 자동화

### Tip 8. Hooks로 자동 에러 감지하기 ⭐⭐⭐

**핵심 한 줄**: `Stop` 이벤트에 빌드 스크립트 훅 → 작업 후 자동으로 TypeScript 에러, Lint 검사

**설정** (`~/.claude/settings.json`):
```json
{
  "hooks": {
    "postToolUse": [
      {
        "name": "TypeScript Check",
        "when": {
          "toolName": "Edit",
          "pathGlob": "**/*.ts?(x)"
        },
        "command": "npm run type-check"
      }
    ],
    "onStop": [
      {
        "name": "Build Validation",
        "command": "npm run build"
      }
    ]
  }
}
```

**훅 종류**:
| 훅 | 발생 시점 | 용도 |
|----|---------|----|
| `preTool` | 도구 실행 전 | 파일 백업, 검증 |
| `postToolUse` | 도구 실행 후 | 포맷팅, Lint, Type 체크 |
| `onStop` | Claude 작업 완료 | 빌드, 테스트, 배포 |

**예시** (자동 포맷팅):
```json
{
  "postToolUse": [
    {
      "when": { "toolName": "Edit", "pathGlob": "**/*.py" },
      "command": "black {file} && isort {file}"
    }
  ]
}
```

**장점**:
- 수동 빌드 체크 불필요
- 에러를 즉시 발견하고 피드백
- CI/CD 파이프라인 자동화

---

### Tip 9. 알림 훅으로 대기 시간 절약하기 ⭐

**핵심 한 줄**: `Notification` 훅 설정 → Claude 대기 중일 때 데스크톱 알림, 터미널만 보고 있을 필요 없음

**설정**:
```json
{
  "hooks": {
    "notification": {
      "enabled": true,
      "triggers": ["userInputRequired", "taskCompleted"]
    }
  }
}
```

**또는 환경 변수**:
```bash
export CLAUDE_NOTIFICATION_ENABLED=1
export CLAUDE_NOTIFICATION_SOUND=1
```

**효과**:
- 다른 작업 진행 가능
- 즉시 피드백 가능

---

### Tip 10. PostToolUse 훅으로 코드 자동 포맷팅 ⭐⭐

**핵심 한 줄**: 파일 편집 후 자동으로 Prettier, Black, Ruff 실행 → 수동 포맷팅 불필요

**설정** (Python 예시):
```json
{
  "hooks": {
    "postToolUse": [
      {
        "name": "Python Format",
        "when": {
          "toolName": "Edit",
          "pathGlob": "**/*.py"
        },
        "command": "bash backend/scripts/format.sh {file}"
      },
      {
        "name": "Import Sort",
        "when": {
          "toolName": "Edit",
          "pathGlob": "**/*.py"
        },
        "command": "isort {file}"
      }
    ]
  }
}
```

**JavaScript 예시**:
```json
{
  "postToolUse": [
    {
      "when": { "toolName": "Edit", "pathGlob": "**/*.{js,ts,jsx,tsx}" },
      "command": "prettier --write {file}"
    }
  ]
}
```

**주의**: 너무 많은 훅은 토큰 낭비 가능 (50만+ 토큰/세션)

---

## 🔌 섹션 4: 확장성

### Tip 11. MCP 서버로 외부 도구 연결하기 ⭐⭐

**핵심 한 줄**: GitHub, PostgreSQL, Slack 등을 MCP 서버로 연결 → 실시간 이슈 조회, DB 쿼리, 알림 발송

**주요 MCP 서버**:
| 서버 | 기능 | 사용 사례 |
|------|------|---------|
| GitHub | PR/Issue 생성, 코드 검색 | 자동 PR 생성, 이슈 관리 |
| PostgreSQL | SQL 쿼리 실행 | DB 직접 조회, 마이그레이션 |
| Slack | 메시지 전송 | 알림, 리포트 자동 발송 |
| Playwright | 브라우저 자동화 | E2E 테스트, 스크린샷 |

**설정** (GitHub 예시):
```json
{
  "mcpServers": {
    "github": {
      "command": "gh",
      "env": {
        "GITHUB_TOKEN": "ghp_..."
      }
    }
  }
}
```

**사용 예시**:
```
Claude: 이 이슈를 수정했으니 PR을 만들어줘
[자동으로 GitHub PR 생성]
```

---

### Tip 12. Skills로 프로젝트 가이드라인 자동 주입 ⭐⭐

**핵심 한 줄**: 특정 파일/키워드 감지 시 관련 Skills(가이드라인)를 자동으로 컨텍스트에 추가

**구조**:
```
.claude/skills/
├── python-patterns.md       # Python 패턴
├── testing.md               # 테스트 전략
├── security.md              # 보안 가이드
└── performance.md           # 성능 최적화
```

**자동 활성화 규칙** (CLAUDE.md):
```yaml
skills:
  - match: "**/*.py"
    include: python-patterns.md
  - match: "test_*.py"
    include: testing.md
  - match: "**/auth/**"
    include: security.md
  - match: "**/api/**"
    include: api-design.md
```

**효과**:
- 필요한 순간에만 가이드라인 로드
- 토큰 절약
- 일관된 코딩 스타일

---

### Tip 13. 커스텀 슬래시 명령어 만들기 ⭐⭐

**핵심 한 줄**: 자주 반복되는 워크플로우를 `/command` 형태로 정의 → 프롬프트 입력 시간 단축

**정의** (CLAUDE.md):
```yaml
skills:
  /review:
    prompt: |
      이 코드를 보안, 성능, 가독성 관점에서 검토해줘.
      [체크리스트 자동 제시]

  /commit:
    prompt: |
      현재 변경사항을 분석하고 Conventional Commits 형식으로 커밋해줘.

  /test:
    prompt: |
      이 파일에 대한 단위 테스트를 pytest 형식으로 작성해줘.
```

**사용**:
```
/review              # 자동으로 리뷰 모드 활성화
/commit              # 커밋 메시지 자동 생성
/test app/module.py  # 테스트 코드 생성
```

---

## 🧠 섹션 5: 컨텍스트 관리

### Tip 14. HANDOFF.md로 세션 인수인계하기 ⭐⭐⭐

**핵심 한 줄**: 작업 완료/세션 말미 시 `HANDOFF.md`에 진행상황 정리 → 새 세션에서 한 파일만 읽고 이어가기

**작성 방법**:
```
세션 말미:
1. "HANDOFF.md로 진행상황 정리해줘" 요청
2. Claude가 자동으로 작성
3. git add HANDOFF.md && git commit

새 세션:
1. /load .claude/HANDOFF.md
2. 이전 진행상황 자동 이해
```

**포함되어야 할 내용**:
```markdown
# HANDOFF.md - 세션 {number}

## 완료된 작업
- [x] User 모델 설계
- [x] Repository 패턴 구현
- [x] 단위 테스트 작성

## 진행 중인 작업
- [ ] Integration Test
- [ ] API 문서화

## 다음 단계
1. ExploitIntel 모듈 구현 (3시간)
2. Slack 알림 연동 (2시간)
3. 통합 테스트 (1시간)

## 주의사항
- GitHub API Rate Limit 고려 (30 req/min)
- PostgreSQL 인덱스 필요 (performance)
```

**이점**:
- 세션 간 컨텍스트 손실 방지
- 장기 프로젝트 진행상황 추적
- 팀 커뮤니케이션 개선

---

### Tip 15. 모델 스위칭으로 토큰 최적화하기 ⭐⭐⭐

**핵심 한 줄**: 작업 복잡도별 모델 선택 (Haiku 검색 / Sonnet 구현 / Opus 설계) → 토큰 비용 50-70% 절약

**선택 기준**:
```yaml
Haiku (가장 저렴):
  - 간단한 검색: "이 함수는 어떻게 동작해?"
  - 코드 이해: "이 에러가 뭐야?"
  - 문서화: README 작성
  - 평균 토큰: 5K-10K

Sonnet (균형):
  - 대부분의 개발 작업
  - 새 기능 구현
  - 일반적인 버그 수정
  - 평균 토큰: 10K-30K

Opus (최고 사양):
  - 복잡한 아키텍처 설계
  - 성능 최적화 (프로파일링)
  - 보안 감사
  - 대규모 리팩토링
  - 평균 토큰: 30K+
```

**실제 사용 예**:
```
작업 1: "이 에러가 왜 나지?" → Haiku (2분)
작업 2: "새 API 엔드포인트 구현" → Sonnet (15분)
작업 3: "마이크로서비스 아키텍처 설계" → Opus (1시간)
```

**UI에서 모델 전환**:
- 우측 상단 "Model" 버튼 클릭
- 또는 커맨드: `/model sonnet`

---

### Tip 16. ! 접두사로 토큰 절약하기 ⭐⭐⭐

**핵심 한 줄**: 간단한 셸 명령은 `!git status` 형태로 실행 → AI 해석 불필요, 토큰 소모 0

**문법**:
```
!명령어 [args]
```

**예시**:
```
!git status              # Git 상태 확인
!npm run build          # 빌드 실행
!pytest tests/          # 테스트 실행
!curl https://api.com   # API 요청
!grep -r "function" .   # 파일 검색
```

**vs 일반 방식**:
```
일반: "git 상태를 확인해줘" → Claude 해석 → git status 실행 (~50 토큰)
최적: !git status → 직행 실행 (0 토큰)

절약: 명령 100개 × 50 토큰 = 5,000 토큰 절약!
```

**주의**:
- 간단한 명령만 사용
- 복잡한 로직이 필요하면 일반 방식 사용
- 명령 결과가 긴 경우 자동 필터링

---

### Tip 17. @파일 참조로 불필요한 검색 줄이기 ⭐⭐

**핵심 한 줄**: 특정 파일을 명시적으로 지정 (`@파일이름`) → Claude가 전체 코드베이스 검색 안 함

**문법**:
```
@파일이름
@디렉토리/파일.py
@*.test.ts
```

**예시**:
```
사용자 요청:
"사용자 생성 로직을 개선해줘" (❌ 전체 코드베이스 검색)
"@services/user.py 의 create_user 함수를 개선해줘" (✅ 해당 파일만 집중)
```

**효과**:
- 검색 시간 50-70% 단축
- 불필요한 파일 로드 방지
- 정확한 컨텍스트 제공

**복합 사용**:
```
@models/user.py 와 @services/user.py 를 리팩토링해줘
```

---

## ⚡ 섹션 6: 생산성

### Tip 18. 서브에이전트로 대량 출력 격리하기 ⭐⭐

**핵심 한 줄**: 테스트 결과, 로그 분석 같은 대량 출력 작업은 서브에이전트에 위임 → 메인 컨텍스트 깨끗하게 유지

**사용 경우**:
- 전체 테스트 실행 (수천 줄 출력)
- 로그 파일 분석
- 대규모 코드 리뷰
- 데이터 처리 및 리포트

**방법**:
```
Claude: "이 테스트들을 실행해줄 수 있어?"
사용자: !pytest tests/ (메인 세션)
→ 결과가 100줄 이상이면 자동 서브에이전트 활성화

또는 명시적:
@claude --subagent 테스트 실행
```

**효과**:
- 메인 세션 컨텍스트 300KB+ 절약
- 다른 작업과의 혼선 방지
- 병렬 처리 가능 (최대 3개)

---

### Tip 19. Undo (되감기)로 실수 빠르게 복구하기 ⭐⭐

**핵심 한 줄**: 원치 않는 코드 생성 시 `Esc` 키 2회 빠르게 → 마지막 작업 즉시 롤백

**단축키**:
- **Double Esc**: 마지막 작업 취소
- **Ctrl+Z** (macOS: Cmd+Z): Git처럼 작동

**시나리오**:
```
Claude:
  ```python
  def delete_all_users():  # 잘못된 구현
      database.truncate()
  ```

사용자: Esc Esc (빠르게 2회)

결과: 코드 생성 이전 상태로 복귀
```

**이점**:
- 잘못된 코드 즉시 제거
- 토큰 낭비 방지
- 대체 구현 신청 가능

---

### Tip 20. Git Worktree로 병렬 개발하기 ⭐⭐

**핵심 한 줄**: 여러 기능 동시 개발 시 Git Worktree로 브랜치별 독립된 디렉토리 생성 → 각 탭에서 다른 Claude 세션 실행

**설정**:
```bash
# feature-auth 브랜치용 새 worktree 생성
git worktree add ../cve-info-auth feature-auth

# feature-exploit 브랜치용 새 worktree 생성
git worktree add ../cve-info-exploit feature-exploit
```

**디렉토리 구조**:
```
프로젝트/
├── cve-info/              (메인, main 브랜치)
├── cve-info-auth/         (feature-auth, Claude 탭 2)
└── cve-info-exploit/      (feature-exploit, Claude 탭 3)
```

**Claude Code 사용**:
```
탭 1: cd cve-info && code .              (main 브랜치)
탭 2: cd cve-info-auth && code .         (feature-auth)
탭 3: cd cve-info-exploit && code .      (feature-exploit)

각 탭에서 독립된 Claude 세션 실행 → 컨텍스트 혼선 없음
```

**정리**:
```bash
# 작업 완료 후 제거
git worktree remove ../cve-info-auth
```

**이점**:
- 기능 간 컨텍스트 분리
- 병렬 개발 가능
- 리뷰 전 완전히 독립된 테스트 가능

---

## 📊 종합 체크리스트

### 🟢 필수 설정 (모든 프로젝트)
- [ ] Tip 1: `/init`으로 CLAUDE.md 생성
- [ ] Tip 2: CLAUDE.md 500줄 이하로 유지
- [ ] Tip 3: .claude/ 폴더를 Git에 커밋
- [ ] Tip 5: Plan Mode 활용 시작

### 🟡 권장 설정 (생산성 향상)
- [ ] Tip 4: CLAUDE.local.md 생성
- [ ] Tip 8: Stop 이벤트 훅 설정
- [ ] Tip 10: PostToolUse 포맷팅 훅
- [ ] Tip 14: HANDOFF.md 작성 습관
- [ ] Tip 16: ! 접두사 활용

### 🔵 고급 설정 (팀 협업)
- [ ] Tip 11: MCP 서버 연결
- [ ] Tip 12: Skills 자동 활성화
- [ ] Tip 13: 커스텀 명령어 정의
- [ ] Tip 20: Git Worktree 병렬 개발

---

## 💰 토큰 절약 요약

| 팁 | 절약 비율 | 누적 효과 |
|----|---------|---------|
| Tip 2: CLAUDE.md 500줄 | 30-40% | 30-40% |
| Tip 16: ! 접두사 (100 명령) | 5,000 토큰 | 5,000 토큰 |
| Tip 15: 모델 스위칭 | 50-70% | 50-70% |
| Tip 14: HANDOFF.md (세션 간) | 40% | 40% |
| Tip 18: 서브에이전트 (대량 출력) | 300KB+ | 300KB+ |
| **합계** | - | **60-80% 절약 가능** |

---

## 🔗 참고 자료

### 공식 문서
- [Claude Code 공식 문서](https://docs.anthropic.com/en/docs/claude-code/)
- [Hooks 가이드](https://code.claude.com/docs/en/hooks#hooks-reference)
- [MCP 서버 연결](https://code.claude.com/docs/en/mcp)
- [Skills 정의](https://code.claude.com/docs/en/skills)
- [Sub-agents](https://code.claude.com/docs/en/sub-agents)

### 커뮤니티 자료
- [Claude Code is a Beast](https://reddit.com/r/ClaudeAI) — 6개월 실무 노하우
- [Claude Code 45가지 팁](https://github.com/ykdojo/claude-code-tips?utm_source=pytorchkr&ref=pytorchkr) — 종합 가이드

---

## 📝 변경 이력

| 버전 | 날짜 | 변경 사항 |
|------|------|---------|
| v1.0 | 2026-02-24 | 초본 작성 (20가지 팁) |

---

**마지막 팁**: 이 문서의 팁들을 모두 한 번에 적용하지 말고, **매주 2-3개씩 실제로 사용해보며** 자신의 워크플로우에 맞게 조정하세요. 효과적인 도구는 반복 사용을 통해 습관이 됩니다. 🚀
