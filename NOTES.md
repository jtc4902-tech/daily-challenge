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
  - 테이블: `members`, `records` / RLS는 로그인 없는 링크 공개형(anon에 select·insert·delete 허용)

## 앱 동작 규칙 (index.html 안)
- **벌금:** 실패 1회당 3,300원 (코드 상단 `PENALTY`).
- **휴가:** 시작월(2026-06)은 0개, 그 다음 달부터 매월 1일 +1 자동 적립. 안 쓰면 계속 누적. 남은 휴가 0이면 카드에 "사용 불가" 표시 + 휴가 추가 차단.
- **멤버 정렬:** 실패 많은 순으로 1·2·3순위(맨 앞=실패 최다). 모두 같으면 이름 ㄱㄴㄷ 순. 카드 색은 사람마다 고정.
- **기록 타입:** 실패 / 휴가 / 모임 / 정산. 캘린더에서 기록 있는 날짜를 누르면 그날 기록을 수정·삭제 가능.

## 수정 → 배포 절차
1. `index.html` 수정
2. GitHub `main`에 커밋 → Vercel이 자동 재배포
3. 라이브 URL에서 확인

## 새 컴퓨터 / 초기화 후 복구하는 법
1. Claude Desktop 설치 후 로그인
2. Cowork에서 새 프로젝트 생성 → 이 GitHub 레포(github.com/jtc4902-tech/daily-challenge)를 폴더로 연결하거나 URL로 추가
3. "이 NOTES.md 읽고 이어서 작업해줘"라고 말하면 끝

> 가입 / 로그인 / 권한 승인(GitHub·Vercel·Supabase)은 사용자가 직접 합니다.

_최종 업데이트: 2026-06-15_
