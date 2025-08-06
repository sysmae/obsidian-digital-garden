---
{"publish":true,"created":"2025-08-06T23:39:54.513+09:00","modified":"2025-08-06T23:41:52.239+09:00","cssclasses":""}
---


<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# FSD 폴더 구조 nextjs

이제 충분한 정보를 수집했으므로 FSD 폴더 구조와 Next.js 적용에 대한 종합적인 핸드북을 작성할 수 있습니다.

# Feature-Sliced Design (FSD) 폴더 구조 Next.js 핸드북

## FSD란 무엇인가?

Feature-Sliced Design(FSD)는 프론트엔드 애플리케이션을 위한 아키텍처 방법론으로, 복잡한 프로젝트를 더 이해하기 쉽고 구조적으로 만들어 끊임없이 변화하는 비즈니스 요구사항에 대응하기 위해 개발되었습니다. FSD는 단순히 코드를 조직화하는 규칙과 관례의 모음이며, 애플리케이션을 기능 단위로 분할하여 각 부분의 역할을 명확하게 정의하는 것을 목표로 합니다.[^1][^2][^3][^4]

### FSD가 만들어진 이유

전통적인 프론트엔드 폴더 구조는 다음과 같은 문제점들을 가지고 있습니다:

- **낮은 응집도(Cohesion)**: 관련된 코드가 여러 폴더에 분산되어 있음[^5]
- **높은 결합도(Coupling)**: 순환 참조 발생과 모듈 간 의존성 관리 어려움[^5]
- **규모 확장성 문제**: 프로젝트가 커질수록 코드 구조가 복잡해짐[^6]
- **협업 어려움**: 명확한 경계가 없어 팀원 간 충돌 발생[^6]

FSD는 이러한 문제들을 해결하기 위해 다음 원칙을 제시합니다:

- **기능 중심 설계**: 비즈니스 기능 단위로 코드 조직화[^7]
- **단방향 의존성**: 상위 레이어만 하위 레이어에 의존 가능[^8]
- **명시적 공개 인터페이스**: Public API를 통한 모듈 간 상호작용[^9]
- **구성 가능성**: 작은 기능 단위를 조합하여 큰 기능 구성[^7]


## FSD 구조 체계

FSD는 최대 3단계의 계층 구조를 가집니다:

### 1. 레이어(Layers) - 첫 번째 단계

레이어는 FSD의 최상위 구조로, 각각 고유한 책임 영역을 가집니다:[^2][^10]

**app**: 애플리케이션 초기화 및 전역 설정

- Provider, Router, 전역 스타일, 전역 타입 선언
- 애플리케이션 전반적으로 적용되는 기능들[^1]

**pages**: 라우팅 가능한 화면 정의

- 애플리케이션의 각 페이지
- URL과 매핑되는 최상위 컴포넌트들[^2]

**widgets**: 독립적인 UI 블록

- 여러 feature를 조합한 의미 있는 블록
- Header, Footer, Sidebar 등[^10][^1]

**features**: 사용자 시나리오와 비즈니스 로직

- 동사적 의미의 비즈니스 기능
- 사용자에게 비즈니스 가치를 전달하는 행위[^11][^1]

**entities**: 비즈니스 엔티티

- User, Product, Order 등 핵심 비즈니스 객체
- 데이터 모델과 관련 로직[^11][^2]

**shared**: 재사용 가능한 공통 요소

- UI 키트, 유틸리티 함수, API 클라이언트
- 프로젝트 특정 로직에 종속되지 않는 기능들[^1][^2]


### 레이어 간 의존성 규칙

FSD의 핵심 원칙은 **단방향 의존성**입니다:[^12][^8]

```
app → pages → widgets → features → entities → shared
```

- 상위 레이어는 하위 레이어만 import 가능
- 하위 레이어는 상위 레이어를 알 수 없음
- 순환 참조 방지와 안정적인 아키텍처 보장[^12]


### 2. 슬라이스(Slices) - 두 번째 단계

슬라이스는 비즈니스 도메인별로 코드를 분할합니다:[^13]

- **비즈니스 중심 명명**: `user`, `post`, `comment`, `auth` 등[^8]
- **독립성 보장**: 같은 레이어 내 슬라이스 간 import 금지[^13]
- **높은 응집도**: 관련 기능을 한곳에 모음[^14]


### 3. 세그먼트(Segments) - 세 번째 단계

세그먼트는 기술적 목적에 따라 코드를 그룹화합니다:[^9][^13]

**ui**: UI 컴포넌트와 관련 요소들

- React 컴포넌트, 스타일, 데이터 포맷팅 함수

**model**: 비즈니스 로직과 데이터 관리

- 상태 관리, 액션, 셀렉터, 비즈니스 규칙

**api**: 외부 API와의 통신

- 백엔드 API 메서드, 요청 함수, 데이터 타입

**lib**: 보조 및 인프라 코드

- 헬퍼 함수, 유틸리티, 인프라 로직

**config**: 설정과 상수

- 환경 변수, 설정 파일, 상수 정의[^9]


### Public API 패턴

각 슬라이스와 세그먼트는 `index.ts` 파일을 통해 Public API를 정의합니다:[^15][^9]

```typescript
// features/auth/index.ts
export { LoginForm } from './ui/LoginForm';
export { RegisterForm } from './ui/RegisterForm';
export { login, logout } from './model/authSlice';
export { selectUser } from './model/authSelectors';
export { fetchUser } from './api/authApi';
```

**Public API의 장점**:

- 내부 구현 캡슐화[^9]
- 모듈 간 의존성 명확화[^16]
- 리팩토링 시 안정성 보장[^15]
- Import 경로 단순화[^16]


## Next.js와 FSD 통합

### Next.js App Router와의 충돌 문제

Next.js App Router는 `app` 폴더를 라우팅에 사용하는데, FSD에서도 `app` 레이어가 존재하여 네이밍 충돌이 발생합니다.[^17][^18][^1]

### 해결 방안

**권장 구조**:[^18][^17]

```
├── app/                    # Next.js App Router 폴더 (루트)
│   ├── layout.tsx
│   ├── page.tsx
│   └── [route]/page.tsx
├── src/                    # FSD 구조
│   ├── app/               # FSD app 레이어
│   ├── pages/             # FSD pages 레이어 (또는 views)
│   ├── widgets/
│   ├── features/
│   ├── entities/
│   └── shared/
├── middleware.ts
└── package.json
```


### Next.js 특화 고려사항

**1. 서버 컴포넌트 분리**:[^19]

```
├── shared/
│   ├── server-utils/      # 서버 전용 유틸리티
│   └── utils/            # 클라이언트/서버 공용
```

**2. FSD pages를 views로 변경**:[^20][^19]

- Next.js Pages Router와의 충돌 방지
- 빌드 에러 해결

**3. 실제 페이지 컴포넌트 구성**:[^21]

```typescript
// app/dashboard/page.tsx
import { DashboardPage } from '@/src/pages/dashboard';

export default DashboardPage;
```


## 실제 구현 예시

### 기본 FSD 구조

```
src/
├── app/
│   ├── providers/         # React Query, Redux 등
│   ├── styles/           # 전역 스타일
│   └── types/            # 전역 타입 정의
├── pages/
│   ├── home/
│   │   ├── ui/
│   │   │   └── HomePage.tsx
│   │   └── index.ts
│   └── dashboard/
│       ├── ui/
│       │   └── DashboardPage.tsx
│       └── index.ts
├── widgets/
│   ├── header/
│   │   ├── ui/
│   │   │   └── Header.tsx
│   │   └── index.ts
│   └── sidebar/
│       ├── ui/
│       │   └── Sidebar.tsx
│       └── index.ts
├── features/
│   ├── auth/
│   │   ├── ui/
│   │   │   ├── LoginForm.tsx
│   │   │   └── LogoutButton.tsx
│   │   ├── model/
│   │   │   └── authSlice.ts
│   │   ├── api/
│   │   │   └── authApi.ts
│   │   └── index.ts
│   └── search/
│       ├── ui/
│       │   └── SearchBar.tsx
│       ├── model/
│       │   └── searchModel.ts
│       └── index.ts
├── entities/
│   ├── user/
│   │   ├── model/
│   │   │   └── types.ts
│   │   ├── api/
│   │   │   └── userApi.ts
│   │   └── index.ts
│   └── post/
│       ├── ui/
│       │   └── PostCard.tsx
│       ├── model/
│       │   └── types.ts
│       └── index.ts
└── shared/
    ├── ui/
    │   ├── Button/
    │   ├── Input/
    │   └── Modal/
    ├── api/
    │   └── client.ts
    ├── config/
    │   └── constants.ts
    └── lib/
        └── utils.ts
```


### Public API 구현 예시

```typescript
// entities/user/index.ts
export type { User } from './model/types';
export { fetchUser, updateUser } from './api/userApi';

// features/auth/index.ts
export { LoginForm } from './ui/LoginForm';
export { useAuth } from './model/authModel';
export { login, logout } from './api/authApi';

// widgets/header/index.ts
export { Header } from './ui/Header';
```


### Import 사용 예시

```typescript
// pages/home/ui/HomePage.tsx
import { Header } from '@/widgets/header';
import { SearchBar } from '@/features/search';
import { PostCard } from '@/entities/post';
import { Button } from '@/shared/ui/Button';

export const HomePage = () => {
  return (
    <div>
      <Header />
      <SearchBar />
      <PostCard />
      <Button>Click me</Button>
    </div>
  );
};
```


## 의존성 관리와 품질 보증

### ESLint 플러그인 활용

**eslint-plugin-boundaries** 설정:[^22][^23]

```json
{
  "plugins": ["boundaries"],
  "settings": {
    "boundaries/elements": [
      { "type": "shared", "pattern": "src/shared/*" },
      { "type": "entities", "pattern": "src/entities/*" },
      { "type": "features", "pattern": "src/features/*" },
      { "type": "widgets", "pattern": "src/widgets/*" },
      { "type": "pages", "pattern": "src/pages/*" },
      { "type": "app", "pattern": "src/app/*" }
    ]
  },
  "rules": {
    "boundaries/element-types": ["error", {
      "default": "disallow",
      "rules": [
        { "from": "shared", "allow": [] },
        { "from": "entities", "allow": ["shared"] },
        { "from": "features", "allow": ["shared", "entities"] },
        { "from": "widgets", "allow": ["shared", "entities", "features"] },
        { "from": "pages", "allow": ["shared", "entities", "features", "widgets"] },
        { "from": "app", "allow": ["shared", "entities", "features", "widgets", "pages"] }
      ]
    }]
  }
}
```

**Import 순서 정리**:[^23]

```json
{
  "rules": {
    "import/order": ["error", {
      "groups": ["builtin", "external", "internal", ["parent", "sibling"], "index", "object", "type"],
      "pathGroups": [
        { "pattern": "@/app/**", "group": "internal", "position": "before" },
        { "pattern": "@/pages/**", "group": "internal", "position": "before" },
        { "pattern": "@/widgets/**", "group": "internal", "position": "before" },
        { "pattern": "@/features/**", "group": "internal", "position": "before" },
        { "pattern": "@/entities/**", "group": "internal", "position": "before" },
        { "pattern": "@/shared/**", "group": "internal", "position": "before" }
      ]
    }]
  }
}
```


## FSD의 장점과 단점

### 장점

**확장성**:[^24][^6]

- 프로젝트가 커져도 구조 유지
- 기능별 독립적 개발 가능
- 팀 협업 효율성 증대

**유지보수성**:[^25][^2]

- 명확한 책임 분리
- 순환 참조 방지
- 리팩토링 용이성

**일관성**:[^3][^16]

- 표준화된 구조
- 새로운 개발자 적응 용이
- 코드 탐색성 향상


### 단점과 한계

**학습 곡선**:[^26][^24]

- 초기 개념 이해 어려움
- 레이어/슬라이스 구분의 모호함
- 팀 적응 시간 필요

**복잡성 증가**:[^24]

- 작은 프로젝트에는 과도할 수 있음
- 많은 파일과 폴더 구조
- 초기 설정 비용

**실무 적용 제약**:[^27][^24]

- 엄격한 규칙 준수의 어려움
- 실제 비즈니스 로직의 복잡성
- 기존 프로젝트 적용 어려움


## 실무 적용 가이드

### 점진적 도입 전략

**1단계: 기본 레이어 구성**:[^24]

```
src/
├── app/
├── pages/
├── widgets/
└── shared/
```

**2단계: 필요시 확장**:

```
src/
├── app/
├── pages/
├── widgets/
├── features/     # 비즈니스 로직 복잡해지면 추가
├── entities/     # 도메인 모델 필요시 추가
└── shared/
```


### 팀 합의사항 정의

**슬라이스 네이밍 규칙**:[^10]

- 비즈니스 도메인 기반 명명
- 동사형(features) vs 명사형(entities) 구분
- 팀 내 용어 통일

**Public API 정책**:[^9]

- 필수 export 요소 정의
- Internal 모듈 접근 금지 규칙
- 린팅 규칙 적용


### 성능 고려사항

**배럴 파일 최적화**:[^28]

- Tree-shaking 고려한 export 구성
- 번들 크기 모니터링
- 필요시 직접 import 허용


## 결론

FSD는 대규모 프론트엔드 프로젝트의 구조적 복잡성을 해결하기 위한 강력한 방법론입니다. Next.js와 함께 사용할 때는 네이밍 충돌 문제를 해결하고, 서버/클라이언트 컴포넌트 분리를 고려해야 합니다.

성공적인 FSD 도입을 위해서는:

- 팀의 프로젝트 규모와 복잡도 평가
- 점진적 도입과 팀 교육
- ESLint 등 자동화 도구 활용
- 실용적 접근과 규칙의 유연한 적용

이 핸드북을 통해 FSD의 핵심 개념을 이해하고, Next.js 프로젝트에 효과적으로 적용할 수 있기를 바랍니다.

<div style="text-align: center">⁂</div>

[^1]: https://alraffe.hashnode.dev/nextjs-fsd

[^2]: https://blog.bizspring.co.kr/테크/효율적인-프론트엔드-설계를-위한-feature-sliced-designfsd의-이해/

[^3]: https://github.com/feature-sliced/documentation

[^4]: https://velog.io/@clydehan/FSDFeature-Sliced-Design-완벽-가이드

[^5]: https://doohyeong.tistory.com/255

[^6]: https://www.codecentric.de/en/knowledge-hub/blog/feature-sliced-design-and-good-frontend-architecture

[^7]: https://j-ho.dev/28/

[^8]: https://velog.io/@seoyong-lee/FSDFeature-Sliced-Design-Architecture

[^9]: https://seo-tory.tistory.com/91

[^10]: https://jhb0412.tistory.com/68

[^11]: https://khjk.tistory.com/240

[^12]: https://github.com/vgratsilev/eslint-plugin-fsd-import/blob/main/docs/rules/layer-imports.md

[^13]: https://sungali.tistory.com/entry/FSD-Reference-Slices-and-segments

[^14]: https://velog.io/@seoyong-lee/FSD-아키텍처에-컴파운드-패턴-적용해보기

[^15]: https://github.com/feature-sliced/steiger/blob/master/packages/steiger-plugin-fsd/src/public-api/README.md

[^16]: https://velog.io/@xxyeon129/기능-분할-설계Feature-Sliced-Design-FSD-아키텍처

[^17]: https://hackernoon.com/how-to-deal-with-the-nextjs-app-router-and-fsd-problem

[^18]: https://dev.to/m_midas/how-to-deal-with-nextjs-using-feature-sliced-design-4c67

[^19]: https://23life.tistory.com/entry/nextjs에-FSD-폴더-구조-패턴-적용하기

[^20]: https://kr.linkedin.com/posts/boaz-hwang_nextjs%EC%97%90%EC%84%9C-feature-sliced-designfsd%EC%9D%84-%EC%A0%81%EC%9A%A9%ED%95%A0-activity-7204100009250684928-47Gj

[^21]: https://acid.tistory.com/4

[^22]: https://www.linkedin.com/pulse/enforcing-architectural-boundaries-eslint-amr-bahaa-4vljf

[^23]: https://www.devunpacker.com/blog/eslint-for-fsd

[^24]: https://www.godeltech.com/blog/feature-sliced-design-a-guide-to-scalable-frontend-architecture/

[^25]: https://hackernoon.com/understanding-feature-sliced-design-benefits-and-real-code-examples

[^26]: https://dev.to/algoorgoal/feature-sliced-design-review-22k0

[^27]: https://www.linkedin.com/posts/54k_why-feature-sliced-design-fsd-isnt-a-revolutionary-activity-7288485090102272000-IZD0

[^28]: https://ppogeul.tistory.com/23

[^29]: https://happhee-dev.tistory.com/51

[^30]: https://github.com/feature-sliced

[^31]: https://workshop-code.tistory.com/101

[^32]: https://emewjin.github.io/feature-sliced-design/

[^33]: https://velog.io/@heoze/슬기롭게-폴더-구조-설계하기-FSD-간단히-살펴보기-feat.-Next.js-App-routing-방식

[^34]: https://feature-sliced.github.io/documentation/

[^35]: https://wonderfulwonder.tistory.com/110

[^36]: https://xionwcfm.tistory.com/462

[^37]: https://ldd6cr-adness.tistory.com/300

[^38]: https://hyunwoo12.tistory.com/72

[^39]: https://github.com/feature-sliced/documentation/releases

[^40]: https://www.gojimin.com/posts/fsd

[^41]: https://developers.hyundaimotorgroup.com/blog/399

[^42]: https://iamseon.com/post/기능-분할-설계FSD-아키텍처로-FE-구조-최적화하기

[^43]: https://www.reddit.com/r/nextjs/comments/1dc17tv/best_practice_for_folder_structure_in_nextjs_app/

[^44]: https://stackoverflow.com/questions/78045996/why-does-next-js-14-fail-to-resolve-page-route-paths

[^45]: https://github.com/nikolay-malygin/i18n-Next.js-14-FSD

[^46]: https://velog.io/@fastfox/기능-분할-설계Feature-Sliced-Design-FSD

[^47]: https://blog.bitsrc.io/developing-scalable-frontends-with-feature-sliced-design-fsd-a2e5aa33d02c

[^48]: https://velog.io/@ujinsimss_/FSD-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-FSD-%ED%8C%A8%ED%84%B4%EC%9D%84-Next-App-%EB%9D%BC%EC%9A%B0%ED%8C%85%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EC%9E%90

[^49]: https://dev.to/m_midas/feature-sliced-design-the-best-frontend-architecture-4noj

[^50]: https://github.com/vgratsilev/eslint-plugin-fsd-import/blob/main/docs/rules/public-api-imports.md

[^51]: https://aws.amazon.com/blogs/infrastructure-and-automation/handling-circular-dependency-errors-in-aws-cloudformation/

[^52]: https://stackoverflow.com/questions/78880672/how-do-i-prevent-circular-dependencies-in-a-general-tree

[^53]: https://velog.io/@poyal/FSDFeature-Sliced-Design-알아가기

[^54]: https://stackoverflow.com/questions/5672308/avoid-circular-references-with-services-and-di

[^55]: https://velog.io/@gehwan96/Handling-Circular-dependency

[^56]: https://www.hkfsd.gov.hk/eng/fire_protection/notices/circular.html

[^57]: https://www.hkfsd.gov.hk/eng/fire_protection/notices/

[^58]: https://lapidix.dev/posts/fsd-ddd-clean-architecture

[^59]: https://www.housing.org.hk/files/filemanager/file/2021-01/FSD-Circular-Letter-No.-2021-1---Shutdown-of-Fire-Service-Installations-for-Maintenance--Inspection--Modification-or-Repair.pdf

[^60]: https://www.archsd.gov.hk/media/publications-publicity/general-specification-for-fire-service-installation/e36.pdf

[^61]: https://www.youtube.com/watch?v=aY4r8z7JYA4

[^62]: https://nx.dev/technologies/eslint/eslint-plugin/recipes/enforce-module-boundaries

[^63]: https://philrich.dev/fsd-vs-clean-architecture/

[^64]: https://www.npmjs.com/package/eslint-plugin-boundaries/v/1.0.0

[^65]: https://min71.dev/posts/fsd-ddd-clean-architecture

[^66]: https://www.linkedin.com/pulse/folder-structures-react-projects-feature-sliced-design-alembikov-m3vle

[^67]: https://github.com/javierbrea/eslint-plugin-boundaries

[^68]: https://fantasmith.com/challenge/opensource/fsd_lint/

[^69]: https://github.com/feature-sliced/documentation/discussions/625

[^70]: https://www.reddit.com/r/dotnet/comments/1iysrq4/what_are_your_experience_with_clean_architecture/

[^71]: https://www.npmjs.com/package/@feature-sliced/eslint-config

[^72]: https://socket.dev/npm/package/eslint-plugin-fsd-lint

[^73]: https://dev.to/petrtcoi/my-experience-with-fsd-feature-sliced-design-architecture-5a6n

[^74]: https://pmc.ncbi.nlm.nih.gov/articles/PMC9996345/

[^75]: https://www.reddit.com/r/ObsidianMD/comments/u79m8v/are_you_using_a_folder_structure_or_a_flat/

[^76]: https://www.accessoriesfortesla.com/post/tesla-highlights-fsd-s-key-benefits-despite-supervised-status

[^77]: https://ottomate.carbonai.ca/resources/comparing-ottofiles-flat-content-management-vs-conventional-folder-content-management

[^78]: https://www.teslarati.com/tesla-reiterates-fsd-supervised-biggest-advantage/

[^79]: https://dev.to/fpaghar/folder-structuring-techniques-for-beginner-to-advanced-react-projects-30d7

[^80]: https://www.reddit.com/r/TeslaLounge/comments/19elg7d/fsd_why/

[^81]: https://inpractise.com/articles/tesla-process-advantage

[^82]: https://careers.moneyforward.vn/blog/feature-sliced-design-in-frontend

[^83]: https://dev.to/m_midas/what-do-you-think-about-feature-sliced-design-fsd-25a8

[^84]: https://velog.io/@teo/folder-structure

[^85]: https://www.reddit.com/r/reactjs/comments/1afywy4/codebase_examples_of_featuredriven_or_vertical/

[^86]: https://stackoverflow.com/questions/78723996/folder-structure-conflict-of-expo-react-native-app-with-fsd-v-2

