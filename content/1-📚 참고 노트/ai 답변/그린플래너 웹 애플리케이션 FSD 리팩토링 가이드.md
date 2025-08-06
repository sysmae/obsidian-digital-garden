---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# 그린플래너 웹 애플리케이션 FSD 리팩토링 가이드

현재 프로젝트 구조를 분석한 결과, 다음과 같은 단계별 FSD 리팩토링 전략을 제안합니다.

## 현재 구조 분석

현재 그린플래너 프로젝트는 **기술 중심(Tech-centric)** 구조로 되어 있습니다:[^1][^2]

- 컴포넌트별 분류: `components/`, `hooks/`, `store/`, `utils/`
- Next.js App Router와 혼재된 구조
- 도메인 로직이 여러 폴더에 분산


## FSD 리팩토링 전략

### 1단계: 기본 FSD 구조 설정

**새로운 폴더 구조**:[^3][^4]

```
src/
├── app/                    # FSD app 레이어
│   ├── providers/         # AppProviders, AuthTokenProvider 등
│   ├── styles/           # globals.css, Tailwind 설정
│   ├── config/           # 전역 설정
│   └── index.ts
├── pages/                 # FSD pages 레이어 (또는 views)
├── widgets/              # 독립적 UI 블록
├── features/             # 비즈니스 기능
├── entities/             # 비즈니스 엔티티
└── shared/               # 공통 요소

app/                       # Next.js App Router (루트 유지)
├── (withRightPanel)/
├── api/
├── layout.tsx
└── page.tsx
```


### 2단계: 도메인별 기능 분석 및 분류

**비즈니스 도메인 식별**:[^5][^6]

**Entities (비즈니스 엔티티)**:

- `user` - 사용자 정보
- `building` - 건물 정보
- `report` - 보고서
- `ticket` - 이용권
- `land` - 토지 정보

**Features (사용자 기능)**:

- `auth` - 인증 (로그인, 회원가입)
- `search-building` - 건물 검색
- `generate-report` - 보고서 생성
- `manage-wishlist` - 관심 부동산 관리
- `energy-analysis` - 에너지 분석

**Widgets (UI 블록)**:

- `header` - 네비게이션
- `map-viewer` - 지도 컴포넌트
- `property-panel` - 부동산 정보 패널
- `report-viewer` - 보고서 뷰어


### 3단계: 점진적 마이그레이션 계획

**Phase 1: Shared 레이어 구축**:[^5][^1]

```
src/shared/
├── ui/                   # 기존 components/ui/* → shared/ui/*
│   ├── button/
│   ├── modal/
│   └── form/
├── api/                  # API 클라이언트 통합
│   ├── client.ts
│   ├── types.ts
│   └── interceptors.ts
├── config/               # 기존 constants/* → shared/config/*
│   ├── constants.ts
│   ├── env.ts
│   └── auth.config.ts
├── lib/                  # 기존 utils/* → shared/lib/*
│   ├── utils.ts
│   ├── date.ts
│   └── validation.ts
└── types/               # 전역 타입 정의
    ├── api.ts
    └── common.ts
```

**Phase 2: Entities 레이어 구축**:

```
src/entities/
├── user/
│   ├── model/
│   │   ├── types.ts      # User 인터페이스
│   │   └── store.ts      # 기존 store/auth/* 일부
│   ├── api/
│   │   └── userApi.ts    # 사용자 관련 API
│   └── index.ts
├── building/
│   ├── model/
│   │   ├── types.ts      # Building 관련 타입
│   │   └── store.ts      # 기존 store/map/* 일부
│   ├── api/
│   │   └── buildingApi.ts
│   └── index.ts
└── report/
    ├── model/
    │   ├── types.ts
    │   └── reportStore.ts
    ├── api/
    │   └── reportApi.ts
    └── index.ts
```

**Phase 3: Features 레이어 구축**:

```
src/features/
├── auth/
│   ├── ui/
│   │   ├── LoginForm.tsx     # 기존 components/auth/*
│   │   ├── RegisterForm.tsx
│   │   └── SocialLogin.tsx
│   ├── model/
│   │   ├── authSlice.ts      # 기존 store/auth/*
│   │   └── hooks.ts          # 기존 hooks/auth/*
│   ├── api/
│   │   └── authApi.ts
│   └── index.ts
├── search-building/
│   ├── ui/
│   │   ├── SearchBar.tsx
│   │   └── SearchResults.tsx
│   ├── model/
│   │   └── searchStore.ts
│   └── index.ts
└── manage-wishlist/
    ├── ui/
    │   ├── WishButton.tsx
    │   └── WishList.tsx
    ├── model/
    │   └── wishStore.ts      # 기존 store/wishBld/*
    └── index.ts
```

**Phase 4: Widgets 레이어 구축**:

```
src/widgets/
├── header/
│   ├── ui/
│   │   └── Header.tsx        # 기존 components/common/Header
│   ├── model/
│   │   └── navigationStore.ts
│   └── index.ts
├── map-viewer/
│   ├── ui/
│   │   ├── MapContainer.tsx  # 기존 components/map/*
│   │   └── MapControls.tsx
│   ├── model/
│   │   └── mapStore.ts       # 기존 store/map/*
│   └── index.ts
└── property-panel/
    ├── ui/
    │   └── PropertyPanel.tsx  # 기존 components/ui/right-panels/*
    ├── model/
    │   └── panelStore.ts     # 기존 store/ui/*
    └── index.ts
```

**Phase 5: Pages 레이어 구축**:

```
src/pages/                    # 또는 views로 네이밍
├── home/
│   ├── ui/
│   │   └── HomePage.tsx
│   └── index.ts
├── map/
│   ├── ui/
│   │   └── MapPage.tsx
│   ├── model/
│   │   └── mapPageStore.ts
│   └── index.ts
└── terms/
    ├── ui/
    │   ├── PrivacyPolicy.tsx
    │   └── ServiceTerms.tsx
    └── index.ts
```


### 4단계: Public API 패턴 적용

**각 슬라이스마다 index.ts 파일 생성**:[^7][^8]

```typescript
// src/features/auth/index.ts
export { LoginForm } from './ui/LoginForm';
export { RegisterForm } from './ui/RegisterForm';
export { useAuth } from './model/hooks';
export { login, logout } from './api/authApi';
export type { LoginCredentials, RegisterData } from './model/types';

// src/entities/user/index.ts
export type { User, UserProfile } from './model/types';
export { useUserStore } from './model/store';
export { fetchUser, updateUser } from './api/userApi';

// src/widgets/header/index.ts
export { Header } from './ui/Header';
```


### 5단계: 의존성 규칙 적용

**ESLint 설정으로 의존성 규칙 강제**:[^9][^10]

```javascript
// .eslintrc.js
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


### 6단계: Next.js 특화 처리

**App Router와 FSD 통합**:[^4][^11]

```typescript
// app/map/page.tsx
import { MapPage } from '@/src/pages/map';

export default MapPage;

// app/(withRightPanel)/layout.tsx
import { Header } from '@/src/widgets/header';
import { RightPanelContainer } from '@/src/widgets/panel-container';

export default function WithRightPanelLayout({
  children,
}: {
  children: React.ReactNode;
}) {
  return (
    <>
      <Header />
      <div className="flex">
        <main>{children}</main>
        <RightPanelContainer />
      </div>
    </>
  );
}
```


## 마이그레이션 우선순위

### 높은 우선순위 (즉시 시작)

1. **Shared 레이어 구축** - 기존 `shared/ui`, `lib`, `config` 이동
2. **Public API 패턴 도입** - 각 모듈별 `index.ts` 생성
3. **Import 경로 정리** - 절대 경로 사용 (`@/src/shared/ui/Button`)

### 중간 우선순위 (2주 내)

1. **Entities 분리** - `user`, `building`, `report` 엔티티 생성
2. **Features 분리** - `auth`, `search` 기능 우선 분리
3. **ESLint 규칙 적용** - 의존성 규칙 강제

### 낮은 우선순위 (1개월 내)

1. **Widgets 분리** - 복잡한 UI 컴포넌트들 분리
2. **Pages 리팩토링** - 페이지별 로직 정리
3. **테스트 코드 추가** - 각 레이어별 테스트 작성

## 예상 효과

### 즉시 개선 효과

- **코드 탐색성 향상**: 기능별로 관련 코드가 한 곳에 모임[^12][^1]
- **Import 경로 명확화**: Public API를 통한 명시적 의존성[^8]
- **신규 개발자 적응 용이**: 표준화된 구조[^5]


### 장기적 개선 효과

- **유지보수성 향상**: 기능별 독립적 수정 가능[^13]
- **테스트 용이성**: 각 레이어별 독립적 테스트[^5]
- **확장성 보장**: 새로운 기능 추가 시 명확한 위치[^12]


## 주의사항

### 점진적 적용 필요

- 한 번에 모든 구조를 바꾸지 말고 단계별 진행[^14]
- 기존 코드의 동작을 보장하면서 점진적 마이그레이션
- 팀원들의 학습 곡선 고려[^15]


### 실용적 접근

- 작은 컴포넌트는 무리하게 분리하지 않음[^16]
- 비즈니스 로직이 명확하지 않은 경우 기존 구조 유지
- 프로젝트 규모에 맞는 적절한 수준의 FSD 적용

이러한 단계별 접근을 통해 그린플래너 프로젝트를 점진적으로 FSD 구조로 전환할 수 있으며, 코드의 유지보수성과 확장성을 크게 개선할 수 있을 것입니다.

<div style="text-align: center">⁂</div>

[^1]: https://blog.bizspring.co.kr/테크/효율적인-프론트엔드-설계를-위한-feature-sliced-designfsd의-이해/

[^2]: https://alraffe.hashnode.dev/nextjs-fsd

[^3]: https://velog.io/@ujinsimss_/FSD-%ED%8C%A8%ED%84%B4%EC%9D%B4%EB%9E%80-FSD-%ED%8C%A8%ED%84%B4%EC%9D%84-Next-App-%EB%9D%BC%EC%9A%B0%ED%8C%85%EC%97%90%EC%84%9C-%EC%82%AC%EC%9A%A9%ED%95%B4%EB%B3%B4%EC%9E%90

[^4]: https://hackernoon.com/how-to-deal-with-the-nextjs-app-router-and-fsd-problem

[^5]: https://www.codecentric.de/en/knowledge-hub/blog/feature-sliced-design-and-good-frontend-architecture

[^6]: https://blog.bitsrc.io/developing-scalable-frontends-with-feature-sliced-design-fsd-a2e5aa33d02c

[^7]: https://github.com/feature-sliced/steiger/blob/master/packages/steiger-plugin-fsd/src/public-api/README.md

[^8]: https://seo-tory.tistory.com/91

[^9]: https://github.com/vgratsilev/eslint-plugin-fsd-import/blob/main/docs/rules/layer-imports.md

[^10]: https://www.devunpacker.com/blog/eslint-for-fsd

[^11]: https://dev.to/m_midas/how-to-deal-with-nextjs-using-feature-sliced-design-4c67

[^12]: https://www.godeltech.com/blog/feature-sliced-design-a-guide-to-scalable-frontend-architecture/

[^13]: https://hackernoon.com/understanding-feature-sliced-design-benefits-and-real-code-examples

[^14]: https://dev.to/theonlineaid/mastering-nextjs-structuring-your-large-scale-project-for-success-42bo

[^15]: https://dev.to/algoorgoal/feature-sliced-design-review-22k0

[^16]: https://github.com/feature-sliced/documentation/releases

[^17]: https://github.com/feature-sliced/documentation/discussions/756

[^18]: https://trailhead.salesforce.com/content/learn/modules/cc-create-functional-consulting-documents/cc-functional-spec-doc

[^19]: https://www.youtube.com/watch?v=BaUsKK6AX5Q

[^20]: https://www.youtube.com/watch?v=aY4r8z7JYA4

[^21]: https://www.politesi.polimi.it/retrieve/a81cb05d-4130-616b-e053-1605fe0a889a/Thesis - Gabriela Menin Machado.pdf

[^22]: https://www.reddit.com/r/nextjs/comments/19c3kb9/bestpro_scalable_next_js_project_folder_structure/

[^23]: https://world-developer.tistory.com/87

[^24]: http://www.researchinchina.com/Htmls/Report/2024/74959.html

[^25]: https://doohyeong.tistory.com/261

[^26]: https://www.the-autonomous.com/wp-content/uploads/2023/12/wg-safetyarchitecture-full-report-a4.pdf

[^27]: https://www.whs.mil/About-WHS/Directorates/Facilities-Services-Directorate-FSD/

[^28]: https://patentpc.com/blog/the-patent-protection-behind-teslas-full-self-driving-system

