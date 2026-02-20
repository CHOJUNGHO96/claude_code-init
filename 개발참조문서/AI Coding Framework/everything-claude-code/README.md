# Everything Claude Code (ECC)

> Claude Code를 실전 수준으로 끌어올리는 검증된 설정 모음집

---

## 개요

**Everything Claude Code**는 Anthropic 해커톤 우승자(`affaan-m`)가 10개월간 실제 프로덕트를 개발하며 쌓은 Claude Code 설정을 오픈소스로 공개한 프로젝트다.

- GitHub: `affaan-m/everything-claude-code` (4.1만+ 스타)
- Claude Code **Plugin**으로 설치하여 즉시 사용 가능
- 6개 언어 지원 (TypeScript, Python, Go, Java, Django, Spring Boot)

---

## 구성 요소

| 구성 | 역할 | 수 | 참조 문서 |
|------|------|----|---------|
| **Agents** | 도메인 전문가 서브에이전트 | 13개 | [subagents.md](./subagents.md) |
| **Skills** | 기술 스택별 전문 지식 주입 | 29개 | [skills.md](./skills.md) |
| **Commands** | 슬래시 커맨드 자동화 | 30개 | [commands.md](./commands.md) |
| **Rules** | 항상 적용되는 코딩 표준 | 8개 | [rules.md](./rules.md) |
| **Hooks** | 이벤트 기반 자동화 | 15개 | [hooks.md](./hooks.md) |

---

## 설치 방법

### 1. Plugin 설치 (자동)

```bash
# Claude Code에서 실행
/plugin install everything-claude-code
```

설치 직후 agents, skills, commands를 `everything-claude-code:` prefix로 즉시 사용 가능.

### 2. Rules 적용 (수동 or configure-ecc)

Rules는 plugin만으로는 자동 적용되지 않는다. 별도 설치 필요.

```bash
# 대화형 설치 마법사 (권장)
/everything-claude-code:configure-ecc

# 수동 설치
mkdir -p ~/.claude/rules
cp -r ~/.claude/plugins/marketplaces/everything-claude-code/rules/common/* ~/.claude/rules/
cp -r ~/.claude/plugins/marketplaces/everything-claude-code/rules/python/* ~/.claude/rules/
```

---

## Plugin 설치 vs configure-ecc

| 기능 | Plugin만 | configure-ecc 이후 |
|------|---------|------------------|
| Agents / Skills / Commands | ✅ prefix 붙여서 사용 | ✅ prefix 없이 사용 |
| Rules (코딩 표준 자동 적용) | ❌ 미적용 | ✅ 적용 |
| 파일 커스터마이징 | ❌ 불가 | ✅ 가능 |

---

## 핵심 사용 패턴

```
# 코드 작성 전
/everything-claude-code:plan       → 구현 계획 수립 (코드 수정 전 확인 대기)

# 개발 중
/everything-claude-code:tdd        → TDD 워크플로우 강제
/everything-claude-code:python-review → Python 코드 리뷰

# 커밋 전
/everything-claude-code:e2e        → E2E 테스트 실행
/code-review                       → 보안·품질 종합 리뷰

# 세션 종료 전
/handoff                           → HANDOFF.md 생성 (컨텍스트 보존)
```

---

## 파일 구조 (설치 후)

```
~/.claude/                          # 글로벌 (모든 프로젝트)
├── agents/                         # 서브에이전트
├── skills/                         # 스킬
├── commands/                       # 커맨드
└── rules/                          # 코딩 표준 규칙

<project>/.claude/                  # 프로젝트별 (우선순위 높음)
├── agents/
├── skills/
├── commands/
├── rules/
└── memories/
    └── HANDOFF.md                  # 세션 간 컨텍스트 전달
```

---

## 참고

- [공식 GitHub](https://github.com/affaan-m/everything-claude-code)
- [Shortform Guide](https://x.com/affaanmustafa/status/2012378465664745795) — 설치, 기초, 철학
- [Longform Guide](https://x.com/affaanmustafa/status/2014040193557471352) — 토큰 최적화, 메모리, 병렬 실행
