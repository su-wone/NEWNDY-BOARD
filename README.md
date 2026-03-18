# Newndy Board

> AI가 기획을 돕고, MCP가 개발자와 보드를 연결하는 — 차세대 경량 프로젝트 관리 도구

---

## 목차

1. [제품 비전 & 목표](#1-제품-비전--목표)
2. [기술 스택 & 아키텍처](#2-기술-스택--아키텍처)
3. [페이지 구성 & 사이드바 구조](#3-페이지-구성--사이드바-구조)
4. [전체 Epic-Story-티켓 구조](#4-전체-epic-story-티켓-구조)
5. [스프린트 타임라인](#5-스프린트-타임라인)

---

## 1. 제품 비전 & 목표

### 왜 만드는가

Jira는 강력하지만 소규모 개발팀이 실제로 쓰기에는 아래와 같은 한계가 있다.

| 문제 | 내용 |
|---|---|
| 높은 진입 장벽 | 초기 설정에 수 시간 소요, 복잡한 권한 구조로 온보딩 부담 |
| AI 미통합 | Task 생성이 수동 작업에 의존, 기획 단계 AI 지원 없음 |
| 개발 환경 단절 | 보드와 IDE가 분리되어 개발자가 컨텍스트를 전환해야 함 |
| 과도한 과금 | 소규모 팀도 엔터프라이즈 수준의 비용 부담 |

### 핵심 차별점

```
Jira          →  Newndy Board
────────────────────────────────────────────────
수동 Task 생성  →  AI 프롬프트 한 줄로 Epic/Story/Task 자동 생성
브라우저 전용   →  MCP로 IDE(Claude Code)에서 Task 직접 조회/다운로드
스페이스 관리   →  GitHub 레포 연동 + 커밋-티켓 자동 연결
복잡한 설정    →  Google 로그인 30초, 5분 안에 첫 스프린트 시작
```

### 핵심 설계 원칙

- **AI-First 기획** — 팀장이 자연어 프롬프트로 Epic → Story → Task 전체 구조를 생성. 우선순위·분야·추천 담당자 자동 부여
- **개발자 워크플로우 통합** — Claude Code에서 `/newndy-board-*` 명령어로 Task 조회·다운로드. 브라우저 없이 작업 가능
- **경량 & 빠른 온보딩** — Google 계정 로그인, 이메일 초대 링크로 팀 구성
- **역할 기반 협업** — 팀장(Admin): AI 생성·배치·스프린트 관리 / 팀원(Member): 할당된 Task 집중

---

## 2. 기술 스택 & 아키텍처

### 기술 스택

| 영역 | 기술 |
|---|---|
| Frontend | Next.js 16 (App Router) · Tailwind CSS · shadcn/ui · TypeScript |
| Backend | NestJS · Prisma ORM · TypeScript |
| Database | PostgreSQL |
| 인증 | Google OAuth 2.0 · JWT (Access + Refresh Token) |
| AI | Anthropic Claude API (claude-sonnet-4) |
| MCP | @anthropic-ai/mcp SDK (독립 Node.js 서비스) |
| Git 연동 | GitHub REST API · GitHub Webhook |
| 배포 | Vercel (Frontend) · Railway (Backend) |
| CI/CD | GitHub Actions |
| 패키지 | pnpm workspace · Turborepo |

### 모노레포 구조

```
newndy-board/
├── apps/
│   ├── web/              ← Next.js 16 (App Router) + Tailwind
│   └── api/              ← NestJS + Prisma
├── packages/
│   └── types/            ← 공유 TypeScript 타입 정의
├── .github/
│   └── workflows/        ← CI/CD 파이프라인
├── docs/
│   └── epic1/            ← Story별 기획 문서
├── pnpm-workspace.yaml
├── turbo.json
└── README.md             ← 이 파일
```

### Prisma 데이터 모델 (주요)

```
User
 └── WorkspaceMember (role: ADMIN | MEMBER)
      └── Workspace
           ├── Sprint
           │    └── Task
           ├── Backlog → Task
           ├── Epic → Story → Task
           ├── ApiToken          ← MCP 인증용
           └── GitRepo
                └── CommitLog ↔ Task  ← [NB-숫자] 파싱 연결
```

### CI/CD 흐름

```
PR 생성
 └── GitHub Actions 자동 트리거
      ├── 1. lint        (ESLint + Prettier)     → 실패 시 머지 블록
      ├── 2. type-check  (tsc --noEmit)           → 실패 시 머지 블록
      └── 3. build       (Next.js 프로덕션 빌드)  → 실패 시 머지 블록

main 머지
 ├── Vercel → 프론트엔드 자동 배포
 └── Railway → 백엔드 자동 배포
```

---

## 3. 페이지 구성 & 사이드바 구조

### 사이드바 네비게이션

```
┌─────────────────────┐
│  Newndy Board  [로고] │
├─────────────────────┤
│  대시보드 (요약)      │  /dashboard  ← AI Task 생성 포함
│  백로그              │  /backlog
│  활성 스프린트        │  /sprint
│  Git Project         │  /git        ← GitHub 연동 (구 스페이스)
│  팀                  │  /team
│  설정                │  /settings
├─────────────────────┤
│  [유저 프로필]        │
└─────────────────────┘
```

### 페이지별 핵심 기능

| 페이지 | 경로 | 핵심 기능 | 구현 Epic |
|---|---|---|---|
| 대시보드 | `/dashboard` | 스프린트 진행률 · 내 Task · AI Task 생성 채팅 | Epic 2, 3 |
| 백로그 | `/backlog` | Task 목록 · Epic/Story 트리 · 스프린트 이동 | Epic 2 |
| 활성 스프린트 | `/sprint` | 칸반 보드 (Todo/In Progress/Done) · 드래그 | Epic 2 |
| Git Project | `/git` | 레포 연결 · 브랜치 현황 · PR 상태 · 커밋 타임라인 | Epic 6 |
| 팀 | `/team` | 멤버 목록 · 역할 관리 · 초대 | Epic 5 |
| 설정 | `/settings` | 워크스페이스 설정 · MCP 토큰 발급 | Epic 4, 5 |

### AI Task 생성 플로우 (대시보드)

```
팀장 프롬프트 입력
 └── Claude API (BMAD 시스템 프롬프트)
      └── JSON 응답: Epic → Story → Task 계층
           └── 렌더링: 트리 UI (우선순위·분야·추천 담당자 표시)
                └── 팀장 수정 (인라인 에디터)
                     └── 배치 선택
                          ├── 백로그로 보내기
                          └── 활성 스프린트로 보내기
```

### MCP 개발자 플로우

```
Claude Code에서 명령어 입력
 ├── /newndy-board-my-task-list          → 내 Task 목록 조회
 └── /newndy-board-my-task-download [id] → Task Markdown 다운로드
      └── 작업 완료 후
           ├── (수동) 웹 활성 스프린트 페이지에서 Done으로 이동
           └── (예정) /newndy-board-done [id] → 자동 상태 업데이트
```

### GitHub 커밋-티켓 연결 플로우

```
git commit -m "[NB-42] MCP 서버 초기화"
 └── git push
      └── GitHub Webhook → NestJS /webhook/github
           └── 커밋 메시지에서 [NB-숫자] 파싱
                └── Task NB-42에 CommitLog 연결
                     └── Task 상세 페이지에 커밋 로그 표시
```

---

## 4. 전체 Epic-Story-티켓 구조

### Epic 1 — 기반 인프라 & 인증
> Sprint 1–2 · 2025.03.17 – 04.13 · [상세 문서](./docs/epic1/README.md)

| Story | 제목 | 티켓 | Sprint |
|---|---|---|---|
| S1-1 | Next.js + Tailwind 셋업, CI/CD | NB-01 ~ NB-06 | S1 |
| S1-2 | Google OAuth 로그인/로그아웃 | NB-07 ~ NB-10 | S1 |
| S1-3 | 워크스페이스 생성 및 팀원 초대 | NB-11 ~ NB-13 | S2 |
| S1-4 | 역할 정의 및 권한 분리 | NB-14 ~ NB-15 | S2 |
| S1-5 | 사이드바 6개 페이지 라우팅 | NB-16 ~ NB-17 | S2 |

### Epic 2 — 핵심 보드 기능
> Sprint 3–4 · 2025.04.14 – 05.11

| Story | 제목 | 티켓 | Sprint |
|---|---|---|---|
| S2-1 | Task CRUD | NB-18 ~ NB-20 | S3 |
| S2-2 | 백로그 페이지 | NB-21 ~ NB-22 | S3 |
| S2-3 | Epic/Story 계층 구조 관리 | NB-23 ~ NB-24 | S3 |
| S2-4 | 스프린트 생성 및 관리 | NB-25 ~ NB-26 | S3 |
| S2-5 | 활성 스프린트 칸반 보드 | NB-27 ~ NB-29 | S4 |
| S2-6 | 요약(대시보드) 위젯 | NB-30 ~ NB-31 | S4 |

### Epic 3 — AI Task 생성 엔진
> Sprint 5–6 · 2025.05.12 – 06.08

| Story | 제목 | 티켓 | Sprint |
|---|---|---|---|
| S3-1 | 대시보드 AI 채팅 UI | NB-32 ~ NB-33 | S5 |
| S3-2 | Claude API 연동 + BMAD 시스템 프롬프트 | NB-34 ~ NB-36 | S5 |
| S3-3 | AI 생성 결과 렌더링 및 편집 | NB-37 ~ NB-39 | S5–S6 |
| S3-4 | Task 배치 — 백로그 / 활성 스프린트 | NB-40 ~ NB-41 | S6 |

### Epic 4 — MCP 플러그인 연동
> Sprint 5–7 · 2025.05.12 – 06.22

| Story | 제목 | 티켓 | Sprint |
|---|---|---|---|
| S4-1 | MCP 서버 기본 스펙 설계 | NB-42 ~ NB-43 | S5 |
| S4-2 | Task 조회 및 다운로드 CLI 명령어 | NB-44 ~ NB-46 | S7 |
| S4-3 | API 토큰 발급 및 인증 | NB-47 ~ NB-48 | S5, S7 |

### Epic 5 — 팀 워크스페이스
> Sprint 6–7 · 2025.05.26 – 06.22

| Story | 제목 | 티켓 | Sprint |
|---|---|---|---|
| S5-1 | 팀 멤버 관리 페이지 | NB-49 ~ NB-50 | S6 |
| S5-2 | 설정 페이지 | NB-51 ~ NB-52 | S7 |
| S5-3 | 담당자 필터 / 본인 Task 모아보기 | NB-53 | S7 |

### Epic 6 — Git Project 연동 (신규)
> Sprint 6–8 · 2025.05.26 – 06.30

| Story | 제목 | 티켓 | Sprint |
|---|---|---|---|
| S6-1 | GitHub 레포 연결 | NB-54 ~ NB-56 | S6 |
| S6-2 | Webhook — 커밋 로그 수신 및 티켓 연결 | NB-57 ~ NB-60 | S7 |
| S6-3 | Git Project 페이지 뷰 | NB-61 ~ NB-63 | S8 |

### Epic 7 — 고도화 & 자동화
> Sprint 8 · 2025.06.23 – 06.30

| Story | 제목 | 티켓 | Sprint |
|---|---|---|---|
| S7-1 | QA 및 안정화 | NB-64 ~ NB-65 | S8 |
| S7-2 | 알림 시스템 | NB-66 | S8 |
| S7-3 | 베타 배포 | NB-67 ~ NB-68 | S8 |
| S7-4 | 자동 업데이트 PoC | NB-69 | S8 |

---

## 5. 스프린트 타임라인

```
Sprint 1  3/17–3/30  ████  Epic1: 프로젝트 셋업 · Google OAuth
Sprint 2  3/31–4/13  ████  Epic1: 워크스페이스 · 초대 · 권한 · 사이드바
Sprint 3  4/14–4/27  ████  Epic2: Task CRUD · 백로그 · Epic/Story · 스프린트 생성
Sprint 4  4/28–5/11  ████  Epic2: 칸반 보드 · 대시보드 위젯
Sprint 5  5/12–5/25  ████  Epic3: AI Task 생성 UI+API  /  Epic4: MCP 서버 설계
Sprint 6  5/26–6/08  ████  Epic3: Task 배치  /  Epic5: 팀 관리  /  Epic6: GitHub 연결
Sprint 7  6/09–6/22  ████  Epic4: CLI 명령어  /  Epic5: 설정  /  Epic6: Webhook+커밋 연결
Sprint 8  6/23–6/30  ████  Epic6: Git UI 완성  /  Epic7: QA · 알림 · 베타 배포
```

| Sprint | 기간 | 주요 산출물 |
|---|---|---|
| Sprint 1 | 3/17 – 3/30 | 모노레포 구조, CI/CD, Google 로그인 |
| Sprint 2 | 3/31 – 4/13 | 워크스페이스, 초대, 역할, 사이드바 |
| Sprint 3 | 4/14 – 4/27 | 백로그, Epic/Story 트리, 스프린트 CRUD |
| Sprint 4 | 4/28 – 5/11 | 칸반 보드, 드래그 앤 드롭, 대시보드 위젯 |
| Sprint 5 | 5/12 – 5/25 | AI Task 생성 (Claude API + BMAD), MCP 서버 초기화 |
| Sprint 6 | 5/26 – 6/08 | AI Task 배치, 팀 관리, GitHub 레포 연결 |
| Sprint 7 | 6/09 – 6/22 | MCP CLI 명령어, 설정 페이지, 커밋-티켓 연결 |
| Sprint 8 | 6/23 – 6/30 | Git Project UI, QA, 알림, 베타 배포 |

---

## 문서 구조

```
newndy-board/
├── README.md              ← 이 파일 (전체 기획 구조)
└── docs/
    └── epic1/
        ├── README.md      ← Epic 1 Story + 티켓 인덱스
        ├── s1-1.md        ← Next.js + Tailwind 셋업, CI/CD
        ├── s1-2.md        ← Google OAuth 로그인
        ├── s1-3.md        ← 워크스페이스 생성 & 팀원 초대
        ├── s1-4.md        ← 역할 정의 & 권한 분리
        └── s1-5.md        ← 사이드바 6개 페이지 라우팅
```

> Epic 2~7 문서는 해당 Sprint 시작 전 `docs/epic2/`, `docs/epic3/` ... 형태로 추가 예정
