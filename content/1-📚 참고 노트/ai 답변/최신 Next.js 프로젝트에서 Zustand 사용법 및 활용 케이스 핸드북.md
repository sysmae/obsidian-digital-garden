---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 최신 Next.js 프로젝트에서 Zustand 사용법 및 활용 케이스 핸드북

## 핵심 요약

Zustand는 **Next.js 15와 완벽 호환**되는 경량 상태 관리 라이브러리로, **App Router**와 **Server Components** 환경에서 Provider 없이도 강력한 전역 상태 관리를 제공한다. 특히 **SSR 안전성**, **TypeScript 완벽 지원**, **persist 미들웨어**를 통한 상태 영속화가 특징이며, E-commerce 카트부터 복잡한 사용자 인증까지 다양한 실제 사용 사례에서 검증되었다.

## 1. Next.js 15 환경에서의 기본 설정

### 1.1 설치 및 초기 설정

```bash
npm install zustand
# 또는
yarn add zustand
```


### 1.2 기본 스토어 생성 (App Router 전용)

```typescript
// stores/counter-store.ts
import { create } from 'zustand'

interface CounterState {
  count: number
  increment: () => void
  decrement: () => void
  reset: () => void
}

export const useCounterStore = create<CounterState>((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 })
}))
```


### 1.3 Client Component에서 사용

```typescript
// components/Counter.tsx
'use client'

import { useCounterStore } from '@/stores/counter-store'

export default function Counter() {
  const { count, increment, decrement } = useCounterStore()

  return (
    <div className="flex flex-col items-center gap-4">
      <h2 className="text-2xl font-bold">Count: {count}</h2>
      <div className="flex gap-2">
        <button onClick={increment} className="px-4 py-2 bg-blue-500 text-white rounded">
          Increment
        </button>
        <button onClick={decrement} className="px-4 py-2 bg-red-500 text-white rounded">
          Decrement
        </button>
      </div>
    </div>
  )
}
```


## 2. SSR 및 하이드레이션 안전 패턴

### 2.1 Context Provider 패턴 (권장)

```typescript
// stores/counter-store.ts
import { createStore } from 'zustand/vanilla'

export type CounterState = {
  count: number
}

export type CounterActions = {
  decrementCount: () => void
  incrementCount: () => void
}

export type CounterStore = CounterState & CounterActions

export const createCounterStore = (
  initState: CounterState = { count: 0 }
) => {
  return createStore<CounterStore>()((set) => ({
    ...initState,
    decrementCount: () => set((state) => ({ count: state.count - 1 })),
    incrementCount: () => set((state) => ({ count: state.count + 1 }))
  }))
}
```

```typescript
// providers/counter-store-provider.tsx
'use client'

import { type ReactNode, createContext, useRef, useContext } from 'react'
import { useStore } from 'zustand'
import { type CounterStore, createCounterStore } from '@/stores/counter-store'

export type CounterStoreApi = ReturnType<typeof createCounterStore>

export const CounterStoreContext = createContext<CounterStoreApi | undefined>(
  undefined
)

export const CounterStoreProvider = ({ children }: { children: ReactNode }) => {
  const storeRef = useRef<CounterStoreApi | null>(null)
  
  if (storeRef.current === null) {
    storeRef.current = createCounterStore()
  }

  return (
    <CounterStoreContext.Provider value={storeRef.current}>
      {children}
    </CounterStoreContext.Provider>
  )
}

export const useCounterStore = <T,>(
  selector: (store: CounterStore) => T
): T => {
  const counterStoreContext = useContext(CounterStoreContext)
  if (!counterStoreContext) {
    throw new Error('useCounterStore must be used within CounterStoreProvider')
  }
  return useStore(counterStoreContext, selector)
}
```


### 2.2 Layout에서 Provider 설정

```typescript
// app/layout.tsx
import { CounterStoreProvider } from '@/providers/counter-store-provider'

export default function RootLayout({
  children
}: {
  children: React.ReactNode
}) {
  return (
    <html lang="ko">
      <body>
        <CounterStoreProvider>
          {children}
        </CounterStoreProvider>
      </body>
    </html>
  )
}
```


## 3. 고급 미들웨어 활용

### 3.1 Persist 미들웨어 (상태 영속화)

```typescript
// stores/auth-store.ts
import { create } from 'zustand'
import { persist, createJSONStorage } from 'zustand/middleware'

interface AuthUser {
  id: string
  name: string
  email: string
}

interface AuthState {
  user: AuthUser | null
  isAuthenticated: boolean
  login: (user: AuthUser) => void
  logout: () => void
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      isAuthenticated: false,
      login: (user) => set({ user, isAuthenticated: true }),
      logout: () => set({ user: null, isAuthenticated: false })
    }),
    {
      name: 'auth-storage',
      storage: createJSONStorage(() => localStorage),
      // 하이드레이션 에러 방지
      skipHydration: true
    }
  )
)
```


### 3.2 하이드레이션 에러 해결

```typescript
// components/AuthGuard.tsx
'use client'

import { useEffect, useState } from 'react'
import { useAuthStore } from '@/stores/auth-store'

export default function AuthGuard({ children }: { children: React.ReactNode }) {
  const [hasHydrated, setHasHydrated] = useState(false)
  
  useEffect(() => {
    useAuthStore.persist.rehydrate()
    setHasHydrated(true)
  }, [])

  if (!hasHydrated) {
    return <div>Loading...</div>
  }

  return <>{children}</>
}
```


### 3.3 DevTools 미들웨어

```typescript
// stores/debug-store.ts
import { create } from 'zustand'
import { devtools } from 'zustand/middleware'

interface DebugState {
  items: string[]
  addItem: (item: string) => void
  removeItem: (index: number) => void
}

export const useDebugStore = create<DebugState>()(
  devtools(
    (set) => ({
      items: [],
      addItem: (item) => set(
        (state) => ({ items: [...state.items, item] }),
        false,
        'addItem'
      ),
      removeItem: (index) => set(
        (state) => ({ items: state.items.filter((_, i) => i !== index) }),
        false,
        'removeItem'
      )
    }),
    { name: 'debug-store' }
  )
)
```


## 4. 실제 활용 사례

### 4.1 E-commerce 쇼핑카트

```typescript
// stores/cart-store.ts
import { create } from 'zustand'
import { persist } from 'zustand/middleware'

interface CartItem {
  id: string
  title: string
  price: number
  quantity: number
  image: string
}

interface CartState {
  items: CartItem[]
  addItem: (item: Omit<CartItem, 'quantity'>) => void
  updateQuantity: (id: string, quantity: number) => void
  removeItem: (id: string) => void
  clearCart: () => void
  getTotalPrice: () => number
  getItemCount: () => number
}

export const useCartStore = create<CartState>()(
  persist(
    (set, get) => ({
      items: [],
      
      addItem: (newItem) => set((state) => {
        const existingItem = state.items.find(item => item.id === newItem.id)
        if (existingItem) {
          return {
            items: state.items.map(item =>
              item.id === newItem.id
                ? { ...item, quantity: item.quantity + 1 }
                : item
            )
          }
        }
        return { items: [...state.items, { ...newItem, quantity: 1 }] }
      }),
      
      updateQuantity: (id, quantity) => set((state) => ({
        items: quantity <= 0
          ? state.items.filter(item => item.id !== id)
          : state.items.map(item =>
              item.id === id ? { ...item, quantity } : item
            )
      })),
      
      removeItem: (id) => set((state) => ({
        items: state.items.filter(item => item.id !== id)
      })),
      
      clearCart: () => set({ items: [] }),
      
      getTotalPrice: () => {
        return get().items.reduce((total, item) => total + (item.price * item.quantity), 0)
      },
      
      getItemCount: () => {
        return get().items.reduce((count, item) => count + item.quantity, 0)
      }
    }),
    {
      name: 'cart-storage'
    }
  )
)
```


### 4.2 모달 상태 관리

```typescript
// stores/modal-store.ts
import { create } from 'zustand'

type ModalType = 'login' | 'signup' | 'profile' | 'cart'

interface ModalState {
  type: ModalType | null
  isOpen: boolean
  data?: any
  openModal: (type: ModalType, data?: any) => void
  closeModal: () => void
}

export const useModalStore = create<ModalState>((set) => ({
  type: null,
  isOpen: false,
  data: undefined,
  
  openModal: (type, data) => set({ type, isOpen: true, data }),
  closeModal: () => set({ type: null, isOpen: false, data: undefined })
}))
```


### 4.3 슬라이스 패턴 (대규모 앱)

```typescript
// stores/slices/user-slice.ts
export interface UserSlice {
  user: User | null
  setUser: (user: User) => void
  clearUser: () => void
}

export const createUserSlice: StateCreator<
  UserSlice & CartSlice & ThemeSlice,
  [],
  [],
  UserSlice
> = (set) => ({
  user: null,
  setUser: (user) => set({ user }),
  clearUser: () => set({ user: null })
})

// stores/slices/cart-slice.ts
export const createCartSlice: StateCreator<
  UserSlice & CartSlice & ThemeSlice,
  [],
  [],
  CartSlice
> = (set, get) => ({
  items: [],
  addItem: (item) => {
    // 카트 로직
  }
})

// stores/app-store.ts
import { create } from 'zustand'
import { createUserSlice } from './slices/user-slice'
import { createCartSlice } from './slices/cart-slice'

export const useAppStore = create<UserSlice & CartSlice>()((...a) => ({
  ...createUserSlice(...a),
  ...createCartSlice(...a)
}))
```


## 5. 성능 최적화 기법

### 5.1 선택적 구독

```typescript
// 🚫 잘못된 방법 - 전체 스토어 구독
const store = useCartStore()

// ✅ 올바른 방법 - 필요한 부분만 구독
const itemCount = useCartStore(state => state.getItemCount())
const totalPrice = useCartStore(state => state.getTotalPrice())
```


### 5.2 useShallow를 통한 객체 구독 최적화

```typescript
import { useShallow } from 'zustand/react/shallow'

// ✅ shallow comparison으로 불필요한 리렌더링 방지
const { items, addItem, removeItem } = useCartStore(
  useShallow(state => ({
    items: state.items,
    addItem: state.addItem,
    removeItem: state.removeItem
  }))
)
```


### 5.3 메모이제이션과 결합

```typescript
const CartSummary = memo(() => {
  const totalPrice = useCartStore(state => state.getTotalPrice())
  const itemCount = useCartStore(state => state.getItemCount())
  
  return (
    <div>
      <p>Items: {itemCount}</p>
      <p>Total: ${totalPrice}</p>
    </div>
  )
})
```


## 6. 트러블슈팅 가이드

### 6.1 하이드레이션 불일치 해결

```typescript
// 문제: "Text content does not match server-rendered HTML"
// 해결책 1: skipHydration 사용
export const useStore = create(
  persist(
    (set) => ({ ... }),
    {
      name: 'storage-key',
      skipHydration: true
    }
  )
)

// 해결책 2: 하이드레이션 상태 추적
const [hasHydrated, setHasHydrated] = useState(false)

useEffect(() => {
  useStore.persist.rehydrate()
  setHasHydrated(true)
}, [])

if (!hasHydrated) return <div>Loading...</div>
```


### 6.2 Server Components 사용 제한

```typescript
// 🚫 Server Component에서 사용 불가
export default function ServerPage() {
  const count = useCounterStore(state => state.count) // 에러!
  return <div>{count}</div>
}

// ✅ Client Component로 분리
'use client'
export default function ClientCounter() {
  const count = useCounterStore(state => state.count)
  return <div>{count}</div>
}
```


### 6.3 함수 undefined 오류 해결

```typescript
// 문제: persist 사용 시 함수가 undefined
interface StoreState {
  count: number
  // 함수도 타입에 명시적으로 포함
  increment: () => void
  decrement: () => void
}

export const useStore = create<StoreState>()(
  persist(
    (set) => ({
      count: 0,
      increment: () => set(state => ({ count: state.count + 1 })),
      decrement: () => set(state => ({ count: state.count - 1 }))
    }),
    {
      name: 'counter-storage',
      // 함수는 저장하지 않도록 설정
      partialize: (state) => ({ count: state.count })
    }
  )
)
```


## 7. 모범 사례 및 패턴

### 7.1 타입 안전성 보장

```typescript
// 엄격한 타입 정의
interface StrictCartState {
  readonly items: readonly CartItem[]
  readonly isLoading: boolean
  readonly error: string | null
}

interface StrictCartActions {
  addItem: (item: Omit<CartItem, 'id'>) => Promise<void>
  removeItem: (id: string) => Promise<void>
  clearError: () => void
}

type StrictCartStore = StrictCartState & StrictCartActions
```


### 7.2 에러 핸들링 패턴

```typescript
export const useApiStore = create<ApiState>((set, get) => ({
  data: null,
  isLoading: false,
  error: null,
  
  fetchData: async () => {
    set({ isLoading: true, error: null })
    try {
      const response = await fetch('/api/data')
      if (!response.ok) throw new Error('Failed to fetch')
      const data = await response.json()
      set({ data, isLoading: false })
    } catch (error) {
      set({ 
        error: error instanceof Error ? error.message : 'Unknown error',
        isLoading: false 
      })
    }
  }
}))
```


### 7.3 테스트 친화적 설계

```typescript
// 테스트를 위한 초기화 함수
export const createTestStore = (initialState?: Partial<CartState>) => {
  return create<CartStore>()((set, get) => ({
    items: [],
    ...initialState,
    addItem: (item) => { /* 구현 */ },
    // ... 기타 액션들
  }))
}

// 테스트 코드
describe('CartStore', () => {
  it('should add item to cart', () => {
    const store = createTestStore({ items: [] })
    // 테스트 로직
  })
})
```


## 8. Next.js 15 특화 고려사항

### 8.1 App Router 완전 활용

```typescript
// app/cart/layout.tsx - 카트 전용 레이아웃
export default function CartLayout({
  children
}: {
  children: React.ReactNode
}) {
  return (
    <CartStoreProvider>
      <div className="cart-layout">
        {children}
      </div>
    </CartStoreProvider>
  )
}
```


### 8.2 Server Actions와의 연동

```typescript
// app/actions/cart-actions.ts
'use server'

export async function syncCartToServer(cartItems: CartItem[]) {
  // 서버에 카트 동기화
  const response = await fetch('/api/cart/sync', {
    method: 'POST',
    body: JSON.stringify({ items: cartItems })
  })
  return response.json()
}

// Client에서 사용
const syncCart = async () => {
  const items = useCartStore.getState().items
  await syncCartToServer(items)
}
```


### 8.3 미래 대응성

```typescript
// React 19 Concurrent Features 대응
export const useConcurrentCartStore = create<CartState>((set) => ({
  items: [],
  addItem: (item) => {
    // startTransition 등을 활용한 비동기 상태 업데이트
    set((state) => ({ items: [...state.items, item] }))
  }
}))
```


## 9. 실제 프로덕션 사례 분석

| 프로젝트 | 활용 분야 | 특징 | 규모 |
| :-- | :-- | :-- | :-- |
| Cal.com[^1] | 일정 관리, 사용자 설정 | tRPC, Prisma와 통합 | 대규모 |
| Documenso[^1] | 문서 상태, 서명 플로우 | Auth.js, Stripe 연동 | 중규모 |
| TypeHero[^1] | 코드 챌린지, 진행상황 | 실시간 상태 동기화 | 중규모 |
| Unkey[^1] | API 키 관리, 대시보드 | Clerk, Planetscale 통합 | 대규모 |

## 10. 결론 및 권장사항

### 10.1 언제 Zustand를 선택할까?

- **중소규모 Next.js 앱**: Context API보다 간단하면서도 Redux보다 가벼운 솔루션
- **TypeScript 프로젝트**: 뛰어난 타입 추론과 안전성
- **App Router 환경**: SSR/SSG와의 완벽한 호환성
- **팀 개발**: 학습 곡선이 낮아 온보딩이 쉬움


### 10.2 2025년 트렌드 전망

Zustand는 **Redux의 복잡성 없이도 강력한 상태 관리**를 제공하며, **AI 개발 도구와의 호환성**[^2]도 우수하다. Next.js 15의 **Server Components**와 **App Router** 환경에서 **표준 상태 관리 솔루션**으로 자리잡을 것으로 예상된다[^3][^4].

### 10.3 최종 권장사항

- **소규모 프로젝트**: 기본 Zustand + persist 미들웨어
- **중규모 프로젝트**: 슬라이스 패턴 + Context Provider
- **대규모 프로젝트**: 엄격한 타입 정의 + 테스트 친화적 설계
- **E-commerce**: 카트, 사용자 인증, 위시리스트 통합 관리
- **대시보드/관리도구**: 모달, 사이드바, 테마 등 UI 상태 중심 관리

Zustand는 **단순함과 강력함의 완벽한 균형**을 제공하며, Next.js 15 환경에서 **현대적이고 확장 가능한 상태 관리 아키텍처**를 구축하는 데 이상적인 선택이다.

<div style="text-align: center">⁂</div>

[^1]: https://dev.to/datarockets/real-world-open-source-projects-built-with-nextjs-14-and-app-router-i1n

[^2]: https://qiita.com/chamudi/items/59122397a593096e1660

[^3]: https://www.linkedin.com/pulse/state-management-2025-redux-zustand-react-query-sbtqc

[^4]: https://dev.to/hijazi313/state-management-in-2025-when-to-use-context-redux-zustand-or-jotai-2d2k

[^5]: https://www.dimasroger.com/blog/how-to-use-zustand-with-next-js-15

[^6]: https://t-cr.jp/article/rt11it7sn7tnbjv

[^7]: https://stackoverflow.com/questions/78886727/hydration-is-not-happening-in-next-js-with-zustand

[^8]: https://dev.to/mrsupercraft/mastering-state-management-with-zustand-in-nextjs-and-react-1g26

[^9]: https://www.reddit.com/r/nextjs/comments/1ah4oc4/zustand_with_rsc_in_nextjs_14_and_app_router/

[^10]: https://github.com/pmndrs/zustand/discussions/2788

[^11]: https://www.linkedin.com/pulse/simplifying-state-management-nextjs-zustand-guide-aakarshit-giri-ch4nc

[^12]: https://zenn.dev/shoji9x9/articles/9fdbc8a9ff1900

[^13]: https://www.reddit.com/r/nextjs/comments/18dyj3n/whats_the_optimal_approach_for_persisting_data/

[^14]: https://zustand.docs.pmnd.rs/guides/nextjs

[^15]: https://github.com/koalamango/next-zustand

[^16]: https://t-cr.jp/article/z5d1ite8bvemcxt

[^17]: https://dev.to/blamsa0mine/building-a-modern-e-commerce-app-with-nextjs-15-zustand-and-typescript-18i5

[^18]: https://zenn.dev/shoji9x9/articles/02ce2c2e26ed3f

[^19]: https://zustand.docs.pmnd.rs/guides/ssr-and-hydration

[^20]: https://www.youtube.com/watch?v=6wfp8tFQiGI

[^21]: https://kt-tech.blog/blogs/9nped3b921

[^22]: https://github.com/rafaelrcamargo/nextjs-zustand-setup-ssr

[^23]: https://qiita.com/nft/items/07b2d22bf3412d16f342

[^24]: https://x.com/fuyu77/status/1660980414734020608

[^25]: https://www.youtube.com/watch?v=PXuPvbUwEqU

[^26]: https://dev.to/abdulsamad/how-to-use-zustands-persist-middleware-in-nextjs-4lb5

[^27]: https://engineering.atlys.com/a-slice-based-zustand-store-for-next-js-14-and-typescript-6b92385a48f5

[^28]: https://zenn.dev/h_tatsuru/articles/f7ad45263fe181

[^29]: https://codersbucket.com/blog/best-way-to-manage-state-in-next-js/

[^30]: https://www.youtube.com/watch?v=vMRkTBM8gJs

[^31]: https://tasukehub.com/articles/typescript-zustand-state-management-guide/

[^32]: https://zenn.dev/dai_shi/articles/af7d1dfe17a35e

[^33]: https://tech.furyu.jp/entry/2025/02/22/093000

[^34]: https://github.com/pmndrs/zustand/discussions/2326

[^35]: https://github.com/pmndrs/zustand/discussions/2476

[^36]: https://github.com/zeevo/next-zustand-starter/

[^37]: https://www.youtube.com/watch?v=U29-3Y4licQ

[^38]: https://dev.classmethod.jp/articles/zustand_middleware/

[^39]: https://hackernoon.com/how-to-build-a-shopping-cart-with-nextjs-and-zustand-state-management-with-typescript

[^40]: https://refine.dev/blog/zustand-react-state/

[^41]: https://www.youtube.com/watch?v=4Zvr3w9TakQ

[^42]: https://www.reddit.com/r/nextjs/comments/1d126ps/app_vs_page_router_for_real_world_production_apps/

[^43]: https://qiita.com/BNR-Long/items/4e8c1ba369cece115896

[^44]: https://www.youtube.com/watch?v=4NMOr3MYIDM

[^45]: https://zenn.dev/web_life_ch/articles/cb49be168ede98

[^46]: https://github.com/nhsz/nextjs-ecommerce-poc

[^47]: https://github.com/pmndrs/zustand/discussions/2496

[^48]: https://github.com/Rabin-Osti/Nextjs14-Ecommerce-With-Zustand

[^49]: https://github.com/pmndrs/zustand/discussions/2897

[^50]: https://nextjs.org/blog/next-15

[^51]: https://dev.to/saidmounaim/pure-casual-nextjs-typescript-shadcnui-zustand-e-commerce-2foj

