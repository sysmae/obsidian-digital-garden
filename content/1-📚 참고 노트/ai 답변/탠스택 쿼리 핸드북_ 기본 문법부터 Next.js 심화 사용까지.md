---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 탠스택 쿼리 핸드북: 기본 문법부터 Next.js 심화 사용까지

## 목차

1. [탠스택 쿼리 개요](#1-%ED%83%A0%EC%8A%A4%ED%83%9D-%EC%BF%BC%EB%A6%AC-%EA%B0%9C%EC%9A%94)
2. [핵심 개념과 기본 설정](#2-%ED%95%B5%EC%8B%AC-%EA%B0%9C%EB%85%90%EA%B3%BC-%EA%B8%B0%EB%B3%B8-%EC%84%A4%EC%A0%95)
3. [Query: 데이터 조회](#3-query-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%A1%B0%ED%9A%8C)
4. [Mutation: 데이터 변경](#4-mutation-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B3%80%EA%B2%BD)
5. [캐싱 전략과 최적화](#5-%EC%BA%90%EC%8B%B1-%EC%A0%84%EB%9E%B5%EA%B3%BC-%EC%B5%9C%EC%A0%81%ED%99%94)
6. [Next.js와의 통합](#6-nextjs%EC%99%80%EC%9D%98-%ED%86%B5%ED%95%A9)
7. [고급 기능](#7-%EA%B3%A0%EA%B8%89-%EA%B8%B0%EB%8A%A5)
8. [성능 최적화와 디버깅](#8-%EC%84%B1%EB%8A%A5-%EC%B5%9C%EC%A0%81%ED%99%94%EC%99%80-%EB%94%94%EB%B2%84%EA%B9%85)
9. [테스팅 가이드](#9-%ED%85%8C%EC%8A%A4%ED%8C%85-%EA%B0%80%EC%9D%B4%EB%93%9C)

## 1. 탠스택 쿼리 개요

### 1.1 만들어진 이유

탠스택 쿼리(이전 React Query)는 현대 웹 애플리케이션에서 서버 상태 관리의 복잡성을 해결하기 위해 만들어졌습니다. 전통적인 상태 관리 라이브러리들이 클라이언트 상태 관리에 특화되어 있는 반면, 서버에서 가져온 데이터는 다음과 같은 고유한 특성을 가집니다[^1]:

- **원격성**: 네트워크를 통해 비동기적으로 가져와야 함
- **공유성**: 여러 컴포넌트에서 동일한 데이터가 필요할 수 있음
- **시간 의존성**: 시간이 지나면서 데이터가 오래될 수 있음
- **소유권 부재**: 클라이언트가 데이터를 직접 제어하지 않음


### 1.2 구조와 철학

탠스택 쿼리는 **서버 상태 관리**에 특화된 라이브러리로, 다음과 같은 핵심 원칙을 기반으로 설계되었습니다[^2]:

- **자동 캐싱**: 중복 요청 방지 및 성능 최적화
- **백그라운드 업데이트**: 사용자 경험을 해치지 않으면서 데이터 최신화
- **스마트 리페칭**: 창 포커스, 네트워크 재연결 등의 상황에서 자동 갱신
- **낙관적 업데이트**: 즉각적인 UI 반응을 위한 optimistic updates


## 2. 핵심 개념과 기본 설정

### 2.1 설치 및 기본 설정

```bash
npm install @tanstack/react-query
```

기본 설정은 QueryClient와 QueryClientProvider로 시작합니다[^3]:

```jsx
import {
  useQuery,
  useMutation,
  useQueryClient,
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query'

// QueryClient 생성
const queryClient = new QueryClient()

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <MyApp />
    </QueryClientProvider>
  )
}
```


### 2.2 핵심 개념

탠스택 쿼리는 세 가지 핵심 개념을 기반으로 합니다[^3]:

1. **Queries**: 데이터 조회
2. **Mutations**: 데이터 변경
3. **Query Invalidation**: 쿼리 무효화

## 3. Query: 데이터 조회

### 3.1 기본 문법

`useQuery` 훅은 데이터를 조회하는 기본 방법입니다[^4]:

```jsx
import { useQuery } from '@tanstack/react-query'

function TodoList() {
  const { data, isLoading, error } = useQuery({
    queryKey: ['todos'],
    queryFn: fetchTodos
  })

  if (isLoading) return <div>Loading...</div>
  if (error) return <div>Error: {error.message}</div>

  return (
    <ul>
      {data?.map(todo => (
        <li key={todo.id}>{todo.title}</li>
      ))}
    </ul>
  )
}
```


### 3.2 Query Key와 Query Function

**Query Key**는 쿼리를 고유하게 식별하는 배열입니다[^5]:

```jsx
// 단순한 키
useQuery({ queryKey: ['todos'], queryFn: fetchTodos })

// 매개변수가 있는 키
useQuery({ 
  queryKey: ['todo', todoId], 
  queryFn: () => fetchTodo(todoId) 
})

// 복잡한 키
useQuery({ 
  queryKey: ['todos', { status: 'done', page: 1 }], 
  queryFn: ({ queryKey }) => fetchTodos(queryKey[^1]) 
})
```

**Query Function**은 Promise를 반환하는 모든 함수가 될 수 있습니다[^6]:

```jsx
// fetch 사용
const fetchTodos = async () => {
  const response = await fetch('/api/todos')
  if (!response.ok) {
    throw new Error('Network response was not ok')
  }
  return response.json()
}

// axios 사용
const fetchTodos = () => axios.get('/api/todos').then(res => res.data)

// queryKey 매개변수 활용
const fetchTodo = ({ queryKey }) => {
  const [, todoId] = queryKey
  return fetch(`/api/todos/${todoId}`).then(res => res.json())
}
```


### 3.3 상태 관리

useQuery는 다양한 상태 정보를 제공합니다[^4]:

```jsx
const {
  data,           // 성공적으로 가져온 데이터
  error,          // 에러 객체
  isLoading,      // 첫 번째 로딩 상태
  isFetching,     // 백그라운드에서 데이터를 가져오는 상태
  isError,        // 에러 상태
  isSuccess,      // 성공 상태
  refetch,        // 수동으로 다시 가져오기
  status,         // 'loading' | 'error' | 'success'
  fetchStatus     // 'fetching' | 'paused' | 'idle'
} = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos
})
```


### 3.4 다중 쿼리 처리

여러 쿼리를 동시에 실행할 때는 `useQueries` 훅을 사용합니다[^7][^8]:

```jsx
// 방법 1: 개별 useQuery 사용
const { data: userData } = useQuery({ 
  queryKey: ['users'], 
  queryFn: fetchUsers 
})
const { data: postData } = useQuery({ 
  queryKey: ['posts'], 
  queryFn: fetchPosts 
})

// 방법 2: useQueries 사용
const [users, posts] = useQueries({
  queries: [
    { queryKey: ['users'], queryFn: fetchUsers },
    { queryKey: ['posts'], queryFn: fetchPosts }
  ]
})

// 모든 쿼리가 완료될 때까지 기다리기
const results = useQueries({
  queries: [
    { queryKey: ['users'], queryFn: fetchUsers },
    { queryKey: ['posts'], queryFn: fetchPosts }
  ]
})

const isLoading = results.some(query => query.isLoading)
```


## 4. Mutation: 데이터 변경

### 4.1 기본 문법

`useMutation`은 데이터를 생성, 수정, 삭제할 때 사용합니다[^9][^10]:

```jsx
import { useMutation, useQueryClient } from '@tanstack/react-query'

function AddTodo() {
  const queryClient = useQueryClient()
  
  const mutation = useMutation({
    mutationFn: (newTodo) => {
      return axios.post('/todos', newTodo)
    },
    onSuccess: () => {
      // 성공 시 todos 쿼리 무효화
      queryClient.invalidateQueries({ queryKey: ['todos'] })
    },
  })

  return (
    <div>
      {mutation.isLoading ? (
        'Adding todo...'
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: Date.now(), title: 'Do Laundry' })
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  )
}
```


### 4.2 Mutation 생명주기

Mutation은 다양한 생명주기 콜백을 제공합니다[^9]:

```jsx
useMutation({
  mutationFn: addTodo,
  onMutate: async (variables) => {
    // 뮤테이션이 시작되기 직전에 호출
    // 낙관적 업데이트를 위한 컨텍스트 반환 가능
    await queryClient.cancelQueries({ queryKey: ['todos'] })
    
    const previousTodos = queryClient.getQueryData(['todos'])
    
    queryClient.setQueryData(['todos'], old => [...old, variables])
    
    return { previousTodos }
  },
  onError: (error, variables, context) => {
    // 에러 발생 시 실행
    queryClient.setQueryData(['todos'], context.previousTodos)
  },
  onSuccess: (data, variables, context) => {
    // 성공 시 실행
    console.log('Todo added successfully!')
  },
  onSettled: (data, error, variables, context) => {
    // 성공이든 실패든 항상 실행
    queryClient.invalidateQueries({ queryKey: ['todos'] })
  },
})
```


### 4.3 Query Invalidation

데이터 변경 후 관련 쿼리를 무효화하여 최신 데이터를 가져올 수 있습니다[^11]:

```jsx
// 특정 키로 시작하는 모든 쿼리 무효화
queryClient.invalidateQueries({ queryKey: ['todos'] })

// 정확히 일치하는 쿼리만 무효화
queryClient.invalidateQueries({ 
  queryKey: ['todos'], 
  exact: true 
})

// 조건부 무효화
queryClient.invalidateQueries({
  predicate: (query) =>
    query.queryKey[^0] === 'todos' && query.queryKey[^1]?.version >= 10,
})
```


## 5. 캐싱 전략과 최적화

### 5.1 캐싱 기본 개념

탠스택 쿼리의 캐싱은 두 가지 주요 옵션으로 제어됩니다[^12][^13]:

- **staleTime**: 데이터가 신선한 상태로 유지되는 시간
- **gcTime** (이전 cacheTime): 사용하지 않는 데이터가 가비지 컬렉션되기까지의 시간

```jsx
useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos,
  staleTime: 5 * 60 * 1000, // 5분간 신선한 상태 유지
  gcTime: 10 * 60 * 1000,   // 10분 후 가비지 컬렉션
})
```


### 5.2 백그라운드 리페칭

탠스택 쿼리는 다양한 상황에서 자동으로 데이터를 다시 가져옵니다[^14]:

```jsx
useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos,
  refetchOnWindowFocus: true,    // 창 포커스 시 리페치
  refetchOnReconnect: true,      // 네트워크 재연결 시 리페치
  refetchInterval: 30000,        // 30초마다 자동 리페치
  refetchIntervalInBackground: false, // 백그라운드에서는 리페치 안함
})
```


### 5.3 낙관적 업데이트

사용자 경험 향상을 위한 낙관적 업데이트[^15][^16]:

```jsx
const updateTodoMutation = useMutation({
  mutationFn: updateTodo,
  onMutate: async (newTodo) => {
    // 진행 중인 쿼리 취소
    await queryClient.cancelQueries({ queryKey: ['todos'] })

    // 이전 값 저장
    const previousTodos = queryClient.getQueryData(['todos'])

    // 낙관적 업데이트
    queryClient.setQueryData(['todos'], old =>
      old.map(todo =>
        todo.id === newTodo.id ? { ...todo, ...newTodo } : todo
      )
    )

    // 롤백을 위한 컨텍스트 반환
    return { previousTodos }
  },
  onError: (err, newTodo, context) => {
    // 에러 시 롤백
    queryClient.setQueryData(['todos'], context.previousTodos)
  },
  onSettled: () => {
    // 최종적으로 서버 데이터로 동기화
    queryClient.invalidateQueries({ queryKey: ['todos'] })
  },
})
```


## 6. Next.js와의 통합

### 6.1 App Router와 함께 사용하기

Next.js 13+ App Router와 함께 사용할 때는 다음과 같이 설정합니다[^17][^18]:

```tsx
// app/providers.tsx
'use client'

import {
  isServer,
  QueryClient,
  QueryClientProvider,
} from '@tanstack/react-query'

function makeQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        // SSR과 함께 사용할 때는 staleTime을 0보다 크게 설정
        staleTime: 60 * 1000,
      },
    },
  })
}

let browserQueryClient: QueryClient | undefined = undefined

function getQueryClient() {
  if (isServer) {
    // 서버: 항상 새로운 클라이언트 생성
    return makeQueryClient()
  } else {
    // 브라우저: 기존 클라이언트가 없으면 생성
    if (!browserQueryClient) browserQueryClient = makeQueryClient()
    return browserQueryClient
  }
}

export default function Providers({ children }: { children: React.ReactNode }) {
  const queryClient = getQueryClient()

  return (
    <QueryClientProvider client={queryClient}>
      {children}
    </QueryClientProvider>
  )
}
```

```tsx
// app/layout.tsx
import Providers from './providers'

export default function RootLayout({
  children,
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="en">
      <head />
      <body>
        <Providers>{children}</Providers>
      </body>
    </html>
  )
}
```


### 6.2 서버사이드 데이터 프리페칭

Next.js에서 서버사이드 데이터 프리페칭을 구현합니다[^18]:

```tsx
// app/posts/page.tsx
import {
  dehydrate,
  HydrationBoundary,
  QueryClient,
} from '@tanstack/react-query'
import { getQueryClient } from './get-query-client'
import Posts from './posts'

export default function PostsPage() {
  const queryClient = getQueryClient()

  queryClient.prefetchQuery({
    queryKey: ['posts'],
    queryFn: getPosts,
  })

  return (
    <HydrationBoundary state={dehydrate(queryClient)}>
      <Posts />
    </HydrationBoundary>
  )
}
```

```tsx
// app/posts/posts.tsx
'use client'

import { useQuery } from '@tanstack/react-query'

export default function Posts() {
  // 이 데이터는 이미 서버에서 프리페치되었음
  const { data } = useQuery({ 
    queryKey: ['posts'], 
    queryFn: getPosts 
  })

  return (
    <div>
      {data?.map(post => (
        <div key={post.id}>{post.title}</div>
      ))}
    </div>
  )
}
```


### 6.3 SSR과 하이드레이션

SSR 환경에서 하이드레이션 처리[^19]:

```tsx
// utils/get-query-client.ts
import { QueryClient, defaultShouldDehydrateQuery } from '@tanstack/react-query'

export function makeQueryClient() {
  return new QueryClient({
    defaultOptions: {
      queries: {
        staleTime: 60 * 1000,
      },
      dehydrate: {
        // 대기 중인 쿼리도 포함
        shouldDehydrateQuery: (query) =>
          defaultShouldDehydrateQuery(query) ||
          query.state.status === 'pending',
      },
    },
  })
}
```


## 7. 고급 기능

### 7.1 무한 쿼리 (Infinite Queries)

페이지네이션이나 무한 스크롤 구현을 위한 `useInfiniteQuery`[^20][^21]:

```jsx
import { useInfiniteQuery } from '@tanstack/react-query'

function Projects() {
  const {
    data,
    error,
    fetchNextPage,
    hasNextPage,
    isFetching,
    isFetchingNextPage,
    status,
  } = useInfiniteQuery({
    queryKey: ['projects'],
    queryFn: ({ pageParam = 0 }) =>
      fetch(`/api/projects?cursor=${pageParam}`)
        .then(res => res.json()),
    initialPageParam: 0,
    getNextPageParam: (lastPage, pages) => lastPage.nextCursor,
  })

  return status === 'loading' ? (
    <p>Loading...</p>
  ) : status === 'error' ? (
    <p>Error: {error.message}</p>
  ) : (
    <>
      {data.pages.map((group, i) => (
        <React.Fragment key={i}>
          {group.data.map(project => (
            <p key={project.id}>{project.name}</p>
          ))}
        </React.Fragment>
      ))}
      <div>
        <button
          onClick={() => fetchNextPage()}
          disabled={!hasNextPage || isFetchingNextPage}
        >
          {isFetchingNextPage
            ? 'Loading more...'
            : hasNextPage
            ? 'Load More'
            : 'Nothing more to load'}
        </button>
      </div>
    </>
  )
}
```


### 7.2 Suspense 지원

React Suspense와 함께 사용하기[^22][^23]:

```jsx
import { useSuspenseQuery } from '@tanstack/react-query'

function Profile() {
  // Suspense 경계에서 자동으로 로딩 처리
  const { data } = useSuspenseQuery({
    queryKey: ['profile'],
    queryFn: fetchProfile,
  })

  return <div>Hello {data.name}!</div>
}

function App() {
  return (
    <Suspense fallback={<div>Loading profile...</div>}>
      <Profile />
    </Suspense>
  )
}
```


### 7.3 의존적 쿼리 (Dependent Queries)

한 쿼리의 결과에 따라 다른 쿼리를 실행[^24]:

```jsx
function Profile({ userId }) {
  // 사용자 정보 먼저 가져오기
  const { data: user } = useQuery({
    queryKey: ['user', userId],
    queryFn: () => fetchUser(userId),
  })

  // 사용자 정보가 있을 때만 프로젝트 가져오기
  const { data: projects } = useQuery({
    queryKey: ['projects', user?.id],
    queryFn: () => fetchUserProjects(user.id),
    enabled: !!user?.id,
  })

  return <div>...</div>
}
```


## 8. 성능 최적화와 디버깅

### 8.1 렌더링 최적화

탠스택 쿼리는 자동으로 렌더링 최적화를 수행합니다[^25][^26]:

```jsx
// select 옵션으로 필요한 데이터만 구독
const todoCount = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos,
  select: data => data.length, // 길이만 필요할 때
})

// notifyOnChangeProps로 특정 속성만 감시
const { data, isError } = useQuery({
  queryKey: ['todos'],
  queryFn: fetchTodos,
  notifyOnChangeProps: ['data', 'isError'], // 이 속성들만 변경될 때 리렌더
})
```


### 8.2 DevTools 활용

개발 환경에서 DevTools 설정[^27][^28]:

```jsx
import { ReactQueryDevtools } from '@tanstack/react-query-devtools'

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <MyApp />
      <ReactQueryDevtools initialIsOpen={false} />
    </QueryClientProvider>
  )
}
```

Chrome 확장 프로그램도 사용 가능합니다[^29]. DevTools를 통해 다음을 확인할 수 있습니다:

- 실시간 쿼리 상태 모니터링
- 캐시 내용 검사
- 뮤테이션 추적
- 쿼리 리페치 및 무효화 제어


### 8.3 에러 처리

전역 에러 처리 설정[^30][^31]:

```jsx
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      onError: (error) => {
        // 전역 에러 처리
        console.error('Query Error:', error)
        toast.error(`Something went wrong: ${error.message}`)
      },
      retry: (failureCount, error) => {
        // 특정 에러는 재시도하지 않음
        if (error.status === 404) return false
        return failureCount < 3
      },
    },
    mutations: {
      onError: (error) => {
        console.error('Mutation Error:', error)
        toast.error(`Failed to update: ${error.message}`)
      },
    },
  },
})
```


## 9. 테스팅 가이드

### 9.1 기본 테스팅 설정

탠스택 쿼리 테스팅을 위한 기본 설정[^32][^33]:

```jsx
// test-utils.jsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query'
import { render } from '@testing-library/react'

export const createTestQueryClient = () =>
  new QueryClient({
    defaultOptions: {
      queries: {
        retry: false,
      },
      mutations: {
        retry: false,
      },
    },
  })

export const renderWithClient = (ui) => {
  const testQueryClient = createTestQueryClient()
  const { rerender, ...result } = render(
    <QueryClientProvider client={testQueryClient}>
      {ui}
    </QueryClientProvider>
  )
  return {
    ...result,
    rerender: (rerenderUi) =>
      rerender(
        <QueryClientProvider client={testQueryClient}>
          {rerenderUi}
        </QueryClientProvider>
      ),
  }
}
```


### 9.2 useQuery 훅 테스팅

```jsx
// TodoList.test.jsx
import { screen, waitFor } from '@testing-library/react'
import { rest } from 'msw'
import { setupServer } from 'msw/node'
import { renderWithClient } from './test-utils'
import TodoList from './TodoList'

const server = setupServer(
  rest.get('/api/todos', (req, res, ctx) => {
    return res(
      ctx.json([
        { id: 1, title: 'Learn React Query' },
        { id: 2, title: 'Build awesome apps' },
      ])
    )
  })
)

beforeAll(() => server.listen())
afterEach(() => server.resetHandlers())
afterAll(() => server.close())

test('renders todos', async () => {
  renderWithClient(<TodoList />)
  
  expect(screen.getByText('Loading...')).toBeInTheDocument()
  
  await waitFor(() => {
    expect(screen.getByText('Learn React Query')).toBeInTheDocument()
  })
})
```


### 9.3 Mutation 테스팅

```jsx
test('adds a new todo', async () => {
  const user = userEvent.setup()
  
  server.use(
    rest.post('/api/todos', (req, res, ctx) => {
      return res(ctx.json({ id: 3, title: 'New Todo' }))
    })
  )

  renderWithClient(<AddTodo />)
  
  const input = screen.getByLabelText('Todo title')
  const button = screen.getByRole('button', { name: 'Add Todo' })
  
  await user.type(input, 'New Todo')
  await user.click(button)
  
  await waitFor(() => {
    expect(screen.getByText('Todo added successfully!')).toBeInTheDocument()
  })
})
```

이 핸드북은 탠스택 쿼리의 기본 개념부터 Next.js와의 심화 통합까지 포괄적으로 다룹니다. 각 섹션의 예제 코드를 참고하여 프로젝트에 적용해보시기 바랍니다. 추가적인 고급 기능이나 특정 사용 사례에 대한 질문이 있으시면 언제든지 문의해주세요.

<div style="text-align: center">⁂</div>

[^1]: https://tanstack.com/query/v4/docs/react/overview

[^2]: https://www.atlantbh.com/asynchronous-state-management-with-tanstack-query/

[^3]: https://tanstack.com/query/v5/docs/react/quick-start

[^4]: https://tanstack.com/query/v4/docs/react/guides/queries

[^5]: https://athanasu.hashnode.dev/ls-tanstack-query-concepts

[^6]: https://tanstack.com/query/v5/docs/framework/react/guides/query-functions

[^7]: https://dev.to/calvin087/how-to-handle-multiple-queries-with-react-query-24gn?comments_sort=oldest

[^8]: https://calvintorra.com/blog/how-to-handle-multiple-queries-with-react-query/

[^9]: https://tanstack.com/query/v4/docs/react/guides/mutations

[^10]: https://tanstack.com/query/v5/docs/react/guides/mutations

[^11]: https://tanstack.com/query/v5/docs/react/guides/query-invalidation

[^12]: https://dev.to/thechaudhrysab/simple-understanding-of-gctime-staletime-in-react-query-35be

[^13]: https://stackoverflow.com/questions/72828361/what-are-staletime-and-cachetime-in-react-query

[^14]: https://www.reddit.com/r/nextjs/comments/184a4aw/tanstackquery_refetching_even_when_data_has/

[^15]: https://www.tenxdeveloper.com/blog/optimistic-updates-react-query-guide

[^16]: https://stackoverflow.com/questions/75443779/how-to-effectively-do-optimistic-update-for-deeply-nested-data-in-react-query

[^17]: https://brockherion.dev/blog/posts/setting-up-and-using-react-query-in-nextjs/

[^18]: https://tanstack.com/query/v5/docs/react/guides/advanced-ssr

[^19]: https://blog.logrocket.com/using-tanstack-query-next-js/

[^20]: https://tanstack.com/query/v5/docs/react/guides/infinite-queries

[^21]: https://tanstack.com/query/v4/docs/react/guides/infinite-queries

[^22]: https://tanstack.com/query/v4/docs/react/guides/suspense

[^23]: https://tanstack.com/query/v5/docs/react/guides/suspense

[^24]: https://tanstack.com/query/v5/docs/react/guides/request-waterfalls

[^25]: https://tanstack.com/query/v5/docs/framework/react/guides/render-optimizations

[^26]: https://tkdodo.eu/blog/react-query-render-optimizations

[^27]: https://tanstack.com/query/v5/docs/react/devtools

[^28]: https://tanstack.com/query/v4/docs/react/devtools

[^29]: https://chromewebstore.google.com/detail/tanstack-query-devtools/annajfchloimdhceglpgglpeepfghfai

[^30]: https://github.com/TanStack/query/discussions/6490

[^31]: https://tkdodo.eu/blog/react-query-error-handling

[^32]: https://stackoverflow.com/questions/70654287/is-there-any-solution-to-mock-react-querys-usequery-and-usemutation-while-worki

[^33]: https://tanstack.com/query/v4/docs/react/guides/testing

[^34]: https://www.youtube.com/watch?v=8K1N3fE-cDs

[^35]: https://www.youtube.com/watch?v=e74rB-14-m8

[^36]: https://www.youtube.com/watch?v=3e-higRXoaM

[^37]: https://daily.dev/blog/from-zero-to-hero-usequery-react-tutorial

[^38]: https://www.youtube.com/watch?v=w9r55wd2CAk

[^39]: https://refine.dev/blog/react-query-guide/

[^40]: https://betterstack.com/community/guides/scaling-nodejs/tanstack-for-beginners/

[^41]: https://tanstack.com/query/latest/docs/react/examples/basic

[^42]: https://www.reddit.com/r/reactjs/comments/xu7qrp/the_cumulative_guide_to_react_query_for_beginners/

[^43]: https://tanstack.com/query

[^44]: https://query.gg

[^45]: https://www.reddit.com/r/react/comments/1jm2gxz/understanding_tanstack_query_reactivity_and_best/

[^46]: https://www.reddit.com/r/reactjs/comments/1ef8c5n/can_someone_link_me_to_a_simple_but_good_example/

[^47]: https://www.reddit.com/r/react/comments/1by61t4/react_query_coursetutorial/

[^48]: https://tanstack.com/query/v5/docs/react/reference/useMutationState

[^49]: https://www.dhiwise.com/post/how-to-effectively-manage-data-mutations-with-usemutation-react-query

[^50]: https://www.reddit.com/r/reactjs/comments/17u2nll/tanstack_react_query_invalidatequeries/

[^51]: https://blog.logrocket.com/deep-dive-mutations-tanstack-query/

[^52]: https://tkdodo.eu/blog/mastering-mutations-in-react-query

[^53]: https://tanstack.com/query/v4/docs/react/guides/query-invalidation

[^54]: https://www.youtube.com/watch?v=33YmNXZFZIY

[^55]: https://dev.to/delisrey/fetching-mutating-data-with-react-query-395f

[^56]: https://tanstack.com/query/v3/docs/framework/react/guides/invalidations-from-mutations

[^57]: https://stackoverflow.com/questions/75222161/how-to-return-data-from-tanstack-react-query-usemutation

[^58]: https://mitchellhein25.github.io/CSharp-and-React-Insights/2023/06/26/React-Query-useMutation-hook.html

[^59]: https://github.com/TanStack/query/discussions/3169

[^60]: https://tanstack.com/query/v4/docs/react/reference/useMutation

[^61]: https://github.com/TkDodo/blog-comments/discussions/70

[^62]: https://tkdodo.eu/blog/automatic-query-invalidation-after-mutations

[^63]: https://tanstack.com/query/v5/docs/framework/react/reference/useQuery

[^64]: https://blog.delpuppo.net/react-query-usemutation

[^65]: https://www.reddit.com/r/nextjs/comments/1fzb1pu/nextjs_app_router_server_actions_tanstack_query/

[^66]: https://blog.gogrow.dev/setting-up-react-query-in-your-next-js-13-app-e8edea0d20cc

[^67]: https://dev.to/thekbbohara/tanstack-query-101-with-nextjs-ale

[^68]: https://github.com/wpcodevo/nextjs13-react-query

[^69]: https://github.com/TanStack/query/discussions/5725

[^70]: https://tanstack.com/query/v4/docs/react/guides/ssr

[^71]: https://supabase.com/blog/react-query-nextjs-app-router-cache-helpers

[^72]: https://www.youtube.com/watch?v=Z4L_UE0hVmo

[^73]: https://www.franciscomoretti.com/blog/how-to-use-react-query-in-next-js-client-components

[^74]: https://faun.pub/from-setup-to-execution-the-most-accurate-tanstack-query-and-next-js-14-integration-guide-8e5aff6ee8ba

[^75]: https://dev.to/rayenmabrouk/advanced-server-rendering-react-query-with-nextjs-app-router-bi7

[^76]: https://www.youtube.com/watch?v=G0BmM-L5FoE

[^77]: https://www.reddit.com/r/nextjs/comments/1ftbpwa/tanstack_query/

[^78]: https://www.youtube.com/watch?v=Ji9OvOtAWBk

[^79]: https://tanstack.com/query/v5/docs/framework/react/examples/nextjs

[^80]: https://tanstack.com/query/v4/docs/framework/react/guides/ssr

[^81]: https://stackoverflow.com/questions/69982787/refetchintervalinbackground-does-not-stop-a-query-with-refetchinterval-from-refe

[^82]: https://stackoverflow.com/questions/70238846/react-query-query-is-not-using-cache

[^83]: https://www.reddit.com/r/reactjs/comments/1lnlzcs/react_query_optimistic_updates_of_relations/

[^84]: https://wagmi.sh/react/guides/tanstack-query

[^85]: https://tanstack.com/query/v4/docs/framework/react/examples/auto-refetching

[^86]: https://stackoverflow.com/questions/74671735/optimistic-updates-with-react-query-trpc

[^87]: https://tanstack.com/query/v4/docs/react/reference/useQuery

[^88]: https://tanstack.com/query/v5/docs/react/guides/caching

[^89]: https://tkdodo.eu/blog/concurrent-optimistic-updates-in-react-query

[^90]: https://tanstack.com/query/v4/docs/vue/guides/background-fetching-indicators

[^91]: https://tanstack.com/query/v4/docs/react/guides/caching

[^92]: https://react.dev/reference/react/useOptimistic

[^93]: https://tanstack.com/query/v4/docs/react/guides/background-fetching-indicators

[^94]: https://github.com/TanStack/query/discussions/7113

[^95]: https://tanstack.com/query/v4/docs/framework/react/guides/optimistic-updates

[^96]: https://javascript.plainenglish.io/pagination-infinite-query-and-mutations-using-react-query-2b41040f1fe3

[^97]: https://tanstack.com/query/v4/docs/framework/react/reference/useInfiniteQuery

[^98]: https://www.teemutaskula.com/blog/exploring-query-suspense

[^99]: https://www.youtube.com/watch?v=aMfBeXD_rnE

[^100]: https://dev.to/dravidjones28/how-to-implement-infinite-queries-in-react-query-22e6

[^101]: https://dev.to/delisrey/infinite-list-loading-with-react-query-useinfinitequery-hook--19i

[^102]: https://www.youtube.com/watch?v=Ns5wBs-HAzc

[^103]: https://www.reddit.com/r/nextjs/comments/1dl2rs7/ispending_of_tanstack_query_or_react_suspense/

[^104]: https://tanstack.com/query/v4/docs/react/guides/paginated-queries

[^105]: https://pieces.app/blog/how-to-implement-react-infinite-scrolling-with-react-query-v5

[^106]: https://github.com/TanStack/query/discussions/6206

[^107]: https://www.reddit.com/r/reactjs/comments/1fg0a3h/tanstack_infinite_query_data_becomes_undefined/

[^108]: https://trpc.io/docs/client/react/useInfiniteQuery

[^109]: https://www.youtube.com/watch?v=JVcZLEFaC2M

[^110]: https://stackoverflow.com/questions/62129771/react-query-how-to-paginate-with-useinfinitequery

[^111]: https://dev.to/thechaudhrysab/tldr-suspense-in-react-query-5f4c

[^112]: https://tanstack.com/db/latest/docs/error-handling

[^113]: https://tanstack.com/query/latest/docs/framework/angular/devtools

