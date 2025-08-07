---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# ESLint 핸드북

## 핵심 요약

**ESLint**는 **플러그형 JavaScript 린터**로, 개발자가 직접 규칙을 정의·확장하여 코드 품질을 일관되게 유지하고 버그를 방지하도록 고안되었다. JavaScript의 동적·느슨한 타입 특성으로 인한 오류를 사전에 식별하며, 모든 규칙과 포매터가 플러그인 형태로 제공되어 완전한 사용자 정의가 가능하다.[^1]

## 1. 탄생 배경 및 철학

ESLint는 2013년 Nicholas C. Zakas가 **개발자가 직접 린팅 규칙을 작성·공유**할 수 있도록 만들기 위해 시작되었다.[^1]

- **정적 분석 기반**: 코드 실행 없이 문법 오류, 잠재적 버그, 스타일 위반을 탐지
- **완전한 플러그형 구조**: 모든 규칙·포매터가 플러그인으로 구현되어 런타임에 추가 또는 교체 가능
- **스타일 중립성**: 특정 코딩 스타일을 강제하지 않고, 프로젝트 요구에 맞춰 자유롭게 켜고 끌 수 있음
- **투명한 커뮤니케이션**: 문서화와 테스트를 중시하여 OSS 생태계 내 신뢰성 유지


## 2. 아키텍처 구조

ESLint는 여러 모듈로 나뉘어 있으며, 각 모듈은 명확한 역할을 수행한다.[^2]


| 모듈 경로 | 주요 역할 |
| :-- | :-- |
| **bin/eslint.js** | CLI 진입점; 명령어 인자 파싱 후 `cli`로 전달 |
| **lib/api.js** | Node.js API 제공; `Linter`, `ESLint`, `RuleTester`, `SourceCode` 클래스 노출 |
| **lib/cli.js** | CLI 엔진; 명령 실행, 파일 입출력, 디렉토리 탐색 담당 |
| **lib/cli-engine/** | `CLIEngine` 클래스: 설정·파서·플러그인·포매터 로딩 및 코드 검증 수행 |
| **lib/linter/** | `Linter` 클래스: AST 기반 코드 검증(입출력 없이 순수 로직만 담당) |
| **lib/rule-tester/** | `RuleTester` 클래스: Mocha 기반 단위 테스트 환경 제공 |
| **lib/source-code/** | `SourceCode` 클래스: 소스코드 파싱 결과를 객체 형태로 관리 |

## 3. 세부 사용법

ESLint 사용 시 **설치**, **설정**, **실행**의 3단계를 거친다. 아래에서는 각 단계를 3단계로 세분화하여 설명한다.

### 3.1 설치 단계

1. **프로젝트 초기화**: Node.js 및 `package.json` 준비
2. **ESLint 설치**
    - npm: `npm install --save-dev eslint@latest`
    - yarn: `yarn add --dev eslint@latest`
3. **초기 설정 도우미 실행**

```
npx eslint --init
```

    - 실행 환경, 스타일 가이드(예: Airbnb, Google), 사용 언어(ES6, TypeScript) 선택
    - `eslint.config.js` 또는 `.eslintrc.*` 파일 자동 생성

### 3.2 구성 단계

1. **공유 설정 확장**

```js
// eslint.config.js
import { defineConfig } from "eslint/config";
export default defineConfig({
  extends: ["eslint:recommended", "google"],
  rules: {
    "no-unused-vars": "warn",
    "no-undef": "error"
  }
});
```

2. **플러그인 추가**

```
npm install --save-dev eslint-plugin-boundaries
```

```js
plugins: ["boundaries"],
rules: {
  "boundaries/element-types": ["error", { default: "disallow", rules: [...] }]
}
```

3. **환경별 및 파일별 오버라이드**

```js
overrides: [
  {
    files: ["**/*.ts"],
    rules: {
      "@typescript-eslint/no-unused-vars": "error"
    }
  }
]
```


### 3.3 실행 단계

1. **CLI를 통한 검증**

```
npx eslint src/**/*.js
```

2. **자동 수정 옵션**

```
npx eslint src --fix
```

3. **CI 파이프라인 통합**

```json
// package.json
"scripts": {
  "lint": "eslint . --max-warnings=0",
  "lint:fix": "eslint . --fix"
}
```


## 4. 고급 활용 및 확장

- **커스텀 규칙 작성**: `Rule API`를 통해 독자적인 규칙 구현 가능.[^1]
- **포매터 확장**: JSON, HTML 등 다양한 출력 포맷 추가 지원.[^1]
- **타사 플러그인 활용**: `eslint-plugin-import`, `@typescript-eslint`, `eslint-plugin-boundaries` 등으로 아키텍처·종속성 제어 강화.
- **편집기 통합**: VSCode, WebStorm 등 주요 에디터 플러그인으로 린트 결과 실시간 표시.


## 5. 결론 및 권장 설정

ESLint는 **프로젝트 규모나 팀 크기에 상관없이** 모든 JavaScript/TypeScript 개발에 필수적인 도구이다. 초기 설정에는 다소 시간이 걸리지만, 일단 안정적인 규칙과 CI 통합이 완료되면 코드 품질 유지와 신규 팀원 온보딩이 획기적으로 간소화된다. 프로젝트 시작 시 다음 구성을 권장한다:

1. `eslint:recommended` 기반 설정
2. Airbnb 또는 Google 스타일 가이드 확장
3. TypeScript 지원 플러그인(`@typescript-eslint`)
4. CI 파이프라인에 lint 스크립트 추가

이로써 **ESLint 핸드북**이 완성되었으며, 각 단계별 가이드를 따라 최적의 린팅 환경을 구축할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://eslint.org/docs/latest/about/

[^2]: https://eslint.org/docs/latest/contribute/architecture/

[^3]: https://eslint.org/docs/latest/use/getting-started

[^4]: https://eslint.org/docs/latest/use/

[^5]: https://dev.to/shivambmgupta/eslint-what-why-when-how-5f1d

[^6]: https://desosa.nl/projects/eslint/2020/03/16/vision-to-architecture

[^7]: https://ingg.dev/eslint/

[^8]: https://www.linkedin.com/pulse/enforcing-architectural-boundaries-eslint-amr-bahaa-4vljf

[^9]: https://eslint.org/docs/latest/use/configure/

[^10]: https://github.com/eslint/eslint

[^11]: https://github.com/CodelyTV/eslint-plugin-hexagonal-architecture

[^12]: https://nextjs.org/docs/app/api-reference/config/eslint

[^13]: https://www.linkedin.com/advice/0/what-benefits-using-eslint-code-quality-consistency

[^14]: https://www.npmjs.com/package/eslint-plugin-hexagonal-architecture

[^15]: https://www.z1.digital/blog/eslint-guide-how-to-use-it-with-confidence

[^16]: https://eslint.org

[^17]: https://github.com/javierbrea/eslint-plugin-boundaries

[^18]: https://tech.kakao.com/posts/375

[^19]: https://velog.io/@rookieand/ESLint-Prettier를-왜-써야-할까

[^20]: https://www.kimcoder.io/blog/clean-frontend-architecture

