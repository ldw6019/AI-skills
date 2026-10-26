# AI Skills
**Codex**와 **Claude Code**에서 사용하는 개인용 AI 스킬 모음입니다.

> 반복해서 하는 작업을 더 일관되고 효율적으로 처리할 수 있도록, 작업별 지침과 절차를 스킬로 정리합니다. 각 스킬은 이름이 있는 폴더에 보관하며, 자세한 사용 방법은 해당 폴더의 `SKILL.md`에서 확인할 수 있습니다.

## Skills
- `study-sheet-design` — `course-study-builder`가 쓴 STUDY.md를 수험서 판면의 A4 PDF로 조판합니다. 본문과 제목은 명조, 왼쪽 날개에는 고딕 메모, 생각 카드는 시험지 문항 모양이고, 단원 강조색은 과목에 맞게 고릅니다. `scaffold.py`로 HTML 초안을 만들고, `render.py`로 PDF를 만들면서 쪽 번호·실행 결과를 원본과 대조하고, 차례 쪽 번호를 맞추고, 쪽 끝에 홀로 남은 제목을 찾습니다. Chromium 계열 브라우저가 없으면 `course-study-builder`의 기본 렌더러를 씁니다.
- `course-study-builder` — 대학 강의 자료를 읽고 검증한 뒤, 핵심 개념·예제·예측형 자기 점검 카드·복습 질문을 담은 시험 대비 공부 자료(STUDY.md와 PDF)를 만듭니다. 시험범위와 강의에서 언급한 출제 신호를 표시합니다.

## 함께 쓰는 순서
1. `course-study-builder`로 STUDY.md를 쓰고 `render_study.py STUDY.md --lint-only`로 점검합니다.
2. `study-sheet-design/scripts/render.py --check`가 브라우저를 찾으면 디자인 경로로, 못 찾으면 `render_study.py`로 PDF를 만듭니다.
3. 디자인 경로: `scaffold.py STUDY.md --accent "#2b44b8"` → TODO 다듬기 → `render.py <html> --source STUDY.md --fix-toc`.

쪽수 목표는 없습니다. 강의 자료가 길면 공부 자료도 길어지고, 대신 빈 쪽 없이 이어서 흐르게 조판합니다.
