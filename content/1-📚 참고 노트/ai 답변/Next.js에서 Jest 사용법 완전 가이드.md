---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Next.js에서 Jest 사용법 완전 가이드

## 주요 개념

Next.js에서 Jest는 **컴포넌트 테스트**, **API 라우트 테스트**, **서버 사이드 함수 테스트**를 포괄하는 종합 테스트 솔루션이다. Next.js 12 이상에서는 **내장 Rust 컴파일러**를 통해 복잡한 설정 없이 Jest를 바로 사용할 수 있으며, TypeScript 지원과 다양한 모킹 기능을 제공한다.

## 1. Next.js 프로젝트에서 Jest 설치 및 설정

### 1.1 빠른 시작 (공식 템플릿 사용)

```bash
npx create-next-app@latest --example with-jest with-jest-app
```


### 1.2 수동 설치 (기존 프로젝트)

```bash
npm install -D jest jest-environment-jsdom @testing-library/react @testing-library/dom @testing-library/jest-dom ts-node @types/jest
```


### 1.3 Jest 설정 파일 생성

`jest.config.ts` 파일을 프로젝트 루트에 생성:

```typescript
import type { Config } from 'jest'
import nextJest from 'next/jest.js'

const createJestConfig = nextJest({
  dir: './',  // Next.js 설정과 .env 파일 로드용 경로
})

const config: Config = {
  coverageProvider: 'v8',
  testEnvironment: 'jsdom',  // DOM 환경 시뮬레이션
  setupFilesAfterEnv: ['<rootDir>/jest.setup.ts'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',  // 절대 경로 별칭 설정
  },
  testMatch: [
    '**/__tests__/**/*.(test|spec).ts?(x)',
    '**/*.(test|spec).ts?(x)'
  ]
}

export default createJestConfig(config)
```


### 1.4 Jest 설정 파일 생성

`jest.setup.ts` 파일:

```typescript
import '@testing-library/jest-dom'
```


## 2. 컴포넌트 테스트

### 2.1 기본 컴포넌트 테스트

```typescript
// components/Button.tsx
interface ButtonProps {
  onClick: () => void;
  children: React.ReactNode;
}

export const Button: React.FC<ButtonProps> = ({ onClick, children }) => (
  <button onClick={onClick}>{children}</button>
);

// __tests__/Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react'
import { Button } from '../components/Button'

describe('Button Component', () => {
  it('renders button with correct text', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click me</Button>)
    
    expect(screen.getByRole('button')).toHaveTextContent('Click me')
  })
  
  it('calls onClick when clicked', () => {
    const handleClick = jest.fn()
    render(<Button onClick={handleClick}>Click me</Button>)
    
    fireEvent.click(screen.getByRole('button'))
    expect(handleClick).toHaveBeenCalledTimes(1)
  })
})
```


### 2.2 스냅샷 테스트

UI 컴포넌트의 예상치 못한 변화를 감지:

```typescript
import { render } from '@testing-library/react'
import { HomePage } from '../pages/index'

test('HomePage snapshot', () => {
  const { asFragment } = render(<HomePage />)
  expect(asFragment()).toMatchSnapshot()
})
```


## 3. API 라우트 테스트

### 3.1 GET 요청 테스트

```typescript
// app/api/users/route.ts
import { NextResponse } from 'next/server'

export async function GET() {
  const users = [
    { id: 1, name: 'John Doe' },
    { id: 2, name: 'Jane Smith' }
  ]
  return NextResponse.json(users, { status: 200 })
}

// app/api/users/route.test.ts
/**
 * @jest-environment node
 */
import { GET } from './route'

describe('/api/users', () => {
  it('returns users list', async () => {
    const response = await GET()
    const data = await response.json()
    
    expect(response.status).toBe(200)
    expect(data).toHaveLength(2)
    expect(data[^0]).toHaveProperty('name', 'John Doe')
  })
})
```


### 3.2 POST 요청 테스트

```typescript
// app/api/users/route.ts
export async function POST(request: Request) {
  const body = await request.json()
  const newUser = { id: Date.now(), ...body }
  return NextResponse.json(newUser, { status: 201 })
}

// route.test.ts
import { POST } from './route'

it('creates new user', async () => {
  const mockRequest = new Request('http://localhost:3000/api/users', {
    method: 'POST',
    body: JSON.stringify({ name: 'New User' }),
    headers: { 'Content-Type': 'application/json' }
  })
  
  const response = await POST(mockRequest)
  const data = await response.json()
  
  expect(response.status).toBe(201)
  expect(data.name).toBe('New User')
})
```


## 4. getServerSideProps 테스트

### 4.1 컨텍스트 모킹

```typescript
// pages/user/[id].page.tsx
export const getServerSideProps: GetServerSideProps = async (context) => {
  const { id } = context.params!
  // API 호출 로직...
  return {
    props: {
      userId: id,
      userData: mockUserData
    }
  }
}

// pages/user/[id].test.tsx
import { getServerSideProps } from './[id].page'
import type { GetServerSidePropsContext } from 'next'

describe('getServerSideProps', () => {
  it('returns correct props', async () => {
    const context = {
      params: { id: '123' },
      query: {},
      req: {},
      res: {}
    } as GetServerSidePropsContext
    
    const result = await getServerSideProps(context)
    
    expect(result).toEqual({
      props: {
        userId: '123',
        userData: expect.any(Object)
      }
    })
  })
})
```


## 5. 모킹(Mocking) 기법

### 5.1 외부 API 모킹

```typescript
// lib/api.ts
export const fetchUserData = async (id: string) => {
  const response = await fetch(`/api/users/${id}`)
  return response.json()
}

// __tests__/api.test.ts
import { fetchUserData } from '../lib/api'

// fetch 모킹
global.fetch = jest.fn()

describe('fetchUserData', () => {
  beforeEach(() => {
    (fetch as jest.Mock).mockClear()
  })
  
  it('fetches user data successfully', async () => {
    const mockUser = { id: '1', name: 'John' };
    (fetch as jest.Mock).mockResolvedValue({
      json: () => Promise.resolve(mockUser)
    })
    
    const result = await fetchUserData('1')
    expect(result).toEqual(mockUser)
    expect(fetch).toHaveBeenCalledWith('/api/users/1')
  })
})
```


### 5.2 Next.js 훅 모킹

```typescript
// __tests__/components/Navigation.test.tsx
import { useRouter } from 'next/router'
import { Navigation } from '../components/Navigation'

jest.mock('next/router', () => ({
  useRouter: jest.fn()
}))

describe('Navigation', () => {
  it('highlights current page', () => {
    const mockPush = jest.fn();
    (useRouter as jest.Mock).mockReturnValue({
      pathname: '/about',
      push: mockPush
    })
    
    render(<Navigation />)
    expect(screen.getByText('About')).toHaveClass('active')
  })
})
```


## 6. 환경 변수 테스트 설정

### 6.1 환경 변수 로딩 설정

```typescript
// setupEnv.ts
import { loadEnvConfig } from '@next/env'

export default async (): Promise<void> => {
  const projectDir = process.cwd()
  loadEnvConfig(projectDir)
}

// jest.config.ts에 추가
const config: Config = {
  globalSetup: '<rootDir>/setupEnv.ts',
  // ... 기타 설정
}
```


### 6.2 테스트용 환경 변수

```bash
# .env.test.local
NEXT_PUBLIC_API_URL=http://localhost:3000/api
DATABASE_URL=postgresql://test:test@localhost/testdb
```


## 7. 고급 테스트 패턴

### 7.1 App Router 서버 컴포넌트 테스트

```typescript
// app/users/page.tsx (Server Component)
async function getUsers() {
  // 서버 사이드 데이터 페칭
  return fetch('https://api.example.com/users').then(r => r.json())
}

export default async function UsersPage() {
  const users = await getUsers()
  return <UsersList users={users} />
}

// __tests__/users.test.tsx
/**
 * @jest-environment node
 */
import UsersPage from '../app/users/page'

// MSW 또는 fetch mock 사용
jest.mock('https://api.example.com/users', () => 
  Promise.resolve([{ id: 1, name: 'Test User' }])
)

describe('UsersPage', () => {
  it('renders users from server', async () => {
    const page = await UsersPage()
    // 서버 컴포넌트 테스트 로직
  })
})
```


### 7.2 커스텀 렌더 함수

```typescript
// test-utils.tsx
import { render, RenderOptions } from '@testing-library/react'
import { ThemeProvider } from 'styled-components'

const AllTheProviders = ({ children }: { children: React.ReactNode }) => (
  <ThemeProvider theme={mockTheme}>
    {children}
  </ThemeProvider>
)

const customRender = (ui: React.ReactElement, options?: RenderOptions) =>
  render(ui, { wrapper: AllTheProviders, ...options })

export * from '@testing-library/react'
export { customRender as render }
```


## 8. 실행 및 CI/CD 통합

### 8.1 package.json 스크립트

```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch",
    "test:coverage": "jest --coverage",
    "test:ci": "jest --ci --coverage --watchAll=false"
  }
}
```


### 8.2 GitHub Actions 설정

```yaml
# .github/workflows/test.yml
name: Test
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run test:ci
```


## 9. 베스트 프랙티스

### 9.1 테스트 구조

- **단위 테스트**: 개별 함수/컴포넌트 (`*.test.tsx`)
- **통합 테스트**: 페이지/API 라우트 (`*.integration.test.tsx`)
- **스냅샷 테스트**: UI 변경 감지용


### 9.2 성능 최적화

- `--runInBand`: 직렬 실행으로 메모리 사용량 제어
- `--maxWorkers=4`: 워커 수 제한
- `setupFilesAfterEnv`보다 `globalSetup` 활용으로 초기화 비용 절감


### 9.3 모킹 전략

- **External API**: MSW(Mock Service Worker) 활용
- **Next.js 훅**: `jest.mock()` 사용
- **환경 변수**: `.env.test.local` 파일 분리


## 10. 트러블슈팅

### 10.1 공통 에러 해결

- **"Cannot find module"**: `moduleNameMapper` 경로 별칭 확인
- **"ReferenceError: fetch is not defined"**: Node.js 환경에서 `global.fetch` 모킹 필요
- **환경 변수 undefined**: `loadEnvConfig` 함수로 명시적 로딩


### 10.2 App Router vs Pages Router

- **App Router**: 서버 컴포넌트는 `@jest-environment node` 주석 필요
- **Pages Router**: 기존 방식대로 `getServerSideProps` 직접 테스트 가능


## 결론

Next.js에서 Jest는 프런트엔드부터 API, 서버 사이드 로직까지 **전 영역 테스트**를 지원하는 강력한 도구다. 내장 Rust 컴파일러를 활용하면 **제로 설정**에 가까운 환경에서 TypeScript와 함께 안정적인 테스트 환경을 구축할 수 있다. 컴포넌트 렌더링 테스트, API 라우트 검증, 서버 사이드 함수 모킹까지 체계적으로 접근하여 **코드 품질과 안정성**을 크게 향상시킬 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://qiita.com/kewpie134134/items/4ed8373dd55f3758df60

[^2]: https://zenn.dev/jinku/articles/f7e1279798b879

[^3]: https://dev.to/dforrunner/how-to-unit-test-nextjs-13-app-router-api-routes-with-jest-and-react-testing-library-270a

[^4]: https://www.ajisaba.net/javascript/nextjs14/nextjs_jest_init.html

[^5]: https://blog.logrocket.com/testing-next-js-apps-jest/

[^6]: https://github.com/KennFatt/nextjs-api-routes-testing

[^7]: https://nextjs.org/docs/app/guides/testing/jest

[^8]: https://articles.readytowork.jp/next-js-with-react-testing-library-jest-and-typescript-a6aa11b85434

[^9]: https://qiita.com/tatsuya-miyamoto/items/f99eb069f65b30f2f816

[^10]: https://nextjsjp.org/docs/app/guides/testing/jest

[^11]: https://www.youtube.com/watch?v=AS79oJ3Fcf0

[^12]: https://www.paigeniedringhaus.com/blog/how-to-unit-test-next-js-api-routes-with-typescript

[^13]: https://zenn.dev/nabee/articles/0dc45c5404490c

[^14]: https://www.wisp.blog/blog/how-to-use-jest-with-nextjs-15-a-comprehensive-guide

[^15]: https://zenn.dev/takepepe/articles/testing-gssp-and-api-routes

[^16]: https://zenn.dev/shootacean/articles/how-to-set-up-jest-in-a-nextjs-project

[^17]: https://www.youtube.com/watch?v=XTNqyEBPAFw

[^18]: https://zenn.dev/codeciao/articles/28e0d24d5bf46a

[^19]: https://qiita.com/masakiwakabayashi/items/204ed2b32254bbc9a5c1

[^20]: https://www.tutorialspoint.com/nextjs/nextjs_testing_jest.htm

[^21]: https://nextjs.org/docs/pages/guides/testing/jest

[^22]: https://jestjs.io/docs/snapshot-testing

[^23]: https://jestjs.io/docs/mock-function-api

[^24]: https://qiita.com/JZ8xNeXY/items/ee223161660c5104deb4

[^25]: https://www.manuel-schoebel.com/blog/jest-unit-snapshot-testing-typescript-nextjs

[^26]: https://www.edupen.in/posts/mocking-in-jest-for-nextjs

[^27]: https://qiita.com/shiro4710/items/22b759d2e100b430ca76

[^28]: https://stackoverflow.com/questions/76946742/unable-to-mock-an-exported-named-function-with-jest-react-testing-library-whil

[^29]: https://qiita.com/does_not_exist/items/d681da6a5d0e6f97f270

[^30]: https://qiita.com/PenPe/items/19d2b456dc88b74244d0

[^31]: https://zenn.dev/hamworks/articles/3d623eede50de4

[^32]: https://qiita.com/fussy113/items/adada0e1c515bf9598e0

[^33]: https://jestjs.io/docs/mock-functions

[^34]: https://zenn.dev/loglass/articles/595a91af94ff27

[^35]: https://ma-vericks.com/blog/next-mock-function/

[^36]: https://www.freecodecamp.org/news/how-to-setup-react-testing-library-with-nextjs/

[^37]: https://zuma-lab.com/posts/next-story-book-csf3

[^38]: https://nishinatoshiharu.com/install-jest-in-next/

[^39]: https://www.youtube.com/watch?v=IQtC5Vlt0XI

[^40]: https://nextjs.org/docs/pages/building-your-application/data-fetching/get-server-side-props

[^41]: https://zenn.dev/cybozu_frontend/articles/next-rsc-testing

[^42]: https://github.com/vercel/next.js/discussions/49603

[^43]: https://stackoverflow.com/questions/67747277/how-to-test-next-jss-getserversideprops-with-jest

[^44]: https://www.commte.co.jp/learn-nextjs/Jest

[^45]: https://note.com/readytowork/n/n451ee36a3dc4

[^46]: https://github.com/takefumi-yoshii/testing-nextjs-gssp

[^47]: https://zenn.dev/c_shiraga/articles/056e7196b41c08

[^48]: https://shinagawa-web.com/blogs/nextjs-app-router-testing-setup

[^49]: https://craigmulligan.com/posts/testing/

[^50]: https://www.docswell.com/s/junseinagao/KGXPXN-nextjs-app-router-testing-strategy

[^51]: https://stackoverflow.com/questions/63934104/environment-variables-undefined-in-nextjs-when-running-jest

[^52]: https://blog.shinki.net/posts/nextjs-jest-environment-variable

[^53]: https://zenn.dev/tkengineer/articles/8cf29c7c8131ba

[^54]: https://nextjs.org/docs/13/pages/building-your-application/optimizing/testing

[^55]: https://github.com/vercel/next.js/discussions/16270

[^56]: https://dev.to/shuvotdr/mastering-jest-a-complete-guide-to-testing-nextjs-applications-e85

[^57]: https://github.com/vercel/next.js/discussions/58994

[^58]: https://nextjs.org/docs/pages/guides/environment-variables

[^59]: https://nextjs.org/docs/14/app/building-your-application/data-fetching/patterns

[^60]: https://nextjs-ja-translation-docs.vercel.app/docs/basic-features/environment-variables

[^61]: https://dev-harry-next.com/frontend/jest-in-nextjs-detail

