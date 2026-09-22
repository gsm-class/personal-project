# 일일 보고 시스템

영업 담당자가 일상 활동을 보고하고 상사가 피드백을 실시하기 위한 일일보고 관리 시스템입니다. 

## 🚀 기술 스택

- **프레임워크**: Next.js 15.5.2 (App Router)
- **언어**: TypeScript
- **스타일링**: Tailwind CSS 3.4 + shadcn/ui
- **데이터베이스**: SQLite + Prisma ORM (개발 환경)
- **인증**: JWT (구현됨)
- **그래프**: Recharts
- **테스트**: Vitest + Testing Library + Playwright (E2E)
- **코드품질**: ESLint + Prettier + Husky

## 📋 요구 사항

- Node.js 18.0.0 이상
- npm 9.0.0 이상
- SQLite (개발 환경)

## 🛠 설정

### 1. 리포지토리 복제

```bash
git clone https://github.com/bupark82/daily-report.git
cd daily-report
```

### 2. 종속성 설치

```bash
npm install
```

### 3. 환경 변수 설정

`.env.example`을 복사하여 `.env`를 만들고 필요한 값을 설정합니다. 

```bash
cp .env.example .env
```

필요한 환경 변수:

- `DATABASE_URL`: SQLite 연결 문자열 (기본값: `file:./dev.db`)
- `JWT_SECRET`: JWT 서명용 비밀 키
- `JWT_REFRESH_SECRET`: 새로 고치 토큰의 비밀 키

### 4. 데이터베이스 설정

```bash
# Prisma 마이그레이션 실행
npx prisma migrate dev

# 초기 데이터 투입
npx prisma db seed
```

### 5. 개발 서버 시작

```bash
npm run dev
```

[http://localhost:3000](http://localhost:3000) 에서 애플리케이션에 액세스 할 수 있습니다. 

## 🔐 테스트 계정

### 일반 사용자
- **이메일 주소**: user@example.com
- **암호**: Password123

### 관리자
- **이메일 주소**: admin@example.com
- **암호**: Password123

## 📁 프로젝트 구성

```
daily-report/
├── src/
│   ├── app/                  # Next.js App Router
│   │   ├── layout.tsx        # 루트 레이아웃
│   │   ├── page.tsx          # 홈페이지
│   │   ├── error.tsx         # 오류 처리
│   │   ├── loading.tsx       # 로딩 상태
│   │   ├── not-found.tsx     # 404 페이지
│   │   └── globals.css       # 글로벌 스타일
│   ├── components/           # 공통 컴포넌트 (향후 추가)
│   ├── lib/                  # 유틸리티 함수 (향후 추가)
│   └── types/                # TypeScript 타입 정의
├── prisma/
│   └── schema.prisma         # 데이터베이스 스키마 (향후 추가)
├── tests/                    # 테스트 파일
├── doc/                      # 문서
│   ├── API_SCHEME.md         # API 사양서
│   ├── ER_DIAGRAM.md         # ER 그림
│   ├── SCREEN_DESIGN.md      # 화면 설계서
│   └── TEST_DEFINITION.md    # 테스트 사양
└── public/                   # 정적 파일
```

## 🔧 사용 간능한 스크립트

```bash
# 개발 서버 시작
npm run dev

# 프로덕션 빌드
npm run build

# 프로덕션 서버 시작
npm run start

# TypeScript 타입 체크
npm run typecheck

# ESLint 체크
npm run lint

# ESLint 자동 수정
npm run lint:fix

# Prettier 형식
npm run format

# Prettier 점검
npm run format:check

# 테스트 실행
npm run test

# 테스트（감시 모드）
npm run test:watch

# 테스트 커버리지
npm run test:coverage
```

## 🎨 스타일 가이드

### Tailwind 사용자 정의 구성 요소

프로제그는 다음 사용자 정의 유틸리티 클래스를 정의합니다.：

- `.btn` - 기본 버튼 스타일
- `.btn-primary` - 기본 버튼
- `.btn-secondary` - 보조 버튼
- `.btn-danger` - 삭제, 위험 조작 버튼
- `.input` - 폼 입력 필드
- `.label` - 양식 라벨
- `.card` - 카드 컴포너느

## 🧪 테스트

```bash
# 단일 테스트 실행
npm run test

# 커버리지 레포트 생성
npm run test:coverage

# UI모드에서 테스트 실행
npm run test:ui
```

## 📝 개발자 가이드라인

### 브랜치 전략
- `main`: 프로덕션 환경
- `develop`: 개발 환경
- `feature/*`: 기능 개발
- `fix/*`: 버그 수정
- `docs/*`: 문서 업데이트

### 커밋 메시지 약관

```
feat: 새로운 기능 추가
fix: 버그 수정  
docs: 문서 업데이트   
style: 코드 스타일 수정
refactor: 리팩토링
test: 테스트 추가/수정
chore: 빌드 및 설정 변경
```

### Pull Request흐름

1. feature 브랜치 생성
2. 변경을 구현
3. 테스트를 추가・실행
4. Pull Request 생성
5. 리뷰・승인
6. main 브랜치에 병합

## ✅ 구현된 기능

- [x] Prisma데이터베이스 설정 (SQLite)
- [x] shadcn/ui컴포넌트 도입
- [x] JWT인증시스템
- [x] 로그인/로그아웃기능
- [x] 일반CRUD기능
- [x] 방문기록관리
- [x] 상사댓글기능
- [x] 고객마스터관리 (관리자 전용)
- [x] 영업담당자마스터관리 (관리자 전용)
- [x] 분석・레포트기능
- [x] 권한 관리 (일반사용자/관리자)

## 🚧 향후 구현 예정

- [ ] OpenAPI 사양 정의
- [ ] Google Cloud Run에 배포
- [ ] 월별 보고 기능
- [ ] 내보내기 기능

## 📄 라이센스

개인 프로젝트

## 👥 컨트리뷰터

- [@bupark82](https://github.com/bupark82)

## 📞 문의사항

Issues: [GitHub Issues](https://github.com/ai-service-code/daily-report/issues)
