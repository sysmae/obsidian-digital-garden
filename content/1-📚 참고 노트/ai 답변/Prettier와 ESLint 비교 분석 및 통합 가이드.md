---
{"publish":true,"created":"2025-08-07T12:50:57.954+09:00","modified":"2025-08-07T12:56:35.963+09:00","cssclasses":""}
---


<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Prettier와 ESLint 비교 분석 및 통합 가이드

**핵심 요약**
Prettier는 **코드 포맷팅**에만 집중해 일관된 스타일을 자동 적용하는 도구이며, ESLint는 **정적 코드 분석(린팅)**을 통해 잠재적 버그와 스타일·품질 위반을 탐지·교정하는 도구이다. 두 도구를 함께 사용하면 **형식 일관성**과 **코드 품질**을 동시에 확보할 수 있다.

## 1. ESLint 개요

ESLint는 2013년 Nicholas C. Zakas가 개발한 **플러깅 가능(pluggable) 린터**로, JavaScript/TypeScript 코드에서 문법 오류, 스타일 위반, 잠재적 버그를 찾아내고 자동 수정(fix) 기능을 제공한다.[^1]

- **주요 기능**:
    - 코드 규칙(rule)별 경고·오류 지정
    - 커스텀 룰 작성·플러그인 확장
    - 자동 수정(`--fix`)
    - 에디터·CI 통합


## 2. Prettier vs. ESLint 비교

| 구분 | Prettier | ESLint |
| :-- | :-- | :-- |
| 주요 목적 | 코드 포맷팅[^2] | 정적 분석 및 코드 품질 검사[^1] |
| 동작 방식 | 파싱→AST 재인쇄[^3] | 토큰 분석→룰별 검사 |
| 구성 옵션 | 최소화된 의견 기반 옵션[^2] | 다양한 룰·레벨·플러그인 설정 가능 |
| 언어 지원 | JS/TS·CSS·HTML·Markdown 등 다수[^3] | JS/TS 중심, 플러그인으로 확장 |
| 자동 수정 | 전체 코드 재포맷[^3] | 룰별 부분 수정(`--fix`)[^1] |
| 학습 곡선 | 낮음, 즉시 사용 가능 | 높음, 룰 이해 필요 |
| 성능 | 빠름 (형식만 처리) | 느림 (심층 분석 수행) |
| 명령어 | `prettier` | `eslint` |
| 통합 검사 | 단순 포맷 일관성 검사 | 버그 탐지·스타일 위반 리포트 |

## 3. 충돌 원인 및 통합 전략

Prettier와 ESLint의 **형식 관련 룰**이 중복·충돌할 수 있다. 예를 들어 ESLint의 `indent`, `quotes`, `max-len` 등이 Prettier와 다르게 동작하면 일관성 문제가 발생한다.[^4]

### 3.1 충돌 해결 플러그인

- **eslint-config-prettier**
    - Prettier와 충돌하는 모든 ESLint 형식 룰을 비활성화하여 **충돌 제로** 환경 제공.[^5]
- **eslint-plugin-prettier**
    - Prettier를 ESLint 룰로 실행해 결과를 오류로 보고. 하지만 속도 저하 및 중복 리포팅 단점 존재.[^6]
- **plugin:prettier/recommended**
    - `eslint-plugin-prettier`와 `eslint-config-prettier`를 조합한 권장 설정. ESLint가 Prettier 형식 위반을 오류로 표시.[^5]


### 3.2 실행 순서

- **Prettier → ESLint** 순으로 실행:

1. Prettier로 전체 코드 포맷팅
2. ESLint `--fix`로 남은 코드 품질·논리 오류 교정
```jsonc
// package.json scripts 예시
{
  "scripts": {
    "format": "prettier --write .",
    "lint": "eslint --fix ."
  }
}
```


## 4. 설정 및 사용 예시

### 4.1 공통 설치

```bash
npm install --save-dev --save-exact prettier eslint eslint-config-prettier eslint-plugin-prettier
```


### 4.2 `.prettierrc`

```json
{
  "printWidth": 80,
  "singleQuote": true,
  "trailingComma": "all"
}
```


### 4.3 `.eslintrc.json`

```json
{
  "env": { "browser": true, "node": true, "es2021": true },
  "extends": [
    "eslint:recommended",
    "plugin:react/recommended",
    "prettier"                    // eslint-config-prettier 적용
  ],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"  // eslint-plugin-prettier 활성화
  }
}
```


### 4.4 VS Code 연동

```jsonc
// .vscode/settings.json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```


## 5. 고급 통합 팁

- **lint-staged** + **husky**: 변경된 파일만 포맷/린트

```jsonc
// package.json
{
  "husky": { "hooks": { "pre-commit": "lint-staged" } },
  "lint-staged": {
    "*.{js,jsx,ts,tsx}": ["prettier --write", "eslint --fix --cache"]
  }
}
```

- **CI 검사**:
    - `prettier --check .` → `eslint .` 순으로 파이프라인 구성
- **프로젝트별 예외**: `.eslintignore`·`.prettierignore` 활용


## 6. 결론 및 권장 워크플로

1. **Prettier**로 모든 코드의 일관된 포맷 확보
2. **ESLint**로 코드 품질·버그 제거
3. **eslint-config-prettier**로 형식 규칙 충돌 방지
4. **eslint-plugin-prettier**로 형식 위반을 린트 오류로 감지
5. **lint-staged + husky**로 커밋 전 자동 포맷/린트

이렇게 구성하면 **스타일 논쟁을 제거**하고, **코드 품질**을 동시에 유지하며, **팀 협업 효율**을 극대화할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://eslint.org/docs/latest/about/

[^2]: https://prettier.io/docs/why-prettier

[^3]: https://prettier.io/docs/

[^4]: https://dataengineerstudy.tistory.com/268

[^5]: https://betterstack.com/community/guides/scaling-nodejs/prettier-vs-eslint/

[^6]: https://yrnana.dev/post/2021-03-21-prettier-eslint/

[^7]: https://dev.to/kazemmdev/how-to-use-eslint-to-boost-your-programming-skills-3ced

[^8]: https://www.geeksforgeeks.org/javascript/eslint-pluggable-javascript-linter/

[^9]: https://aglowiditsolutions.com/blog/eslint-vs-prettier/

[^10]: https://blog.logrocket.com/using-prettier-eslint-javascript-formatting/

[^11]: https://velog.io/@https00200/project-setting-eslint-prettier

[^12]: https://www.reddit.com/r/typescript/comments/1kif745/what_do_you_use_for_code_style_linting/

[^13]: https://soojae.tistory.com/39

[^14]: https://prettier.io/docs/integrating-with-linters

[^15]: https://trunk.io/blog/improving-linting-experience-in-eslint-s-open-source-repo-with-trunk-code-quality

[^16]: https://www.reddit.com/r/node/comments/w95n80/why_use_prettier_if_eslint_can_format/

[^17]: https://github.com/prettier/eslint-plugin-prettier

[^18]: https://eslint.org

[^19]: https://helloinyong.tistory.com/325

[^20]: https://dudghsx.tistory.com/entry/ESLint-Prettier-적용하기

[^21]: https://eslint.org/docs/latest/use/getting-started

[^22]: https://dev-102.tistory.com/entry/eslint와-prettier-구분하기aka-eslint-plugin-prettier-제거하기

