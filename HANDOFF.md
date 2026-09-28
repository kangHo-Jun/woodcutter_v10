# v10 인계

## 2026-09-22 — Test 승인 후 반복 배치 비교 기능 적용

- 사용자 Test 통과 승인에 따라 기본 통합 페이지 `index.html`에 비교 버튼/창/PDF를 연결.
- 추가: `js/repeatedLayout.js`, `js/repeatedLayoutUI.js`, `css/repeated-layout.css`.
- 기존 JavaScript 전부 해시 유지. 패킹/상태/요금/main-unified 로직과 기존 PDF 경로는 수정하지 않음.
- v10의 전단 여백/톱날 계산 규칙 보존. 비교 스냅샷은 결과 bin의 실제 유효 크기를 사용.
- 비교 후보는 미리보기이며 자동 채택/저장 결과 대체/요금 변경 없음. PDF와 버튼에는 v10 표기.
- 별도 구형 페이지 `index-mobile.html`, `index-pc-old.html`은 변경하지 않음. PC/모바일 검증은 반응형 기본 `index.html` 대상.

## 검증

- 변경 전 10개 사례의 실제 브라우저 결과를 저장하고 적용 후 배치/절단/비용/부품을 정확히 비교: 모두 일치.
- CASE1~4는 최초 Test 기준 결과와도 일치.
- 10개 사례 비교 ON/OFF, 부적합 후보 없음, 입력 변경 무효화, PDF, 직접 폼 입력 통과.
- 처음부터 390×844 모바일로 접속한 입력→계산→비교→PDF 및 기존 결과 보존 통과.
- 공유 모듈 Test 자동 검증 28/28 PASS. 복사 파일은 Test와 바이트 단위 동일.
- 비교 PDF CASE1 2페이지, CASE5 1페이지 및 기존 CASE5 PDF 다운로드 확인. CASE5 비교 PDF 렌더 검토 완료.
- 결과/화면/PDF는 `../woodcutter_Test/output/playwright/rollout-v10/`에 저장.

## 복원 및 다음 작업

- 수정 전 태그 `repeated-layout-before-20260922`, 기준 커밋 dc9fb83.
- 로컬 확인: http://127.0.0.1:8766/woodcutter_v10/ (프로젝트 상위 폴더 HTTP 서버 필요).
- 로컬 파일 적용만 완료. 커밋/푸시/웹 배포는 하지 않음.
- 현장 묶음 절단 조건과 자동 후보 채택/청구 방식은 미확정.
- 기존 B 엔진 three-length-300 절단선 기록 오류는 새 비교 기능과 별개로 미해결.

## 2026-09-28 — 승인된 CASE5 기본 배치 및 PDF 10MB 제한 반영

- Test에서 승인한 CASE5 서명만 기본 결과로 채택하도록 `approvedDefaultResult` 연결. v10 전단 규칙(전단 여백+톱날 유효 폭), 축 규칙, 회전 입력 동작을 유지했다. 다른 입력은 v10 패커의 기존 결과 객체를 그대로 사용한다.
- AREA_TARGET_HYBRID에서 변경 bin만 복사하고 상태 점수 캐싱 및 동일한 탐색 순서 생략 최적화를 이식했다. 패킹의 방향/비용 정책은 추가 변경하지 않았다.
- 기본 PDF와 비교 PDF 모두 공통 `PdfExport`를 거쳐 최대 10,000,000바이트로 제한한다. PNG 우선 프로파일 후 제한된 JPEG 대안을 쓰며 초과 시 저장을 중단한다. 비교 PDF의 비동기 오류 처리와 진행 중 입력 변경 검사도 포함했다.
- `index.html`에서 변경된 JS/CSS 리소스에 `20260928-v10-pdf-1` 캐시 버전을 적용했다. 기존 v10 비교 UI의 표기와 레이아웃은 보존했다.

### 검증

- 수정 전 태그: `pre-approved-pdf-rollout-20260928` (기준 HEAD `dc9fb83`). 수정 전 10개 CASE 결과, 기존 미커밋 비교 UI 파일, CASE5 화면/PDF를 `/Users/zart/Projects/재단/woodcutter_Test/output/rollout-final-v10/before/`에 보관했다.
- 수정 후 Chromium에서 CASE1~10 계산을 재실행했다. CASE5를 제외한 9개 결과의 전체 배치·절단·잔재와 요금이 v10 수정 전 기준과 일치했다. CASE5는 판재 1장, 부품 16개, 20회, 30,000원이며 Y 시작 좌표 0, 264.2, 528.4, 792.6 및 행별 X 좌표/규격이 승인 도면과 일치했다.
- CASE5 기본 PDF는 167,493바이트, 2쪽 A4로 열렸다. 기존 기본 PDF 7,137,798바이트보다 작고, 대표 PDF 페이지에서 한글·치수·절단선을 읽을 수 있었다. 화면 PNG와 PDF 렌더, CASE1~10 비교 결과는 `../woodcutter_Test/output/rollout-final-v10/after/`에 있다.
- 대상 v10 패커로 690개 부품 대량 시험: 207장, 패턴 2종(115장/92장), 미배치 0, geometry/cutDetails/guillotine 검증 모두 통과. 결과는 `bulk-results.json` 참조.
- 공통 PDF 브라우저 회귀(CASE1~10 기본/비교, CASE5 모바일, 30/80쪽 fixture, 실패 fallback)는 감독자 별도 검증에서 통과했다. 전 파일 커밋/푸시는 하지 않았다.

### 미해결

- 기준 태그 이후 비교 UI 파일(`index.html`, `js/repeatedLayout.js`, `js/repeatedLayoutUI.js`, `css/repeated-layout.css`)은 작업 전부터 미커밋/미추적 상태였다. 이번 반영은 저장된 사본을 보관하고 해당 기능을 유지한 상태로 진행했다.
- 구형 `js/pdfGenerator.js`와 별도 `js/app.js`에는 자체 `doc.save()` 경로가 남아 있다. 현재 반응형 `index.html`의 기본/비교 버튼은 `main-unified.js`와 `repeatedLayoutUI.js` 경로를 사용한다. 별도 구형 페이지까지 10MB 제한을 적용한 것은 아니다.
- 대량 부하 시험은 Node 패커 검증이며, 207쪽을 실제 PDF로 내보내는 시험은 아니다. 사용자 CASE PDF는 각 10MB 이하로 생성됐다.
