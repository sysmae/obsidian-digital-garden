---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 리액트 상태(React State) 핸드북

## 핵심 요약

리액트의 **상태(State)**는 컴포넌트 내부에서 관리되는 데이터의 “기억”으로, UI의 동적 변화를 가능하게 한다. 컴포넌트가 생성된 이유, 상태의 구조 설계, 업데이트 방법, 그리고 대규모 애플리케이션에서의 상태 공유 기법까지 총체적으로 이해해야 안정적이고 유지보수하기 쉬운 리액트 애플리케이션을 구축할 수 있다.

## 1. 상태의 개념과 도입 배경

컴포넌트가 사용자 입력, 네트워크 응답 등 다양한 이벤트에 반응하여 UI를 갱신해야 할 때, 단순 변수만으로는

- 렌더링 간 데이터 유지 불가
- 변경 시 리렌더링 미발동
라는 한계가 있다.
이를 해결하기 위해 리액트는 컴포넌트별 **상태 객체**를 도입하였다. 상태는 리액트가 내부에서 관리하며, `setState`(클래스) 또는 `useState`(함수)로 갱신 시 해당 컴포넌트를 다시 렌더링하도록 스케줄링한다[^1].


## 2. 상태의 구조와 초기화

### 2.1 클래스 컴포넌트

```javascript
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0, items: [] };
  }
}
```

- `this.state` 객체에 속성들을 정의
- `setState` 메서드를 통해 불변성을 지키며 업데이트[^2]


### 2.2 함수형 컴포넌트 (Hooks)

```javascript
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  // 객체 상태: const [user, setUser] = useState({ name: '', age: 0 });
}
```

- `useState(initialValue)` 호출로 `[값, 업데이트함수]` 쌍 반환
- 여러 개 상태 변수를 선언 가능[^3]


## 3. 상태 업데이트 메커니즘

- **비동기 스케줄링**: `setState`/`setCount` 호출은 즉시 적용되지 않고 다음 렌더링 사이클에서 병합·적용된다[^4].
- **얕은 병합**: 클래스 컴포넌트의 `setState`는 새 객체를 이전 상태와 얕은 병합(shallow merge)한다[^5].
- **함수형 업데이트**: 이전 상태에 의존할 때는 함수 형태로 전달하여 최신 상태를 안전하게 참조한다.

```javascript
setCount(prev => prev + 1);
```


## 4. 상태 구조 설계 원칙

### 4.1 관련 데이터 그룹화

항상 함께 업데이트되는 값들은 하나의 객체나 배열로 묶어 관리한다.

```javascript
// Bad
const [x, setX] = useState(0);
const [y, setY] = useState(0);

// Good
const [position, setPosition] = useState({ x:0, y:0 });
```

이로써 동기화 버그를 방지할 수 있다[^6].

### 4.2 상호 모순 방지

두 개 이상의 상태가 서로 모순될 수 있는 구조는 피해야 한다.

```javascript
// Bad: isLoading && hasError 가능
const [isLoading, setIsLoading] = useState(false);
const [hasError, setHasError] = useState(false);

// Good: 유한 FSM
const [status, setStatus] = useState('idle');
// 'idle'|'loading'|'success'|'error'
```

상태 기계(FSM) 모델로 예측 가능한 상태만 허용[^6].

### 4.3 불필요한 중복 제거

기존 상태나 props에서 유도 가능한 정보는 별도 상태로 저장하지 않는다.

```javascript
// Bad
const [items, setItems] = useState([]);
const [total, setTotal] = useState(0);

// Good
const [items, setItems] = useState([]);
const total = items.reduce((sum, i) => sum + i.price, 0);
```

항상 일관된 데이터 유지[^6].

### 4.4 깊은 중첩 지양

중첩된 객체 구조는 업데이트가 복잡해진다.

```javascript
// Bad
const [user, setUser] = useState({ profile:{ contact:{ email:'' }}});

// Good
const [email, setEmail] = useState('');
// 또는
const [contact, setContact] = useState({ email:'', phone:'' });
```

상태를 플랫하게 유지하여 가독성과 업데이트 용이성 확보[^6].

## 5. 상태 공유 및 확장 전략

### 5.1 상태 끌어올리기(Lifting State Up)

공통 조상 컴포넌트로 상태를 끌어올려 하위 컴포넌트에 props로 전달한다[^3].

### 5.2 Context API

깊은 컴포넌트 트리 사이에서 prop drilling을 피하기 위해 전역처럼 상태 제공자와 소비자를 구성한다.

### 5.3 전역 상태 관리 라이브러리

Redux, MobX 등 중앙 저장소를 활용하여 대규모 앱의 데이터 흐름을 명확히 한다.

- 장점: 디버깅·타임트래블, 미들웨어 통한 로깅
- 단점: 보일러플레이트 증가, 러닝커브 존재


## 6. 실전 사용 예제

```javascript
import { useState } from 'react';

function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [input, setInput] = useState('');

  function addTodo() {
    setTodos([...todos, { id:Date.now(), text:input }]);
    setInput('');
  }

  return (
    <>
      <input
        value={input}
        onChange={e => setInput(e.target.value)}
      />
      <button onClick={addTodo}>추가</button>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>{todo.text}</li>
        ))}
      </ul>
    </>
  );
}
```

1) **입력 제어**: `input` 상태로 폼 컴포넌트 제어
2) **리스트 상태**: 불변성을 지키며 `todos` 배열에 항목 추가

## 7. 결론 및 권장 사항

- **단순한 상태**부터 시작해, 애플리케이션 복잡도가 커지면 **구조 설계 원칙**(4.1–4.4)을 적용하라.
- 상태 변경 시 **불변성**과 **함수형 업데이트**를 지켜 예측 가능한 동작을 보장해야 한다.
- 컴포넌트 재사용성·가독성 향상을 위해 **Context**나 **전역 관리** 도입 시점과 범위를 신중히 결정하라.

이 핸드북을 통해 리액트 상태의 근본 개념부터 고급 설계 패턴까지 단계적으로 이해하고, 유지보수성 높은 컴포넌트를 작성할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://react.dev/learn/state-a-components-memory

[^2]: https://www.geeksforgeeks.org/reactjs/reactjs-state/

[^3]: https://react.dev/learn/managing-state

[^4]: https://legacy.reactjs.org/docs/faq-state.html

[^5]: https://www.simplilearn.com/tutorials/reactjs-tutorial/reactjs-state

[^6]: https://certificates.dev/blog/structuring-state-in-react-5-essential-patterns

[^7]: https://dev.to/sonaykara/reactjs-choosing-the-state-structure-5gnp

[^8]: https://www.w3schools.com/react/react_state.asp

[^9]: https://www.reddit.com/r/reactjs/comments/17om8v9/what_is_the_point_of_state_management/

[^10]: https://www.theodinproject.com/lessons/node-path-react-new-more-on-state

[^11]: https://react.dev/learn/preserving-and-resetting-state

[^12]: https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Frameworks_libraries/React_interactivity_events_state

[^13]: https://react.dev/learn/choosing-the-state-structure

[^14]: https://www.reddit.com/r/learnprogramming/comments/e5ha6n/explain_like_im_five_state_in_react/

[^15]: https://www.reddit.com/r/reactjs/comments/1bz5agf/how_do_you_typically_plan_your_state_structure_in/

[^16]: https://www.youtube.com/watch?v=RXrqnlyAEZo

[^17]: https://www.youtube.com/watch?v=cdvnGqjo_rQ

[^18]: https://www.freecodecamp.org/news/what-is-state-in-react-explained-with-examples/

[^19]: https://legacy.reactjs.org/docs/state-and-lifecycle.html

