---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Next.js 프로젝트에서 Jest를 활용한 유닛·통합 테스트 완전 가이드

## 핵심 요약

Next.js 환경에서 React Query, Zod, Zustand, Radix UI와 같은 최신 라이브러리들을 사용하는 프로젝트에서는 **유닛 테스트와 통합 테스트를 층별로 분리**하고, 각 라이브러리의 특성에 맞는 모킹 전략을 적용하여 **신뢰성 높은 테스트 환경**을 구축해야 한다.

## 1. 기본 환경 설정

### 필수 패키지 설치

```bash
# Jest 및 테스팅 라이브러리 설치
npm install -D jest jest-environment-jsdom @testing-library/react @testing-library/jest-dom @testing-library/user-event

# TypeScript 지원
npm install -D ts-jest @types/jest

# API 통합 테스트용 (선택사항)
npm install -D supertest @types/supertest next-test-api-route-handler
```


### Jest 설정 파일 (`jest.config.ts`)

```typescript
import type { Config } from 'jest'
import nextJest from 'next/jest.js'

const createJestConfig = nextJest({
  dir: './',
})

const config: Config = {
  coverageProvider: 'v8',  
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.ts'],
  moduleNameMapper: {
    '@/(.*)$': '<rootDir>/src/$1',
  },
  // 테스트 유형별 분리
  projects: [
    {
      displayName: 'unit',
      testMatch: ['<rootDir>/**/*.unit.test.{ts,tsx}'],
      testEnvironment: 'jsdom',
    },
    {
      displayName: 'integration',  
      testMatch: ['<rootDir>/**/*.integration.test.{ts,tsx}'],
      testEnvironment: 'jsdom',
    },
    {
      displayName: 'api',
      testMatch: ['<rootDir>/src/pages/api/**/*.test.{ts,tsx}', '<rootDir>/src/app/api/**/*.test.{ts,tsx}'],
      testEnvironment: 'node', // API 테스트는 Node 환경
    }
  ]
}

export default createJestConfig(config)
```


### 테스트 초기화 파일 (`jest.setup.ts`)

```typescript
import '@testing-library/jest-dom'

// React Query 테스트 설정
import { cleanup } from '@testing-library/react'
import { afterEach } from '@jest/globals'

afterEach(() => {
  cleanup()
})

// Zustand 초기화
beforeEach(() => {
  // Zustand 스토어 초기화 로직
})

// Radix UI 테스트용 모킹
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation(query => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
})

// Radix UI 포인터 이벤트 모킹  
global.ResizeObserver = jest.fn().mockImplementation(() => ({
  observe: jest.fn(),
  unobserve: jest.fn(),
  disconnect: jest.fn(),
}))
```


## 2. React Query 테스트 설정

### 테스트 유틸리티 (`test-utils.tsx`)

```typescript
import React, { ReactElement } from 'react'
import { render, RenderOptions } from '@testing-library/react'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'

const createTestQueryClient = () => new QueryClient({
  defaultOptions: {
    queries: {
      retry: false, // 테스트에서는 재시도 비활성화
      cacheTime: Infinity,
    },
    mutations: {
      retry: false,
    },
  },
})

interface AllProvidersProps {
  children: React.ReactNode
}

const AllProviders = ({ children }: AllProvidersProps) => {
  const testQueryClient = createTestQueryClient()
  
  return (
    <QueryClientProvider client={testQueryClient}>
      {children}
    </QueryClientProvider>
  )
}

const customRender = (
  ui: ReactElement,
  options?: Omit<RenderOptions, 'wrapper'>,
) => render(ui, { wrapper: AllProviders, ...options })

export * from '@testing-library/react'
export { customRender as render }
```


### React Query 훅 테스트 예시

```typescript
// hooks/useUser.integration.test.tsx
import { renderHook, waitFor } from '@testing-library/react'
import { useUser } from '@/hooks/useUser'
import { createTestQueryClient } from '@/test-utils'

describe('useUser Integration', () => {
  it('should fetch user data successfully', async () => {
    // API 모킹
    global.fetch = jest.fn().mockResolvedValue({
      ok: true,
      json: async () => ({ id: 1, name: 'John Doe' }),
    })

    const { result } = renderHook(() => useUser(1), {
      wrapper: ({ children }) => (
        <QueryClientProvider client={createTestQueryClient()}>
          {children}
        </QueryClientProvider>
      ),
    })

    await waitFor(() => expect(result.current.isSuccess).toBe(true))
    expect(result.current.data).toEqual({ id: 1, name: 'John Doe' })
  })
})
```


## 3. Zustand 스토어 테스트

### Zustand 모킹 설정 (`__mocks__/zustand.ts`)

```typescript
import { act } from '@testing-library/react'

const { create: actualCreate } = jest.requireActual('zustand')

// 스토어 상태 초기화 유틸리티
export const storeResetFns = new Set<() => void>()

const create = (createState: any) => {
  const store = actualCreate(createState)
  const initialState = store.getState()
  
  storeResetFns.add(() => {
    store.setState(initialState, true)
  })
  
  return store
}

export { create }
```


### Zustand 스토어 단위 테스트

```typescript
// stores/userStore.unit.test.ts
import { renderHook, act } from '@testing-library/react'
import { useUserStore } from '@/stores/userStore'

// Zustand 모킹
jest.mock('zustand', () => require('@/__mocks__/zustand'))

describe('UserStore Unit Tests', () => {
  beforeEach(() => {
    // 각 테스트 전 스토어 상태 초기화
    const { storeResetFns } = require('@/__mocks__/zustand')
    storeResetFns.forEach((resetFn: () => void) => resetFn())
  })

  it('should update user data', () => {
    const { result } = renderHook(() => useUserStore())

    act(() => {
      result.current.setUser({ id: 1, name: 'Alice' })
    })

    expect(result.current.user).toEqual({ id: 1, name: 'Alice' })
  })
})
```


### Zustand와 컴포넌트 통합 테스트

```typescript
// components/UserProfile.integration.test.tsx
import { render, screen, fireEvent } from '@/test-utils'
import UserProfile from '@/components/UserProfile'

// Zustand 스토어 특정 모킹
jest.mock('@/stores/userStore', () => ({
  useUserStore: (selector: any) => {
    const mockStore = {
      user: { id: 1, name: 'Test User' },
      setUser: jest.fn(),
      loading: false,
    }
    return selector(mockStore)
  }
}))

describe('UserProfile Integration', () => {
  it('should display user information and handle updates', () => {
    render(<UserProfile />)
    
    expect(screen.getByText('Test User')).toBeInTheDocument()
    
    // 사용자 상호작용 테스트
    const editButton = screen.getByRole('button', { name: /edit/i })
    fireEvent.click(editButton)
    
    expect(screen.getByRole('textbox')).toBeInTheDocument()
  })
})
```


## 4. Zod 스키마 테스트

### Zod 스키마 단위 테스트

```typescript
// schemas/userSchema.unit.test.ts
import { userSchema } from '@/schemas/userSchema'

describe('User Schema Validation', () => {
  it('should validate correct user data', () => {
    const validUser = {
      name: 'John Doe',
      email: 'john@example.com',
      age: 25
    }

    const result = userSchema.safeParse(validUser)
    expect(result.success).toBe(true)
    
    if (result.success) {
      expect(result.data).toEqual(validUser)
    }
  })

  it('should reject invalid email format', () => {
    const invalidUser = {
      name: 'John Doe',
      email: 'invalid-email',
      age: 25
    }

    const result = userSchema.safeParse(invalidUser)
    expect(result.success).toBe(false)
    
    if (!result.success) {
      expect(result.error.issues).toContainEqual(
        expect.objectContaining({
          path: ['email'],
          code: 'invalid_string'
        })
      )
    }
  })

  it('should handle schema transformations', () => {
    const dateSchema = z.string().transform(str => new Date(str))
    const result = dateSchema.safeParse('2023-01-01')
    
    expect(result.success).toBe(true)
    if (result.success) {
      expect(result.data instanceof Date).toBe(true)
    }
  })
})
```


### 폼 검증과 Zod 통합 테스트

```typescript
// components/UserForm.integration.test.tsx
import { render, screen, fireEvent, waitFor } from '@/test-utils'
import UserForm from '@/components/UserForm'
import userEvent from '@testing-library/user-event'

describe('UserForm with Zod Validation', () => {
  it('should show validation errors for invalid input', async () => {
    const user = userEvent.setup()
    render(<UserForm />)
    
    const emailInput = screen.getByLabelText(/email/i)
    const submitButton = screen.getByRole('button', { name: /submit/i })
    
    // 잘못된 이메일 입력
    await user.type(emailInput, 'invalid-email')
    await user.click(submitButton)
    
    await waitFor(() => {
      expect(screen.getByText(/invalid email format/i)).toBeInTheDocument()
    })
  })
})
```


## 5. Radix UI 컴포넌트 테스트

### Radix UI 컴포넌트 모킹 설정

```typescript
// __mocks__/radix-ui.ts
export const mockRadixSelect = {
  Root: ({ children }: { children: React.ReactNode }) => <div data-testid="select-root">{children}</div>,
  Trigger: ({ children, ...props }: any) => (
    <button data-testid="select-trigger" {...props}>{children}</button>
  ),
  Content: ({ children }: { children: React.ReactNode }) => (
    <div data-testid="select-content">{children}</div>
  ),
  Item: ({ children, value, ...props }: any) => (
    <div data-testid="select-item" data-value={value} {...props}>{children}</div>
  ),
}

jest.mock('@radix-ui/react-select', () => mockRadixSelect)
```


### Radix UI 통합 테스트

```typescript
// components/CustomSelect.integration.test.tsx
import { render, screen, fireEvent } from '@/test-utils'
import CustomSelect from '@/components/CustomSelect'

// Radix UI 포인터 이벤트 모킹
beforeAll(() => {
  window.HTMLElement.prototype.scrollIntoView = jest.fn()
  window.HTMLElement.prototype.releasePointerCapture = jest.fn()
  window.HTMLElement.prototype.hasPointerCapture = jest.fn()
})

describe('CustomSelect Integration', () => {
  it('should handle select interactions', async () => {
    const onValueChange = jest.fn()
    
    render(
      <CustomSelect 
        options={[{ value: 'option1', label: 'Option 1' }]}
        onValueChange={onValueChange}
      />
    )
    
    const trigger = screen.getByTestId('select-trigger')
    
    // 포인터 이벤트로 상호작용 시뮬레이션
    fireEvent.pointerDown(trigger)
    fireEvent.pointerUp(trigger)
    
    const option = screen.getByTestId('select-item')
    fireEvent.click(option)
    
    expect(onValueChange).toHaveBeenCalledWith('option1')
  })
})
```


## 6. Next.js API Routes 테스트

### App Router API 테스트

```typescript
// app/api/users/route.test.ts
/**
 * @jest-environment node
 */
import { GET, POST } from './route'
import { NextRequest } from 'next/server'

describe('/api/users', () => {
  describe('GET', () => {
    it('should return users list', async () => {
      const response = await GET()
      const data = await response.json()
      
      expect(response.status).toBe(200)
      expect(Array.isArray(data)).toBe(true)
    })
  })

  describe('POST', () => {
    it('should create new user', async () => {
      const requestBody = { name: 'John Doe', email: 'john@example.com' }
      
      const request = new NextRequest('http://localhost:3000/api/users', {
        method: 'POST',
        body: JSON.stringify(requestBody),
        headers: { 'Content-Type': 'application/json' }
      })
      
      const response = await POST(request)
      const data = await response.json()
      
      expect(response.status).toBe(201)
      expect(data).toMatchObject(requestBody)
    })
  })
})
```


### API 통합 테스트 (Supertest 활용)

```typescript
// api/integration/users.integration.test.ts
import request from 'supertest'
import { createServer } from 'http'
import { NextApiHandler } from 'next'
import handler from '@/pages/api/users'

const server = createServer((req, res) => {
  return handler(req as any, res as any)
})

describe('/api/users Integration', () => {
  it('should handle full user workflow', async () => {
    // 사용자 생성
    const createResponse = await request(server)
      .post('/api/users')
      .send({ name: 'Alice', email: 'alice@example.com' })
      .expect(201)

    const userId = createResponse.body.id

    // 사용자 조회
    await request(server)
      .get(`/api/users/${userId}`)
      .expect(200)
      .expect(res => {
        expect(res.body.name).toBe('Alice')
      })
  })
})
```


## 7. 통합 테스트 시나리오 예시

### 전체 워크플로우 통합 테스트

```typescript
// integration/userWorkflow.integration.test.tsx
import { render, screen, waitFor } from '@/test-utils'
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import UserManagementPage from '@/pages/users'
import userEvent from '@testing-library/user-event'

describe('User Management Workflow', () => {
  let queryClient: QueryClient

  beforeEach(() => {
    queryClient = new QueryClient({
      defaultOptions: { queries: { retry: false } }
    })
    
    // API 모킹
    global.fetch = jest.fn()
  })

  it('should complete full user management flow', async () => {
    const user = userEvent.setup()

    // API 응답 모킹
    ;(global.fetch as jest.Mock)
      .mockResolvedValueOnce({
        ok: true,
        json: async () => ({ users: [] })
      })
      .mockResolvedValueOnce({
        ok: true,
        json: async () => ({ id: 1, name: 'New User', email: 'new@example.com' })
      })

    render(
      <QueryClientProvider client={queryClient}>
        <UserManagementPage />
      </QueryClientProvider>
    )

    // 초기 로딩 확인
    expect(screen.getByText(/loading/i)).toBeInTheDocument()

    // 사용자 추가 폼 작성
    await waitFor(() => {
      expect(screen.getByRole('button', { name: /add user/i })).toBeInTheDocument()
    })

    const addButton = screen.getByRole('button', { name: /add user/i })
    await user.click(addButton)

    const nameInput = screen.getByLabelText(/name/i)
    const emailInput = screen.getByLabelText(/email/i)
    const submitButton = screen.getByRole('button', { name: /submit/i })

    await user.type(nameInput, 'New User')
    await user.type(emailInput, 'new@example.com')
    await user.click(submitButton)

    // 성공 메시지 확인
    await waitFor(() => {
      expect(screen.getByText(/user created successfully/i)).toBeInTheDocument()
    })
  })
})
```


## 8. 테스트 실행 스크립트

### `package.json` 설정

```json
{
  "scripts": {
    "test": "jest",
    "test:unit": "jest --selectProjects unit",
    "test:integration": "jest --selectProjects integration", 
    "test:api": "jest --selectProjects api",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:ci": "jest --ci --coverage --watchAll=false"
  }
}
```


## 9. 모범 사례 및 주의사항

### 테스트 분리 전략

- **유닛 테스트**: 개별 함수, 훅, 컴포넌트의 독립적 동작 검증
- **통합 테스트**: 여러 컴포넌트·서비스가 연동되는 워크플로우 검증
- **API 테스트**: 서버 엔드포인트의 요청·응답 사이클 검증


### 성능 최적화

- **테스트 병렬 실행**: Jest의 `--maxWorkers` 옵션 활용
- **스마트 모킹**: 필요한 부분만 모킹하여 테스트 신뢰성 유지
- **캐시 관리**: React Query 캐시 무효화로 테스트 간 격리 보장


### 지속적 통합

- **GitHub Actions/Jenkins**: 자동화된 테스트 파이프라인 구축
- **코드 커버리지**: 80% 이상 목표, 핵심 로직 우선 커버
- **테스트 안정성**: Flaky 테스트 최소화를 위한 적절한 대기시간 설정


## 10. 결론

Next.js 환경에서 현대적인 라이브러리 스택을 활용한 프로젝트의 테스트는 **각 도구의 특성을 이해하고 적절한 모킹 전략을 적용**하는 것이 핵심이다. 유닛 테스트로 개별 로직을 검증하고, 통합 테스트로 사용자 시나리오를 보장하며, API 테스트로 서버 로직을 확인함으로써 **신뢰할 수 있는 애플리케이션**을 구축할 수 있다. 지속적인 자동화와 적절한 테스트 커버리지를 통해 장기적으로 유지보수 가능한 고품질 코드베이스를 만들어 나가는 것이 중요하다[^1][^2][^3][^4][^5][^6].

<div style="text-align: center">⁂</div>

[^1]: https://nextjs.org/docs/app/guides/testing/jest

[^2]: https://dev.to/dforrunner/how-to-unit-test-nextjs-13-app-router-api-routes-with-jest-and-react-testing-library-270a

[^3]: https://gist.github.com/abdmmar/1936d6ff82396cbc87b505b83608bc64

[^4]: https://tanstack.com/query/latest/docs/react/guides/testing

[^5]: https://stackoverflow.com/questions/74861753/mocking-zustand-store-for-jest-test

[^6]: https://app.studyraid.com/en/read/11289/352212/testing-schema-implementations

[^7]: https://note.com/cyberz_cto/n/n3ba47e0dcfd2

[^8]: https://nextjs.org/docs/pages/guides/testing/jest

[^9]: https://github.com/KennFatt/nextjs-api-routes-testing

[^10]: https://coderpad.io/blog/development/how-to-write-integration-tests-with-jest-and-react-testing-library/

[^11]: https://zenn.dev/hamworks/articles/3d623eede50de4

[^12]: https://www.youtube.com/watch?v=2HFEFz7LWdE

[^13]: https://articles.readytowork.jp/next-js-with-react-testing-library-jest-and-typescript-a6aa11b85434

[^14]: https://zenn.dev/codeciao/articles/28e0d24d5bf46a

[^15]: https://zenn.dev/nabee/articles/0dc45c5404490c

[^16]: https://www.reddit.com/r/nextjs/comments/17y1jia/jest_test_for_nextjs_13_api_route_handlers_test/

[^17]: https://tanstack.com/query/v4/docs/react/guides/testing

[^18]: https://www.youtube.com/watch?v=XTNqyEBPAFw

[^19]: https://qiita.com/tatsuya-miyamoto/items/f99eb069f65b30f2f816

[^20]: https://stackoverflow.com/questions/65694015/integrate-testing-for-react-query-with-testing-library

[^21]: https://www.wisp.blog/blog/how-to-use-jest-with-nextjs-15-a-comprehensive-guide

[^22]: https://zenn.dev/takepepe/articles/testing-gssp-and-api-routes

[^23]: https://radixweb.com/blog/snapshot-testing-in-react-testing-library

[^24]: https://www.reddit.com/r/reactjs/comments/1df33a5/mocking_zustand_with_jest_stack_overflow/

[^25]: https://www.reddit.com/r/node/comments/uk07hu/best_practice_where_to_test_validation/

[^26]: https://zenn.dev/tenta_shiratori/articles/69c296e6946b74

[^27]: https://v9.mateusf.com/blog/tag/zustand

[^28]: https://scrapbox.io/haruharu/zodのバリデーションメッセージテストをjestでやる

[^29]: https://github.com/radix-ui/primitives/discussions/1571

[^30]: https://zustand.docs.pmnd.rs/guides/testing

[^31]: https://github.com/colinhacks/zod

[^32]: https://github.com/radix-ui/primitives/issues/1220

[^33]: https://github.com/pmndrs/zustand/discussions/1546

[^34]: https://qiita.com/diskszk/items/a50b72e5dd89f933e1bd

[^35]: https://zenn.dev/nuintee/articles/ec86be61e187ed

[^36]: https://github.com/pmndrs/zustand/discussions/2852

[^37]: https://zenn.dev/yamakenji24/articles/rhf-with-zod-jest

[^38]: https://codesandbox.io/s/radix-ui-tests-0xzzh

[^39]: https://t-cr.jp/article/apk1iusk3ankpto

[^40]: https://qiita.com/marl0401/items/8ceef93a850b1c6567f5

[^41]: https://qiita.com/masakiwakabayashi/items/204ed2b32254bbc9a5c1

[^42]: https://www.reddit.com/r/nextjs/comments/16z4l49/how_the_hell_do_i_test_nextjs_app_router_api/

[^43]: https://zenn.dev/hamworks/articles/7d84f6335596d8

[^44]: https://push.tokyo/nextjs-jest-setup/

[^45]: https://nextjs.org/docs/pages/building-your-application/routing/api-routes

[^46]: https://zenn.dev/shootacean/articles/how-to-set-up-jest-in-a-nextjs-project

[^47]: https://zenn.dev/takepepe/articles/nextjs13-components-integration-test

[^48]: https://zenn.dev/illionillion/articles/github-zenn-linkage-20240205-1

[^49]: https://supertokens.com/blog/mastering-nextjs-api-routes

[^50]: https://nextjs-ja-translation-docs.vercel.app/docs/testing

[^51]: https://blog.arcjet.com/testing-next-js-app-router-api-routes/

[^52]: https://nextjsjp.org/docs/app/guides/testing/jest

[^53]: https://www.youtube.com/watch?v=XVBLrpZ5WD0

[^54]: https://note.com/readytowork/n/n451ee36a3dc4

