---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Zustand 핸드북

**핸드북 요약:**
Zustand는 React를 위한 **가볍고** **간결하며** **확장 가능한** 전역 상태 관리 라이브러리입니다. Context API나 Redux 대비 최소한의 보일러플레이트로 단일 소스의 상태를 효율적으로 관리하도록 설계되었습니다.

## 1. 탄생 배경 및 특징

Zustand는 2019년 Jürgen Martens에 의해 만들어졌으며, 복잡한 설정과 보일러플레이트가 요구되는 Redux의 대안으로 등장했습니다. 주요 특징은 다음과 같습니다.

- **경량(1.2 KB)**: 번들 크기가 작아 앱 퍼포먼스에 부담이 적습니다[^1].
- **간단한 API**: `create` 함수 하나로 스토어를 선언하고, 리액트 훅처럼 사용합니다[^2].
- **보일러플레이트 최소화**: 상태와 업데이트 함수를 선언만 하면 자동으로 병합(set)됩니다[get_url_content].
- **빠른 렌더 최적화**: 선택자(selector)로 읽을 상태를 지정해 불필요한 리렌더를 방지합니다[^2].
- **미들웨어 지원**: Immer, Persist, DevTools, 사용자 정의 미들웨어 등을 플러그인처럼 적용할 수 있습니다[^3][^4].


## 2. 구조

### 2.1 스토어 생성

```javascript
import { create } from 'zustand';

const useStore = create((set) => ({
  bears: 0,
  increase: () => set((state) => ({ bears: state.bears + 1 })),
  reset: () => set({ bears: 0 }),
}));
```

- `create`에 전달한 함수는 `set`과 `get`(선택적)을 인자로 받습니다[get_url_content].
- 반환값 `useStore`는 훅으로, 컴포넌트 어디서나 호출할 수 있습니다[get_url_content].


### 2.2 상태 읽기 및 업데이트

```jsx
function Counter() {
  const bears = useStore((state) => state.bears);
  return <h1>{bears} bears</h1>;
}

function Controls() {
  const increase = useStore((state) => state.increase);
  return <button onClick={increase}>+1</button>;
}
```

- 훅 호출 시 **선택자(selector)**를 전달해 필요한 부분만 구독하도록 권장합니다. 그렇지 않으면 전체 스토어 객체 변경 시 리렌더가 발생합니다[^2].


## 3. 주요 사용법

### 3.1 선택자 사용

- **단일 값**: `useStore(state => state.value)`
- **여러 값**:

```javascript
const [a, b] = useStore(
  state => [state.a, state.b],
  useShallow
);
```

`useShallow`로 얕은 비교를 적용해 객체 재생성 시 리렌더를 억제합니다[^2].


### 3.2 비동기 액션

```javascript
const useAsyncStore = create((set) => ({
  data: null,
  fetchData: async () => {
    const res = await fetch('/api');
    set({ data: await res.json() });
  },
}));
```

- `set`은 비동기 함수 내에서도 문제없이 호출됩니다[^2].


### 3.3 컴포넌트 외부에서 상태 읽기

```javascript
import { useStore } from './store';
console.log(useStore.getState().bears);
```

- `useStore.getState()`로 React 바깥에서도 현재 상태를 참조할 수 있습니다[^2].


## 4. 미들웨어

Zustand는 함수 합성 방식의 미들웨어를 지원합니다. 대표적으로 다음이 있습니다.


| 미들웨어 | 용도 |
| :-- | :-- |
| Immer | 불변 데이터 업데이트 간소화[^3] |
| Persist | 로컬/세션 스토리지에 상태 지속[^4] |
| DevTools | Redux DevTools 연동[^5] |
| Custom Middleware | 로깅, 에러 처리, 액션 후킹 등 사용자 정의 |

### 4.1 Immer 미들웨어

```javascript
import { create } from 'zustand';
import { immer } from 'zustand/middleware/immer';

const useTodoStore = create(immer((set) => ({
  todos: [],
  addTodo: (todo) => set((state) => { state.todos.push(todo); }),
})));
```


### 4.2 Persist 미들웨어

```javascript
import { create } from 'zustand';
import { persist, createJSONStorage } from 'zustand/middleware';

const useBearStore = create(
  persist(
    (set, get) => ({
      bears: 0,
      addBear: () => set({ bears: get().bears + 1 }),
    }),
    { name: 'bear-storage', storage: createJSONStorage(() => localStorage) }
  )
);
```


## 5. 고급 패턴 및 팁

### 5.1 파생 상태(Computed State)

- 선택자 내부에서 계산해 반환할 수 있습니다.

```javascript
const useStats = () => useStore((state) => ({
  isHungry: state.bears > 5,
}));
```


### 5.2 스토어 분할

- 기능별로 여러 스토어를 생성해 모듈화할 수 있습니다.
- 컴포넌트 트리 특정 부분에서만 사용할 스토어는 별도 파일로 분리합니다.


### 5.3 테스트 전략

- React Testing Library + MSW로 컴포넌트 소비 스토어를 테스트합니다.
- `useStore.getState()`를 활용해 액션 후 상태 검증이 가능합니다[^1].


## 6. 결론 및 권장 사용 시나리오

Zustand는 **작고** **단순한** API로 **강력한** 상태 관리를 제공합니다.
**추천 시나리오**:

- 전역 상태가 많지 않고, 간단한 CRUD 패턴일 때
- Context API의 리렌더 이슈를 피하고자 할 때
- Redux의 보일러플레이트가 부담될 때

핸드북을 참고하여, 프로젝트 규모와 요구사항에 맞게 Zustand를 도입·확장해 보십시오.

<div style="text-align: center">⁂</div>

[^1]: https://blog.logrocket.com/zustand-adoption-guide/

[^2]: https://frontendmasters.com/blog/introducing-zustand/

[^3]: https://zustand.docs.pmnd.rs/integrations/immer-middleware

[^4]: https://zustand.docs.pmnd.rs/integrations/persisting-store-data

[^5]: https://zustand.docs.pmnd.rs/middlewares/devtools

[^6]: https://github.com/pmndrs/zustand/discussions/1770

[^7]: https://tkdodo.eu/blog/zustand-and-react-context

[^8]: https://theirstack.com/en/technology/zustand

[^9]: https://www.reddit.com/r/reactjs/comments/1ekww1m/should_i_use_usestate_or_zustand/

[^10]: https://dev.to/ricardogesteves/zustand-when-how-and-why-1kpi

[^11]: https://www.npmjs.com/package/zustand-middleware-yjs

[^12]: https://refine.dev/blog/zustand-react-state/

[^13]: https://www.youtube.com/watch?v=_ngCLZ5Iz-0

[^14]: https://github.com/pmndrs/zustand

[^15]: https://awesomedevin.github.io/zustand-vue/en/docs/basic/middleware

[^16]: https://zustand.docs.pmnd.rs

[^17]: https://www.youtube.com/watch?v=8QkXAj1s5GA

[^18]: https://www.youtube.com/watch?v=-Y8brhQKvtA

[^19]: https://aaronpresley.com/writing/understanding-zustand-middleware

[^20]: https://zustand-demo.pmnd.rs

