---
name: iran-war-newspaper
description: 이란 전쟁 관련 최근 5일 뉴스를 조사하여 한국어 HTML 신문(iran-war-daily.html)을 산출하는 마스터 워크플로우. 5인 에이전트 팀(편집장·종군기자·외교기자·팩트체커·신문 디자이너)을 가동하여 다중 출처 취재→교차 검증→편집→레이아웃까지 끝낸다. "이란 전쟁 신문", "이란 뉴스 페이퍼", "전쟁 신문 만들어", "이란 전황 정리해줘", "신문 다시 만들어", "톱 기사 다른 거로", "사설 다시 써줘", "디자인만 바꿔줘" 등 이란 전쟁 신문의 초기 생성·재실행·부분 수정 요청 시 반드시 이 스킬을 사용할 것.
---

# 이란 전쟁 신문 오케스트레이터

5인 에이전트 팀을 가동하여 이란 전쟁 신문을 산출한다.

## 실행 모드

**에이전트 팀** (TeamCreate). 이유:
- 기자 간 발견 공유 필요(군사 사건 → 외교 반응 추적)
- 팩트체커가 기자에게 재조사 요청
- 편집장이 실시간 우선순위 조정

## Phase 0: 컨텍스트 확인 (필수)

워크플로우 시작 시 `/Users/robin/Downloads/war-news/_workspace/`를 확인:

| 상태 | 사용자 요청 | 모드 |
|------|----------|------|
| _workspace 비어있음 | 임의 | **초기 실행** |
| _workspace 존재 + 새 5일 윈도우 요청 | "다시 만들어", "오늘자 신문" | **새 실행** (기존 → `_workspace_prev/`로 이동) |
| _workspace 존재 + 부분 수정 | "톱기사 다른 거로", "사설만", "디자인만" | **부분 재실행** (해당 에이전트만 호출) |

부분 재실행 매핑:
- "톱기사·헤드라인·편집안" → editor-in-chief 재호출
- "군사·전황 보강" → war-correspondent + editor-in-chief
- "외교·시장 보강" → diplomatic-correspondent + editor-in-chief
- "팩트체크 강화" → fact-checker + editor-in-chief
- "디자인·레이아웃" → newspaper-designer

## Phase 1: 팀 구성

```
TeamCreate(
  team_name: "iran-war-daily",
  description: "이란 전쟁 신문 데일리 편집팀 — 최근 5일 취재·검증·편집·디자인",
  agent_type: "editor-in-chief"
)
```

팀원 5명을 Agent 도구로 스폰 (모두 `model: "opus"`, `subagent_type: "general-purpose"`, `team_name: "iran-war-daily"`):
- name: `editor-in-chief`
- name: `war-correspondent`
- name: `diplomatic-correspondent`
- name: `fact-checker`
- name: `newspaper-designer`

각 스폰 prompt에는 해당 `.claude/agents/{name}.md` 파일을 읽고 그 역할을 수행하라고 명시.

## Phase 2: 워크플로우 (파이프라인)

### Step 1: 편집장 → 두 기자에 취재 가이드 발송
- editor-in-chief가 워크플로우 첫 메시지로 war-correspondent와 diplomatic-correspondent에게 SendMessage:
  - 취재 윈도우: 2026-04-27 ~ 2026-05-02 KST
  - 우선순위 키워드(이란, 호르무즈, 미사일, 제재, 유가 등)
  - 마감 시각: 가능한 빨리

### Step 2: 두 기자 병렬 취재 (TaskCreate)
- 종군기자 → `02_war_correspondent_report.md`
- 외교기자 → `02_diplomatic_correspondent_report.md`
- 두 기자는 SendMessage로 서로 발견을 공유 (군사→외교 반응 트래킹)

### Step 3: 팩트체커 검증
- 두 기자 보고서 완성 후 fact-checker가 `03_fact_checker_verification.md` 작성
- F등급 발견 시 즉시 editor-in-chief에 경고
- 추가 취재 필요 시 해당 기자에게 재요청 (1회만)

### Step 4: 편집장 편집안 작성
- editor-in-chief가 세 보고서를 통합하여 `04_editor_layout_plan.md` 작성
- 1~4면 구성, 헤드라인, 사설(600~800자) 모두 작성
- 팩트체커 D/F등급 항목 처리 방침 반영

### Step 5: 신문 디자이너 HTML 생성
- newspaper-designer가 편집안을 받아 `iran-war-daily.html` 생성
- 인쇄 가능한 단일 HTML 파일

### Step 6: 팀 정리
- 모든 산출물 확인 후 SendMessage로 팀원 shutdown_request

## Phase 3: 산출물 확인

최종 산출물:
- `/Users/robin/Downloads/war-news/iran-war-daily.html` (사용자 전달)
- `_workspace/` 안의 중간 산출물 (감사 추적용 보존)

브라우저에서 열어 확인하도록 사용자에 안내.

## 데이터 전달 규칙

- **태스크 기반**: TaskCreate로 단계별 작업 추적
- **파일 기반**: `_workspace/{phase}_{agent}_{artifact}.md` 컨벤션
- **메시지 기반**: 실시간 조율과 follow-up 요청

## 에러 핸들링

| 에러 | 대응 |
|------|------|
| WebSearch 실패 (네트워크/한도) | 1회 재시도 → 실패 시 해당 영역 누락 표시 후 진행 |
| 기자 보고서 빈약 | 편집장이 추가 취재 1회 요청, 재실패 시 가용 자료로 진행 |
| 팩트체커 F등급 빈발 | 편집장이 톱 후보를 B/C 등급으로 교체 |
| 디자이너 산출물 깨짐 | 디자이너가 자체 점검(체크리스트) 후 1회 재생성 |
| 팀원 응답 없음 | 30초 이상 무응답 시 SendMessage로 ping, 그래도 없으면 해당 산출물 누락 처리 |

## 팀 크기 가이드 적용

작업 규모: 중규모(취재/검증/편집/디자인 ~15개 작업), 팀원 5명, 팀원당 평균 3개 작업.

## 후속 작업 키워드 (description 보강)

다음 표현 등장 시 본 스킬 트리거:
- "이란 전쟁 신문", "이란 뉴스 페이퍼", "이란 전황", "전쟁 신문 만들어"
- "오늘자로 다시", "재실행", "업데이트"
- "톱기사 바꿔", "사설 다시", "디자인만 바꿔", "팩트체크 강화"
- "신문 보강해줘", "이전 결과 기반으로"

## 테스트 시나리오

**정상 흐름:**
1. 사용자: "이란 전쟁 최근 5일 신문 만들어줘"
2. Phase 0: _workspace 비어있음 → 초기 실행
3. Phase 1~3 순차 진행
4. `iran-war-daily.html` 산출, 사용자에게 경로 안내

**에러 흐름 (검색 결과 부족):**
1. 사용자: "이란 전쟁 신문"
2. 두 기자가 5일 윈도우 검색 → 일부 영역 결과 빈약
3. 편집장이 가용 자료 기반으로 재구성, 빈약한 영역은 "보도 부족" 박스로 명시
4. 신문 산출 (단, 1면 톱은 검증 가능한 사건으로 한정)
