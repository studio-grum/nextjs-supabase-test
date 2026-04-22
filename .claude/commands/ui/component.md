shadcn/ui 패턴과 이 프로젝트의 디자인 시스템에 맞는 UI 컴포넌트를 생성해주세요.

## 요청 내용

$ARGUMENTS

## 프로젝트 컴포넌트 규칙

### 파일 위치
- `components/ui/` — Radix 기반 프리미티브 (shadcn 패턴, `React.forwardRef` 사용)
- `components/` — 비즈니스 로직이 포함된 복합 컴포넌트

### 필수 패턴
1. **스타일링**: `cn()` (`@/lib/utils`) + Tailwind CSS 사용. 임의 값 대신 디자인 토큰 우선 (`bg-card`, `text-muted-foreground`, `border-input` 등)
2. **다크모드**: 모든 색상에 `dark:` variant 적용. `next-themes`와 호환되도록 CSS 변수 기반 토큰 사용
3. **Variant 설계**: 여러 스타일이 필요하면 `cva` (`class-variance-authority`) 패턴 사용 (`button.tsx` 참고)
4. **접근성**: Radix UI primitive 위에 구축 시 aria 속성 및 keyboard navigation 자동 처리됨. 직접 구현 시 `role`, `aria-*` 명시
5. **클라이언트 컴포넌트**: `useState`, `useRouter` 등 훅 사용 시 파일 최상단에 `"use client"` 선언
6. **Props 타입**: `React.ComponentPropsWithoutRef<"div">` 또는 `React.HTMLAttributes<HTMLElement>` 상속 후 확장

### 참고할 기존 패턴
- 프리미티브: `components/ui/button.tsx` (cva + forwardRef)
- 복합 폼: `components/login-form.tsx` (Card + Input + Label + 에러 상태)
- 카드 레이아웃: `components/ui/card.tsx` (forwardRef + cn)

### 금지 사항
- 인라인 스타일 (`style={{}}`) 사용 금지
- Tailwind 임의 값(`w-[137px]`) 남용 금지 — 디자인 토큰으로 대체 가능한 경우
- 하드코딩된 색상값 (`text-gray-500`) 금지 — `text-muted-foreground` 등 토큰 사용

## 작업 절차

1. 요청 내용 분석 — `components/ui/` vs `components/` 중 적절한 위치 판단
2. 기존 유사 컴포넌트 확인 (`Glob`으로 `components/**/*.tsx` 검색)
3. 컴포넌트 생성 (위 규칙 준수)
4. 생성한 파일 경로와 사용 예시 코드 출력
