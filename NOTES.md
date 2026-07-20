# 데일리 챌린지 — 프로젝트 노트 (복구용)

> Cowork 프로젝트는 로컬 전용(클라우드 동기화 없음)이라, 컴퓨터가 바뀌어도 이 파일만 있으면 똑같이 이어갈 수 있게 핵심 정보를 담아둔 문서입니다. 이 레포(GitHub)가 곧 클라우드 백업입니다.

## 한 줄 소개
친구 3명(구지영 · 김시연 · 정태찬)이 쓰는 "데일리 챌린지" 벌금/휴가 트래커. 단일 파일 정적 웹앱(`index.html`).

## 주소 / 스택
- **라이브:** https://daily-challenge-jtc4902-techs-projects.vercel.app  (로그인 없이 링크만으로 접속 — 누구나 보기/기록 가능)
- **GitHub:** github.com/jtc4902-tech/daily-challenge  (`main` 브랜치, `index.html` 한 파일이 전부)
- **호스팅:** Vercel — 프리셋 "Other"(빌드 없는 정적). `main`에 푸시하면 ~30초 후 자동 재배포. Deployment Protection(Vercel Authentication)은 꺼서 공개 상태.
- **백엔드:** Supabase — 프로젝트 ref `pkmgspdlnveknkxxenqm`
  - URL: `https://pkmgspdlnveknkxxenqm.supabase.co`
  - 공개 키(anon/publishable, 브라우저 노출 안전): `sb_publishable_7iR6bfcN6eNwdYRfnbnM7g_5xtdXd3p`
  - 테이블: `members`, `records` / RLS는 로그인 없는 링크 공개형(anon에 select·insert·**update**·delete 허용 — update 정책은 2026-07-20 추가. 그 전엔 update 정책이 없어서 '수정'이 조용히 무시되는 버그 있었음)

## 앱 동작 규칙 (index.html 안)
- **미션(2026-07 개정):** 챌린지 목록(공부 20분·독서·일기·얼굴팩·콘텐츠 후기·기타) 중 **1개만 하면 성공**. ※구규칙 "공부 20분+챌린지 1개 둘 다"는 폐지.
- **벌금:** 실패 1회당 3,300원 (코드 상단 `PENALTY`).
- **휴가:** 매월 1일 +1 자동 적립(가입월/매년 1월은 0). **남은 휴가는 매년 1월 1일에 0으로 초기화(이월 없음)** — 몰아쓰기 방지. 계획된 사정(여행·가족행사·예비군)이 기본 용도이나, **2026-07 개정으로 실패를 휴가로 차감하는 것도 허용**(실패 기록을 휴가로 수정하면 벌금 면제). 남은 휴가 0이면 "사용 불가" 표시 + 추가 차단. 적립·잔여는 올해 기록만으로 계산.
- **멤버 정렬:** 실패 많은 순으로 1·2·3순위(맨 앞=실패 최다). 모두 같으면 이름 ㄱㄴㄷ 순. 카드 색은 사람마다 고정.
- **기록 타입:** 실패 / 휴가 / 모임 / 정산. 캘린더에서 기록 있는 날짜를 누르면 그날 기록을 수정·삭제 가능.

## 📸 추억 탭 (2026-07-20 추가 — 사진/영상 아카이브)
상단 탭 `[🏆 챌린지][📸 추억]`으로 전환. **챌린지 로직(달력·벌금·휴가)은 1도 안 건드림** — 추억은 완전히 별개 저장소라 장애 격리됨(추억 서버가 죽어도 벌금 기록은 Supabase라 멀쩡).
- **프론트:** 같은 `index.html`. `MEM_API = https://memories.apexlog.kr`로 REST 호출(fetch). 월별 앨범 그리드, 업로더 색점(멤버 sort_order 색), 영상 썸네일+길이, 뷰어(확대·영상재생·삭제), 다중 업로드(파일 촬영일 자동).
- **백엔드(우리 오라클 서버):** `~/challenge-memory/server.py` — 파이썬 표준라이브러리 단일 파일(무의존, 썸네일/길이만 ffmpeg·ffprobe). systemd user 서비스 `challenge-memory.service`(포트 8789, linger로 재부팅 자동시작).
  - 저장: **외장디스크** `/mnt/photos/challenge-memories/` (media/ 원본, thumbs/ 썸네일, memories.db=SQLite 메타). ※새 디스크 아님 — 기존 Immich 외장 안 폴더. 86GB 여유.
  - 노출: **Cloudflare Tunnel**(`~/.cloudflared/config.yml`에 memories.apexlog.kr → localhost:8789 ingress 추가). DNS는 `cloudflared tunnel route dns`로 등록. HTTPS 자동.
  - 엔드포인트: `GET /api/stats·/api/list·/thumb/<id>·/media/<id>(Range)`, `POST /api/upload`(multipart), `DELETE /api/item/<id>`. CORS `*`.
  - ⚠️**CDN 캐시 함정(2026-07-20 겪음):** Cloudflare가 빈 목록 응답을 4시간 캐싱해 새 항목이 안 보이던 버그 → server의 j()에 `Cache-Control: no-store` + 프론트 fetch에 `?_=Date.now()` 캐시버스터로 해결. list/stats는 절대 캐시 금지(thumb/media는 고유 id라 캐시 OK).
  - **업로드 암호 없음**(링크 공유=접근, 챌린지 앱과 동일 보안 수준). 잠그려면 서비스에 `MEM_UPLOAD_KEY` env 추가 → 프론트에 X-Upload-Key 헤더 로직 부활 필요.
- **🔄 Immich 자동 동기화(2026-07-20):** `~/challenge-memory/immich_sync.py` — 규칙 **"김시연 OR 구지영이 나온" 모든 사진·영상**을 Immich 얼굴인식에서 뽑아 추억 탭에 자동 추가(정태찬 단독 셀카 570장은 제외). 크론 `0 5 * * *`(매일 새벽 5시, 로그 `sync.log`). 중복은 `immich_id` 컬럼으로 스킵 → 수동 업로드(immich_id NULL)와 공존. Immich API는 `~/immich/.env`의 `IMMICH_API_KEY`, `POST /api/search/metadata`(personIds=AND). ⚠️촬영일: 카톡 사진은 EXIF가 저장일로 덮여있어, **파일명 유닉스 타임스탬프를 우선**(`best_date()`)해 실제 날짜 복원. 첫 동기화 결과 121개(사진113·영상8, 2023.03~2026.02).

## 수정 → 배포 절차
1. `index.html` 수정
2. GitHub `main`에 커밋 → Vercel이 자동 재배포
3. 라이브 URL에서 확인

## 새 컴퓨터 / 초기화 후 복구하는 법
1. Claude Desktop 설치 후 로그인
2. Cowork에서 새 프로젝트 생성 → 이 GitHub 레포(github.com/jtc4902-tech/daily-challenge)를 폴더로 연결하거나 URL로 추가
3. "이 NOTES.md 읽고 이어서 작업해줘"라고 말하면 끝

> 가입 / 로그인 / 권한 승인(GitHub·Vercel·Supabase)은 사용자가 직접 합니다.

_최종 업데이트: 2026-07-20 (①UPDATE RLS 정책 추가 ②규칙 개정: 미션 1개만·실패의 휴가 차감 허용 ③📸 추억 탭 추가 — memories.apexlog.kr / 오라클 외장 저장)_
