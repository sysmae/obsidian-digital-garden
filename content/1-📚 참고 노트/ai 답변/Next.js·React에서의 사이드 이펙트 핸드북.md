---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Next.js·React에서의 사이드 이펙트 핸드북

**핵심 요약:**
Next.js·React 애플리케이션에서 사이드 이펙트는 컴포넌트 렌더링 외에 외부 API 호출, 상태 변경, DOM 직접 조작 등을 포함한다. 이를 체계적으로 관리·최적화하지 않으면 렌더링 비효율·버그·메모리 누수가 발생하므로, 이 핸드북은 기본 개념·발생 원리·관리 패턴·실전 적용 방안을 세 단계(기본→심화→현장 적용)로 정리한다.

## 1부: 개념 및 분류

### 1.1 기본

- **사이드 이펙트 정의:**
React 컴포넌트 함수가 JSX 반환 외에 수행하는 모든 작업(데이터 페칭·구독·타이머 설정·DOM 조작 등).
- **Next.js 특수 고려사항:**
    - 서버사이드 렌더링(SSR) 중 실행되는 코드와 클라이언트에서만 실행돼야 하는 코드 분리
    - `getStaticProps`·`getServerSideProps`에서 발생하는 데이터 로딩도 부수 효과로 간주


### 1.2 심화

- **분류:**
    - *데이터 페칭* (REST/GraphQL 요청, SWR·React Query 사용)
    - *구독·이벤트 리스너* (WebSocket, `addEventListener`)
    - *타이머·애니메이션* (`setTimeout`·`requestAnimationFrame`)
    - *글로벌 상태 변경* (Redux·Recoil·Context API 업데이트)
    - *DOM 직접 조작* (`useRef` + `ref.current.*`)


### 1.3 현장 적용

- **렌더링 단계 구분:**
    - 서버 사이드: `getStaticProps`·`getServerSideProps`
    - 클라이언트 사이드: `useEffect`, `useLayoutEffect`
- **렌더링 조건 점검:**
    - `useEffect` 의존성 배열 최적화
    - Next.js 빌드 시 `window`·`document` 참조 방지


## 2부: 발생 메커니즘 및 위험 요인

### 2.1 기본

- **React 라이프사이클 훅:**
    - `useEffect`: 렌더 후 실행, 비동기 작업 적합
    - `useLayoutEffect`: DOM 마운트 직후 동기 실행, 레이아웃 측정 시 사용
- **Next.js 데이터 패칭:**
    - 빌드 타임 사전 렌더링(`getStaticProps`)
    - 요청 시 렌더링(`getServerSideProps`)


### 2.2 심화

- **무한 루프·불필요 재실행:**
    - 의존성 누락 → `useEffect`가 매 렌더마다 재실행
    - 객체·함수 참조를 직접 의존성에 포함 시 재생성으로 인한 재실행
- **메모리 누수:**
    - 구독·리스너 해제 누락 (`useEffect` cleanup 필수)
    - 타이머·애니메이션 클리어 미실행


### 2.3 현장 적용

- **문제 진단 도구:**
    - React DevTools: 훅 실행 횟수·렌더링 원인 확인
    - Lighthouse: SSR 성능·렌더 차단 자원 파악
- **디버깅 팁:**
    - 의존성 배열에 `// eslint-disable-next-line react-hooks/exhaustive-deps` 남용 자제
    - 커스텀 훅으로 로직 추출 후 독립 테스트


## 3부: 관리·최적화·활용

### 3.1 기본

- **최소화 원칙:**
    - 사이드 이펙트는 최소한의 컴포넌트(주로 최상위 페이지 컴포넌트)에서만 실행
    - 비즈니스 로직과 부수 효과 분리


### 3.2 심화

- **데이터 페칭 전략:**
    - **SWR/React Query**: 캐싱·중복 제거·자동 재요청 제공
    - **ISR(Incremental Static Regeneration)**: 빌드 이후에도 정적 페이지 갱신
- **형태 분리(Separation of Concerns):**
    - `hooks/` 디렉토리에 커스텀 훅 저장 (`useFetch`, `useSubscription`)
    - 컴포넌트에서는 UI 렌더링만 담당


### 3.3 현장 적용

- **Next.js 최적화 설정:**
    - `next.config.js`에서 `reactStrictMode` 활성화하여 사이드 이펙트 위험 감지
    - `webpack` 번들 분석으로 초기 로드 시점 부수 효과 코드 분리
- **테스트·유지보수:**
    - **Jest + React Testing Library**: `useEffect` 내 비동기 작업 모킹
    - **E2E 테스트(Cypress)**: 페이지 네비게이션 시 스크립트 실행 검증
- **CI/CD 파이프라인:**
    - ESLint 훅 룰(`react-hooks/exhaustive-deps`) 강제
    - Lighthouse·Bundle Analyzer 리포트 자동 생성

**기대 효과:**
Next.js·React에서 사이드 이펙트를 체계적으로 관리하면 렌더링 안정성·성능·유지보수성이 향상되어, 대규모 애플리케이션에서도 일관된 사용자 경험과 개발 생산성을 동시에 달성할 수 있다.

