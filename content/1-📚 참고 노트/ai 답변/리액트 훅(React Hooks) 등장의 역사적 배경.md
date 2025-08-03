---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 리액트 훅(React Hooks) 등장의 역사적 배경

## 핵심 요약

리액트 훅은 2013년부터 2018년까지의 **5년간 축적된 문제점들을 해결**하기 위해 등장했다. 클래스 컴포넌트의 복잡성, 코드 재사용 문제, JavaScript의 `this` 바인딩 혼란, 그리고 "래퍼 지옥(Wrapper Hell)" 등이 주요 동기였다. **2019년 2월 6일** React 16.8과 함께 정식 출시되어 함수형 컴포넌트가 클래스 컴포넌트의 모든 기능을 사용할 수 있게 되었다[^1][^2].

## 1. 리액트 초기 역사 (2013-2016): 컴포넌트 작성 방식의 변천

### 1.1 createClass 시대 (2013-2015)

리액트가 **2013년 JSConf에서 공개**되었을 때는 `React.createClass()` 방식을 사용했다[^3][^4]. 이는 JavaScript ES5에 클래스 문법이 없었기 때문이다.

```javascript
var ExampleComponent = React.createClass({
  getInitialState: function() {
    return { count: 0 };
  },
  mixins: [SomeMixin],
  render: function() {
    return <div>{this.state.count}</div>;
  }
});
```

**Mixins의 문제점**이 점차 드러났다[^5][^1]:

- **의존성 추적 어려움**: 어떤 mixin에서 메서드가 오는지 파악 곤란
- **네임스페이스 충돌**: 여러 mixin이 같은 메서드명 정의 시 오류 발생[^6]
- **암묵적 의존성**: mixin 간 숨겨진 결합도


### 1.2 ES6 클래스 전환 (2015-2016)

**JavaScript ES6(ES2015)가 2015년에 출시**되면서 클래스 문법이 도입되었다[^7][^8]. React는 **2016년경 ES6 클래스 컴포넌트를 표준**으로 채택했다[^9][^10].

```javascript
class ExampleComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    this.handleClick = this.handleClick.bind(this); // this 바인딩 필요
  }
  
  handleClick() {
    this.setState({ count: this.state.count + 1 });
  }
  
  render() {
    return <button onClick={this.handleClick}>{this.state.count}</button>;
  }
}
```


## 2. 클래스 컴포넌트의 근본적 문제들

### 2.1 JavaScript의 `this` 바인딩 문제

클래스 컴포넌트 최대 난제는 **이벤트 핸들러에서 `this`가 `undefined`**가 되는 것이었다[^11][^12][^13].

```javascript
// 문제 코드
<button onClick={this.handleClick}>Click</button>
// this.handleClick이 호출될 때 this가 undefined

// 해결 방법들
this.handleClick = this.handleClick.bind(this); // 생성자에서 바인딩
<button onClick={() => this.handleClick()}>Click</button> // 화살표 함수
```

이는 **JavaScript의 기본 특성**으로, 메서드가 객체에서 분리되어 호출되면 `this` 컨텍스트를 잃기 때문이다[^13][^14].

### 2.2 생명주기 메서드의 로직 분산 문제

**관련 없는 로직이 하나의 생명주기 메서드에 섞이고**, 연관된 로직은 여러 메서드에 흩어지는 문제가 있었다[^1]:

```javascript
componentDidMount() {
  // 데이터 페칭
  this.fetchUserData();
  // 이벤트 리스너 등록
  window.addEventListener('resize', this.handleResize);
  // 타이머 설정  
  this.timer = setInterval(this.updateClock, 1000);
}

componentWillUnmount() {
  // 정리 작업들이 별도 메서드에 분산
  window.removeEventListener('resize', this.handleResize);
  clearInterval(this.timer);
}
```


### 2.3 컴포넌트 간 로직 공유의 복잡성

클래스 컴포넌트에서 상태 있는 로직을 공유하려면 **HOC(Higher-Order Components)나 Render Props** 패턴을 사용해야 했다[^1][^15].

#### HOC 패턴의 문제점

```javascript
// 여러 HOC 중첩 시 "래퍼 지옥" 발생
const EnhancedComponent = withAuth(
  withTheme(
    withLoading(
      withUserData(MyComponent)
    )
  )
);
```


#### Render Props의 문제점

```javascript
<DataProvider>
  {data => (
    <ThemeProvider>
      {theme => (
        <AuthProvider>
          {auth => (
            <MyComponent data={data} theme={theme} auth={auth} />
          )}
        </AuthProvider>
      )}
    </ThemeProvider>
  )}
</DataProvider>
	```

이런 패턴들은 **"래퍼 지옥(Wrapper Hell)"**을 만들어 DevTools에서 컴포넌트 트리가 복잡해지고 디버깅이 어려워졌다[^1][^16][^17].

## 3. 훅 도입의 직접적 동기 (2018년)

### 3.1 React팀의 공식 동기 발표

**2018년 React Conf에서 Dan Abramov가 발표**한 Hooks 도입 동기는 3가지였다[^1][^18]:

1. **컴포넌트 간 상태 로직 재사용의 어려움**
2. **복잡한 컴포넌트의 이해 어려움**
3. **클래스가 사람과 기계 모두에게 혼란스러움**

### 3.2 성능 및 최적화 관점

클래스 컴포넌트는 **미래의 React 최적화 기법과 호환성 문제**가 있었다[^1]:

- **컴포넌트 폴딩(Component Folding)** 최적화에서 더 느린 경로로 빠짐
- **Ahead-of-Time 컴파일**에서 제약 사항
- **코드 압축(Minification)**에서 비효율적


### 3.3 학습 곡선과 개발자 경험

클래스 컴포넌트는 **React 학습의 큰 장벽**이었다[^1][^19]:

- JavaScript `this` 개념 이해 필요
- 생명주기 메서드 암기
- 바인딩 문법의 복잡성
- 경험 많은 개발자들 사이에서도 함수형 vs 클래스형 선택에 논란


## 4. 훅의 해결책과 출시 (2019년)

### 4.1 훅이 제공한 해결책

```javascript
// 클래스 컴포넌트 (Before)
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    this.handleClick = this.handleClick.bind(this);
  }
  
  handleClick() {
    this.setState({ count: this.state.count + 1 });
  }
  
  render() {
    return <button onClick={this.handleClick}>{this.state.count}</button>;
  }
}

// 함수형 컴포넌트 + 훅 (After)
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(count + 1)}>
      {count}
    </button>
  );
}
```


### 4.2 커스텀 훅으로 로직 재사용

```javascript
// 커스텀 훅으로 깔끔한 로직 공유
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  return { count, increment, decrement };
}

// 사용
function MyComponent() {
  const { count, increment, decrement } = useCounter(10);
  return (
    <div>
      <span>{count}</span>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
    </div>
  );
}
```


### 4.3 정식 출시

**2019년 2월 6일 React 16.8.0**과 함께 안정화된 훅이 출시되었다[^2][^20]. 이는 **React 역사상 가장 중요한 전환점** 중 하나로 평가받는다[^21].

## 5. 훅 도입의 역사적 의의

### 5.1 패러다임 변화

훅은 단순한 새 기능이 아니라 **React 철학의 진화**를 보여준다[^22]:

- 클래스 없이도 완전한 컴포넌트 작성 가능
- 함수형 프로그래밍 패러다임 강화
- 컴포넌트 로직의 선언적 표현


### 5.2 생태계 영향

훅 출시 이후 **React 생태계 전반이 함수형 컴포넌트 중심**으로 재편되었다[^23]:

- 새로운 라이브러리들이 훅 기반으로 설계
- 기존 HOC/Render Props 패턴에서 커스텀 훅으로 전환
- React 공식 문서도 함수형 컴포넌트 우선으로 개편


## 결론

리액트 훅은 **5년간의 실전 경험에서 나온 문제점들을 체계적으로 해결**한 혁신이다. 클래스 컴포넌트의 복잡성, 로직 재사용의 어려움, JavaScript `this`의 혼란, 래퍼 지옥 등 개발자들이 실제로 겪던 고충을 해결했다. 2019년 정식 출시 이후 React는 더욱 직관적이고 재사용 가능한 방향으로 발전했으며, 이는 현대 웹 개발 패러다임에 큰 영향을 미치고 있다.

<div style="text-align: center">⁂</div>

[^1]: https://legacy.reactjs.org/docs/hooks-intro.html

[^2]: https://ko.legacy.reactjs.org/blog/2019/02/06/react-v16.8.0.html

[^3]: https://blog.risingstack.com/the-history-of-react-js-on-a-timeline/

[^4]: https://www.geeksforgeeks.org/reactjs/history-and-evolution-of-react/

[^5]: https://ko.legacy.reactjs.org/blog/2016/07/13/mixins-considered-harmful.html

[^6]: https://github.com/facebook/react/issues/3027

[^7]: https://jusungpark.tistory.com/53

[^8]: https://www.w3schools.com/js/js_es6.asp

[^9]: https://www.newmediacampaigns.com/blog/refactoring-react-components-to-es6-classes

[^10]: https://www.robinwieruch.de/react-component-types/

[^11]: https://typeofnan.dev/how-to-fix-undefined-this-state-in-react-class-components/

[^12]: https://www.basefactor.com/react-class-components-pitfalls/

[^13]: https://exploringreact.com/2020/11/28/why-do-we-need-to-use-bind-for-events-in-class-based-components/

[^14]: https://stackoverflow.com/questions/69267661/why-my-class-method-works-in-react-without-explicitly-binding-it-to-this

[^15]: https://reacttraining.com/blog/where-did-hooks-come-from

[^16]: https://yusufuysal.hashnode.dev/react-interview-questions-part-8-higher-order-components-hocs-render-props

[^17]: http://thefarmersfront.github.io/blog/thinking-in-react/

[^18]: https://reactiver.dev/why-react-hooks/

[^19]: https://dev.to/_ndeyefatoudiop/10-reasons-why-you-should-never-use-react-class-components-1h76

[^20]: https://appinventiv.com/blog/react-16-8-released/

[^21]: https://dev.to/ryansolid/the-react-hooks-announcement-in-retrospect-2-years-later-18lm

[^22]: https://css-tricks.com/intro-to-react-hooks/

[^23]: https://dev.to/colocodes/react-class-components-vs-function-components-23m6/comments

[^24]: https://forum.freecodecamp.org/t/class-problems-learning-with-react-js/454676

[^25]: https://blog.rhostem.com/posts/2019-08-18-reason-whey-react-hooks-opt-in

[^26]: https://www.geeksforgeeks.org/reactjs/limitations-of-class-components-in-react/

[^27]: https://blog.bitsrc.io/6-reasons-to-use-react-hooks-instead-of-classes-7e3ee745fe04

[^28]: https://en.wikipedia.org/wiki/React_(software)

[^29]: https://stackoverflow.com/questions/67300845/react-class-component-not-working-properly-for-me

[^30]: https://ui.dev/why-react-hooks

[^31]: https://clouddevs.com/react/reasons-react-hooks-are-future-web-development/

[^32]: https://www.frontendundefined.com/posts/monthly/react-state-management-libraries-history/

[^33]: https://www.reddit.com/r/reactjs/comments/1cdtvxu/why_react_hooks_are_better_than_classes/

[^34]: https://reactrouter.com/web/api/Hooks

[^35]: https://www.w3schools.com/react/react_class.asp

[^36]: https://dev.to/vtrpldn/hooked-on-hooks-a-late-introduction-to-my-favorite-thing-in-react-1nfe

[^37]: https://wikidocs.net/197629

[^38]: https://www.reddit.com/r/react/comments/1c1kwic/are_classbased_components_still_relevant/

[^39]: https://ding-co.tistory.com/581

[^40]: https://www.slideshare.net/knoldus/basics-of-react-hookspptxpdf

[^41]: https://cottonwood-moa.tistory.com/154

[^42]: https://www.uxpin.com/studio/blog/functional-vs-class-components/

[^43]: https://coffeeandcakeandnewjeong.tistory.com/32

[^44]: https://iconof.com/my-top-react-techtalks-of-2019/

[^45]: https://www.xenonstack.com/blog/functional-vs-class-components

[^46]: https://www.geeksforgeeks.org/reactjs/reactjs-lifecycle-components/

[^47]: https://stackoverflow.com/questions/21854938/using-mixins-vs-components-for-code-reuse-in-facebook-react

[^48]: https://forum.freecodecamp.org/t/why-dont-we-bind-this-to-the-render-method/644109

[^49]: https://react.dev/learn/lifecycle-of-reactive-effects

[^50]: https://stackoverflow.com/questions/62400814/react-lifecycle-methods-not-trigger-is-there-some-hack-behind-lifecycle-methods

[^51]: https://news.ycombinator.com/item?id=42331207

[^52]: https://www.reddit.com/r/reactjs/comments/12m0j30/does_thinking_in_lifecycle_still_a_thing_given/

[^53]: https://kentcdodds.com/blog/when-to-break-up-a-component-into-multiple-components

[^54]: https://stackoverflow.com/questions/70609878/fix-scss-path-issues-in-react-app-while-compiling/70610106

[^55]: https://www.reddit.com/r/reactjs/comments/j37xsk/whats_the_scope_of_this_in_a_react_class/

[^56]: https://www.reddit.com/r/reactjs/comments/oomo1w/what_are_the_biggest_issues_you_see_with_react_in/

[^57]: https://github.com/sudheerj/reactjs-interview-questions

[^58]: https://velog.io/@yena1025/React-클래스-컴포넌트에서-bind-메서드-사용하기

[^59]: https://github.com/facebook/react/issues/4023

[^60]: https://www.apptunix.com/blog/introducing-the-all-new-react-16-8-with-stable-hook-implementation/

[^61]: https://github.com/rackt/react-router/issues/835

[^62]: https://stackoverflow.com/questions/58831176/how-to-update-the-react-js-project-from-15-6-to-16-8

[^63]: https://learn.react-js.dev/basics/excursus-es2015+

[^64]: https://stackoverflow.com/questions/30668464/react-component-vs-react-createclass

[^65]: https://blog.voidmainvoid.net/67

[^66]: https://engineering.fb.com/2019/02/22/open-source/react-hooks/

[^67]: https://handhand.tistory.com/entry/Design-Pattern-Mixin을-사용해보며-느낀점들

[^68]: https://blog.qvil.dev/javascript/es6/

[^69]: https://www.reddit.com/r/reactjs/comments/al3zj7/react_168_the_one_hopefully_with_hooks_planned/

[^70]: https://ko.legacy.reactjs.org/blog/2017/04/07/react-v15.5.0.html

