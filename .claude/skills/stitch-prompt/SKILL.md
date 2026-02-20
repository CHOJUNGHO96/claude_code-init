---
name: stitch-prompt
description: Google Stitch AI에 요청할 UI/UX 프롬프트를 자동 생성한다. 사용자의 한국어 요청을 영어 Stitch 양식으로 변환하고, 정보가 부족하면 원하는 결과물이 나올 때까지 추가 질문을 반복한다.
version: 1.0.0
---

# Stitch Prompt Generator

**Google Stitch** (https://stitch.withgoogle.com/) 는 프롬프트로 고품질 UI 레이아웃을 생성하는 AI 서비스다.
이 스킬은 사용자의 한국어 요청을 Stitch 최적화 영문 프롬프트로 변환한다.

---

## When to Activate

- 사용자가 "Stitch", "스티치", "stitch.withgoogle.com" 를 언급할 때
- "UI 프롬프트 만들어줘", "레이아웃 요청서 작성해줘" 등을 요청할 때
- 이 스킬을 직접 호출할 때 (`/stitch-prompt`)

---

## Output Template

생성할 최종 프롬프트 양식:

```
You are a professional UI/UX designer.

Project:
[서비스 목적 — 1~2문장, 영어]

Target Users:
[주요 사용자 설명 — 영어]

Design Rules:
- Primary Color: #[HEX]
- Secondary Color: #[HEX]
- Background Color: #[HEX]
- Font style: modern, minimal
- Layout: clean, dashboard style
- Mobile responsive

Main Screens:
1. [Screen name]
2. [Screen name]
3. [Screen name]

Functional Requirements:
- [feature 1]
- [feature 2]
- [feature 3]

Output:
Create high-fidelity UI screens in a modern SaaS style.
```

---

## Execution Flow (실행 흐름)

### Step 1: 컬러 확인

**우선순위 1 — tailwind.config.js 자동 읽기**

프로젝트 루트에서 `tailwind.config.js` 또는 `tailwind.config.ts`를 탐색한다:

```bash
# Glob으로 탐색
tailwind.config.js
tailwind.config.ts
frontend/tailwind.config.js
*/tailwind.config.js
```

파일을 찾으면 `theme.extend.colors.primary` / `theme.extend.colors.secondary` / `theme.extend.colors.background` 값을 추출한다.

**우선순위 2 — 파일 없으면 사용자에게 질문**

```
tailwind.config.js를 찾지 못했습니다. 색상을 직접 알려주세요:
- Primary Color (주 색상, 버튼/강조 요소): #
- Secondary Color (보조 색상, 배경/서브 요소): #
- Background Color (전체 배경색, 기본값 #F4FDAF): #

없으시면 서비스 특징을 알려주시면 추천해 드릴게요.
```

> Background Color는 `tailwind.config.js`의 `background` 값을 우선 사용한다.
> 파일이 없고 사용자가 따로 지정하지 않으면 `#F4FDAF`를 기본값으로 사용한다.

---

### Step 2: 정보 수집 (부족한 항목만 질문)

아래 4가지 항목을 순서대로 확인한다. 이미 제공된 항목은 건너뛴다.

#### 항목 1 — Project (서비스 목적)

```
이 서비스는 어떤 목적의 서비스인가요?
예: "보안 취약점(CVE) 정보를 검색하고 관리하는 대시보드"
```

수집 후 영어로 변환 예:
> "A security dashboard for searching and managing CVE (Common Vulnerabilities and Exposures) information."

#### 항목 2 — Target Users (주 사용자)

```
주요 사용자는 누구인가요?
예: "보안 담당자, 개발자, IT 운영팀"
```

수집 후 영어로 변환 예:
> "Security engineers, developers, and IT operations teams who need to monitor and respond to known vulnerabilities."

#### 항목 3 — Main Screens (주요 화면)

```
어떤 화면들이 필요한가요? (3개 이상 권장)
예:
1. 대시보드 (CVE 통계 요약)
2. CVE 검색 페이지
3. CVE 상세 정보 페이지
4. 알림 설정 페이지
```

수집 후 영어 화면명으로 변환:
> 1. Dashboard (CVE statistics overview)
> 2. CVE Search Page
> 3. CVE Detail Page
> 4. Notification Settings

#### 항목 4 — Functional Requirements (기능 요구사항)

```
각 화면에서 꼭 들어가야 할 기능을 알려주세요.
예:
- CVE ID, 심각도, 날짜로 검색 및 필터링
- CVSS 점수 시각화 (차트)
- 즐겨찾기 및 북마크 기능
- 이메일/슬랙 알림 설정
```

---

### Step 3: 프롬프트 품질 검증 (자동 체크)

모든 항목이 채워지면 아래 기준으로 품질을 검증한다:

| 체크 항목 | 기준 |
|---------|-----|
| Project 설명 | 1~2문장, 명확한 서비스 목적 포함 |
| Target Users | 구체적인 역할/직군 언급 |
| Main Screens | 3개 이상 |
| Functional Requirements | 3개 이상, 구체적인 기능 명시 |
| Primary / Secondary / Background Color | 유효한 Hex 코드 (`#` + 6자리) |

**품질 미달 시 보완 질문 예시:**

```
Main Screens가 2개뿐입니다. Stitch에서 더 풍부한 결과물을 얻으려면
화면이 3개 이상 있는 것이 좋습니다. 추가할 화면이 있나요?
예: 로그인 페이지, 설정 페이지, 리포트 내보내기 페이지 등
```

---

### Step 4: 최종 프롬프트 출력

검증 통과 후 완성된 영문 프롬프트를 코드 블록으로 출력한다.

출력 형식:
1. **완성된 Stitch 프롬프트** (코드 블록, 바로 복사 가능하게)
2. **사용 방법**: "위 프롬프트를 https://stitch.withgoogle.com/ 에 붙여넣으세요."
3. **수정 제안** (선택): 결과 개선을 위한 팁 1~2개

---

## Iterative Refinement (반복 개선)

사용자가 결과물이 마음에 들지 않으면 프롬프트를 개선한다.

### 개선 요청 패턴 처리

| 사용자 발언 | 처리 방식 |
|-----------|--------|
| "화면이 너무 단순해" | Main Screens 상세화, UI 복잡도 키워드 추가 |
| "좀 더 전문적인 느낌으로" | Font style, Layout 키워드 보강 |
| "색상이 어울리지 않아" | Primary/Secondary 재설정 요청 |
| "기능이 더 들어가야 해" | Functional Requirements 항목 추가 |
| "다크모드로 해줘" | Design Rules에 `- Color scheme: dark mode` 추가 |

### 개선 프롬프트 추가 키워드 모음

**전문성 강화:**
```
- Use data-dense layouts with clear information hierarchy
- Include micro-interactions and hover states
- Apply enterprise-grade UI patterns
```

**화면 상세화:**
```
- Show realistic data with charts, tables, and KPI cards
- Include navigation sidebar with icons
- Add breadcrumb navigation and contextual actions
```

**모바일 강화:**
```
- Prioritize mobile-first design with bottom navigation
- Use card-based layout for small screens
- Include swipe gestures and touch-friendly targets
```

---

## Example Output

사용자 요청: "CVE 정보 관리 대시보드 만들고 싶어. 보안 담당자용이야."

```
You are a professional UI/UX designer.

Project:
A security operations dashboard for searching, monitoring, and managing CVE
(Common Vulnerabilities and Exposures) information in real time.

Target Users:
Security engineers, IT operations teams, and developers who need to track,
prioritize, and respond to known software vulnerabilities.

Design Rules:
- Primary Color: #1A56DB
- Secondary Color: #E8F0FE
- Background Color: #F4FDAF
- Font style: modern, minimal
- Layout: clean, dashboard style
- Mobile responsive

Main Screens:
1. Dashboard (CVE overview with severity distribution charts and recent alerts)
2. CVE Search & Filter Page (search by CVE ID, CVSS score, vendor, date range)
3. CVE Detail Page (full vulnerability description, affected versions, remediation steps)
4. Watchlist Page (bookmarked CVEs with status tracking)
5. Notification Settings Page (email and Slack alert configuration)

Functional Requirements:
- Real-time CVE feed with severity badges (Critical, High, Medium, Low)
- Advanced filtering by CVSS score range, vendor, product, and date
- CVSS score visualization with bar charts and trend graphs
- Bookmark and watchlist management with status labels
- Bulk export to CSV/PDF for reporting
- Email and webhook alert configuration for new CVEs matching saved filters

Output:
Create high-fidelity UI screens in a modern SaaS style.
```

---

## Color Reference

`web-design-guidelines` 스킬의 컬러 규칙을 따른다:
- Primary: CTA 버튼, 핵심 강조 요소
- Secondary: 배경, 보조 장식
- Background: `tailwind.config.js`의 `background` 값 우선 사용, 없으면 `#F4FDAF` 기본값
- 나머지: 중립색(흰색/검정/회색) 사용

Tailwind 설정 예시:
```js
// tailwind.config.js
module.exports = {
  theme: {
    extend: {
      colors: {
        primary: '#1A56DB',
        secondary: '#E8F0FE',
        background: '#F4FDAF',
      }
    }
  }
}
```
