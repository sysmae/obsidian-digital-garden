---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 제스트(Jest) 핸드북

## 핵심 요약

제스트(Jest)는 Facebook(現 Meta)에서 개발한 **JavaScript 테스트 프레임워크**로, React 및 일반 JavaScript 프로젝트의 단위(Unit)·통합(Integration) 테스트를 간편하게 실행할 수 있도록 설계되었다. 제스트는 **“제로 설정(zero config)”** 철학, **병렬 실행**, **스냅샷 테스트**, **모의(Mock) 기능** 등 풍부한 기능을 내장하여 테스트 작성부터 리포팅, 코드 커버리지 분석까지 일체형 솔루션을 제공한다.

## 1. 제스트가 탄생한 배경

- **기원**: 2011년 Meta 내부에서 JavaScript 채팅 기능 재구축 과정에서 ‘jst’라는 이름으로 개발이 시작되었으며, 2014년 오픈소스화, 2016년부터 전담팀 관리[^1].
- **목표**: 프런트엔드 단위 테스트의 복잡한 설정을 획기적으로 줄이고, 설정 없이 즉시 사용 가능한 테스트 환경 제공[^2].
- **철학**:
    - **제로 설정**: 대부분 프로젝트에서 추가 설정 없이 바로 테스트 실행 가능[^3].
    - **빠르고 안전한 실행**: 병렬 프로세스, 실패 테스트 우선 실행 등의 최적화로 높은 성능 보장[^3].


## 2. 구조 및 아키텍처

제스트는 여러 개의 **독립 패키지**로 분할되어 있으며, CLI 호출부터 테스트 격리 실행, 결과 리포팅까지 단계별로 구성된다[^4][^5].

1. **jest-cli**
    - 사용자 명령(`jest`)을 해석하고, 설정·실행 흐름을 조율
2. **jest-config**
    - `package.json` 또는 `jest.config.js` 내 설정을 읽어 **Global Config**·**Project Config**로 정규화
3. **jest-haste-map**
    - 프로젝트 파일과 의존성 그래프를 탐색·캐싱(watchman 또는 Node 크롤러 사용)
4. **jest-worker**
    - CPU 코어 활용 병렬 처리로 파일 메타데이터 수집 및 테스트 실행 작업 분산
5. **SearchSource → TestSequencer → TestScheduler**
    - 테스트 파일 패턴 검색 → 우선순위(과거 실패 여부·소요 시간) 정렬 → 병렬·직렬 실행 방식 결정
6. **jest-runner**
    - `jest-worker`를 통해 VM 격리 환경( `jest-environment` + `jest-runtime`)에서 개별 테스트 실행
7. **jest-circus / jest-jasmine**
    - `describe`, `it` API 제공 및 테스트 실행 플로우 관리
8. **jest-runtime \& jest-environment**
    - 각 테스트 격리 실행, 모듈 변환(Babel·TypeScript)·모킹·Require 구현
9. **Reporters \& AggregatedTestResult**
    - 결과 집계·출력 및 종료 코드 반환

## 3. 기본 설치 및 사용법

1. **설치**

```bash
npm install --save-dev jest
# 또는
yarn add --dev jest
```

2. **테스트 스크립트 추가** (`package.json`)

```json
{
  "scripts": {
    "test": "jest"
  }
}
```

3. **테스트 파일 작성**

```javascript
// sum.js
function sum(a, b) { return a + b; }
module.exports = sum;

// sum.test.js
const sum = require('./sum');
test('1+2=3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

4. **실행 옵션**
    - `--coverage`: 코드 커버리지 리포트 생성
    - `--watch`: 변경된 테스트 자동 재실행
    - `--runInBand`: 직렬 실행
    - `-t '테스트 이름'`: 특정 테스트만 실행

## 4. 주요 기능 및 확장성

### 4.1 스냅샷 테스트

- **개념**: 컴포넌트 렌더링 결과를 `.snap` 파일에 직렬화 저장 후, 이후 변경 시 차이점 비교
- **사용 예**:

```javascript
test('UI snapshot', () => {
  const tree = renderer.create(<Component />).toJSON();
  expect(tree).toMatchSnapshot();
});
```

- **업데이트**: `jest --updateSnapshot`


### 4.2 모킹(Mock) \& 스파이(Spy)

- **jest.fn() / jest.mock()**: 함수·모듈 모의 구현
- **mockReturnValue / mockImplementationOnce** 등을 이용한 동적 동작 제어[^6]


### 4.3 코드 커버리지

- **내장 지원**: `--coverage` 플래그로 HTML·콘솔 리포트 제공
- **커버리지 임계치**: `coverageThreshold` 설정으로 기준 미달 시 실패 처리


### 4.4 병렬 실행 최적화

- 실패 테스트 우선 실행, 과거 실행 시간 기반 시퀀싱으로 **빠른 피드백** 제공


### 4.5 설정 확장

- **Config 파일**: `jest.config.js` 또는 `package.json` 내 `jest` 필드
- **플러그인·스냅샷 시리얼라이저** 등 커스터마이징 가능


## 5. 고급 활용 및 팁

- **Watch Mode**: `--watch`로 파일 변경 자동 재실행
- **Retry \& Timeout**: `jest.retryTimes(n)`, `jest.setTimeout(ms)`로 비동기 테스트 안정화[^7]
- **테스트 격리**: `beforeAll`/`beforeEach`, `afterEach`/`afterAll` 훅으로 환경 셋업·정리
- **CI/CD 통합**: GitHub Actions·Jenkins 등 파이프라인에서 자동 테스트 및 커버리지 체크
- **멀티 프로젝트**: 하나의 레포지토리 내 여러 프로젝트 설정 동시 실행 지원


## 6. 베스트 프랙티스

1. **단위·통합 테스트 분리**: `*.unit.test.js` vs `*.integration.test.js`
2. **의존성 모킹 최소화**: 실제 API 호출 대신 Mock으로 테스트 속도·안정성 향상
3. **테스트 이름 명확화**: `describe`, `it`로 역할·조건을 문장형으로 표현
4. **커버리지 임계치 설정**: 주요 모듈 80% 이상 권장
5. **주기적 스냅샷 검토**: 의도치 않은 UI 변경 감지 및 관리

## 7. 결론 및 추천

제스트는 **간편한 설정**과 **강력한 기능**을 양립한 JavaScript 테스트 솔루션으로, React뿐 아니라 범용 JS 프로젝트에도 적합하다. 아키텍처가 모듈화되어 있어 **고유 워크플로우**에 맞춘 확장이 용이하며, **병렬 실행**과 **커버리지 분석** 등 기본 제공 기능으로 안정적이고 효율적인 테스팅을 지원한다. 신규 프로젝트에는 초기 설정 부담 없이 즉시 도입을, 기존 프로젝트에는 코드 커버리지 기준 강화 및 스냅샷 테스트 도입을 적극 권장한다.

<div style="text-align: center">⁂</div>

[^1]: https://blog.logrocket.com/jest-adoption-guide/

[^2]: https://www.browserstack.com/guide/jest-framework-tutorial

[^3]: https://jestjs.io

[^4]: https://www.w3resource.com/jest/jest-architecture.php

[^5]: https://techwithkunal.com/blog/jest-architecture

[^6]: https://moldstud.com/articles/p-what-are-some-advanced-features-of-jest-that-developers-should-know-about

[^7]: https://sylhare.github.io/2022/09/21/Jest-testing.html

[^8]: https://www.adesso.de/en/news/blog/the-jest-testing-framework-our-top-five-features-2.jsp

[^9]: https://www.lambdatest.com/jest

[^10]: https://jestjs.io/docs/configuration

[^11]: https://jestjs.io/docs/architecture

[^12]: https://blogs.perficient.com/2024/03/05/mastering-advanced-features-and-techniques-of-jest-react-testing-library/

[^13]: https://www.testim.io/blog/jest-testing-a-helpful-introductory-tutorial/

[^14]: https://www.youtube.com/watch?v=3YDiloj8_d0

[^15]: https://testomat.io/blog/tutorial-on-how-to-organize-an-advanced-jest-testing-framework/

[^16]: https://jestjs.io/docs/getting-started

[^17]: https://mintaku-blog.net/api-jest/

[^18]: https://www.startearly.ai/post/a-developers-tutorial-to-jest-unit-testing

[^19]: https://www.youtube.com/watch?v=x6NUZ8dc9Qg

[^20]: https://runebook.dev/ja/docs/jest/architecture

