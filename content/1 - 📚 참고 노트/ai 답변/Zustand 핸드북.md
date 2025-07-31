---
publish:  true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Zustand 핸드북

## 핵심 요약

Zustand는 **경량·고성능·확장성**을 모두 갖춘 React 상태 관리 라이브러리로, 보일러플레이트가 거의 없고 훅 기반 API를 통해 전역 상태를 간편하게 정의·접근·갱신할 수 있다. Context나 Redux에 비해 학습 곡선이 완만하며, 구독 선택자(selector)를 통해 불필요한 리렌더링을 최소화할 수 있다.

## 1. 탄생 배경 및 철학

- **문제의식**: Redux의 복잡한 액션/리듀서/스토어 설계와 과도한 보일러플레이트[^1]
- **설계 목표**

1. *최소한의 코드*로 *최대한의 기능* 제공
2. **훅(React Hooks)** 기반으로 컴포넌트와 자연스럽게 통합
3. Context API 의 단점(전역 리렌더링, 중첩된 Provider) 해소
- **이름의 유래**: 독일어로 “state”를 뜻함


## 2. 아키텍처 개요

1. **스토어(Store)**
    - `create` 함수로 정의
    - 내부에 상태(state) 및 갱신 메서드(actions)를 포함
2. **구독(Subscription)**
    - 소비 컴포넌트는 `useStore(selector)` 훅으로 특정 상태만 구독
    - 선택한 상태 변화 시에만 리렌더링
3. **미들웨어 지원**
    - 로깅, 퍼시스턴스, 리덕스 DevTools 연동 가능
4. **슬라이스 패턴**
    - 대규모 앱 시 기능별로 스토어 분할 후 결합하여 사용

## 3. 설치 및 기본 사용법

```bash
npm install zustand
# 또는
yarn add zustand
```


### 3.1 스토어 정의

```javascript
import { create } from 'zustand';

const useBearStore = create((set) => ({
  bears: 0,                                  // state
  increase: () => set((s) => ({ bears: s.bears + 1 })),  
  reset: () => set({ bears: 0 }),
}));
```


### 3.2 컴포넌트에서 사용

```javascript
function BearCounter() {
  const bears = useBearStore((state) => state.bears);
  return <h1>{bears} bears around here…</h1>;
}

function Controls() {
  const increase = useBearStore((state) => state.increase);
  return <button onClick={increase}>Add Bear</button>;
}
```


## 4. 고급 패턴과 모범 사례

### 4.1 슬라이스 패턴으로 스토어 분할

```javascript
// fishSlice.js
export const createFishSlice = (set) => ({
  fishes: 0,
  addFish: () => set((s) => ({ fishes: s.fishes + 1 })),
});

// bearSlice.js
export const createBearSlice = (set) => ({
  bears: 0,
  addBear: () => set((s) => ({ bears: s.bears + 1 })),
});

// store.js
import { create } from 'zustand';
import { createFishSlice } from './fishSlice';
import { createBearSlice } from './bearSlice';

const useStore = create((set, get) => ({
  ...createFishSlice(set, get),
  ...createBearSlice(set, get),
}));
```


### 4.2 리덕스 스타일 `dispatch` 사용

```javascript
const types = { INC: 'INC', DEC: 'DEC' };
const reducer = (state, { type, by = 1 }) => {
  switch (type) {
    case types.INC: return { count: state.count + by };
    case types.DEC: return { count: state.count - by };
    default: return state;
  }
};

const useStore = create((set) => ({
  count: 0,
  dispatch: (action) => set((s) => reducer(s, action)),
}));
```


### 4.3 비동기 처리

```javascript
const useStore = create((set) => ({
  data: null,
  fetchData: async () => {
    const res = await fetch('/api/data');
    const json = await res.json();
    set({ data: json });
  },
}));
```


### 4.4 퍼포먼스 최적화

- **선택적 구독**: `useStore(state => state.foo)`
- **불변성 유지**: `set((s) => ({…}))` 패턴
- **구독 해제**: 외부 구독 시 `subscribe`와 `useEffect` 조합


## 5. DevTools 및 미들웨어

- **Redux DevTools** 연동: `import { devtools } from 'zustand/middleware'`
- **퍼시스턴스**: `persist` 미들웨어로 상태 로컬·세션 저장
- **로깅**: `import { subscribeWithSelector } from 'zustand/middleware'` 등


## 6. 사용 시 고려사항

1. **작은 앱**: 간단한 전역 상태만 필요 → Zustand 단독 사용 추천
2. **대규모 앱**: 슬라이스 패턴 + 미들웨어 조합으로 확장성 확보
3. **서버 상태**: React Query 등과 분리하여 관리
4. **테스트 용이성**: 순수 함수 형태로 스토어 정의 → 모킹·단위 테스트 수월

## 7. 결론

Zustand는 **간결하면서도 강력한** React 상태 관리 도구로, **Minimal API**와 **훅 기반 설계**를 통해 생산성을 높인다. 중소 규모 프로젝트부터 대규모 애플리케이션까지, **슬라이스 패턴**과 **미들웨어**를 활용해 유연하게 확장할 수 있어, 현대 React 개발 환경에서 훌륭한 선택지가 된다.

<div style="text-align: center">⁂</div>

[^1]: https://zustand.docs.pmnd.rs/getting-started/introduction

[^2]: https://www.pedroalonso.net/blog/react-state-management-zustand/

[^3]: https://blog.logrocket.com/zustand-adoption-guide/

[^4]: https://zenn.dev/b13o/articles/tutorial-zustand

[^5]: https://www.issoh.co.jp/tech/details/7615/

[^6]: https://dev.to/michaeljota/zustand-entityadapter-an-entityadapter-example-for-zustand-cd2

[^7]: https://zenn.dev/yamabiko/articles/zutand-context

[^8]: https://zustand.docs.pmnd.rs/guides/flux-inspired-practice

[^9]: https://www.youtube.com/watch?v=_ngCLZ5Iz-0

[^10]: https://envader.plus/article/524

[^11]: https://dev.to/neetigyachahar/architecture-guide-building-scalable-react-or-react-native-apps-with-zustand-react-query-1nn4

[^12]: https://qiita.com/s_taro/items/0c16f077d843ac1a78fa

[^13]: https://frontendmasters.com/blog/introducing-zustand/

[^14]: https://zenn.dev/yoshida_yoshida/articles/e59bc94f7589bd

[^15]: https://codesandbox.io/examples/package/zustand

[^16]: https://refine.dev/blog/zustand-react-state/

[^17]: https://brainhub.eu/library/zustand-architecture-patterns-at-scale

[^18]: https://github.com/pmndrs/zustand

[^19]: https://kakehashi-dev.hatenablog.com/entry/2024/09/10/110000

[^20]: https://zustand.docs.pmnd.rs

