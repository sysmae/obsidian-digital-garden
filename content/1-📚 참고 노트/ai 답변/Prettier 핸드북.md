---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# Prettier 핸드북

**핵심 요약**
Prettier는 코드 스타일 논쟁을 제거하고 일관된 코드 포맷팅을 자동화하기 위해 만들어진 **의견 기반(opinionated) 코드 포매터**이다. 파서(parser)→AST→프린터(printer) 구조로 동작하며, 다양한 언어 지원과 풍부한 통합 옵션을 통해 **개발 생산성**과 **코드 가독성**을 크게 향상시킨다.

## 1. 도입 배경 및 목적

소프트웨어 팀 내에서 스타일 규칙 협의와 유지보수는 비생산적인 논쟁을 야기하고, 코드 리뷰를 지연시킨다.

- **스타일 논쟁 종식**: Prettier는 자동화된 스타일 가이드를 제공하여 “들여쓰기, 따옴표, 줄바꿈”과 같은 세부 규칙 논의를 제거한다.[^1]
- **일관성 확보**: 모든 코드베이스에 동일한 포맷팅을 강제하여 가독성과 유지보수성을 높인다.[^2]
- **신규 기여자 진입 장벽 완화**: 프로젝트 합의된 스타일을 몰라도 즉시 일관된 코드를 작성할 수 있다.[^1]


## 2. 아키텍처 및 동작 원리

Prettier는 크게 세 가지 컴포넌트로 구성된다:

1. **파서(Parser)**
    - 입력된 코드를 언어별 문법에 맞춰 토큰화 및 AST(Abstract Syntax Tree)로 변환
2. **AST 포맷팅**
    - 트리 탐색을 통해 줄 길이, 들여쓰기, 공백 옵션 등을 고려해 재배치
3. **프린터(Printer)**
    - 재구성된 AST를 텍스트로 출력하며, **프린터 규칙**에 따라 자동 줄바꿈 및 여백 삽입

이 구조 덕분에 원본 스타일을 **파싱 단계에서 제거**하고 일관된 규칙으로 재인쇄할 수 있다.[^3][^2]

## 3. 주요 특징

- **광범위 언어 지원**: JavaScript·TypeScript·JSX·Angular·Vue·CSS·HTML·Markdown·GraphQL·YAML 등 거의 모든 프론트엔드 언어 지원.[^2]
- **플러그인 아키텍처**: 추가 언어·프레임워크 지원을 플러그인으로 확장 가능.
- **의견 기반 설정**: 최소한의 옵션(`printWidth`, `tabWidth`, `singleQuote`, `trailingComma` 등)만 제공하여 설정 복잡도 최소화.
- **에디터 및 CI 통합**: VS Code·WebStorm·IntelliJ 플러그인, CLI, Git 훅(husky) 및 GitHub Actions 등.


## 4. 설치 및 기본 사용법

### 4.1 설치

프로젝트 루트에서 패키지 매니저를 통해 설치:

```bash
npm install --save-dev --save-exact prettier  
# 또는  
yarn add --dev --exact prettier  
```

빈 설정파일 생성:

```bash
node --eval "fs.writeFileSync('.prettierrc','{}\n')"  
```

무시할 파일 지정(.prettierignore):

```
build/
coverage/
```


### 4.2 CLI 사용 예시

- 전체 파일 포맷팅:

```bash
npx prettier . --write  
```

- 포맷 검사:

```bash
npx prettier --check "src/**/*.js"  
```

- 특정 옵션 적용:

```bash
npx prettier --write --single-quote --trailing-comma all .  
```


## 5. 설정 파일(.prettierrc)

```json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "bracketSpacing": true,
  "arrowParens": "avoid"
}
```

- **printWidth**: 최대 줄 길이
- **tabWidth/useTabs**: 탭 너비 및 탭 사용 여부
- **semi**: 세미콜론 강제 삽입
- **singleQuote**: 문자열 따옴표 스타일
- **trailingComma**: 후행 콤마 처리 방식
- **arrowParens**: 화살표 함수 파라미터 괄호 처리
[^4][^5]


## 6. 에디터 통합 및 자동화

- **VS Code**: `esbenp.prettier-vscode` 확장 설치 후 `Format On Save`·`Default Formatter`로 설정
- **Git 훅**: Husky + lint-staged 구성으로 커밋 전 자동 포맷 적용
- **CI 파이프라인**: GitHub Actions 등에서 `prettier --check`로 포맷 검사
[^6][^7]


## 7. 팀 협업을 위한 베스트 프랙티스

- **프로젝트별 설정 공유**: `.prettierrc`와 `.prettierignore`를 버전 관리
- **Githooks 활용**: 커밋 시 일관성 보장
- **ESLint 통합**: `eslint-config-prettier`·`eslint-plugin-prettier`로 충돌 없는 린트·포맷팅 워크플로 구성
- **플러그인 및 프리셋**: 조직 스타일 가이드에 맞춘 플러그인 개발 및 배포
- **정기 리포맷**: 대규모 코드베이스 초기 도입 시 전체 리포맷으로 일관성 확보

Prettier는 **스타일 논쟁 소모를 제거**하고 **코드 작성에 집중**할 수 있는 환경을 제공한다. 위 가이드를 따라 설정하고 팀에 도입하면, **코드 품질**과 **개발 효율**을 동시에 향상시킬 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://prettier.io/docs/why-prettier

[^2]: https://prettier.io/docs/

[^3]: https://github.com/prettier/prettier

[^4]: https://modolee-blog.tistory.com/5

[^5]: https://csimmons.dev/blog/2025/02/prettier-with-a-sample-prettierrc-and-explanation-of-options/

[^6]: https://joshua1988.github.io/vue-camp/format/prettier.html

[^7]: https://www.digitalocean.com/community/tutorials/how-to-format-code-with-prettier-in-visual-studio-code

[^8]: https://blog.pixelfreestudio.com/how-to-use-prettier-for-consistent-code-formatting/

[^9]: https://www.geeksforgeeks.org/installation-guide/formatting-code-with-prettier/

[^10]: https://prettier.io/docs/cli

[^11]: https://prettier.io/docs/install

[^12]: https://www.youtube.com/watch?v=__eiQumLOEo

[^13]: https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode

[^14]: https://www.youtube.com/watch?v=5pcnFzIISXU

[^15]: https://www.inflearn.com/blogs/2156

[^16]: https://www.freecodecamp.org/news/how-to-use-prettier-in-visual-studio-code/

[^17]: https://prettier.io

[^18]: https://www.reddit.com/r/vscode/comments/d5drm2/curious_why_should_one_use_prettier_for_code/

[^19]: https://contentstoaster.tistory.com/entry/vscode-formatter-prettier

