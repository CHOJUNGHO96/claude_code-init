---
name: web-design-guidelines
description: UI/UX 작업 시 항상 참조하는 디자인 가이드라인. 색상 시스템, 컴포넌트 라이브러리, 레이아웃 프로세스, 일관성 규칙을 정의한다. 프론트엔드 컴포넌트 생성, 페이지 레이아웃, 스타일링 작업 시 자동 활성화.
---

# Web Design Guidelines

## When to Activate

- UI 컴포넌트 생성 또는 수정 시
- 페이지 레이아웃 설계 시
- 색상, 스타일링 관련 작업 시
- Next.js + shadcn/ui 프로젝트 작업 시
- 사용자가 "디자인", "UI", "컴포넌트", "레이아웃", "색상" 등을 언급할 때

---

## 1. Color System (색상 규칙) — CRITICAL

### 핵심 원칙: 유채색은 딱 2가지만

| 색상 역할 | 사용 위치 | 규칙 |
|----------|----------|------|
| **Main Color (Primary)** | CTA 버튼, 핵심 강조 요소 | 작업 시작 전 Hex 코드 확정 필수 |
| **Sub Color (Secondary)** | 배경, 보조 장식 요소 | 작업 시작 전 Hex 코드 확정 필수 |
| **나머지 전부** | 텍스트, 보더, 카드 배경 등 |  작업 시작 전 Hex 코드 확정 필수 |

### 반드시 지켜야 할 규칙

- ✅ [Coolors.co](https://coolors.co) 등 검증된 팔레트에서 추출한 Hex 코드 사용
- ✅ 제공된 Hex 코드 그대로 적용 (임의 수정 금지)
- ❌ 직접 색상 선택 금지 — 반드시 검증된 Hex 코드 사용
- ❌ 유채색 3가지 이상 사용 금지
- ❌ `opacity`, `brightness` 등으로 Hex 코드 임의 변형 금지

### 작업 시작 전 확인 사항

사용자가 메인/서브 컬러 Hex 코드를 제공하지 않았다면 **반드시 먼저 물어본다**:

```
"작업 전에 색상을 확정해야 합니다.
- 메인 컬러(Primary) Hex 코드가 있으신가요?
- 서브 컬러(Secondary) Hex 코드가 있으신가요?
없으시다면 서비스 특징을 알려주시면 Coolors.co 기반 팔레트를 추천해 드릴게요."
```

---

## 2. Component Library

### shadcn/ui 사용 (MUI 사용 금지)

**이유**: shadcn/ui는 소스 코드가 프로젝트 내부에 직접 위치 → AI가 코드를 직접 읽고 수정 가능

### 프로젝트 초기 세팅 (코딩 시작 전 필수)

```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#확정된_메인_HEX',           // 예: '#007AFF'
        secondary: '#확정된_서브_HEX',         // 예: '#E5F1FF'
        background: '#확정된_백그라운드_HEX',   // 예: '#E5F1FF'
      }
    }
  }
}
```

```bash
# shadcn/ui 초기화
npx shadcn@latest init
```

**Early Setting 원칙**: 기능 구현 후 디자인을 입히는 것이 아니라, **컬러와 shadcn/ui 세팅을 먼저 완료한 후** 코딩을 시작한다.

---

## 3. UI/UX Execution Process (실행 프로세스)

### 레이아웃 요청 방식

1. **English-Based Layout**: 레이아웃 묘사는 반드시 구체적인 **영문 프롬프트**를 사용한다
   - 한국어보다 AI의 레이아웃 이해도가 훨씬 높음

2. **프롬프트 추출 요청**: 레이아웃 묘사가 어려울 경우:
   ```
   "구현하려는 서비스의 특징: {서비스 설명}
   이에 맞는 상세 영문 레이아웃 프롬프트를 추출해줘."
   ```

### 레이아웃 작업 순서

```
1. 메인/서브 컬러 Hex 코드 확정
2. tailwind.config.js에 primary/secondary 등록
3. shadcn/ui 초기화
4. 영문 레이아웃 프롬프트 작성 (또는 추출 요청)
5. 컴포넌트 구현
```

---

## 4. Efficiency & Consistency (효율성 규칙)

### Anti-MCP for Design

- **피그마 MCP 등 직접 디자인 조작 지양**
- 이유: 토큰 소모 과다 + 맥락 단절로 디자인 일관성 붕괴
- 대신: 이 스킬 문서를 기반으로 텍스트 규칙 적용

### 일관성 체크리스트

코드 생성 시 매번 확인:

- [ ] 유채색이 Primary, Secondary 2가지만 사용됐는가?
- [ ] Hex 코드가 임의로 수정되지 않았는가?
- [ ] shadcn/ui 컴포넌트를 사용했는가?
- [ ] Tailwind의 `primary`, `secondary` 클래스를 사용했는가? (하드코딩 금지)
- [ ] 중립색(흰색/검정/회색)이 나머지 영역을 채우고 있는가?

---

## 활성화 프롬프트 예시

사용자가 디자인 작업을 시작할 때 아래 패턴으로 요청하도록 안내한다:

```
"지금부터 Next.js와 shadcn/ui를 사용해 프로젝트를 진행할 거야.
web-design-guidelines 스킬을 엄격히 준수해줘.
메인 컬러는 #007AFF(파란색), 서브 컬러는 #E5F1FF(연한 파란색)야.
이 규칙에 맞춰서 첫 번째 랜딩 페이지 레이아웃을 잡아줘."
```
