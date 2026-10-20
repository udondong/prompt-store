# CLAUDE.md — 나라가족 프롬프트 저장소

프롬프트 전용 저장·관리 웹앱. 메모장과 달리 프롬프트만 다루며 ① 한 번에 전체 복사 ② `{{변수}}` 템플릿 채우기 ③ 마크다운 미리보기가 핵심.

## ▶ 바로 쓰기 (주소 · 기기별)

- **웹앱 주소**: https://udondong.github.io/prompt-store/  (아무 기기·브라우저에서 열면 바로 사용)
- **모든 기기 공통 기능**: 프롬프트 작성·검색·태그·`{{변수}}` 채우기·마크다운 미리보기·복사·저장(로컬)·내보내기/불러오기(JSON). 기기 안 가림.
- **Safari·Chrome·Edge 공통 자동 동기화**: 사이드바 **"Google Drive 연결"** → 같은 Google 계정으로 승인하면 비공개 앱 데이터 영역의 `prompts.json`을 읽고 쓴다. 연결된 세션에서는 편집 즉시 저장, 창 포커스+15초 폴링으로 다른 기기 변경을 병합한다.
- **보안상 재연결**: 정적 GitHub Pages 앱이라 OAuth 액세스 토큰은 브라우저 메모리에만 두며 저장하지 않는다. 페이지를 새로 열거나 약 1시간 토큰이 만료되면 "Google Drive 다시 연결"을 한 번 누른다. 프롬프트 데이터와 OAuth 토큰은 repo에 올리지 않는다.
- **맥·윈도우 Chrome/Edge 보조 방식**: 기존 **"데스크탑 폴더 연결"**도 유지한다. File System Access API로 사용자가 고른 로컬 Google Drive 동기화 폴더의 `prompts.json`을 직접 읽고 쓴다. Safari에서는 이 보조 버튼을 숨긴다.
- **갤럭시탭·아이패드**: 현재 Safari/Chrome에서 Google 로그인 팝업이 동작하면 같은 Google Drive 자동 동기화를 쓸 수 있다. 구형 iOS 12 Safari 실기기 호환은 미검증이며, 로그인 불가 시 내보내기/불러오기(JSON)를 사용한다. "홈 화면에 추가"하면 앱처럼 실행된다.

## 무엇인가

- **성격**: 단일 HTML 웹앱(self-contained `index.html`). 브라우저만 있으면 맥·윈도우·갤럭시탭·아이패드 네 기기에서 동일하게 작동.
- **스택 예외 사유(§5)**: 루트 §5는 맥+윈도우 데스크탑 앱을 Python+Flask+pywebview로 규정하지만, 이 앱은 순수 텍스트+클립보드만 다루고 **크로스기기 동기화가 주목적**이라 단일 웹앱으로 통일했다. 네이티브 OS 기능이 없어 별도 데스크탑 빌드를 만들 이유가 없다(지니어스 게임 선례와 동일 범주).
- **디자인**: 루트 `_UI프롬프트.md`의 🔒 나라가족 코어(솔리드 카드·radius 20~22·라우터 색·글라스 금지·One UI 풍). 라이트/다크 자동.
- **호환**: 아이패드 Air 1(iOS 12.5 Safari)까지 목표 → 구형 WebKit 금지문법 회피(`?.`·`??`·flex `gap`·`clamp()`·`aspect-ratio` 미사용).

## 기능

- 좌측: 검색창(제목·본문·태그 즉시 필터) + 태그 칩 필터 + 프롬프트 목록 카드(제목·태그·본문 미리보기·빠른 복사).
- 우측: [편집]/[미리보기] 세그먼트.
  - 편집: 제목·태그(쉼표 구분)·본문(마크다운 + `{{변수}}`).
  - 미리보기: 본문의 `{{변수}}`를 자동 감지해 입력칸 생성 → 값 채우면 최종 프롬프트로 치환 → 마크다운 렌더. 상단 **복사** 버튼은 변수 채운 최종 텍스트를 클립보드로.
- 저장: 브라우저 IndexedDB(오프라인 작동). 편집 시 자동 저장.
- 백업/이동: JSON 내보내기·불러오기. 데스크탑에선 그 JSON을 동기화폴더에 두면 구글 드라이브가 동기화.
- **통째로 붙여넣기 → 자동 정리** (핵심): 프롬프트 원문을 본문에 그대로 붙여넣고 "자동 정리" 버튼을 누르면 앱이 규칙 기반으로 파싱해 **본문·변수·선택지·기본값으로 자동 분리·저장**한다(AI·왕복 없음). 인식 규칙: `{변수} = 값` → 변수+기본값 / `○○ 예시`·`○○ 목록` 헤더 뒤 번호·백틱·불릿 목록 → 그 변수의 선택지(★ 등급줄·카테고리 헤더 자동 스킵) / `[프롬프트 본문]` 마커 뒤 → 본문(그 안 `{변수}`는 `{{변수}}`로 자동 변환). 사용자가 준 걸 다시 손으로 나눠 넣지 않게 하는 게 이 앱의 요지(2026-07-05, 사용자 지적 반영). ⚠️ 이 파서는 위 양식에 맞춰진 규칙 기반 — 양식이 다르면 인식이 줄 수 있으니 정리 후 편집 화면에서 눈으로 확인.
- **변수별 선택지 (프롬프트 안, 인라인)**: 편집 화면에서 본문 아래에 감지된 변수 목록이 뜬다. 각 변수를 펼쳐 **선택지를 붙여넣으면**(한 줄에 하나) → 미리보기에서 그 변수가 **드롭다운(골라 쓰기)**, 비우면 자유 입력. "✎ 직접 입력"으로 임시 자유입력 전환. 선택지는 그 프롬프트에 저장(`prompt.options[변수명]=[…]`)되어 프롬프트와 함께 폴더/JSON 동기화됨. **별도 "옵션 세트" 메뉴·이름 맞추기 없음** — 전부 프롬프트 한 화면 안에서(2026-07-05 단순화 재설계). 용도: 경우의 수 많은 프롬프트(이미지 생성의 `{{의상}}`·`{{상황}}` 등)에서 본문·변수는 고정, 선택지만 골라 최종본 생성. (구버전 공유 옵션 세트 JSON 파일은 불러오기 시 프롬프트별 선택지로 자동 변환.)

## 저장·동기화 설계

- **로컬(항상)**: IndexedDB. 오프라인 작동, 모든 기기.
- **주 동기화 = Google Drive API + OAuth**: Safari·Chrome·Edge 공통. Google Identity Services 토큰 모델과 비민감 최소 권한 `drive.appdata`만 요청한다. 파일은 Google Drive UI에 노출되지 않는 앱 전용 `appDataFolder/prompts.json`이며, 같은 OAuth 클라이언트 ID와 Google 계정으로 연결한 브라우저끼리 공유한다.
  - 최초 연결: Google Cloud에서 웹 애플리케이션 OAuth 클라이언트 ID를 만든 뒤 앱 연결 창에 붙여넣는다. 공개 클라이언트 식별자라 비밀키가 아니며 브라우저 IndexedDB `meta`에만 저장한다.
  - 토큰: 액세스 토큰은 메모리에만 보관하고 약 1시간 만료·페이지 재시작 시 사용자 클릭으로 재발급한다. refresh token·client secret은 사용하지 않는다.
  - 반영 시점: 편집/생성/삭제/가져오기 시 debounce push. 창 focus + 15초 폴링으로 상대 기기 변경 pull.
  - API 파일: 없으면 multipart upload로 생성, 있으면 media PATCH로 갱신. 연결 직후 원격과 로컬을 병합하고 합본을 다시 저장한다.
- **보조 동기화 = 데스크탑 폴더 저장(OAuth 없음)**: 기존 File System Access API 방식. Chrome·Edge 데스크탑에서만 보이며, 사용자가 지정한 로컬 폴더의 `prompts.json`을 직접 읽고 쓴다.
  - 병합: `prompts.json`은 `{prompts, deleted}`. 병합은 id별 `updated` 최신 우선 + **tombstone**(`deleted` 맵의 삭제시각이 항목 `updated`보다 크거나 같으면 제거). 삭제도 기기 간 전파. 편집이 삭제보다 나중이면 부활.
  - 재연결: 디렉터리 핸들을 IndexedDB(meta)에 저장 → 재방문 시 권한 granted면 자동, 아니면 "폴더 다시 연결" 클릭 1회.

## Google OAuth 최초 설정

1. Google Cloud Console에서 프로젝트를 만들거나 선택하고 **Google Drive API**를 사용 설정한다.
2. OAuth 동의 화면을 구성한다. 테스트 상태라면 실제 사용할 Google 계정을 테스트 사용자에 추가한다.
3. 사용자 인증 정보 → OAuth 클라이언트 ID → **웹 애플리케이션**을 만든다.
4. 승인된 JavaScript 원본에 `https://udondong.github.io`를 등록한다. 로컬 OAuth 실측이 필요하면 `http://localhost:8788`도 별도 등록한다.
5. 발급된 `…apps.googleusercontent.com` 클라이언트 ID를 앱의 "Google Drive 연결" 최초 입력창에 붙여넣는다.
6. 각 기기에서 같은 Google 계정으로 연결한다. 첫 연결 시 로컬 IndexedDB와 원격 앱 데이터가 병합된다.

클라이언트 ID는 공개 웹앱 식별자이며 client secret이 아니다. client secret·refresh token·액세스 토큰을 이 repo나 `prompts.json`에 기록하지 않는다.

## 배포 (별도 repo · GitHub Pages)

- **공개 repo**: `udondong/prompt-store` (이 폴더가 자체 nested git repo). 루트 munseo-workspace repo는 `.gitignore`로 이 폴더를 제외하고, `나라가족 동기화/클로드동기화/extra-repos.txt`에 등록되어 `sync-work.sh`가 함께 동기화(지니어스 게임과 동일 패턴).
- **라이브 주소**: https://udondong.github.io/prompt-store/ (main 브랜치 루트 서빙, Pages).
- **수정 반영 절차**: 이 폴더에서 `index.html` 수정 → 이 폴더의 git repo에 커밋 → `git push`(origin main) → 1~2분 뒤 Pages 반영. 데이터(프롬프트)는 repo에 올라가지 않음(브라우저 IndexedDB·향후 Drive에만).

## 실행·검증

- 로컬 실행: 프로젝트 폴더에서
  ```
  python3 -m http.server 8788 --directory "나라가족 프롬프트 저장소"
  ```
  후 브라우저에서 `http://localhost:8788` — 또는 `index.html`을 브라우저로 직접 열어도 됨.
- 검증 도구: `node --check`는 인라인 스크립트라 미적용 → Claude_Preview MCP로 렌더·동작 눈검증.

## 현재 진행 상태

- ① 마지막 작업: 2026-07-23
- ② 완료: `index.html` 구현(목록/검색/태그, 편집, `{{변수}}` 템플릿, 마크다운 미리보기, 한번에 복사, IndexedDB 자동저장+헤더 저장상태 표시, "+새 프롬프트" 상단 버튼, JSON 내보내기/불러오기, 라이트·다크, 구형 WebKit 안전문법). **GitHub Pages 배포 완료** → https://udondong.github.io/prompt-store/ (별도 repo `udondong/prompt-store`, extra-repos.txt 등록).
- ② 추가: 예시(시드) 제거 — 빈 상태 시작.
- ② 제거: "붙여넣기로 가져오기 · AI 정리"(챗지피티 왕복) 폐지 — 규칙기반 "자동 정리"와 중복·혼동이라 사용자 요청으로 삭제. 정리 경로는 "새 프롬프트 → 붙여넣기 → 자동 정리" 하나로 통일.
- ② 추가: UI 디자인 산출물 HTML을 `design/프롬프트저장소_UI_산출_20260705.html`에 저장(작업지시서로 뽑은 결과물, 정본 아님·산출물 기록용, AdGuard 주입 스크립트 제거). 앱 아이콘 심볼 확정 = 말풍선 안 굵은 중괄호 `{ }`, 정체성 색 #2E8DEE(현재 favicon과 일치).
- ② 추가: **Safari·Chrome·Edge 공통 Google Drive 동기화** 구현(Google Identity Services + Drive API `appDataFolder`). 최초 OAuth 클라이언트 ID 입력·메모리 전용 액세스 토큰·최초 파일 생성·기존 파일 읽기/병합·수정 저장·focus/15초 폴링·토큰 만료 재연결 상태를 추가. 기존 Chrome/Edge 폴더 동기화는 보조 방식으로 유지하고 Safari에서는 해당 버튼을 숨김.
- ② 검증: 모의 GIS/Drive API를 사용한 Playwright 실제 브라우저 검증에서 ① 최초 `appDataFolder/prompts.json` 생성 ② 편집 후 자동 PATCH ③ 기존 원격 프롬프트+로컬 프롬프트 병합 후 합본 업로드 ④ File System Access 미지원 환경에서 Google 버튼만 표시 ⑤ 콘솔 오류 0을 확인.
- ② 재설계(단순화): 공유 옵션 세트 메뉴 폐지 → **변수별 선택지를 프롬프트 편집 화면 안으로** 이동(`prompt.options`). 별도 메뉴·이름 맞추기 없음. 구버전 optionSets 파일은 불러오기 시 자동 변환.
- ② **통째로 붙여넣기 → 자동 정리** 추가: 원문 붙여넣고 버튼 1번 → 본문·변수·선택지·기본값 자동 분리(`autoOrganize`, 규칙 기반, AI 없음). `{변수}=값`·`○○ 예시 목록`·`[프롬프트 본문]` 인식, `{ }`→`{{ }}` 변환, ★등급·카테고리·번호·백틱 스킵, 설정값→미리보기 기본값 프리필. ⚠️ 한글 `\b` 미작동 함정 있었음(수정: 헤더 키워드 뒤 `\b` 대신 문자셋 룩). 로컬 검증: 사용자 실제 양식(설정값·의상 예시·상황 예시·본문) 대표 샘플로 본문 변환·의상/상황 선택지 추출·기본값·미리보기 드롭다운/프리필 확인, 콘솔 에러 0.
- ② 추가: **PWA(홈 화면 추가)** — manifest.webmanifest + apple-touch-icon.png + icon-192/512.png + favicon.svg(디자인 심볼: 말풍선+`( )`, #2E8DEE) + theme-color/apple 메타. 태블릿 홈 화면 추가 시 앱처럼 실행·iOS 저장 소실 방지. 폴더 미지원 기기(태블릿·사파리)는 folderStat가 "내보내기/불러오기로 이동" 안내. 로컬 검증: 매니페스트 유효(아이콘 2개)·전 에셋 200·콘솔 에러 0. CLAUDE.md 상단에 주소·기기별 사용법 정리.
- ③ 남은 것(사용자 확인): (a) Google Cloud OAuth 클라이언트 생성·Drive API 활성화·승인 원본 등록 (b) 실제 Google 계정으로 macOS Safari↔Chrome/Edge 교차 동기화 (c) iOS 12 Safari·갤럭시탭 Google 로그인 호환 (d) 기존 Chrome/Edge 폴더 연결 실기기 확인 (e) 앱 아이콘 네이티브 에셋과 디자인 산출물 반영 여부.
- ④ 검증한 것: 로컬 Claude_Preview로 목록/검색/태그/선택/편집/`{{변수}}` 치환/마크다운 렌더/저장상태/IndexedDB 영속/라이트·다크 동작 확인. 라이브 URL HTTP 200·앱 서빙 확인.
- ⑤ 검증 못 한 것: 실제 Google OAuth/Drive 계정 호출, macOS·iOS Safari 실기기, 갤럭시탭, 클립보드 복사 실기기, 대량 프롬프트 성능.
- ⑥ 다음 작업자 실행: 위 "Google OAuth 최초 설정"을 완료해 클라이언트 ID를 준비 → 라이브 앱에서 Safari와 Chrome으로 같은 계정 연결 → 프롬프트 생성/수정/삭제가 양방향 반영되는지 확인.
