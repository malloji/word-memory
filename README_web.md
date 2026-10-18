# 온라인 실험(PsychoJS) — 본실험 단어셋 v1

기존 https://malloji.github.io/word-memory/ (english_three_level_pilot.js, 학교 1세트 데모)를 바탕으로 만든 본실험 버전.

## 파일
- index.html            : 그대로 GitHub Pages 루트에 두면 됨 (korean_three_level.js 를 module 로 불러옴)
- korean_three_level.js : 실험 본체. 설정 상수는 파일 머리에(INCLUDE_SPARE, LIST_LEN, 산수 문제 수·최대 시간, DataPipe ID, 완료 후 구글폼 URL)
- stimuli.js            : 자극. 손으로 고치지 말고 `python make_stimuli_js.py` 로 다시 생성 (원본: stimuli_sets_v0.5.csv(강 15), unrelated_pool_v0.4.csv)

## 설계 (korean_three_level.js 의 DESIGN 상수)
- 기본 'themes_plus_unrelated': 모든 참가자가 블록 4개 — 주제 목록 3개(학교·음료·병원, 각 강 15단어) + 무관 목록 1개(무관 풀 15단어), 순서 무선.
  주제 블록 검사 = old 8(학습 순서 1,3,5,…,15번째) + 유인어 1 + 무관 foil 7. 무관 블록 검사 = old 8 + 무관 풀 새 단어 8(유인어 없음). 기존 실험(15단어, old 8)과 같은 길이.
  비교: 주제 블록 유인어 오인률 vs 무관 블록 새 단어 오경보율(및 모든 블록 foil).
- 'latin_square': 유인어 고정·조건 회전(참가자 번호 % 3 으로 각 유인어를 강/약/무관 중 하나에), 블록 3개. experiment_design_v1.md 의 안. 약 목록이 10개뿐이라 이 설계에서는 LIST_LEN=10, N_OLD_TEST=5, N_FOILS=4 로 바꿔야 함.

## 기존 데모와 달라진 점
1. 자극을 stimuli.js 로 분리(세트 간 중복·무관 풀 겹침은 make_stimuli_js.py 가, 세션 안 단어 유일성은 실행 시 코드가 검사)
2. 블록당 학습 15단어(1.5 s + 0.5 s), 산수(덧셈 5문제를 맞히거나 20초가 지나면 끝, 틀리면 같은 문제 다시; 시간 초과로 못 푼 문제는 item_type=timeout 으로 기록), 재인 검사 old 8 + new 8, 4점 확신 + RT
3. 무관 학습 목록과 foil 은 60개 무관 풀에서 참가자마다 중복 없이 추출. 같은 참가자 ID → 같은 무선화(재현 가능)
4. 연습 블록 1개(condition=practice, 분석 제외)
5. CSV 열 추가: version, design, set_id, study_position

## URL 매개변수
- ?participant=12        참가자 번호(정수 권장; latin_square 설계에서는 1,2,3,… 순으로 배정해야 버전이 균형)
- ?test=1                DataPipe 업로드 없이 CSV 다운로드(점검용)
- ?quick=1               제시 0.3초·산수 2문제·최대 5초(동작 점검용). 본실험에서는 절대 붙이지 말 것

## 배포 (GitHub Pages)
1. 저장소(현재 https://github.com/100ga/world-memory)에 세 파일(index.html, korean_three_level.js, stimuli.js)을 커밋·푸시
   (기존 english_three_level_pilot.js 는 남겨 두어도 됨; index.html 이 새 파일을 가리킴)
2. Settings → Pages 에서 Deploy from a branch(main, /root) 저장 → 몇 분 뒤 https://100ga.github.io/world-memory/?participant=1&test=1 로 열어 끝까지 한 번 돌려 CSV 확인
3. DataPipe: 본실험용 실험을 새로 만들고 DATAPIPE_EXPERIMENT_ID 교체 권장(지금은 데모 ID 그대로라 데모 폴더에 섞여 저장됨)
4. 참가자에게는 https://100ga.github.io/world-memory/?participant=<번호> 형태로 배포

## 로컬 점검
python3 -m http.server 8765 --directory web  →  http://localhost:8765/?participant=1&test=1&quick=1
(2026-10-16 검증: 참가자 1·2·3·P07 로 끝까지 실행, 콘솔 오류 없음, CSV 76행 구조 확인)
