---
name: newspaper-design
description: 클래식 신문 미학을 따르는 한국어 HTML 신문 레이아웃 디자인 스킬. 4면 구성(1면 톱·2면 외교·3면 분석·4면 데이터), Noto Serif KR 기반 타이포, 흑백+진홍 액센트, 인쇄 가능한 단일 HTML 파일 산출. newspaper-designer 에이전트가 사용한다.
---

# 신문 디자인 스킬 (Newspaper Design)

편집장의 편집안을 받아 **인쇄 가능한 단일 HTML 신문**을 만든다.

**중요 원칙**: 모든 한국어 표기는 한글로 풀어 쓴다. 한자(漢字) 사용 금지 — 戰況→전황, 美/中/日/韓→미국/중국/일본/한국, 發→발, 行→행, 大→큰 등 모두 한글 변환. 마스트헤드·헤드라인·박스 제목·날짜 표기 모두 적용.

## 1. 디자인 철학

**클래식 신문 + 한글 가독성**

- 흑백 기반에 진홍색(#8B0000) 단일 액센트
- 세리프 본문 + 명조체 헤드라인 — 무게감 있는 보도 톤
- 멀티 칼럼 그리드, 단 분리선
- 사진은 회색 placeholder 박스 + 캡션 (저작권 회피)
- 이모지·아이콘·화려한 색상 금지

## 2. 기술 사양

- **단일 HTML 파일**: 외부 의존성은 Google Fonts CDN만. 스크립트 불필요.
- **폰트**: `Noto Serif KR`(헤드라인·본문), `Noto Sans KR`(메타·캡션). 영문 보조: `Playfair Display`.
- **인쇄 대응**: `@media print { @page { size: A3 portrait; margin: 1cm; } }`. 각 "면"마다 `page-break-after: always`.
- **반응형**: 1024px 이하에서 단 수 줄임(5단→3단). 모바일 우선순위 낮음.

## 3. 4면 구성 명세

### 1면 (Front Page) — 5단 그리드
```
┌─────────────────────────────────────────────┐
│             [마스트헤드 — 신문 제호]           │
│  발행일·호수·발행기관 (좌)        날씨/지수 (우)│
├──────────────────────────┬──────────────────┤
│                          │  [사이드 톱 1]    │
│   [메인 톱 헤드라인]      │   헤드라인        │
│                          │   리드 4~5줄      │
│   [부제(deck)]           ├──────────────────┤
│                          │  [사이드 톱 2]    │
│   [사진 placeholder]     │                  │
│   [메인 리드 + 본문 요약] │                  │
│                          ├──────────────────┤
│                          │  [오늘의 요약]    │
│                          │   5일 핵심 5줄    │
├──────────────────────────┴──────────────────┤
│      [면 인덱스: 2면 외교 / 3면 분석 / 4면 데이터]│
└─────────────────────────────────────────────┘
```

### 2면 (국제·외교) — 6단 그리드
- 외교 헤드라인 기사 (3단 폭, 사진 박스 포함)
- 진영별 입장 비교 박스 (2단 폭, 표 형식)
- 제재·외교조치 요약 (1단 폭, 박스)

### 3면 (분석·사설) — 6단 그리드
- 사설 박스 (2단 폭, 좌상단, 회색 배경, 흑백 톤)
- 분석 기사 (3단 폭, 헤드 + 부제 + 본문)
- 한반도 영향 박스 (1단 폭)

### 4면 (데이터·인포그래픽) — 6단 그리드
- 가로 5일 타임라인 (전체 폭, SVG)
- 유가 추이 미니 차트 (2단 폭, SVG)
- 사상자 통계 표 (2단 폭, HTML 표)
- 호르무즈 해협 모식도 (2단 폭, SVG)

## 4. 타이포 위계

| 요소 | 폰트/크기/무게 | 비고 |
|------|--------------|------|
| 신문 제호(masthead) | Playfair Display 64~72px Black | 영문 병기 |
| 1면 톱 헤드라인 | Noto Serif KR Bold 48~56px | line-height 1.2 |
| 1면 부제(deck) | Noto Serif KR SemiBold 22px | 회색 #444 |
| 사이드 헤드라인 | Noto Serif KR Bold 28px | |
| 본문 | Noto Serif KR Regular 14px | line-height 1.7, word-break: keep-all |
| 바이라인 | Noto Sans KR Medium 12px | 회색 #666 |
| 캡션·메타 | Noto Sans KR Regular 11px | 회색 #888 |

## 5. 색상 토큰

```css
--ink: #1a1a1a;       /* 본문 잉크 */
--paper: #fafaf7;     /* 신문지 색 (살짝 따뜻한 흰색) */
--rule: #1a1a1a;      /* 단 분리선 */
--accent: #8B0000;    /* 진홍 — 마스트헤드, 톱 헤드라인 강조, 박스 헤더 */
--mute: #888;         /* 메타 정보 */
--soft: #ddd;         /* 사진 placeholder 배경 */
--paper-warm: #f4f1e8;/* 사설 박스 배경 */
```

## 6. 사진 Placeholder

```html
<div class="photo-placeholder">
  <div class="photo-frame">
    <span class="photo-label">[사진]</span>
  </div>
  <p class="photo-caption">호르무즈 해협 인근 — 자료사진 / 본 기사용 실제 사진은 게재되지 않았습니다.</p>
</div>
```

```css
.photo-frame {
  background: linear-gradient(135deg, #ddd 25%, #ccc 25%, #ccc 50%, #ddd 50%, #ddd 75%, #ccc 75%);
  background-size: 8px 8px; /* 점묘 패턴 */
  aspect-ratio: 4/3;
  display: flex; align-items: center; justify-content: center;
  border: 1px solid var(--ink);
}
```

## 7. SVG 인포그래픽 가이드

### 5일 타임라인
- 가로 800px × 세로 200px
- 5개 마커(날짜) 가로 배치, 각 마커 위/아래에 사건 라벨
- 군사 이벤트 = 빨간 마커, 외교 = 검정 마커

### 유가 추이
- 200px × 120px
- 5일치 점 + 선 그래프, 시작/끝 가격 라벨
- 상승/하락 색상은 흑백 + 진홍

### 호르무즈 모식도
- 단순화된 SVG (Iran 대륙 + Hormuz 해협 라인 + 통항 화살표)
- 회색 톤, 핵심 지점만 라벨

## 8. 본문 작성 규칙

기자 보고서 → 신문 본문 변환 시:
- 한 단락 3~5줄, 단락 사이 0.5em 간격
- 첫 단락(리드)은 5W1H 압축
- 직접 인용은 큰따옴표 + 한글 + (영문 원문)
- 출처는 단락 끝 또는 기사 끝 박스에 모아 표기

## 9. 인쇄 친화 설정

```css
@media print {
  @page { size: A3 portrait; margin: 1cm; }
  .page-break { page-break-after: always; }
  body { font-size: 11pt; background: white; }
  .no-print { display: none; }
}
```

## 10. 산출 체크리스트

HTML 완성 전 확인:

- [ ] 마스트헤드에 발행일(2026-05-02), 호수, 발행기관 표기
- [ ] 1면 톱·사이드 톱·요약 박스 모두 채워짐
- [ ] 2~4면 헤드라인 모두 편집장 편집안과 일치
- [ ] 모든 기사에 바이라인(by 종군기자/외교기자) 표기
- [ ] 출처 박스가 각 기사 끝에 있음
- [ ] @media print 규칙 적용됨
- [ ] 사진 placeholder가 모두 캡션 포함
- [ ] 4면 인포그래픽 SVG가 작동
- [ ] 한글 폰트가 Google Fonts에서 로드됨 (preconnect 포함)
