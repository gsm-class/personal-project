# 일일보고 시스템 API 사양서

## 1. API 개요

### 1.1 기본 정보

- ** 기본 URL ** :`https://api.sales-report.example.com/v1`
- **인증 방식**: Bearer Token(JWT)
- **데이터 형식**: JSON
- **문자 코드**: UTF-8

### 1.2 엔드포인트 목록

| 카테고리 | 방법 | 엔드포인트 | 설명 |
| ---------- | -------- | ---------------------- | ------------------------ |
| 인증 | POST | /auth/login | 로그인 |
| 인증 | POST | /auth/logout | 로그아웃 |
| 인증 | GET | /auth/me | 로그인 사용자 정보 획득 |
| 일보 | GET | /reports | 일보 일람 취득 |
| 일보 | GET | /reports/{id} | 일보 상세 취득 |
| 일보 | POST | /reports | 일보 작성 |
| 일보 | PUT | /reports/{id} | 일보 갱신 |
| 일보 | DELETE | /reports/{id} | 일보 삭제 |
| 코멘트 | GET | /reports/{id}/comments | 코멘트 목록 취득 |
| 코멘트 | POST | /reports/{id}/comments | 코멘트 작성 |
| 영업 담당자 | GET | /sales-persons | 영업 담당자 일람 취득 |
| 영업 담당자 | GET | /sales-persons/{id} | 영업 담당자 상세 취득 |
| 영업 담당자 | POST | /sales-persons | 영업 담당자 작성 |
| 영업 담당자 | PUT | /sales-persons/{id} | 영업 담당자 갱신 |
| 영업 담당자 | DELETE | /sales-persons/{id} | 영업 담당자 삭제 |
| 고객 | GET | /customers | 고객 목록 취득 |
| 고객 | GET | /customers/{id} |고객 세부 정보 취득 |
| 고객 | POST | /customers | 고객 작성 |
| 고객 | PUT | /customers/{id} | 고객 갱신 |
| 고객 | DELETE | /customers/{id} | 고객 삭제 |

## 2. 공통 사양

### 2.1 요청 헤더

```
Authorization: Bearer {token}
Content-Type: application/json
Accept: application/json
```

### 2.2 응답 헤더

```
Content-Type: application/json; charset=utf-8
X-Request-Id: {request-id}
```

### 2.3 HTTP 상태 코드

| 코드 | 설명 |
|------| ---------------------- |
| 200  | 성공 |
| 201  | 작성 성공 |
| 204  | 삭제 성공 |
| 400  | 요청 오류 |
| 401  | 인증 오류 |
| 403  | 권한 오류 |
| 404  | 자원을 찾을 수 없습니다 |
| 409  | 충돌 오류 |
| 500  | 서버 오류 |

### 2.4 오류 응답 형식

```json
{ 
    "error": { 
        "code": "ERROR_CODE", 
        "message": "오류 메시지", 
        "details": [ 
            { 
                "field": "필드 이름", 
                "message": "자세한 메시지" 
            } 
        ] 
    }
}
```

### 2.5 페이지 네이션

```json
{ 
    "data": [...], 
    "pagination": { 
        "total": 100, 
        "page": 1, 
        "per_page": 20, 
        "total_pages": 5 
    }
}
```

## 3. API 세부정보

### 3.1 인증 API

#### POST /auth/login

로그인 인증을 하고 액세스 토큰 발급

**요청**

```json
{ 
    "email": "yamada@example.com", 
    "password": "password123"
}
```

**응답**

```json
{ 
    "token": "eyJhbGciOiJIUzI1NiIs...", 
    "expires_at": "2025-07-28T12:00:00Z", 
    "user": { 
        "id": 1, 
        "name": "야마다 타로", 
        "email": "yamada@example.com", 
        "department": "영업 1과", 
        "is_manager": false 
    }
}
```

#### POST /auth/logout

로그아웃 처리(토큰 비활성화)

**응답**

```json
204 No Content
```

#### GET /auth/me

현재 로그인한 사용자 정보 얻기

**응답**

```json
{ 
    "id": 1, 
    "name": "야마다 타로", 
    "email": "yamada@example.com", 
    "department": "영업 1과", 
    "is_manager": false
}
```

### 3.2 일보 API

#### GET /reports

일보 일람 취득

**쿼리 매개변수**  

|매개 변수 | 유형 | 필수 | 설명 |  
|------------|-----|------|------|  
| start_date | string | - | 시작일(YYYY-MM-DD) |  
| end_date | string | - | 종료일(YYYY-MM-DD) |  
| sales_person_id | integer | - | 영업 담당자 ID |  
| page | integer | - | 페이지 번호(기본값: 1) |  
| per_page | integer | - | 페이지당 수(기본값: 20) |  

**응답**

```json
{ 
    "data": [ 
      { 
        "id": 1, 
        "report_date": "2025-07-27", 
        "sales_person": { 
            "id": 1, 
            "name": "야마다 타로" 
        }, 
        "visit_count": 3, 
        "has_comments": true, 
        "created_at": "2025-07-27T09:00:00Z" 
      } 
    ], 
    "pagination": { 
        "total": 50, 
        "page": 1, 
        "per_page": 20, 
        "total_pages": 3 
    }
}
```

#### GET /reports/{id}

일보 상세 정보 취득

**응답**

```json
{ 
    "id": 1, 
    "report_date": "2025-07-27", 
    "sales_person": { 
        "id": 1, 
        "name": "야마다 타로", 
        "email": "yamada@example.com" 
    }, 
    "problem": "새로운 개척의 진행이 늦어져...", 
    "plan": "ABC 상사에 대한 견적 작성...", 
    "visits": [ 
        { 
            "id": 1, 
            "customer": { 
                "id": 10, 
                "company_name": "ABC 상사" 
            }, 
            "visit_time": "10:00", 
            "visit_content": "신제품 제안 수행..." 
        } 
    ], 
    "comments": [ 
        { 
        "id": 1, 
        "manager": { 
            "id": 2, 
            "name": "다나카 부장" 
        }, 
        "comment": "신규 개척에 대해서는 내일 상담합시다.", 
        "created_at": "2025-07-27T18:00:00Z" 
        } 
    ], 
    "created_at": "2025-07-27T09:00:00Z", 
    "updated_at": "2025-07-27T17:30:00Z"
}
```

#### POST /reports

일보 만들기

**요청**

```json
{ 
    "report_date": "2025-07-27", 
    "problem": "새로운 개척의 진행이 늦어져...", 
    "plan": "ABC 상사에 대한 견적 작성...", 
    "visits": [ 
        { 
            "customer_id": 10, 
            "visit_time": "10:00", 
            "visit_content": "신제품 제안 수행..."
        } 
    ]
}
```

**응답**

```json
  { 
    "id": 1, 
    "report_date": "2025-07-27", 
    "sales_person_id": 1, 
    "problem": "새로운 개척의 진행이 늦어져...", 
    "plan": "ABC 상사에 대한 견적 작성...", 
    "created_at": "2025-07-27T09:00:00Z"
 }
```
#### PUT /reports/{id}

일보 업데이트

**요청**

```json
{ 
    "problem": "새로운 개척의 진행이 지연되었습니다(업데이트)...", 
    "plan": "ABC 상사에 대한 견적 작성(업데이트)...", 
    "visits": [ 
        { 
            "id": 1, 
            "customer_id": 10, 
            "visit_time": "10:00", 
            "visit_content": "신제품 제안 실시(업데이트)..." 
        }, 
        { 
            "customer_id": 11, 
            "visit_time": "14:00", 
            "visit_content": "기존 시스템 유지보수 상담..." 
        } 
    ]
}
```

**응답**

```json
{ 
    "id": 1, 
    "report_date": "2025-07-27", 
    "sales_person_id": 1, 
    "problem": "새로운 개척의 진행이 지연되었습니다(업데이트)...", 
    "plan": "ABC 상사에 대한 견적 작성(업데이트)...", 
    "updated_at": "2025-07-27T17:30:00Z"
}
```

#### DELETE /reports/{id}
일일보고 삭제

**응답**

```json
{ 
    "message": "일일보고가 성공적으로 삭제되었습니다."
}
```

### 3.3 코멘트 API

#### GET /reports/{id}/comments

일보에 대한 코멘트 목록 얻기

**응답**

```json
{ 
    "data": [ 
        { 
            "id": 1, 
            "manager": { 
            "id": 2, 
            "name": "다나카 부장" 
            }, 
            "comment": "신규 개척에 대해서는 내일 상담합시다.", 
            "created_at": "2025-07-27T18:00:00Z" 
        } 
    ]
}
```

#### POST /reports/{id}/comments

일보에 댓글 추가(관리자 전용)

**요청**

```json
{ 
"comment": "신규 개척에 대해서는 내일 상담합시다."
}
```

**응답**

```json
{ 
    "id": 1, 
    "report_id": 1, 
    "manager_id": 2, 
    "comment": "신규 개척에 대해서는 내일 상담합시다.", 
    "created_at": "2025-07-27T18:00:00Z"
}
```

### 3.4 영업 담당자 API

#### GET /sales-persons

영업 담당자 일람 취득(관리자만)

**쿼리 매개변수**
|매개 변수 | 유형 | 필수 | 설명 |
|------------|-----|------|------|
| department | string | - | 부서명으로 필터링 |
| is_manager | boolean | - | 관리자 플래그로 필터링 |

**응답**

```json
{ 
    "data": [ 
        { 
            "id": 1, 
            "name": "야마다 타로", 
            "email": "yamada@example.com", 
            "department": "영업 1과", 
            "is_manager": false 
        } 
    ]
}
```

#### GET /sales-persons/{id}

영업 담당자 세부 정보 취득(관리자 전용)

**응답**

```json
{ 
    "id": 1, 
    "name": "야마다 타로", 
    "email": "yamada@example.com", 
    "department": "영업 1과", 
    "is_manager": false, 
    "created_at": "2025-01-01T09:00:00Z", 
    "updated_at": "2025-07-01T10:00:00Z"
}
```

#### POST /sales-persons

영업 담당자 만들기(관리자 전용)

**요청**

```json
{ 
    "name": "야마다 타로", 
    "email": "yamada@example.com", 
    "password": "password123", 
    "department": "영업 1과", 
    "is_manager": false
}
```

**응답**

```json
{ 
    "id": 1, 
    "name": "야마다 타로", 
    "email": "yamada@example.com", 
    "department": "영업 1과", 
    "is_manager": false, 
    "created_at": "2025-07-27T09:00:00Z"
}
```

#### PUT /sales-persons/{id}

영업 담당자 업데이트(관리자 전용)

**요청**

```json
{ 
    "name": "야마다 타로", 
    "email": "yamada@example.com", 
    "department": "영업 2과", 
    "is_manager": true
}
```

**응답**

```json
{ 
    "id": 1, 
    "name": "야마다 타로", 
    "email": "yamada@example.com", 
    "department": "영업 2과", 
    "is_manager": true, 
    "updated_at": "2025-07-27T10:00:00Z"
}
```

#### DELETE /sales-persons/{id}

영업 담당자 삭제(관리자 전용)

**응답**

```
204 No Content
```

### 3.5 고객 API

#### GET /customers

고객 목록 얻기

**쿼리 매개변수**
|매개 변수 | 유형 | 필수 | 설명 |
|------------|-----|------|------|
| search | string | - | 회사 / 담당자 이름으로 검색 |
| page | integer | - | 페이지 번호 |
|per_page|integer|-|페이지당 개수 |

**응답**

```json
{ 
    "data": [ 
        { 
            "id": 10, 
            "company_name": "ABC 상사", 
            "contact_person": "사토 이치로", 
            "phone": "03-1234-5678", 
            "email": "sato@abc.co.jp" 
        } 
    ], 
    "pagination": { 
        "total": 100, 
        "page": 1, 
        "per_page": 20, 
        "total_pages": 5 
    }
}
```

#### GET /customers/{id}

고객 세부정보 얻기

**응답**

```json
{ 
    "id": 10, 
    "company_name": "ABC 상사", 
    "contact_person": "사토 이치로", 
    "phone": "03-1234-5678", 
    "email": "sato@abc.co.jp", 
    "address": "도쿄도 치요다구...", 
    "created_at": "2025-01-01T09:00:00Z", 
    "updated_at": "2025-07-01T10:00:00Z"
}
```

#### POST /customers

고객 만들기(관리자 전용)

**요청**

```json
{ 
    "company_name": "ABC 상사", 
    "contact_person": "사토 이치로", 
    "phone": "03-1234-5678", 
    "email": "sato@abc.co.jp", 
    "address": "도쿄도 치요다구..."
}
```

**응답**

```json
{ 
    "id": 10, 
    "company_name": "ABC 상사", 
    "contact_person": "사토 이치로", 
    "phone": "03-1234-5678", 
    "email": "sato@abc.co.jp", 
    "address": "도쿄도 지요다구...", 
    "created_at": "2025-07-27T09:00:00Z"
}
```

#### PUT /customers/{id}

고객 업데이트(관리자 전용)

**요청**

```json
{ 
    "company_name": "ABC 상사", 
    "contact_person": "사토 이치로", 
    "phone": "03-1234-5678", 
    "email": "sato@abc.co.jp", 
    "address": "도쿄도 치요다구..."
}
```

**응답**

```json
{ 
    "id": 10, 
    "company_name": "ABC 상사", 
    "contact_person": "사토 이치로", 
    "phone": "03-1234-5678", 
    "email": "sato@abc.co.jp", 
    "address": "도쿄도 지요다구...", 
    "updated_at": "2025-07-27T10:00:00Z"
}
```

#### DELETE /customers/{id}

고객 삭제(관리자 전용)

**응답**

```
204 No Content
```

## 4. 오류 코드 목록

| 코드 | 설명 |
| ------------------------ | ------------------------------------------------ |
| AUTH_INVALID_CREDENTIALS | 이메일 주소 또는 비밀번호가 잘못되었습니다 |
| AUTH_TOKEN_EXPIRED | 토큰이 만료되었습니다 |
| AUTH_UNAUTHORIZED | 인증 필요 |
| FORBIDDEN | 이 작업을 수행할 권한이 없습니다 |
| VALIDATION_ERROR | 입력 값이 잘못되었습니다 |
| DUPLICATE_REPORT | 같은 날짜의 일보가 이미 있습니다 |
| NOT_FOUND | 리소스를 찾을 수 없습니다 |
| INTERNAL_ERROR | 서버 오류가 발생했습니다 |

## 5. 제한 사항

- API 속도 제한: 시간당 1000개의 요청/사용자
- 요청 본문 크기: 최대 1MB
- 응답 시간 초과: 30초
- 동시 연결 수: 100 연결/사용자