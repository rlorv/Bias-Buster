## 전체 요약

유튜브 홈의 영상들을 수집·분석하여 정치 성향(진보/보수/판단불가)을 추정하고, 사용자가 슬라이더로 지정한 비율에 맞춰 콘텐츠를 재배열하거나 시각 표시(테두리)를 적용하는 크롬 확장
(백그라운드에서 YouTube Data API와 OpenAI Chat Completions API를 호출)


## 기능 개요

- 유튜브 영상 ID 수집(최대 100개), 자동 스크롤로 크롤링
- 제목/설명/댓글 수집 → OpenAI로 정치 성향 1단어 분류(“진보/보수/판단불가”) 
- 분석 결과를 기준으로 테두리 색상 적용(진보=파랑, 보수=빨강, 판단불가=노랑)  
- 슬라이더(5% 단위)로 보수/진보 비율 지정 → 해당 비율로 100개 카드 재배열
- 추가/보충용 하드코딩 영상 ID 풀 사용(진보/보수)
- 진행 상태 저장 및 UI 동기화(chrome.storage.local)


## 폴더/파일 구조


background.js   # 백그라운드 서비스 워커: 크롤링/분석/메시지 라우팅/상태 저장
content.js      # 컨텐츠 스크립트: DOM 수집/재배열/테두리 스타일 적용
content.css     # 컨텐츠 스타일(성향별 테두리 및 카드 UI)
popup.html      # 팝업 UI 마크업(슬라이더/버튼/상태 표시)
popup.css       # 팝업 스타일
popup.js        # 팝업 동작(분석 시작/재배열/상태 동기화)
manifest.json   # 확장 설정(권한/실행 스크립트/호스트 권한/팝업 등록)
icon.png        # 확장 아이콘


- manifest.json: MV3, `storage` 및 `scripting` 권한, 유튜브/구글 API/OpenAI에 대한 `host_permissions` 지정, `background.service_worker`로 `background.js` 등록, `content_scripts`로 유튜브 도메인에 `content.js`·`content.css` 주입, 액션 팝업으로 `popup.html` 사용.  
- popup.html / popup.css: 슬라이더(0~100), “분석 시작”/“슬라이드 적용” 버튼, 상태 메시지 영역으로 구성.



## 동작 흐름(Architecture)

1) 초기화 & 크롤링
- 페이지 로드 시 `content.js`가 `pageReloaded`를 `background.js`로 보내 초기 상태를 재설정(상태=`reload`).  
- 4초 후 자동으로 스크롤하며 영상 ID를 최대 100개 수집 → `collectedVideoIds` 메시지로 백그라운드에 전송.

2) 데이터 수집 & 분석
- `background.js`가 YouTube Data API로 제목/설명/댓글 가져오기.  
- OpenAI Chat Completions API에 전달하여 각 영상의 정치 성향을 “진보/보수/판단불가” 중 하나로 분류.

3) UI 반영
- `content.js`가 분석 결과를 받아 각 카드에 테두리 색 적용.  
- 팝업에서 사용자가 슬라이더로 설정한 비율에 따라 카드 DOM을 재배열.

4) 상태 저장
- 진행 단계, 분석 결과, 슬라이더 값 등을 `chrome.storage.local`에 저장하여 새로고침 후에도 유지.
