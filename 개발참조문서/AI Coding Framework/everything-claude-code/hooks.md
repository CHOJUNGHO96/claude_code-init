# Everything Claude Code — 훅 시스템 가이드

훅(Hook)은 Claude Code의 특정 이벤트(도구 실행 전/후, 세션 시작/종료 등)에 자동으로 실행되는 셸 명령이다. ECC는 사전 정의된 훅 설정(`hooks.json`)을 제공하여 코드 품질 유지, 자동 포맷, 보안 검사 등을 자동화한다.

---

## 훅 카테고리 개요

| 카테고리 | 트리거 시점 | 훅 수 | 주요 목적 |
|----------|-----------|------|-----------|
| `PreToolUse` | 도구 실행 전 | 5개 | 위험 작업 차단, 사전 검증 |
| `PostToolUse` | 도구 실행 후 | 5개 | 자동 포맷, 정적 분석 |
| `PreCompact` | 컨텍스트 압축 전 | 1개 | 상태 저장 |
| `SessionStart` | 세션 시작 시 | 1개 | 이전 컨텍스트 로드 |
| `Stop` | Claude 응답 완료 후 | 1개 | `console.log` 감사 |
| `SessionEnd` | 세션 종료 시 | 2개 | 상태 저장 + 패턴 추출 |

---

## PreToolUse — 도구 실행 전 검증

도구가 실행되기 전에 개입하여 위험하거나 잘못된 작업을 차단하거나 경고를 발생시킨다.

### 1. Dev 서버 tmux 강제 실행
- **매처**: `npm run dev`, `pnpm dev`, `yarn dev`, `bun run dev`
- **동작**: tmux 외부에서 개발 서버 실행을 **차단(block)**
- **이유**: tmux 세션 내에서만 로그 접근이 가능하도록 강제

```
[Hook] BLOCKED: Dev server must run in tmux for log access
[Hook] Use: tmux new-session -d -s dev "npm run dev"
```

### 2. 장시간 명령어 tmux 권고
- **매처**: `npm install/test`, `cargo build`, `make`, `docker`, `pytest`, `vitest`, `playwright` 등
- **동작**: tmux 외부 실행 시 **경고 출력** (차단하지 않음)
- **이유**: 장시간 실행 명령어는 세션 지속성을 위해 tmux 권장

### 3. Git Push 전 검토 알림
- **매처**: `git push`
- **동작**: 푸시 전 변경 사항 검토 알림 메시지 출력
- **이유**: 의도치 않은 코드 푸시 방지를 위한 의식적 확인 유도

### 4. 불필요한 문서 파일 생성 차단
- **매처**: `Write` 도구로 `.md` 또는 `.txt` 파일 생성 시
- **예외**: `README.md`, `CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`
- **동작**: 해당 파일 생성을 **차단(block)**
- **이유**: 문서를 README.md에 통합하여 파일 난립 방지

### 5. 컨텍스트 압축 제안 (strategic-compact)
- **매처**: `Edit` 또는 `Write` 도구 사용 시
- **동작**: 논리적 작업 구간에서 수동 `/compact` 실행 권고
- **이유**: 임의 자동 압축으로 인한 컨텍스트 손실 방지

---

## PostToolUse — 도구 실행 후 자동 처리

도구 실행이 완료된 후 자동으로 포맷, 타입 체크, 경고를 수행한다.

### 1. PR 생성 후 URL 로깅
- **매처**: `Bash` 도구로 `gh pr create` 실행 후
- **동작**: 생성된 PR URL을 출력하고 리뷰 커맨드를 안내
- **이유**: PR 생성 결과를 빠르게 확인하고 후속 작업 진행

### 2. 빌드 완료 후 비동기 분석 (예시)
- **매처**: `npm run build`, `pnpm build`, `yarn build`
- **동작**: 비동기(async) 모드로 백그라운드 분석 실행 (30초 타임아웃)
- **이유**: 빌드를 블로킹하지 않고 병렬로 분석 수행

### 3. JS/TS 파일 Prettier 자동 포맷
- **매처**: `Edit` 도구로 `.ts`, `.tsx`, `.js`, `.jsx` 파일 수정 후
- **동작**: `npx prettier --write <파일>` 자동 실행
- **이유**: 코드 스타일 일관성 자동 유지

### 4. TypeScript 타입 체크
- **매처**: `Edit` 도구로 `.ts`, `.tsx` 파일 수정 후
- **동작**: 가장 가까운 `tsconfig.json` 기준으로 `npx tsc --noEmit` 실행, 해당 파일의 에러만 출력
- **이유**: 수정 즉시 타입 오류 조기 발견

### 5. `console.log` 경고
- **매처**: `Edit` 도구로 `.ts`, `.tsx`, `.js`, `.jsx` 파일 수정 후
- **동작**: 파일 내 `console.log` 발견 시 위치와 함께 경고 출력
- **이유**: 디버그 로그가 커밋에 포함되는 것을 방지

---

## PreCompact — 컨텍스트 압축 전 상태 저장

### 1. 압축 전 상태 저장
- **매처**: 모든 컨텍스트 압축 이벤트
- **동작**: `scripts/hooks/pre-compact.js` 실행 → 현재 작업 상태 파일로 저장
- **이유**: 컨텍스트 압축 후에도 중요 정보가 복구 가능하도록 보존

---

## SessionStart — 세션 시작 시 초기화

### 1. 이전 컨텍스트 로드 & 패키지 매니저 탐지
- **매처**: 모든 새 세션 시작
- **동작**: `scripts/hooks/session-start.js` 실행
  - 이전 세션의 저장된 컨텍스트 복원
  - 프로젝트의 패키지 매니저(npm/pnpm/yarn/bun) 자동 탐지
- **이유**: 새 세션에서도 이전 작업 상태를 이어받아 연속성 확보

---

## Stop — 응답 완료 후 검사

### 1. `console.log` 최종 감사
- **매처**: 모든 Claude 응답 완료 후
- **동작**: `scripts/hooks/check-console-log.js` 실행 → 수정된 파일의 `console.log` 전수 검사
- **이유**: 세션 전체에서 디버그 로그 누락 방지를 위한 최종 안전망

---

## SessionEnd — 세션 종료 시 처리

### 1. 세션 상태 저장
- **매처**: 모든 세션 종료
- **동작**: `scripts/hooks/session-end.js` 실행 → 현재 세션 상태를 파일로 영속화
- **이유**: 다음 세션에서 `SessionStart` 훅이 복원할 수 있도록 상태 보존

### 2. 패턴 추출 (Continuous Learning)
- **매처**: 모든 세션 종료
- **동작**: `scripts/hooks/evaluate-session.js` 실행 → 세션에서 재사용 가능한 패턴 식별·저장
- **이유**: 세션 경험을 자동으로 학습하여 미래 작업 품질 향상

---

## 훅 설정 위치

ECC 훅은 `~/.claude/plugins/marketplaces/everything-claude-code/hooks/hooks.json`에 정의되어 있으며, ECC 설치 후 `~/.claude/settings.json`의 `hooks` 섹션에 통합된다.

```json
// ~/.claude/settings.json
{
  "hooks": {
    "PreToolUse": [...],
    "PostToolUse": [...],
    "PreCompact": [...],
    "SessionStart": [...],
    "Stop": [...],
    "SessionEnd": [...]
  }
}
```
