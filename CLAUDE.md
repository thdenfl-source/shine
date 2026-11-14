# shine — Claude 작업 지침

## 이 저장소는
**thdenfl-source/shine** — **헬기 기종을 바꿔 가며 비행 연습을 하는 시뮬레이터.**
`thdenfl-source/shinesong`(종합 시뮬레이터)의 파일을 그대로 복사해 시작했고(2026-11),
이제부터는 **이 저장소에서 따로 고쳐 나간다** — shinesong · Flightsimulator 와 코드를 맞추지 않는다.

| 저장소 | 목적 |
|---|---|
| **thdenfl-source/shine** (이 저장소) | **기종 교체 · 비행 연습 특화** — 비행 모델·조종석·계기를 기종별로, 비행 연습 과목 |
| thdenfl-source/shinesong | 모든 걸 합친 종합 시뮬레이터 (SIM 모드 · 3D 조종석 · 함정 착함 등) |
| thdenfl-source/Flightsimulator | 절차 훈련 (AIP 절차 · CDU · FPL · SHIP 접근 등) |

- 요청이 어느 저장소의 일인지 먼저 가린다. "SHINE" 이라 하면 이 저장소다.
- 코드 구조(js/00~16 · tests/ · index.html · sw.js)는 shinesong 과 같은 데서 출발했다.
- `js/00-edition.js` — 주소에 `flightsimulator` 가 없으므로 이 저장소는 늘 **full 판(SIM 모드 있음)** 으로 뜬다.
  절차 훈련판(proc) 관련 코드는 남아 있지만 이 저장소에서는 쓰지 않는다(정리해도 된다).

## 항상 따를 규칙

### 작업 단위 완료 시 Push + PR
1. 변경 파일을 `git add` → `git commit` (의미 있는 단위로)
2. 세션이 정해 준 작업 브랜치로 `git push -u origin <브랜치>`
3. GitHub MCP 로 PR 생성 (owner `thdenfl-source`, repo `shine`, base `main`)
4. **PR 머지는 사용자가 직접 GitHub 에서 한다. Claude 는 절대 머지하지 않는다.**
   (Claude 가 API 로 머지하면 Actions 가 트리거되지 않아 GitHub Pages 에 반영되지 않는다)

#### PR 분할 기준
- **한 작업 요청 = 1 PR.** 같은 요청의 후속 미세 수정은 같은 브랜치에 커밋을 쌓는다.
- 직전 PR 이 머지·종료됐는지 먼저 확인하고, 머지됐으면 새 PR 을 만든다.

### 테스트 실행 원칙 (시간 절약)
- 기본은 **고친 부분과 관련된 검사 묶음만** (`node tests/run.mjs '<이름>'`). 통과하면 PR 을 올린다.
- 버튼·화면(index.html · data-act)을 건드렸으면 **'스모크' '액션 위임'** 도 함께 돌린다.
- 전체 실행(약 40분)은 비행 물리처럼 두루 걸치는 큰 변경이거나 사용자가 요청할 때만.
- 'SIM 모드' 는 다섯 묶음(`SIM 모드 · 1 …` ~ `· 5 …`) — 고친 주제만, 다 돌릴 때는 `JOBS=5 node tests/run.mjs 'SIM 모드'`.

### 캐시 버전 올리기
- 앱 파일을 고치면 `sw.js` 의 `CACHE`(vfr-flight-vNNN)와 `index.html` 스크립트 꼬리표(`js/…js?v=NNN`)를
  **같은 번호로 함께** 올린다 — `tests/check-swver.mjs` 가 둘이 다르면 실패시킨다.
  예: `sed -i "s/vfr-flight-v500/vfr-flight-v501/" sw.js && sed -i 's/\.js?v=500"/.js?v=501"/g' index.html`
