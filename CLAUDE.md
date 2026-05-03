# 이란 전쟁 신문 프로젝트 (war-news)

## 하네스: 이란 전쟁 데일리 신문

**목표:** 최근 5일치 이란 전쟁 관련 뉴스를 다중 출처로 취재·검증하여 인쇄 가능한 한국어 HTML 신문(`iran-war-daily.html`)을 생성한다.

**트리거:** 이란 전쟁 / 이란 전황 / 이란 신문 / 뉴스 페이퍼 관련 요청 시 `iran-war-newspaper` 스킬을 사용하라. 부분 수정("톱기사 바꿔", "사설 다시", "디자인만") 요청도 동일 스킬이 처리한다. 단순 사실 질문(예: "이란 어디에 위치?")은 직접 응답.

**팀 구성:** editor-in-chief, war-correspondent, diplomatic-correspondent, fact-checker, newspaper-designer (총 5명, 모두 opus)

**산출물 경로:**
- 최종: `/Users/robin/Downloads/war-news/iran-war-daily.html`
- 중간: `/Users/robin/Downloads/war-news/_workspace/`

**표기 규칙:** 모든 한국어 산출물은 한글로 풀어 쓴다. 한자(漢字) 사용 금지 — 마스트헤드·헤드라인·박스 제목·날짜 표기 모두 적용.

**변경 이력:**
| 날짜 | 변경 내용 | 대상 | 사유 |
|------|----------|------|------|
| 2026-05-02 | 초기 구성 | 전체 (5 에이전트 + 4 스킬) | - |
| 2026-05-02 | 한자 → 한글 일괄 치환, 표기 규칙 추가 | iran-war-daily.html, 04_editor_layout_plan.md, editor-in-chief/newspaper-designer/diplomatic-correspondent 정의, newspaper-design 스킬 | 사용자 요청 "한자는 한글로" |
| 2026-05-02 | gpt-image-2로 신문 사진 3장 생성·삽입 (1면 호르무즈 봉쇄, 2면 외교 회담장, 3면 유조선) | iran-war-daily.html photo-frame CSS, images/ 디렉토리 | 사용자 요청 "신문 이미지 생성·삽입" |
