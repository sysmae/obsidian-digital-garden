---
publish:  true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Next.js 앱 라우터 + 백엔드 API 프록시 패턴에서 Zod 활용 가이드

## 현재 패턴 분석과 권장 Zod 적용 지점

귀하의 패턴은 **Next.js API Routes를 백엔드 API 프록시로 활용**하는 일반적이고 효과적인 구조입니다[^1][^2][^3]. 이 패턴에서 Zod를 전략적으로 적용하면 **데이터 안전성**과 **타입 안전성**을 크게 향상시킬 수 있습니다.

## 1. 추천 Zod 적용 지점

### 1.1 **Next.js API Routes (프록시 레이어)** - 최우선 적용 지점[^4][^5][^6]

```typescript
// app/api/users/route.ts
import { z } from 'zod';
import { NextRequest, NextResponse } from 'next/server';

// 백엔드 API 응답 스키마 정의
const UserResponseSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
  role: z.enum(['user', 'admin']),
  createdAt: z.string().transform(str => new Date(str))
});

// 클라이언트 요청 스키마 정의
const CreateUserRequestSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  role: z.enum(['user', 'admin']).optional()
});

export async function GET(request: NextRequest) {
  try {
    // 1. 백엔드 API 호출
    const backendResponse = await fetch(`${process.env.BACKEND_URL}/api/users`, {
      headers: {
        'Authorization': `Bearer ${getToken(request)}`,
        'Content-Type': 'application/json'
      }
    });
    
    const rawData = await backendResponse.json();
    
    // 2. 백엔드 응답 검증 (핵심!)
    const validatedData = UserResponseSchema.array().parse(rawData);
    
    return NextResponse.json(validatedData);
  } catch (error) {
    if (error instanceof z.ZodError) {
      console.error('백엔드 응답 스키마 오류:', error.errors);
      return NextResponse.json(
        { error: '백엔드 데이터 형식 오류' }, 
        { status: 500 }
      );
    }
    return NextResponse.json({ error: '서버 오류' }, { status: 500 });
  }
}

export async function POST(request: NextRequest) {
  try {
    // 1. 클라이언트 요청 검증
    const body = await request.json();
    const validatedInput = CreateUserRequestSchema.parse(body);
    
    // 2. 백엔드 API 전달
    const backendResponse = await fetch(`${process.env.BACKEND_URL}/api/users`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${getToken(request)}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify(validatedInput)
    });
    
    const rawData = await backendResponse.json();
    
    // 3. 백엔드 응답 검증
    const validatedResponse = UserResponseSchema.parse(rawData);
    
    return NextResponse.json(validatedResponse, { status: 201 });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { error: '입력값 검증 실패', details: error.flatten().fieldErrors },
        { status: 400 }
      );
    }
    return NextResponse.json({ error: '서버 오류' }, { status: 500 });
  }
}
```

**이 지점이 중요한 이유**[^7][^8]:

- 백엔드 API 응답이 예상과 다를 때 조기 발견
- 클라이언트로 전달되기 전에 데이터 정규화
- 백엔드 API 변경사항에 대한 방어 계층


### 1.2 React Query 훅 레벨 - 2차 적용 지점[^9][^10][^11]

```typescript
// hooks/useUsers.ts
import { useQuery, useMutation, useQueryClient } from '@tanstack/react-query';
import { z } from 'zod';

// 클라이언트에서 사용할 타입 (API Routes에서 검증된 데이터)
const UserSchema = z.object({
  id: z.number(),
  name: z.string(),
  email: z.string().email(),
  role: z.enum(['user', 'admin']),
  createdAt: z.date() // API Routes에서 이미 변환됨
});

type User = z.infer<typeof UserSchema>;

// API 호출 함수
async function fetchUsers(): Promise<User[]> {
  const response = await fetch('/api/users');
  
  if (!response.ok) {
    throw new Error('사용자 조회 실패');
  }
  
  const data = await response.json();
  
  // 선택적: Next.js API Routes를 신뢰한다면 생략 가능
  // 하지만 추가 안전성을 위해 유지 권장
  return UserSchema.array().parse(data);
}

async function createUser(userData: Omit<User, 'id' | 'createdAt'>): Promise<User> {
  const response = await fetch('/api/users', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify(userData)
  });
  
  if (!response.ok) {
    const errorData = await response.json();
    throw new Error(errorData.error || '사용자 생성 실패');
  }
  
  const data = await response.json();
  return UserSchema.parse(data);
}

// React Query 훅
export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: fetchUsers,
    staleTime: 5 * 60 * 1000, // 5분
  });
}

export function useCreateUser() {
  const queryClient = useQueryClient();
  
  return useMutation({
    mutationFn: createUser,
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ['users'] });
    },
  });
}
```


### 1.3 환경 변수 및 설정 검증[^12]

```typescript
// lib/env.ts
import { z } from 'zod';

const envSchema = z.object({
  BACKEND_URL: z.string().url(),
  NEXT_PUBLIC_APP_URL: z.string().url(),
  JWT_SECRET: z.string().min(32),
  NODE_ENV: z.enum(['development', 'production', 'test'])
});

export const env = envSchema.parse(process.env);
```


## 2. 실전 구현 패턴

### 2.1 스키마 모듈화 및 공유[^13][^14]

```typescript
// schemas/index.ts
import { z } from 'zod';

// 기본 엔티티 스키마
export const UserSchema = z.object({
  id: z.number(),
  name: z.string().min(1),
  email: z.string().email(),
  role: z.enum(['user', 'admin']),
  createdAt: z.date()
});

export const ProductSchema = z.object({
  id: z.number(),
  name: z.string().min(1),
  price: z.number().positive(),
  categoryId: z.number(),
  isActive: z.boolean()
});

// API 요청/응답 스키마
export const CreateUserRequestSchema = UserSchema.omit({ 
  id: true, 
  createdAt: true 
});

export const UpdateUserRequestSchema = UserSchema.partial().omit({ 
  id: true, 
  createdAt: true 
});

// 백엔드 API 응답 스키마 (날짜가 문자열로 오는 경우)
export const UserBackendResponseSchema = UserSchema.extend({
  createdAt: z.string().transform(str => new Date(str))
});

// 타입 내보내기
export type User = z.infer<typeof UserSchema>;
export type CreateUserRequest = z.infer<typeof CreateUserRequestSchema>;
export type UpdateUserRequest = z.infer<typeof UpdateUserRequestSchema>;
```


### 2.2 통합 API 클라이언트[^9][^11]

```typescript
// lib/api-client.ts
import { z } from 'zod';

interface ApiClientConfig<TRequest, TResponse> {
  method: 'GET' | 'POST' | 'PUT' | 'DELETE';
  path: string;
  requestSchema?: z.ZodSchema<TRequest>;
  responseSchema: z.ZodSchema<TResponse>;
}

class ApiClient {
  private baseUrl = '/api';

  async request<TRequest, TResponse>(
    config: ApiClientConfig<TRequest, TResponse>,
    data?: TRequest
  ): Promise<TResponse> {
    // 요청 데이터 검증
    if (data && config.requestSchema) {
      config.requestSchema.parse(data);
    }

    const response = await fetch(`${this.baseUrl}${config.path}`, {
      method: config.method,
      headers: { 'Content-Type': 'application/json' },
      body: data ? JSON.stringify(data) : undefined
    });

    if (!response.ok) {
      const errorData = await response.json().catch(() => ({}));
      throw new Error(errorData.error || `HTTP ${response.status}`);
    }

    const responseData = await response.json();
    
    // 응답 데이터 검증
    return config.responseSchema.parse(responseData);
  }
}

export const apiClient = new ApiClient();

// 사용 예시
export const userApi = {
  getUsers: () => apiClient.request({
    method: 'GET',
    path: '/users',
    responseSchema: UserSchema.array()
  }),
  
  createUser: (data: CreateUserRequest) => apiClient.request({
    method: 'POST',
    path: '/users',
    requestSchema: CreateUserRequestSchema,
    responseSchema: UserSchema
  }, data)
};
```


## 3. 에러 처리 및 사용자 경험

### 3.1 글로벌 에러 처리[^15]

```typescript
// hooks/useApiError.ts
import { z } from 'zod';
import { useQueryClient } from '@tanstack/react-query';

export function useApiErrorHandler() {
  const queryClient = useQueryClient();

  const handleError = (error: unknown) => {
    if (error instanceof z.ZodError) {
      // Zod 검증 오류
      console.error('데이터 검증 오류:', error.flatten());
      return {
        type: 'validation',
        message: '데이터 형식이 올바르지 않습니다',
        details: error.flatten().fieldErrors
      };
    }

    if (error instanceof Error) {
      // 일반 오류
      return {
        type: 'general',
        message: error.message || '알 수 없는 오류가 발생했습니다'
      };
    }

    return {
      type: 'unknown',
      message: '예상치 못한 오류가 발생했습니다'
    };
  };

  return { handleError };
}
```


### 3.2 React Query 설정[^15]

```typescript
// lib/query-client.ts
import { QueryClient } from '@tanstack/react-query';
import { z } from 'zod';

export const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      retry: (failureCount, error) => {
        // Zod 검증 오류는 재시도하지 않음
        if (error instanceof z.ZodError) {
          return false;
        }
        return failureCount < 3;
      },
      staleTime: 5 * 60 * 1000
    },
    mutations: {
      retry: (failureCount, error) => {
        if (error instanceof z.ZodError) {
          return false;
        }
        return failureCount < 1;
      }
    }
  }
});
```


## 4. 성능 및 최적화 고려사항

### 4.1 스키마 캐싱[^16][^17]

```typescript
// 스키마를 모듈 레벨에서 정의하여 재사용
const userSchema = z.object({ ... }); // ✅ 한 번만 생성

// 컴포넌트 내부에서 스키마 생성 금지
function UserComponent() {
  const schema = z.object({ ... }); // ❌ 매 렌더링마다 생성
}
```


### 4.2 조건부 검증

```typescript
// 개발 환경에서만 상세 검증
const isDev = process.env.NODE_ENV === 'development';

const responseSchema = isDev 
  ? DetailedUserSchema 
  : BasicUserSchema;
```


## 결론 및 권장사항

**핵심 적용 순서**:

1. **Next.js API Routes**: 백엔드 응답 검증 (필수)
2. **환경 변수**: 애플리케이션 설정 검증 (필수)
3. **React Query 훅**: 추가 안전성 확보 (권장)
4. **글로벌 에러 처리**: 사용자 경험 개선 (권장)

**성능과 안전성의 균형**:

- 프로덕션에서는 백엔드 응답 검증에 집중
- 개발 환경에서는 더 상세한 검증 활용
- 스키마 재사용을 통한 성능 최적화

이 패턴을 통해 백엔드 API의 변경사항이나 예상치 못한 응답으로부터 프론트엔드를 보호하면서도, 타입 안전성과 개발자 경험을 크게 향상시킬 수 있습니다[^18][^19].

<div style="text-align: center">⁂</div>

[^1]: https://www.reddit.com/r/nextjs/comments/1d75a0x/nextjs_app_router_flow_with_an_external_api/

[^2]: https://maxschmitt.me/posts/next-js-api-proxy

[^3]: https://www.reddit.com/r/nextjs/comments/15kodr9/correct_way_of_using_the_nextjs_13_api_as_a_proxy/

[^4]: https://kirandev.com/nextjs-api-routes-zod-validation

[^5]: https://www.youtube.com/watch?v=dcicL7Nmahg

[^6]: https://giancarlobuomprisco.com/next/protect-next-api-zod

[^7]: https://dub.co/blog/zod-api-validation

[^8]: https://makerkit.dev/docs/next-supabase/tutorials/api-routes-validation

[^9]: https://polvara.me/posts/effective-query-functions-for-react-query-with-zod/

[^10]: https://mlm.dev/posts/modern-react-data-fetching-with-typescript-query-zod

[^11]: https://noahflk.com/blog/typesafe-rest-api

[^12]: https://stackoverflow.com/questions/77255954/validating-environment-variables-in-next-js-with-zod

[^13]: https://github.com/melvynx/next-zod-route

[^14]: https://github.com/hacktisch/next-zod-api

[^15]: https://velog.io/@zad1264/Zod-Zustand-ReactQuery로-auth-구현하기-feat.-ErrorBoundary

[^16]: https://dev.to/dzakh/zod-v4-17x-slower-and-why-you-should-care-1m1

[^17]: https://www.linkedin.com/pulse/9-best-practices-using-zod-2025-joodi--lqnff

[^18]: https://www.geeksforgeeks.org/system-design/how-to-sync-front-end-and-back-end-validation/

[^19]: https://www.highlight.io/blog/3-levels-of-data-validation-in-a-full-stack-application-with-react

[^20]: https://dev.to/devkiran/how-to-validate-nextjs-api-routes-using-zod-2133

[^21]: https://curiousweek.tistory.com/206

[^22]: https://yogjin.tistory.com/129

[^23]: https://nextjs.org/docs/14/app/building-your-application/data-fetching/server-actions-and-mutations

[^24]: https://partnerjun.tistory.com/99

[^25]: https://www.reddit.com/r/nextjs/comments/1cqfoea/using_nextauth_v5_prisma_zod_and_shadcn_with/?tl=ko

[^26]: https://makerkit.dev/docs/next-supabase/development/validating-api-input-zod

[^27]: https://tkdodo.eu/blog/type-safe-react-query

[^28]: https://stackoverflow.com/questions/76537656/how-to-validate-api-response-in-rtk-query-using-zod-schema

[^29]: https://dianapps.com/blog/how-to-conduct-api-response-validation-in-react/

[^30]: https://community.auth0.com/t/next-js-external-api-without-proxying-requests-through-next-js-backend/109244

[^31]: https://www.freecodecamp.org/news/how-to-use-zod-for-react-api-validation/

[^32]: https://stackoverflow.com/questions/68701776/frontend-and-backend-validation-synergy

[^33]: https://stackoverflow.com/questions/60925133/proxy-to-backend-with-default-next-js-dev-server

[^34]: https://www.reddit.com/r/reactjs/comments/u8hw41/in_reacttypescript_app_do_you_validate_the_data/

[^35]: https://www.reddit.com/r/webdev/comments/1bslonl/how_do_you_keep_front_end_validations_on_sync/

[^36]: https://nextjs.org/docs/app/guides/backend-for-frontend

[^37]: https://learn.microsoft.com/en-us/azure/architecture/patterns/backends-for-frontends

[^38]: https://nextjs.org/blog/building-apis-with-nextjs

[^39]: https://cocococo.tistory.com/entry/ReacTS-React-Query

[^40]: https://betterprogramming.pub/backend-data-validations-and-why-you-need-them-73ea9004c6d7

[^41]: https://github.com/4dboard/next.js-proxy

[^42]: https://23life.tistory.com/entry/react-query와-error-전파-handling

