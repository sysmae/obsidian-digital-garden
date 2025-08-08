---
publish: true
---

'Headless UI'에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘

# Headless UI 핸드북

Headless UI는 **스타일이 완전히 제거된 채 접근성과 동작 로직만 제공**하는 컴포넌트 집합으로, Tailwind CSS와의 결합을 염두에 두고 Tailwind Labs가 설계한 라이브러리이다[^1][^2][^3]. 전통적인 UI 라이브러리가 겪던 “강제된 테마” 문제를 해결하면서도, ARIA·키보드 네비게이션·포커스 트래핑 같은 복잡한 구현을 직접 고생하지 않게 해 준다. 본 핸드북은 Headless UI가 탄생한 이유부터 구조, Next.js 환경 사용법, 확장 패턴까지 통합적으로 안내한다.

## 목차

- Headless UI가 등장한 배경
- 핵심 개념과 설계 철학
- 아키텍처 및 컴포넌트 구조
- Next.js + Tailwind CSS 설치·설정
- 주요 컴포넌트별 사용법
- 상태·이벤트·전환(Transition) 다루기
- 접근성(A11y) 구현 원칙
- 스타일링 전략과 패턴
- 고급 통합(SSR·동적 import 등)
- 다른 Headless 계열 라이브러리 비교
- 실무 적용 가이드라인
- FAQ \& 트러블슈팅
- 향후 로드맵과 커뮤니티 리소스


## Headless UI가 등장한 배경

### 디자인 강제와 재사용의 딜레마

1. **기존 컴포넌트 라이브러리의 한계**
Material UI·Ant Design처럼 디자인이 강하게 결합된 컴포넌트는 프로젝트 전반을 ‘라이브러리 룩’으로 물들이며, 세밀한 커스텀 과정에서 CSS override 지옥이 발생한다[^4][^5].
2. **접근성 로직 구현 부담**
드롭다운·모달·토글 등 복합 위젯은 키보드 장애 지원, ARIA 속성, 포커스 관리 등을 제대로 구현해야만 한다. 팀이 직접 만들 경우 2~4주가 순식간에 소모된다[^1][^6].
3. **디자인 시스템 확산**
조직마다 자체 디자인 시스템을 도입하면서 “동작은 빌리고 UI는 우리가 만든다”는 수요가 급증했다[^7].

### Tailwind Labs의 제안

Tailwind CSS를 사용하던 팀들은 “유틸리티 클래스로 스타일링을 마음껏 할 수 있는데, 컴포넌트 로직만 제공해 주면 안 될까?”라는 요구를 반복했다. Tailwind Labs는 2020년 10월 **Headless UI v0**를 발표했고, 2021년 4월 v1, 2024년 5월 v2로 발전해 왔다[^2][^8][^9].

## 핵심 개념과 설계 철학

### 1. Renderless (Headless) Pattern

- **UI = 머리(Head), 로직·상태 = 몸(Body)**
Headless UI는 ‘머리’를 떼어내고 ‘몸’만 공급한다[^10][^11].
- **관심사 분리**
시각적 표현은 소비자가 100% 책임지고, 동작·접근성 로직은 라이브러리가 맡는다[^12][^13].
- **슬롯·Render Prop 기반**
현재 상태(active·open 등)를 슬롯 prop 또는 data-attribute로 노출한다[^14][^15].


### 2. 핵심 설계 원칙

| 원칙 | 설명 |
| :-- | :-- |
| 접근성 우선 | WAI-ARIA Authoring Practices를 준수하고, 스크린리더·키보드 네비게이션을 자동 지원[^16][^17] |
| 불변 스타일 | 어떤 CSS도 기본 제공하지 않으며 0바이트 스타일 시트[^3] |
| 프레임워크 호환 | React·Vue 3 지원(Alpine .js 예정)[^1][^18] |
| Tailwind 친화 | `@headlessui/tailwindcss` 플러그인으로 상태별 utility 변형 제공[^19] |
| 적은 런타임 부하 | 필요 최소한의 상태·이벤트만 관리하고, 비 DOM 플랫폼과도 연계 가능[^20] |

## 아키텍처 및 컴포넌트 구조

### 1. 모듈 배포

- `@headlessui/react` – React 17+
- `@headlessui/vue` – Vue 3.x 전용[^18]
- `@headlessui/tailwindcss` – Tailwind variant 플러그인[^19]


### 2. 컴포넌트 생태계

| 카테고리 | React 컴포넌트 | Vue 컴포넌트 | 주요 State 노출 |
| :-- | :-- | :-- | :-- |
| Overlay | `Dialog`,`Popover` | 동일 | `open`,`close`,`afterLeave`[^21] |
| Navigation | `Menu`,`Disclosure`,`Tabs` | 동일 | `active`,`selected`[^15] |
| Selection | `Listbox`,`RadioGroup`,`Checkbox`(v2) | 동일 | `value`,`focus`,`disabled`[^22][^8] |
| Form | `Switch`,`Combobox`,`NumberField`(β) | 동일 | `checked`,`open`,`inputValue` |

`data-*` attribute로 상태를 노출해 Tailwind CSS selector(`data-focus:bg-blue-100`)를 바로 적용할 수 있다[^17][^15].

## Next.js + Tailwind CSS 설치·설정

### 단계별 가이드

```bash
npx create-next-app my-headless-demo --use-npm
cd my-headless-demo
npm install -D tailwindcss postcss autoprefixer
npx tailwindcss init -p
npm install @headlessui/react @heroicons/react
```

`tailwind.config.js`

```js
module.exports = {
  content: [
    "./pages/**/*.{js,ts,jsx,tsx}",
    "./components/**/*.{js,ts,jsx,tsx}",
  ],
  plugins: [
    require('@headlessui/tailwindcss')({ prefix: 'ui' }),
  ],
}
```

> 서버 컴포넌트(Next.js 13+) 안에서 Headless UI를 쓰려면 **클라이언트 바운더리**에 위치시키고 파일 상단에 `'use client'` 지시자를 넣어야 한다[^23][^24].

## 주요 컴포넌트별 사용법

### 1. Dialog (Modal)

```jsx
'use client'
import { Dialog } from '@headlessui/react'
import { useState } from 'react'

export default function MyModal() {
  const [open,setOpen]=useState(false)
  return (
    <>
      <button onClick={()=>setOpen(true)}>Open</button>
      <Dialog open={open} onClose={setOpen} className="fixed inset-0 z-50">
        <div className="flex min-h-screen items-center justify-center p-4">
          <Dialog.Panel className="w-full max-w-md rounded bg-white p-6">
            <Dialog.Title className="font-bold">Deactivate</Dialog.Title>
            <p className="mt-2">정말 비활성화할까요?</p>
            <button className="mt-4" onClick={()=>setOpen(false)}>닫기</button>
          </Dialog.Panel>
        </div>
      </Dialog>
    </>
  )
}
```

- 포커스 트래핑·ARIA 속성·배경 스클롤 락이 자동 처리된다[^21][^16].
- Transition을 추가할 때 `<Transition>` 랩핑 또는 v2 `transition` prop을 활용한다[^9][^25].


### 2. Listbox (Select)

```jsx
import { Listbox } from '@headlessui/react'
const people=[{id:1,name:'Wade'},{id:2,name:'Arlene'}]
export default function SelectUser(){
  const [selected,setSelected]=useState(people[^0])
  return(
    <Listbox value={selected} onChange={setSelected}>
      <Listbox.Button className="border px-3 py-2">{selected.name}</Listbox.Button>
      <Listbox.Options className="absolute mt-1 w-full rounded bg-white shadow-lg">
        {people.map(p=>(
          <Listbox.Option key={p.id} value={p}
            className="ui-active:bg-indigo-600 ui-active:text-white px-4 py-2">
            {p.name}
          </Listbox.Option>
        ))}
      </Listbox.Options>
    </Listbox>
  )
}
```

데이터 속성 `ui-active` variant 덕분에 Tailwind CSS만으로 상태별 스타일링이 가능하다[^17].

### 3. Menu (Dropdown)

- `MenuButton·MenuItems·MenuItem` 조합으로 구현.
- `data-focus`,`data-active` 셀렉터로 hover·open 상태를 제어[^15].


### 4. Switch (Toggle)

React state 두 줄로 접근성 완비 토글 구현:

```jsx
import { Switch } from '@headlessui/react'
const [enabled,setEnabled]=useState(false)
<Switch
  checked={enabled}
  onChange={setEnabled}
  className={`${
    enabled ? 'bg-blue-600' : 'bg-gray-200'
  } relative inline-flex h-6 w-11 items-center rounded-full`}>
  <span
    className={`${
      enabled ? 'translate-x-6' : 'translate-x-1'
    } inline-block h-4 w-4 transform rounded-full bg-white transition`} />
</Switch>
```

접근성 라벨은 `sr-only` 스팬으로 추가[^26].

## 상태·이벤트·전환 다루기

### 1. 상태 노출 방식

- Slot Props : `{ active }`,`{ selected }` 등 Vue Template 슬롯[^14].
- Render Prop 패턴 : React children-as-function 구현[^16].
- Data Attribute : `data-open`,`data-focus` 변형으로 CSS 선택자에 직접 사용[^17].


### 2. Transition

| 접근 방식 | v1 | v2.1 이후 |
| :-- | :-- | :-- |
| 랩핑 컴포넌트 | `<Transition>` 필수[^25] | 불필요 |
| API 단순화 | 속성 4종(enter·enterFrom·enterTo·leave) | `transition` Boolean prop + `data-*` stage[^9] |
| CSS 작성 | Tailwind 클래스 연결 | `data-closed`,`data-enter`,`data-leave` 디코레이션 |

### 3. 이벤트

모든 컴포넌트가 **제어(Controlled) 패턴**을 지향한다. 예) `Menu` open/close 제어 시, `MenuButton` 내부에서 자동 관리하지만 외부 state와 동기화하려면 `onClose` prop 사용[^15].

## 접근성 (A11y) 구현 원칙

- **ARIA 역할**: `role="dialog"`,`role="menu"` 등 자동 할당[^21][^15].
- **포커스 관리**: Tab 포커스 루프·Escape 키 닫기·Outside Click 감지 내장[^16].
- **스크린 리더 친화**: `aria-labelledby`,`aria-describedby` 연결 도우미 제공[^16].
- **국제화**: 라이브러리 자체는 i18n 미포함, React Aria hook 결합을 권장[^27][^28].


## 스타일링 전략과 패턴

### 1. Tailwind CSS Variant

`ui-open:bg-blue-500` 같이 상태별 class 변형을 플러그인 한 줄로 활성화[^19].

### 2. Design Token 접목

Headless UI는 스타일 독립적이므로 **디자인 시스템 토큰**(CSS Custom Property, `@tailwind base`확장)을 적용해도 충돌이 없다[^4].

### 3. 레이아웃 컨트롤

Dialog 패널을 CSS Grid·Flex로 자유 배치 가능. Floating UI 엔진 내장(v2)으로 앵커 포지셔닝 API 제공[^8].

## 고급 통합 가이드

| 시나리오 | 권장 패턴 | 주의사항 |
| :-- | :-- | :-- |
| Next.js App Router (13+) Server Component | `'use client'` 바운더리 분리[^23] | 서버 파일에서 직접 import 금지 |
| 동적 import (코드 스플릿) | `const Modal=dynamic(()=>import('./Modal'),{ssr:false})` | Dialog + Transition은 클라이언트 전용[^29] |
| SSR 폰트와 충돌 | Dialog 내 폰트 누락 시 `className="font-sans"` 직접 지정[^30] | `@next/font`와 z-index 체크 |
| React Native·Electron | DOM 접근성 API를 쓰므로 정식 지원 X |  |

## Library 비교

| 항목 | Headless UI | Radix UI | React Aria |
|---|---|---|
|스타일 제공|없음[^1]|없음(Primitives)[^31]|없음(훅)[^27]|
|지원 프레임워크|React,Vue|React|React|
|접근성 커버리지|A11y완전,포커스트랩[^16]|동일[^31]|WAI-ARIA 가이드라인 심층[^28]|
|컴포넌트 수|React 25종,Vue 23종(v2)[^8]|React 32종[^32]|훅 40+개[^28]|
|Tailwind 통합|공식 plugin[^19]|shadcn/ui 통합[^33]|별도|
|커뮤니티 Stars(GitHub)|27,700개[^3]|5,500개[^34]|9,600개[^28]|

## 실무 적용 가이드라인

### 1. 언제 선택해야 하나?

- 강력한 **브랜드 스타일**을 유지해야 하거나, 기존 디자인 시스템에 맞춰야 할 때.
- 접근성·키보드 네비게이션 규격을 직접 구현할 인력이 부족할 때.


### 2. 프로젝트 구조 제안

```
/components
  /ui   ← Headless 컴포넌트 래퍼 (Button, Modal 등)
  /pages
    index.tsx
/styles
  tailwind.css
```

- **래퍼 컴포넌트** 계층을 만들어 디자인 토큰을 캡슐화한다.


### 3. 성능 최적화

- SSR 페이지에서 Dialog 같은 무거운 오버레이는 **다이나믹 import**로 분리.
- Listbox 옵션이 많을 경우 **가상 스크롤**(react-window)과 결합.


### 4. 팀 워크플로

- Storybook or Histoire에 Headless 컴포넌트와 스타일 변형을 전수검사.
- ESLint plugins(`jsx-a11y`)로 ARIA 속성 과다 중복 체크.


## FAQ \& 트러블슈팅

| 질문 | 해결 팁 |
| :-- | :-- |
| Dialog 오픈 시 스크롤이 안 막힘 | `<body>` custom overflow 스타일 제거 후 재현[^16] |
| Transition enter 애니메이션 안 작동 | v2.1부터 `transition` prop 사용·`data-closed` 스타일 지정[^9][^24] |
| Next.js 13 서버컴포넌트 오류 | 컴포넌트 파일 상단 `'use client'` 추가, 부모는 서버 렌더[^23] |
| 리스트박스 항목 겹침 | `relative z-10` 추가 및 옵션 랩퍼 `position-relative` 적용[^35] |

## 향후 로드맵·커뮤니티

- **Vue v2 지원 종료** → Vue 3 집중[^18].
- **Alpine.js Adapter** 예정(ETA 2025 Q4)[^1].
- **웹 커스텀 엘리먼트** 실험적 빌드 논의 중[^3].

Tailwind Labs Discord 채널·GitHub Discussion 게시판에서 기능 제안을 활발히 수용한다.

## 결론

Headless UI는 “동작 로직과 접근성만 공유하고 스타일은 0”이라는 과감한 철학으로, **디자인 일관성**과 **개발 생산성**을 동시에 확보하는 현대 프런트엔드 트렌드의 핵심이다. Next.js와 Tailwind CSS 조합에서 구현 난이도를 획기적으로 낮추며, Radix UI·React Aria 등과 함께 “Headless 생태계”를 이루고 있다.

이 핸드북을 따라 프로젝트에 도입한다면, **브랜드-맞춤 UI**를 손쉽게 구축하면서도 접근성 검증에 드는 시간을 대폭 절감할 수 있다. 무엇보다 라이브러리 자체가 **머리 없는 유연성**을 제공하므로, 팀의 디자인 시스템을 미래 변화에 맞춰 진화시키는 데 걸림돌이 되지 않을 것이다.

<div style="text-align: center">⁂</div>

[^1]: https://tailwindcss.com/blog/headless-ui-unstyled-accessible-ui-components

[^2]: https://tailwindcss.com/blog/headless-ui-v1

[^3]: https://github.com/tailwindlabs/headlessui

[^4]: https://blog.tinstack.net/posts/28

[^5]: https://www.howdy-mj.me/design/headless-components

[^6]: https://www.linkedin.com/pulse/improving-web-accessibility-headless-components-daniel-ikoyo

[^7]: https://www.subframe.com/blog/how-headless-components-became-the-future-for-building-ui-libraries

[^8]: https://tailwindcss.com/blog/headless-ui-v2

[^9]: https://tailwindcss.com/blog/2024-06-21-headless-ui-v2-1

[^10]: https://devella.oopy.io/b19dcc48-dad5-4fe2-9ee2-a9ed57563b09

[^11]: https://velog.io/@dhgg321/Headless한-컴포넌트-만들기

[^12]: https://jbee.io/articles/react/Headless UI Library란

[^13]: https://www.patterns.dev/vue/renderless-components/

[^14]: https://headlessui.com/v1/vue/menu

[^15]: https://headlessui.com/react/menu

[^16]: https://headlessui.com/v1/react/dialog

[^17]: https://headlessui.com/react/listbox

[^18]: https://www.npmjs.com/package/@headlessui/vue

[^19]: https://www.npmjs.com/package/@headlessui/tailwindcss

[^20]: https://martinfowler.com/articles/headless-component.html

[^21]: https://headlessui.com/react/dialog

[^22]: https://headlessui.com/v1/vue/listbox

[^23]: https://stackoverflow.com/questions/77076739/how-to-use-tailwind-ui-headless-ui-components-with-next-js-server-components

[^24]: https://stackoverflow.com/questions/76854340/headless-ui-transition-not-working-with-dialog

[^25]: https://javascript.plainenglish.io/headlessui-listbox-part-5-transition-7cf2796d9ac

[^26]: https://www.frontoverflow.com/magazine/2/스타일이 없는 UI 라이브러리가 있다%3F!

[^27]: https://react-spectrum.adobe.com/react-aria/hooks.html

[^28]: https://www.npmjs.com/package/react-aria

[^29]: https://www.reddit.com/r/nextjs/comments/1115864/dynamically_importing_a_modal_that_uses_headless/

[^30]: https://github.com/tailwindlabs/headlessui/issues/2225

[^31]: https://www.subframe.com/tips/headless-ui-vs-radix

[^32]: https://www.greatfrontend.com/blog/top-headless-ui-libraries-for-react-in-2024

[^33]: https://o-yeon.tistory.com/231

[^34]: https://www.youtube.com/watch?v=BtdwOdtg03c

[^35]: https://stackoverflow.com/questions/74986882/listbox-options-similar-to-dropdown-overlapping

[^36]: https://dev.to/joodi/top-6-headless-ui-libraries-for-react-developers-3mfi

[^37]: https://nuxt.com/modules/headlessui

[^38]: https://strapi.io

[^39]: https://www.reddit.com/r/reactjs/comments/1bphm39/best_headless_component_lib_for_use_with_tailwind/

[^40]: https://www.npmjs.com/package/@headlessui/react

[^41]: https://madewithvuejs.com/headless-ui-vue

[^42]: https://github.com/rad-ui/ui

[^43]: https://tailwindcss.com/plus/ui-kit

[^44]: https://velog.io/@seeh_h/react-reanger-%EB%9C%AF%EC%96%B4%EB%B3%B4%EA%B8%B0

[^45]: https://codecourse.com/articles/renderless-vue-components-in-5-minutes

[^46]: https://react-spectrum.adobe.com/react-aria/useField.html

[^47]: https://www.telerik.com/blogs/understanding-renderless-components-vue

[^48]: https://www.reddit.com/r/reactjs/comments/1989sd3/radixui_vs_react_aria_vs_headless_ui/

[^49]: https://itchallenger.tistory.com/823

[^50]: https://dev.to/verthon/headless-ui-libraries-the-key-to-flexible-and-accessible-user-interfaces-546p

[^51]: https://reactspectrum.blob.core.windows.net/reactspectrum/068581f762798b4bbecc62eade5462ad76c8933c/docs/react-aria/getting-started.html

[^52]: https://thoughtprovo-king.tistory.com/97

[^53]: https://namastedev.com/blog/intro-to-headless-ui-with-react-2/

[^54]: https://krutiepatel.com/5-renderless-components

[^55]: https://dania.hashnode.dev/optimizing-web-performance-with-headless-ui

[^56]: https://www.reddit.com/r/reactjs/comments/17lz5gr/headless_component_a_pattern_for_composing_react/

[^57]: https://tailwindweekly.com/p/tailwind-weekly-155-headless-ui-v2-is-out

[^58]: https://javascript.plainenglish.io/headless-ui-87062802d76f

[^59]: https://study-record99.tistory.com/105

[^60]: https://tailwindcss.ru/blog/headless-ui-unstyled-accessible-ui-components

[^61]: https://news.ycombinator.com/item?id=29116806

[^62]: https://www.youtube.com/watch?v=8q8ypSxt9X0

[^63]: https://javascript.plainenglish.io/headlessui-switch-part-1-install-necessary-packages-and-basic-example-b54a3bc4aeff

[^64]: https://www.youtube.com/watch?v=ypRJ9ScLmco

[^65]: https://www.reddit.com/r/nextjs/comments/1dmkds5/portfolio_in_nextjs_w_tailwind_headless_ui_or/

[^66]: https://tailwindcss.com/docs/guides/nextjs

[^67]: https://javascript.plainenglish.io/headlessui-listbox-part-1-install-necessary-packages-and-basic-example-6fadbc7c83fd

[^68]: https://www.youtube.com/watch?v=yi3NZn9HSx8

[^69]: https://www.youtube.com/watch?v=rnAUcpLprPU

