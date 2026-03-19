# Newndy Board — 개발 환경 정보

## 런타임 & 도구 버전

| 도구 | 버전 | 비고 |
|---|---|---|
| Node.js | v20.17.0 | LTS (nvm으로 관리) |
| npm | v11.12.0 | Node.js 내장 |
| Turborepo | v2.8.17 | 모노레포 빌드 도구 |
| Next.js | v16.1.7 | App Router + Turbopack |
| TypeScript | strict: true | |

## 모노레포 구조

- `apps/web` — Next.js 프론트엔드
- `apps/api` — NestJS 백엔드 (추후 생성)
- `packages/types` — 공유 TypeScript 타입

## 명령어

| 명령어 | 설명 |
|---|---|
| `npm run dev` | 전체 개발 서버 실행 |
| `npm run build` | 전체 프로덕션 빌드 |
| `npm run lint` | 전체 코드 검사 |
| `npm run type-check` | 전체 타입 검사 |
| `npm run dev -w apps/web` | web만 개발 서버 실행 |

## 작업 규칙

- Bash에서 Node.js 사용 시: `source ~/.nvm/nvm.sh && nvm use &&` 접두사 필요
- 패키지 설치는 루트에서 `npm install`로 통합 실행
- 커밋 메시지 형식: `[NB-번호] 작업 내용`