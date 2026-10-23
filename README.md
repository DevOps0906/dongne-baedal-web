# dongne-baedal-web

동네배달의 웹 레포입니다. 한 Next.js 앱 안에 세 영역이 있습니다.

| 경로 | 대상 | 화면 |
|---|---|---|
| `/` | 고객 웹/PWA | 홈, 가게, 장바구니, 주문, 주문 현황 지도 |
| `/partner` | 계약업체 | 주문 칸반, 상품 등록, 가게 관리, 매출·정산 |
| `/admin` | 관리자(본사) | 관제, 입점 심사, 주문·결제, 라이더 관제, 정산 관리 |

기획·디자인·토큰·DB 스키마는 `spec/` 서브모듈(`dongne-baedal-spec`)에 있습니다.
**작업 전에 `spec/docs/04-dev-instructions.md`(개발지시서)를 먼저 읽습니다.**

> 현재 상태: 개발 전. 코드는 개발지시서 `WEB-001`부터 시작합니다.

---

## 개발 방법

### 1. 기술 스택

| 영역 | 선택 |
|---|---|
| 프레임워크 | Next.js 15 (App Router), React 19, TypeScript |
| 스타일 | Tailwind CSS v4 + `spec/tokens/tokens.json`에서 생성한 CSS 변수, shadcn/ui |
| 데이터 | Supabase (`@supabase/ssr`), TanStack Query |
| 폼·검증 | React Hook Form, Zod |
| 지도 | 네이버 지도 JS API (주문 현황, 배달 반경, 라이더 관제) |
| 차트 | Recharts (매출·정산) |
| 배포 | Vercel |

### 2. 받기와 실행

```bash
git clone --recurse-submodules https://github.com/DevOps0906/dongne-baedal-web.git
cd dongne-baedal-web
# 서브모듈 없이 받았다면
git submodule update --init

# WEB-001 이후
npm install
npm run tokens        # spec/tokens/tokens.json → src/styles/tokens.css
cp .env.example .env.local
npm run dev           # http://localhost:3000
```

`.env.local`

```
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
NEXT_PUBLIC_NAVER_MAP_CLIENT_ID=
SUPABASE_SERVICE_ROLE_KEY=     # 서버 라우트 전용, 클라이언트에서 쓰지 않음
```

로컬 DB는 spec 레포에서 `supabase start` 후 나온 값을 넣습니다.

### 3. spec 갱신

```bash
git submodule update --remote spec
npm run tokens
supabase gen types typescript --local > src/types/database.ts
git commit -am "chore: spec 갱신"
```

`src/styles/tokens.css`와 `src/types/database.ts`는 생성 파일이라 직접 고치지 않습니다.

### 4. 폴더 구조

```
src/app/(customer)/   고객
src/app/partner/      계약업체
src/app/admin/        관리자
src/components/{ui,customer,partner,admin,shared}/
src/lib/{supabase,queries,validators}/
src/styles/tokens.css   (생성)
src/types/database.ts   (생성)
spec/                   서브모듈
```

### 5. 반응형

| 구간 | 고객 | /partner · /admin |
|---|---|---|
| ~767 | 1열, 하단 5탭 | 하단 탭, 카드 리스트 |
| 768~1199 | 2열, 하단 탭 | 아이콘 레일 + 칸반 2열 |
| 1200~ | 3열, 상단 헤더 + 좌측 카테고리 | 사이드바 + KPI 4열 + 칸반 4열 |

글자 크기는 토큰의 `typography.responsive`를 따릅니다.

### 6. 작업 규칙

- 티켓 하나에 브랜치 하나: `feat/WEB-032-menu-builder`
- 커밋: Conventional Commits, 제목에 티켓 번호
- PR 전에 `npm run lint && npm run typecheck && npm run build`
- PR에 반응형 3구간 캡처 첨부
- 금액 계산은 서버(Edge Function, 서버 라우트)에서만 합니다

### 7. 웹 티켓 요약

| 단계 | 티켓 |
|---|---|
| P0 | WEB-001 생성·토큰, WEB-002 Supabase 연결, WEB-003 레이아웃 3종 |
| P1 | WEB-010~015 고객 탐색 |
| P2 | WEB-020~022 장바구니·결제·주문내역 |
| P3 | WEB-030~034 계약업체 대시보드 |
| P4 | WEB-043 주문 현황 지도 |
| P5 | WEB-050~054 정산·관리자 |

자세한 완료 기준은 `spec/docs/04-dev-instructions.md`에 있습니다.
