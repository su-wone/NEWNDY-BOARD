# Epic 1 — 기반 인프라 & 인증

| 항목 | 내용 |
|---|---|
| 기간 | Sprint 1–2 · 2025.03.17 – 04.13 |
| 목표 | 전체 개발의 기반이 되는 인프라, 인증, 워크스페이스 구축 |
| 기술 스택 | Next.js 16 · NestJS · Prisma · PostgreSQL · Google OAuth |

---

## Story 목록

| Story | 제목 | Sprint | 상태 |
|---|---|---|---|
| [S1-1](./s1-1.md) | Next.js + Tailwind 프로젝트 초기 셋업, CI/CD 파이프라인 구성 | Sprint 1 | 🔲 대기 |
| [S1-2](./s1-2.md) | Google OAuth 로그인 / 로그아웃 구현 | Sprint 1 | 🔲 대기 |
| [S1-3](./s1-3.md) | 워크스페이스 생성 및 팀원 초대 | Sprint 2 | 🔲 대기 |
| [S1-4](./s1-4.md) | 역할 정의 및 권한 분리 | Sprint 2 | 🔲 대기 |
| [S1-5](./s1-5.md) | 사이드바 6개 페이지 라우팅 | Sprint 2 | 🔲 대기 |

---

## 티켓 전체 목록

| 티켓 ID | 제목 | Story | 우선순위 | 분야 | Sprint |
|---|---|---|---|---|---|
| NB-01 | pnpm workspace + Turborepo 모노레포 초기화 | S1-1 | 최상 | Infra | S1 |
| NB-02 | Next.js 16 App Router 프로젝트 생성 | S1-1 | 최상 | Frontend | S1 |
| NB-03 | Tailwind CSS + shadcn/ui 초기 설치 및 디자인 토큰 정의 | S1-1 | 높음 | Frontend | S1 |
| NB-04 | ESLint + Prettier + Husky 코드 품질 도구 설정 | S1-1 | 높음 | Infra | S1 |
| NB-05 | GitHub Actions CI 파이프라인 구성 | S1-1 | 최상 | Infra | S1 |
| NB-06 | Vercel 자동 배포 연동 및 Preview URL PR 첨부 | S1-1 | 높음 | Infra | S1 |
| NB-07 | NestJS passport-google-oauth20 설정 | S1-2 | 최상 | Backend | S1 |
| NB-08 | JWT 발급 및 세션 관리 (Access + Refresh Token) | S1-2 | 최상 | Backend | S1 |
| NB-09 | Next.js 로그인 페이지 UI 및 Google 로그인 버튼 | S1-2 | 최상 | Frontend | S1 |
| NB-10 | 로그인 여부에 따른 미들웨어 리다이렉트 처리 | S1-2 | 높음 | Frontend | S1 |
| NB-11 | 워크스페이스 생성 API 및 UI | S1-3 | 최상 | FullStack | S2 |
| NB-12 | 이메일 초대 링크 발송 (Nodemailer) | S1-3 | 높음 | Backend | S2 |
| NB-13 | 초대 수락 플로우 및 워크스페이스 합류 처리 | S1-3 | 높음 | FullStack | S2 |
| NB-14 | Admin / Member 역할 DB 모델 및 Guard 구현 | S1-4 | 최상 | Backend | S2 |
| NB-15 | 역할 기반 UI 분기 처리 | S1-4 | 높음 | Frontend | S2 |
| NB-16 | 사이드바 레이아웃 컴포넌트 구현 | S1-5 | 높음 | Frontend | S2 |
| NB-17 | 6개 페이지 라우팅 플레이스홀더 구성 | S1-5 | 높음 | Frontend | S2 |
