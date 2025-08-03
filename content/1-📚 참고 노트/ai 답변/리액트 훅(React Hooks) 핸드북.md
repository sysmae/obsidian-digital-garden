---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 리액트 훅(React Hooks) 핸드북

## 핵심 요약

리액트 훅은 함수형 컴포넌트에서 상태 관리, 생명주기 메서드, 컨텍스트, 성능 최적화 기능 등을 사용할 수 있게 해주는 **특수 함수**이다. 클래스 없이도 컴포넌트를 동작시키고, 로직 재사용성과 코드 가독성을 획기적으로 향상시킨다.

## 1. 훅의 도입 배경 및 개념

클래스 컴포넌트 전환 없이도

- 상태(state) 관리
- 사이드 이펙트(부수 효과) 처리
- 컨텍스트 구독
- 레퍼런스(ref) 접근
를 가능하게 하기 위해 React 16.8에서 도입되었다[^1].

훅은 반드시 **함수형 컴포넌트 최상단**에서만 호출해야 하며(룰 오브 훅스), 이름은 `use`로 시작해야 한다.

## 2. 내장 훅(Built-in Hooks) 분류 및 주요 사용법

React가 제공하는 내장 훅은 크게 **State**, **Context**, **Ref**, **Effect**, **Performance**, **기타**로 구분된다[^2].


| 분류            | 훅 이름                                                       | 역할 및 예시                                          |
| :------------ | :--------------------------------------------------------- | :----------------------------------------------- |
| State Hooks   | useState, useReducer                                       | 상태 선언(`useState(0)`), 복잡 로직 시 리듀서 활용             |
| Context Hooks | useContext                                                 | Context 구독(`const theme = useContext(ThemeCtx)`) |
| Ref Hooks     | useRef, useImperativeHandle                                | DOM 노드·값 레퍼런스(`const ref = useRef(null)`)        |
| Effect Hooks  | useEffect, useLayoutEffect, useInsertionEffect             | 사이드 이펙트 관리(`useEffect(()=>{},[])`)               |
| Performance   | useMemo, useCallback, useTransition, useDeferredValue      | 계산·함수 캐싱, 업데이트 우선순위 제어                           |
| 기타            | useDebugValue, useId, useSyncExternalStore, useActionState | DevTools 표시, 고유 ID 생성, 외부 스토어 구독 등               |

## 3. 주의사항: 룰 오브 훅스(Rules of Hooks)

1. **함수형 컴포넌트 또는 커스텀 훅** 최상단에서만 호출
2. **조건문·반복문·중첩 함수** 내부에서 호출 금지
3. 훅 이름은 반드시 **`use`로 시작**

이를 어기면 훅 호출 순서가 뒤바뀌어 오류가 발생한다.

## 4. 커스텀 훅(Custom Hooks)

커스텀 훅은 **`use`로 시작**하며, 여러 훅을 조합해 재사용 가능한 로직을 추출한 함수이다.

### 4.1 생성 원칙

- 한 훅당 **하나의 책임**
- 내부에서 여러 내장 훅 호출 가능
- 파라미터와 반환값으로 유연성 제공


### 4.2 예제: 데이터 페칭 훅

```javascript
import { useState, useEffect } from 'react';

function useFetch(url) {
  const [data, setData]       = useState(null);
  const [isLoading, setLoading] = useState(true);
  const [error, setError]     = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error('Fetch error');
        return res.json();
      })
      .then(setData)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, [url]);

  return { data, isLoading, error };
}
```

이후 컴포넌트에서 `const { data, isLoading, error } = useFetch('/api')`처럼 사용 가능하다.

## 5. 훅 활용 모범 사례

- **로직 재사용**: 공통 처리(네트워크, 폼 관리 등)를 커스텀 훅으로 캡슐화
- **코드 가독성**: 컴포넌트 본문은 렌더링 로직에 집중
- **테스트 용이성**: 훅 단위로 단위 테스트 가능
- **타입 안전성**: TypeScript 사용 시 제네릭으로 반환 타입 정의


## 6. 고급 활용 및 패턴

### 6.1 제어 흐름 분리

`useTransition`, `useDeferredValue`로 **동기·비동기 업데이트** 분리해 UX 부드럽게 유지[^2].

### 6.2 외부 상태 구독

`useSyncExternalStore`로 Redux·MobX 등 외부 스토어와 안정적 동기화 구현[^2].

### 6.3 디버깅 지원

`useDebugValue`로 React DevTools에 **커스텀 라벨** 표시 가능[^2].

## 7. 실전 예시: 테마 전환 훅

```javascript
import { useState, useEffect } from 'react';

function useTheme() {
  const [theme, setTheme] = useState('light');

  useEffect(() => {
    document.body.dataset.theme = theme;
  }, [theme]);

  const toggle = () => setTheme(prev => prev === 'light' ? 'dark' : 'light');
  return { theme, toggle };
}
```

컴포넌트에서 `const { theme, toggle } = useTheme()`로 간단히 테마 제어 가능하다.

## 결론

리액트 훅은 **함수형 컴포넌트를 강력하게** 확장하는 핵심 기능이다.

- 내장 훅을 적절히 활용해 상태·이펙트·성능을 관리하고
- 커스텀 훅으로 비즈니스 로직을 추출해 재사용성과 유지보수성을 극대화하라.

위 원칙과 모범 사례를 따르면 복잡한 UI도 깔끔하고 예측 가능하게 구현할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://www.geeksforgeeks.org/reactjs/reactjs-hooks/

[^2]: https://react.dev/reference/react/hooks

[^3]: https://www.uxpin.com/studio/blog/react-hooks/

[^4]: https://www.linkedin.com/posts/priya-bagde_frontend-javascript-react-activity-7084082381669699584-YuwN

[^5]: https://www.pullrequest.com/blog/implementing-a-custom-react-hook-best-practices-and-a-practical-example/

[^6]: https://www.geeksforgeeks.org/reactjs/built-in-react-hooks/

[^7]: https://dev.to/hasancse/best-practices-for-creating-reusable-custom-hooks-in-react-37nj

[^8]: https://www.w3schools.com/react/react_hooks.asp

[^9]: https://react-redux.js.org/api/hooks

[^10]: https://www.w3schools.com/react/react_customhooks.asp

[^11]: https://www.reddit.com/r/react/comments/11ftu0p/what_are_hooks/

[^12]: https://builtin.com/software-engineering-perspectives/react-hooks

[^13]: https://react.dev/learn/reusing-logic-with-custom-hooks

[^14]: https://react.dev/reference/react-dom/hooks

[^15]: https://www.youtube.com/watch?v=I2Bgi0Qcdvc

[^16]: https://daily.dev/blog/react-hooks-explained-simply

[^17]: https://legacy.reactjs.org/docs/hooks-reference.html

[^18]: https://wikidocs.net/273977

[^19]: https://chimsil-story.tistory.com/entry/React-Hooks-정리

[^20]: https://ko.legacy.reactjs.org/docs/hooks-reference.html

