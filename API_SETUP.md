# 영업일보 시스템 OpenAPI & Zod 스키마 설정

## 개요

영업일보 시스템의 Issue#4 「【기반】OpenAPI 사양 정의와 Zod 스키마 생성」이 완료되었습니다.
이 문서에서는 구현된 기능을 사용하는 방법을 설명합니다.

## 구현된 기능

### 1. Zod 스키마 정의

- **위치**: `src/lib/schemas/`
- **내용**: 
- 공통 스키마 (`common.ts`) 
- 인증 스키마 (`auth.ts`) 
- 영업 담당자 스키마 (`sales-person.ts`) 
- 고객 스키마 (`customer.ts`) 
- 일보 스키마 (`report.ts`)

### 2. OpenAPI 사양 생성

- **위치**: `openapi.json`
- **생성 방법**: `npm run openapi:generate`
- **커버 범위**: 
- 인증 API (`/auth/login`,`/auth/logout`,`/auth/me`) 
- 일보 API (`/reports` CRUD +`/reports/{id}/comments`) 
- 영업 담당자 API (`/sales-persons` CRUD) 
- 고객 API (`/customers` CRUD)

### 3. TypeScript 유형 정의 자동 생성

- **위치**: `src/types/api-generated.ts`, `src/types/api-utilities.ts`
- **생성 방법**: `npm run api:types`
- **기능**: OpenAPI 사양에서 타입 안전한 TypeScript 타입을 자동 생성

### 4. API 클라이언트

- **위치**: `src/lib/api/`
- **기능**: 
- 유형 안전한 API 클라이언트 (`client.ts`) 
- React Hook 용 유틸리티 (`hooks.ts`) 
- 통합 오류 처리

## 사용법

### NPM 스크립트

```bash
# OpenAPI 사양書를 생성
npm run openapi:generate

# TypeScript 유형 정의를 생성
npm run api:types

# 상기 2개를 연속 실행
npm run api:generate
```

### API 클라이언트의 사용

```typescript
import { api, setAuthToken } from '@/lib/api';

// 로그인
const loginResponse = await api.auth.login({
  email: 'user@example.com',
  password: 'password123'
});

// 토큰 설정
setAuthToken(loginResponse.token);

// 일일보고 생성
const report = await api.reports.create({
  report_date: '2025-07-27',
  problem: '새로운 개척의 진행이 지연됨',
  plan: 'ABC상사에 대한 견적 작성',
  visits: [
    {
      customer_id: 1,
      visit_time: '10:00',
      visit_content: '신제품 제안 실시'
    }
  ]
});
```

### Zod 스키마에서 유효성 검사

```typescript
import { CreateReportRequestSchema } from '@/lib/schemas';

// 요청 데이터의 유효성 검사
try {
  const validatedData = CreateReportRequestSchema.parse(formData);
  // 유효성 검사 성공 시의 처리
} catch (error) {
  // 유효성 검사 오류의 처리
  console.error('Validation error:', error);
}
```

### React Hooksの使用

```typescript
import { useAuth, useReports } from '@/lib/api';

function MyComponent() {
  const { data: user, loading, login } = useAuth();
  const { data: reports, fetchReports } = useReports();

  const handleLogin = async () => {
    try {
      await login({ email: 'user@example.com', password: 'password' });
    } catch (error) {
      // 에러 핸들링
    }
  };

  // ...
}
```

## 파일 구조

```
src/lib/
├── schemas/           # Zod스키마 정의
│   ├── common.ts     # 공통스키마
│   ├── auth.ts       # 인증스키마
│   ├── sales-person.ts # 영업담당자스키마
│   ├── customer.ts   # 고객스키마
│   ├── report.ts     # 일일보고스키마
│   └── index.ts      # 내보내기
├── api/              # API클라이언트
│   ├── client.ts     # 메인클라이언트
│   ├── hooks.ts      # React Hooks
│   └── index.ts      # 내보내기
└── openapi-simple.ts # OpenAPI사양생성

src/types/
├── api-generated.ts  # 자동 생성된 타입 정의
└── api-utilities.ts  # 타입 유틸리티

scripts/
├── generate-openapi.ts # OpenAPI 사양 생성 스크립트
└── generate-api-types.ts # 타입 정의 생성 스크립트

examples/
└── api-usage.ts      # 사용 예시
```

## 테스트

```bash
# 스키마의 테스트
npm test -- src/lib/schemas/__tests__/

# API클라이언트의 테스트  
npm test -- src/lib/api/__tests__/

# 모든 테스트 실행
npm test
```

## 에러 핸들링

API클라이언트는 통합된 에러 핸들링을 제공합니다：

```typescript
import { ApiError } from '@/lib/api';

try {
  await api.reports.create(reportData);
} catch (error) {
  if (error instanceof ApiError) {
    console.error(`API Error [${error.code}]:`, error.message);
    
    // HTTP 상태 코드별 처리
    switch (error.status) {
      case 400: // 유효성 검사 오류
        if (error.details) {
          error.details.forEach(detail => {
            console.error(`${detail.field}: ${detail.message}`);
          });
        }
        break;
      case 401: // 인증 오류
        // 로그인 화면으로 리디렉션
        break;
      case 403: // 권한 오류
        // 권한 부족 메시지 표시
        break;
      case 409: // 경합 오류（중복 등）
        // 충돌 해결 메시지 표시
        break;
    }
  }
}
```

## 미래의 확장

1. **React Query 통합**: `src/lib/api/hooks.ts`에 React Query 기반 구현 추가
2. **Swagger UI**: 개발용 Swagger UI 페이지 추가
3. **API Mock**: 테스트용 모의 서버 구현
4. **밸리데이션 미들웨어**: Next.js API 루트용 Zod 밸리데이션 미들웨어

## 주의사항

- 현재 OpenAPI 사양은 간략한 버전입니다. 필요한 경우 세부정보를 추가하세요.
- API 클라이언트는 `openapi-fetch`를 사용하며 유형 안전성을 제공합니다.
- Zod 스키마는 프런트 엔드에서 유효성 검사와 OpenAPI 사양 생성 모두에 사용됩니다.
- 환경 변수 `NEXT_PUBLIC_API_BASE_URL`로 API 기반 URL을 설정할 수 있습니다.

## 지원

자세한 사용 예는 `examples/api-usage.ts`를 참조하십시오.
질문이나 문제가 있는 경우 프로젝트의 Issue를 만듭니다.