# CLAUDE.md

이 파일은 Claude Code (claude.ai/code)가 이 저장소에서 작업할 때 참고하는 가이드입니다.

## 명령어

```bash
npm run dev      # 개발 서버 시작 (localhost:3000)
npm run build    # 프로덕션 빌드
npm run lint     # ESLint 검사
```

## 환경 변수

`.env.local`에 필요한 항목:
```
NEXT_PUBLIC_SUPABASE_URL=...
NEXT_PUBLIC_SUPABASE_PUBLISHABLE_KEY=...
```

두 값 모두 Supabase 프로젝트의 API 설정에서 확인할 수 있습니다. publishable key는 기존 anon key와 동일합니다.

## 아키텍처

**Next.js App Router** 기반에 쿠키 세션을 사용하는 Supabase 인증(`@supabase/ssr`)을 적용한 구조입니다.

### Supabase 클라이언트 패턴

렌더링 컨텍스트에 따라 세 가지 클라이언트가 분리되어 있으며, 항상 올바른 파일에서 import해야 합니다:

- `lib/supabase/client.ts` — 브라우저/클라이언트 컴포넌트 (`createBrowserClient`)
- `lib/supabase/server.ts` — 서버 컴포넌트 및 라우트 핸들러 (`createServerClient`, Next.js cookies 사용)
- `lib/supabase/proxy.ts` — 미들웨어 세션 갱신 (`updateSession`)

**중요:** 서버 클라이언트를 전역 변수에 저장하지 마세요. Next.js Fluid compute 호환성을 위해 항상 함수 스코프 내에서 인스턴스를 생성해야 합니다.

### 미들웨어 / 인증 가드

루트의 `proxy.ts`가 미들웨어 역할을 합니다. 정적 파일을 제외한 모든 요청에서 `updateSession`을 실행해 Supabase 세션을 갱신합니다. `/` 및 `/auth/*` 이외의 경로에 비인증 사용자가 접근하면 `/auth/login`으로 리다이렉트됩니다.

### 라우트 구조

- `/` — 공개 홈 페이지
- `/auth/login`, `/auth/sign-up`, `/auth/forgot-password`, `/auth/update-password` — 인증 플로우
- `/auth/confirm` — 이메일 확인 라우트 핸들러
- `/protected` — 인증 필요 예시 페이지 (서버 컴포넌트, 세션 없으면 리다이렉트)
- `/instruments` — Supabase `instruments` 테이블 데이터 조회 예시 페이지

### UI 스택

- **shadcn/ui** 컴포넌트 (`components/ui/`, `components.json`으로 설정)
- **Tailwind CSS** 스타일링
- **next-themes** — 루트 레이아웃의 `ThemeProvider`로 다크/라이트 모드 지원
- **Radix UI** — shadcn 컴포넌트의 기반 프리미티브

### 인증 플로우

서버 컴포넌트와 미들웨어에서 세션 확인 시 `getUser()` 대신 `supabase.auth.getClaims()`를 사용합니다. 추가 네트워크 요청 없이 사용자 클레임을 읽는 이 코드베이스의 권장 패턴입니다.
