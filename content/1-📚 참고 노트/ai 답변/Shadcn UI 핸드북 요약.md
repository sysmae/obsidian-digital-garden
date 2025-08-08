---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Shadcn UI 핸드북 요약

**핵심 개요**
Shadcn UI는 *“컴포넌트 라이브러리가 아닌, 어떻게 컴포넌트 라이브러리를 구축할지에 대한 방법론”* 을 제공하는 오픈 소스 툴킷입니다[^1]. Next.js 개발자는 Shadcn UI를 통해 프로젝트에 필요한 UI 컴포넌트를 CLI로 쉽게 복사·붙여넣기 하고, 코드 소유권을 갖고 자유롭게 커스터마이징할 수 있습니다.

## 1. Shadcn UI가 만들어진 이유

Shadcn UI는 기존의 `npm` 패키지 방식 컴포넌트 라이브러리들이 가진 다음과 같은 한계를 해소하기 위해 고안되었습니다[^2]:

- **커스터마이징 난항**: 라이브러리 업데이트 시 스타일 재정의와 래핑 코드가 복잡해짐
- **버전·의존성 관리 부담**: 외부 패키지 버전 충돌 가능성
- **번들 사이즈 과다**: 사용하지 않는 컴포넌트까지 포함

이를 해결하기 위해 Shadcn UI는

- **오픈 코드(Open Code)**: 모든 컴포넌트 소스 코드를 프로젝트에 직접 제공[^1]
- **조합적 인터페이스(Composition)**: 일관된 API로 예측 가능하고 조합 가능한 컴포넌트
- **플랫 파일 배포(Distribution)**: CLI로 필요한 컴포넌트 파일만 프로젝트에 추가
- **아름다운 기본값(Beautiful Defaults)**: Tailwind CSS 유틸리티 기반으로 세련된 스타일
- **AI 친화적(AI-Ready)**: LLM이 코드 이해 및 개선 가능하도록 설계[^1]


## 2. 아키텍처와 구조

### 2.1 두 계층 아키텍처

모든 컴포넌트는 **구조·동작 계층**과 **스타일 계층**으로 나뉘어 있습니다[^3]:

- **구조·동작 계층(Structure \& Behavior Layer)**:
    - Radix UI 프리미티브 기반
    - 접근성(a11y), 키보드 내비게이션, ARIA 속성 등 UI 동작 로직 담당
- **스타일 계층(Style Layer)**:
    - Tailwind CSS 유틸리티 클래스 적용
    - Variants, CVA(Class-Variance-Authority)로 테마·사이즈·버튼 상태 등 조합적 스타일 처리


### 2.2 핵심 기술 스택

| 기술 | 역할 |
| :-- | :-- |
| Radix UI | 헤드리스 컴포넌트 프리미티브 (접근성·동작) |
| Tailwind CSS | 유틸리티 기반 스타일링 |
| TypeScript | 정적 타입 보장 및 개발자 경험 향상 |
| CLI (shadcn-ui) | 컴포넌트 초기화·추가·테마 설정 툴 |

## 3. Next.js 프로젝트에서의 사용법

### 3.1 초기 설치 및 설정

1. 프로젝트 루트에서 CLI 실행

```bash
npx shadcn-ui@latest init
```

2. 설정 질문에 답변
    - TypeScript 사용 여부
    - 스타일(예: Default, New York)
    - 기본 색상(Base Color)
    - 전역 CSS 경로(`app/globals.css` 등)
    - CSS 변수 사용 여부
    - Tailwind 설정 파일 위치
    - React 서버 컴포넌트 사용 여부

설정 완료 후 `components.json`, 전역 CSS, `tailwind.config.js`, `lib/utils.ts`(cn 함수) 등이 자동 생성·추가됩니다[^4].

### 3.2 컴포넌트 추가

필요한 UI 컴포넌트를 CLI로 프로젝트에 복사:

```bash
npx shadcn-ui@latest add button
```

- `components/ui/button.tsx` 파일 생성
- 소스가 직접 프로젝트에 포함되어 자유롭게 수정 가능[^5]


### 3.3 컴포넌트 사용 예시

```jsx
import { Button } from "@/components/ui/button";

export default function Home() {
  return <Button size="sm" variant="destructive">로그아웃</Button>;
}
```


### 3.4 테마 설정 및 다크 모드

1. `next-themes` 설치
2. 최상위 레이아웃(`layout.tsx`)에 `ThemeProvider` 추가
3. `<html suppressHydrationWarning>` 속성 설정
4. 토글 컴포넌트로 라이트/다크 모드 전환[^6]

## 4. 구조 기반 활용 팁

- **코드 소유권**: 복사된 `.tsx` 파일은 원하는 대로 리팩토링 및 파일 구조 변경 가능
- **CVA 활용**: `class-variance-authority`를 통해 variants 및 사이즈 조합을 일관성 있게 관리
- **테마 확장**: CSS 변수로 커스텀 테마 값을 `globals.css`에 정의하고 Tailwind 설정에 연결


## 5. 맺음말

Shadcn UI는 **초기 개발 속도**와 **장기적인 유연성**을 모두 충족시키는 현대적 UI 솔루션입니다. Next.js 프로젝트에 도입 시, **필요한 컴포넌트만** 가져와 **완벽히 통제 가능한 코드베이스**를 구축할 수 있어 유지보수와 커스터마이징 부담을 획기적으로 줄여줍니다.

지금 바로 Shadcn UI CLI를 통해 시작해 보세요!

<div style="text-align: center">⁂</div>

[^1]: https://ui.shadcn.com/docs

[^2]: https://peerlist.io/blog/engineering/what-is-shadcn-and-why-you-should-use-it

[^3]: https://manupa.dev/blog/anatomy-of-shadcn-ui

[^4]: https://freeislet.tistory.com/56

[^5]: https://peerlist.io/blog/engineering/how-to-use-shadcn-ui-with-nextjs

[^6]: https://ui.shadcn.com/docs/dark-mode/next

[^7]: https://www.youtube.com/watch?v=raKAoh9mWzM

[^8]: https://www.freecodecamp.org/news/shadcn-with-next-js-14/

[^9]: https://www.banani.co/blog/shadcn-ui-review

[^10]: https://apidog.com/kr/blog/what-is-shadcn-ui-tutorial-kr/

[^11]: https://www.youtube.com/watch?v=V8rGgwQ-VJ0

[^12]: https://kofearticle.substack.com/p/korean-fe-article-shadcnui

[^13]: https://codingworld2002.tistory.com/244

[^14]: https://dev.to/mechcloud_academy/shadcnui-the-component-library-that-isnt-a-library-5b94

[^15]: https://velog.io/@gardener/shadcnui에-대해서

[^16]: https://www.reddit.com/r/react/comments/1ffomud/the_story_of_shadcnui/

[^17]: https://gugu76.tistory.com/135

[^18]: https://contents.premium.naver.com/codetree/funcoding/contents/240920215117963wr

[^19]: https://ui.shadcn.com

[^20]: https://ui.shadcn.com/docs/installation/next

