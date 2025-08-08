---
publish: true
---

문법과 실제 nextjs 프로젝트에서의 사용 사례를 중심으로 핸드북 형식으로 요약 정리. 기본부터 고급까지

# GitHub Actions 문법과 Next.js 실제 사용 사례 핸드북

## GitHub Actions 핵심 문법 가이드

### 기본 YAML 구조

GitHub Actions는 YAML 문법을 사용하며, 워크플로우 파일은 반드시 `.github/workflows` 디렉터리에 `.yml` 또는 `.yaml` 확장자로 저장되어야 합니다[^1].

**기본 구조:**

```yaml
name: 워크플로우 이름
on: [트리거 이벤트]
jobs:
  작업명:
    runs-on: 실행환경
    steps:
      - name: 단계 이름
        uses: actions/checkout@v4
      - name: 커스텀 명령어
        run: echo "Hello World"
```


### 워크플로우 메타데이터

**name (선택사항)**
워크플로우의 이름을 정의합니다. 생략하면 파일 경로가 표시됩니다[^1].

**run-name (선택사항)**
특정 워크플로우 실행의 이름을 동적으로 설정할 수 있습니다[^1].

```yaml
name: 배
run-name: ${{ inputs.environment }}에 배포 by @${{ github.actor }}
```


### 이벤트 트리거 (on)

#### 단일 이벤트

```yaml
on: push
```


#### 복수 이벤트

```yaml
on: [push, pull_request]
```


#### 고급 이벤트 설정

```yaml
on:
  push:
    branches: [main, develop]
    paths: ['src/**', '!src/test/**']
  pull_request:
    types: [opened, synchronize, reopened]
    branches: [main]
  schedule:
    - cron: '0 2 * * 1-5'  # 평일 오전 2시
  workflow_dispatch:
    inputs:
      environment:
        description: '배포 환경'
        required: true
        type: choice
        options: [dev, staging, prod]
```


### 작업 (Jobs) 문법

#### 기본 작업 구조

```yaml
jobs:
  build:
    name: 빌드 작업
    runs-on: ubuntu-latest
    timeout-minutes: 30
    environment: production
    
    steps:
      - uses: actions/checkout@v4
      - run: npm install
```


#### 작업 의존성 (needs)

```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - run: npm test
      
  deploy:
    needs: [test]  # test가 성공해야 실행
    runs-on: ubuntu-latest
    steps:
      - run: npm run deploy
```


#### 조건부 실행 (if)

```yaml
jobs:
  deploy:
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: echo "메인 브랜치에서만 실행"
```

**복잡한 조건식 처리:**

```yaml
jobs:
  conditional-job:
    if: >-
      github.event_name == 'pull_request' ||
      (
        github.event_name == 'push' &&
        contains(github.event.head_commit.message, '[deploy]')
      )
```


### 매트릭스 전략 (Matrix Strategy)

매트릭스 전략은 여러 환경에서 동일한 작업을 병렬로 실행할 수 있게 해주는 강력한 기능입니다[^2][^3].

#### 기본 매트릭스

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [16, 18, 20]
        # 총 9개 작업 (3 × 3) 실행
    
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
```


#### 매트릭스 확장 및 제외

```yaml
strategy:
  matrix:
    os: [ubuntu-latest, windows-latest]
    node-version: [16, 18]
    include:
      # 특정 조합 추가
      - os: macos-latest
        node-version: 18
        experimental: true
    exclude:
      # 특정 조합 제외
      - os: windows-latest
        node-version: 16
  
  fail-fast: false  # 하나 실패해도 나머지 계속 실행
  max-parallel: 2   # 최대 2개까지 병렬 실행
```


#### 동적 매트릭스

```yaml
jobs:
  generate-matrix:
    runs-on: ubuntu-latest
    outputs:
      matrix: ${{ steps.set-matrix.outputs.matrix }}
    steps:
      - id: set-matrix
        run: |
          echo "matrix=[\"app1\", \"app2\", \"app3\"]" >> $GITHUB_OUTPUT
  
  deploy:
    needs: generate-matrix
    strategy:
      matrix:
        app: ${{ fromJson(needs.generate-matrix.outputs.matrix) }}
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying ${{ matrix.app }}"
```


### 환경 변수와 Secrets

#### 환경 변수 설정

```yaml
env:
  GLOBAL_VAR: "전체 워크플로우 변수"

jobs:
  build:
    env:
      JOB_VAR: "작업 레벨 변수"
    
    steps:
      - name: 환경 변수 사용
        run: echo $GLOBAL_VAR $JOB_VAR
        env:
          STEP_VAR: "단계 레벨 변수"
```


#### Secrets 사용

```yaml
jobs:
  deploy:
    steps:
      - name: API 호출
        run: curl -H "Authorization: Bearer ${{ secrets.API_TOKEN }}"
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
```

**Secrets 보안 모범 사례:**

- 민감한 정보는 절대 하드코딩하지 않기[^4]
- `::add-mask::`를 사용하여 로그에서 숨기기
- 환경별 secrets 관리[^5]
- 정기적인 rotation 수행


### 조건부 표현식과 함수

#### 상태 확인 함수

```yaml
steps:
  - name: 성공 시에만 실행
    if: success()
    run: echo "모든 이전 단계 성공"
    
  - name: 실패 시에도 실행
    if: always()
    run: echo "항상 실행"
    
  - name: 실패 시에만 실행
    if: failure()
    run: echo "이전 단계 실패"
```


#### 컨텍스트 활용

```yaml
jobs:
  info:
    runs-on: ubuntu-latest
    steps:
      - name: 컨텍스트 정보 출력
        run: |
          echo "이벤트: ${{ github.event_name }}"
          echo "브랜치: ${{ github.ref_name }}"
          echo "배우: ${{ github.actor }}"
          echo "러너 OS: ${{ runner.os }}"
```


### 액션 (Actions) 활용

#### 공식 액션 사용

```yaml
steps:
  - name: 체크아웃
    uses: actions/checkout@v4
    with:
      fetch-depth: 0
      
  - name: Node.js 설정
    uses: actions/setup-node@v4
    with:
      node-version: '18'
      cache: 'npm'
      registry-url: 'https://registry.npmjs.org'
```


#### 버전 관리 모범 사례

```yaml
steps:
  # 권장: 특정 커밋 SHA 사용 (최고 보안)
  - uses: actions/checkout@8f4b7f84864484a7bf31766abe9204da3cbe65b3
  
  # 권장: 메이저 버전 태그 (편의성과 보안 균형)
  - uses: actions/checkout@v4
  
  # 주의: 특정 버전 태그
  - uses: actions/checkout@v4.2.0
  
  # 비권장: 브랜치 참조 (보안 위험)
  - uses: actions/checkout@main
```


## 고급 워크플로우 패턴

### 재사용 가능한 워크플로우 (Reusable Workflows)

재사용 가능한 워크플로우는 여러 저장소에서 공통 로직을 공유할 수 있게 해줍니다[^6][^7].

#### 재사용 가능한 워크플로우 정의

```yaml
# .github/workflows/reusable-build.yml
name: 재사용 가능한 빌드
on:
  workflow_call:
    inputs:
      node-version:
        description: 'Node.js 버전'
        required: false
        type: string
        default: '18'
      environment:
        description: '배포 환경'
        required: true
        type: string
    secrets:
      NPM_TOKEN:
        description: 'NPM 토큰'
        required: true
    outputs:
      build-id:
        description: '빌드 ID'
        value: ${{ jobs.build.outputs.build-id }}

jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      build-id: ${{ steps.build.outputs.id }}
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}
      
      - name: 빌드
        id: build
        run: |
          npm ci
          npm run build
          echo "id=$(date +%s)" >> $GITHUB_OUTPUT
        env:
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```


#### 재사용 가능한 워크플로우 호출

```yaml
# .github/workflows/main.yml
name: 메인 워크플로우
on: [push]

jobs:
  call-reusable:
    uses: ./.github/workflows/reusable-build.yml
    with:
      node-version: '20'
      environment: 'production'
    secrets:
      NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
  
  deploy:
    needs: call-reusable
    runs-on: ubuntu-latest
    steps:
      - run: echo "빌드 ID: ${{ needs.call-reusable.outputs.build-id }}"
```


### 컴포지트 액션 (Composite Actions)

컴포지트 액션은 여러 단계를 하나의 액션으로 묶어 재사용성을 높입니다[^8][^9].

#### 컴포지트 액션 생성

```yaml
# .github/actions/setup-node/action.yml
name: 'Node.js 환경 설정'
description: 'Node.js 설치 및 의존성 캐싱'

inputs:
  node-version:
    description: 'Node.js 버전'
    required: false
    default: '18'
  cache-dependency-path:
    description: '의존성 파일 경로'
    required: false
    default: 'package-lock.json'

outputs:
  cache-hit:
    description: '캐시 히트 여부'  
    value: ${{ steps.cache.outputs.cache-hit }}

runs:
  using: 'composite'
  steps:
    - name: Node.js 설정
      uses: actions/setup-node@v4
      with:
        node-version: ${{ inputs.node-version }}
        
    - name: 의존성 캐시
      id: cache
      uses: actions/cache@v3
      with:
        path: ~/.npm
        key: ${{ runner.os }}-node-${{ hashFiles(inputs.cache-dependency-path) }}
        
    - name: 의존성 설치
      if: steps.cache.outputs.cache-hit != 'true'
      run: npm ci
      shell: bash
```


#### 컴포지트 액션 사용

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: ./.github/actions/setup-node
        with:
          node-version: '20'
      - run: npm run build
```


## Next.js 프로젝트 실제 사용 사례

### 기본 CI 파이프라인

```yaml
name: Next.js CI
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '20'

jobs:
  code-quality:
    name: 코드 품질 검사
    runs-on: ubuntu-latest
    
    steps:
      - name: 체크아웃
        uses: actions/checkout@v4
      
      - name: Node.js 설정    
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: 의존성 설치
        run: npm ci
      
      - name: TypeScript 타입 검사
        run: npx tsc --noEmit
      
      - name: ESLint 검사
        run: npm run lint
      
      - name: Prettier 포맷 검사
        run: npm run format:check
  
  test:
    name: 테스트 실행
    runs-on: ubuntu-latest
    needs: code-quality
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - run: npm ci
      
      - name: 단위 테스트
        run: npm test -- --coverage --watchAll=false
      
      - name: 테스트 커버리지 업로드
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/lcov.info
  
  build:
    name: 빌드 검증
    runs-on: ubuntu-latest
    needs: [code-quality, test]
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - run: npm ci
      
      - name: Next.js 빌드
        run: npm run build
        env:
          NEXT_TELEMETRY_DISABLED: 1
      
      - name: 빌드 아티팩트 저장
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: .next/
          retention-days: 1
```


### ESLint 및 Prettier 통합

**package.json 스크립트 설정:**

```json
{
  "scripts": {
    "lint": "eslint . --ext .js,.jsx,.ts,.tsx --max-warnings 0",
    "lint:fix": "eslint . --ext .js,.jsx,.ts,.tsx --fix",
    "format": "prettier --write .",
    "format:check": "prettier --check .",
    "type-check": "tsc --noEmit"
  }
}
```

**GitHub Actions 워크플로우:**

```yaml
name: 코드 품질
on: [push, pull_request]

jobs:
  quality-checks:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - run: npm ci
      
      - name: ESLint 실행
        run: |
          npm run lint 2>&1 | tee eslint-report.txt
          exit ${PIPESTATUS[^0]}
      
      - name: Prettier 검사
        run: npm run format:check
      
      - name: TypeScript 검사  
        run: npm run type-check
      
      - name: ESLint 결과 코멘트
        if: failure()
        uses: actions/github-script@v6
        with:
          script: |
            const fs = require('fs');
            const eslintOutput = fs.readFileSync('eslint-report.txt', 'utf8');
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: `## ESLint 검사 실패\n\`\`\`\n${eslintOutput}\n\`\`\``
            });
```


### 매트릭스를 활용한 다중 환경 테스트

```yaml
name: 크로스 플랫폼 테스트
on: [push, pull_request]

jobs:
  test-matrix:
    name: 테스트 (${{ matrix.os }}, Node ${{ matrix.node }})
    runs-on: ${{ matrix.os }}
    
    strategy:
      fail-fast: false
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node: [18, 20]
        include:
          - os: ubuntu-latest
            node: 21
            experimental: true
        exclude:
          - os: windows-latest
            node: 18  # Windows에서 Node 18 제외
    
    continue-on-error: ${{ matrix.experimental == true }}
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Node.js ${{ matrix.node }} 설정
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
          cache: 'npm'
      
      - name: 의존성 설치
        run: npm ci
      
      - name: 테스트 실행
        run: npm test
      
      - name: 빌드 테스트
        run: npm run build
        env:
          NODE_ENV: production
```


### 스테이징/프로덕션 배포 파이프라인

```yaml
name: 배포 파이프라인
on:
  push:
    branches: [main]
  workflow_dispatch:
    inputs:
      environment:
        description: '배포 환경'
        required: true
        type: choice
        options: [staging, production]

jobs:
  deploy:
    name: ${{ inputs.environment || 'staging' }} 배포
    runs-on: ubuntu-latest
    environment: 
      name: ${{ inputs.environment || 'staging' }}
      url: ${{ steps.deploy.outputs.url }}
    
    steps:
      - uses: actions/checkout@v4
      
      - name: 환경 설정
        id: config
        run: |
          if [[ "${{ inputs.environment ]]; then
            echo "domain=app.example.com" >> $GITHUB_OUTPUT
            echo "next_public_api_url=https://api.example.com" >> $GITHUB_OUTPUT
          else
            echo "domain=staging.example.com" >> $GITHUB_OUTPUT  
            echo "next_public_api_url=https://staging-api.example.com" >> $GITHUB_OUTPUT
          fi
      
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - run: npm ci
      
      - name: 환경별 빌드
        run: npm run build
        env:
          NEXT_PUBLIC_API_URL: ${{ steps.config.outputs.next_public_api_url }}
          NODE_ENV: production
      
      - name: Vercel 배포
        id: deploy
        run: |
          npx vercel --token ${{ secrets.VERCEL_TOKEN }} \
            --scope ${{ secrets.VERCEL_ORG_ID }} \
            --prod=${{ inputs.environment == 'production' && 'true' || 'false' }} \
            --yes > deployment-url.txt
          
          url=$(cat deployment-url.txt)
          echo "url=$url" >> $GITHUB_OUTPUT
      
      - name: 배포 알림
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: |
            ${{ inputs.environment || 'staging' }} 환경 배포 완료!
            🚀 URL: ${{ steps.deploy.outputs.url }}
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```


### GitHub Pages 정적 배포

```yaml
name: GitHub Pages 배포
on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: 체크아웃
        uses: actions/checkout@v4
      
      - name: Node.js 설정
        uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - name: 의존성 설치
        run: npm ci
      
      - name: Next.js 설정 확인
        run: |
          cat > next.config.js << 'EOF'
          /** @type {import('next').NextConfig} */
          const nextConfig = {
            output: 'export',
            basePath: process.env.NODE_ENV === 'production' ? '/repository-name' : '',
            assetPrefix: process.env.NODE_ENV === 'production' ? '/repository-name/' : '',
            images: {
              unoptimized: true
            },
            trailingSlash: true
          }
          module.exports = nextConfig
          EOF
      
      - name: 정적 빌드
        run: npm run build
        env:
          NODE_ENV: production
      
      - name: Pages 아티팩트 업로드
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./out
  
  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    
    steps:
      - name: GitHub Pages 배포
        id: deployment
        uses: actions/deploy-pages@v2
```


### 성능 모니터링 및 최적화

```yaml
name: 성능 모니터링
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  performance:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
          cache: 'npm'
      
      - run: npm ci
      - run: npm run build
      
      - name: Bundle Analyzer
        run: |
          npm install -g @next/bundle-analyzer
          ANALYZE=true npm run build
      
      - name: Lighthouse CI
        uses: treosh/lighthouse-ci-action@v10
        with:
          configPath: './.lighthouserc.json'
          uploadArtifacts: true
          temporaryPublicStorage: true
        env:
          LHCI_GITHUB_APP_TOKEN: ${{ secrets.LHCI_GITHUB_APP_TOKEN }}
      
      - name: 성능 리포트 코멘트
        uses: actions/github-script@v6
        if: github.event_name == 'pull_request'
        with:
          script: |
            const fs = require('fs');
            const lighthouse = JSON.parse(fs.readFileSync('./lhci_reports/manifest.json'));
            const summary = lighthouse[^0].summary;
            
            const comment = `## 🚀 성능 리포트
            
            | 메트릭 | 점수 |
            |--------|------|
            | Performance | ${summary.performance} |
            | Accessibility | ${summary.accessibility} |
            | Best Practices | ${summary['best-practices']} |
            | SEO | ${summary.seo} |
            `;
            
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: comment
            });
```


### 보안 검사 및 의존성 관리

```yaml
name: 보안 검사
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]
  schedule:
    - cron: '0 2 * * 1'  # 매주 월요일 오전 2시

jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: npm audit
        run: |
          npm audit --audit-level=high
          npm audit --output=json > audit-results.json
        continue-on-error: true
      
      - name: Snyk 보안 테스트
        uses: snyk/actions/node@master
        env:
          SNYK_TOKEN: ${{ secrets.SNYK_TOKEN }}
        with:
          args: --severity-threshold=high
      
      - name: CodeQL 분석
        uses: github/codeql-action/init@v2
        with:
          languages: javascript
      
      - name: CodeQL 분석 수행
        uses: github/codeql-action/analyze@v2
      
      - name: 의존성 검토
        uses: actions/dependency-review-action@v3
        if: github.event_name == 'pull_request'
```

이 핸드북을 통해 GitHub Actions의 핵심 문법부터 Next.js 프로젝트의 실제 CI/CD 구축까지 체계적으로 학습할 수 있습니다. 특히 매트릭스 전략, 재사용 가능한 워크플로우, 컴포지트 액션 등의 고급 기능을 활용하면 더욱 효율적이고 유지보수 가능한 파이프라인을 구축할 수 있습니다.

<div style="text-align: center">⁂</div>

[^1]: https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions

[^2]: https://www.geeksforgeeks.org/git/the-matrix-strategy-in-github-actions/

[^3]: https://graphite.dev/guides/github-actions-matrix

[^4]: https://docs.github.com/actions/security-guides/using-secrets-in-github-actions

[^5]: https://docs.github.com/en/actions/concepts/security/secrets

[^6]: https://resources.github.com/learn/pathways/automation/intermediate/create-reusable-workflows-in-github-actions/

[^7]: https://thearchivelog.dev/article/how-to-reduce-duplication-in-workflows/

[^8]: https://earthly.dev/blog/composite-actions-github/

[^9]: https://deku.posstree.com/ko/github_actions/composite-action/

[^10]: https://docs.github.com/en/actions/reference/metadata-syntax-reference

[^11]: https://stackoverflow.com/questions/71786326/github-action-how-to-deal-with-standalone-config-file

[^12]: https://docs.github.com/en/actions/reference/metadata-syntax-for-github-actions

[^13]: https://docs.github.com/en/actions/reference/contexts-reference

[^14]: https://www.linkedin.com/learning/25-github-configuration-files-you-should-be-using/where-to-store-your-action-definitions

[^15]: https://docs.github.com/ko/enterprise-cloud@latest/actions/sharing-automations/creating-actions/metadata-syntax-for-github-actions

[^16]: https://somaz.tistory.com/233

[^17]: https://dev.to/jajera/understanding-github-actions-working-directory-550o

[^18]: https://stackoverflow.com/questions/75572493/github-actions-script-error-in-yaml-syntax

[^19]: https://docs.github.com/en/actions/reference

[^20]: https://docs.github.com/actions/using-workflows/about-workflows

[^21]: https://github.com/yaml/yaml-grammar

[^22]: https://docs.github.com/en/actions/reference/workflow-commands-for-github-actions

[^23]: https://docs.github.com/actions/creating-actions/about-custom-actions

[^24]: https://docs.github.com/en/actions/reference/evaluate-expressions-in-workflows-and-actions

[^25]: https://docs.github.com/actions

[^26]: https://docs.github.com/actions/quickstart

[^27]: https://github.com/yaml/yaml-grammar/actions

[^28]: https://jinmay.github.io/2020/05/13/git/github-action-syntax/

[^29]: https://jonghoonpark.com/2024/10/29/next-js-github-hosting-pnpm

[^30]: https://dallotech.com/blogs/65d337cd8ea596dd2ca73051

[^31]: https://github.com/gregrickaby/nextjs-github-pages

[^32]: https://magdongsani.tistory.com/6

[^33]: https://www.youtube.com/watch?v=V-VXTashnE4

[^34]: https://github.com/nextjs/deploy-github-pages

[^35]: https://staticmania.com/blog/how-to-deploy-next-js-application-with-github-action

[^36]: https://github.com/bhall2001/serverless-nextjs-github-ci-cd

[^37]: https://www.youtube.com/watch?v=WoL3xbkAfOU

[^38]: https://gist.github.com/officialrajdeepsingh/3714f17122ada14e399b8e43c19384b1

[^39]: https://www.youtube.com/watch?v=JXGljLzvL2w

[^40]: https://hackernoon.com/lang/ko/nextjs-앱을-github-페이지에-게시해야-합니다

[^41]: https://javascript.plainenglish.io/nextjs-github-actions-deployment-b14dea6cfed5

[^42]: https://dev.to/kaizerpwn/cicd-pipeline-for-a-nextjs-application-using-github-actions-argocd-and-microk8s-7a4

[^43]: https://velog.io/@singingsandhill/GitHub-Pages로-Next.js-프로젝트-배포하기

[^44]: https://dev.to/yousufbasir/automating-nextjs-application-deployment-with-github-actions-38ac

[^45]: https://velog.io/@youngeui_hong/Docker-GitHub-Actions-Google-Cloud%EB%A1%9C-Next.js-CICD-%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0

[^46]: https://bepyan.github.io/blog/nextjs-blog/4-deploy

[^47]: https://seonlog.tistory.com/62

[^48]: https://rexavllp.com/nextjs-ci-cd-github-vercel/

[^49]: https://thomasthornton.cloud/2023/08/11/if-elseif-or-else-in-github-actions/

[^50]: https://docs.github.com/en/enterprise-cloud@latest/actions/writing-workflows/choosing-when-your-workflow-runs/using-conditions-to-control-job-execution

[^51]: https://codefresh.io/learn/github-actions/github-actions-matrix/

[^52]: https://gist.github.com/brianjbayer/53ef17e0a15f7d80468d3f3077992ef8

[^53]: https://docs.github.com/en/enterprise-server@3.13/actions/writing-workflows/choosing-what-your-workflow-does/evaluate-expressions-in-workflows-and-actions

[^54]: https://runs-on.com/github-actions/the-matrix-strategy/

[^55]: https://www.blacksmith.sh/blog/best-practices-for-managing-secrets-in-github-actions

[^56]: https://stackoverflow.com/questions/67531606/how-to-deal-with-long-conditional-expression-in-github-actions-workflow/67532120

[^57]: https://dev.to/tejastn10/mastering-github-actions-strategy-matrix-deploy-smarter-not-harder-28po

[^58]: https://www.getorchestra.io/guides/github-actions-api-create-or-update-an-environment-secret

[^59]: https://docs.github.com/ko/actions/writing-workflows/choosing-when-your-workflow-runs/using-conditions-to-control-job-execution

[^60]: https://somaz.tistory.com/293

[^61]: https://www.youtube.com/watch?v=dPLPSaFqJmY

[^62]: https://docs.github.com/en/actions/how-tos/writing-workflows/choosing-when-your-workflow-runs/using-conditions-to-control-job-execution

[^63]: https://github.com/samueljmello/github-actions-exercises/blob/main/08-Matrix-Strategy.md

[^64]: https://docs.github.com/en/actions/sharing-automations/creating-actions/about-custom-actions

[^65]: https://www.youtube.com/watch?v=gDBZGCGSs6E

[^66]: https://www.youtube.com/watch?v=w8ZALhqoHgs

[^67]: https://resources.github.com/learn/pathways/automation/advanced/building-your-first-custom-github-action/

[^68]: https://docs.github.com/en/actions/how-tos/create-and-publish-actions

[^69]: https://docs.github.com/en/actions/how-tos/sharing-automations/reusing-workflows

[^70]: https://docs.github.com/en/actions/tutorials/creating-a-composite-action

[^71]: https://s-core.co.kr/insight/view/github-actions-소개-및-custom-action-만들어보기/

[^72]: https://docs.github.com/en/actions/how-tos/sharing-automations/reuse-workflows

[^73]: https://blog.outsider.ne.kr/1592

[^74]: https://junhyunny.github.io/github/custom-action-in-github-actions/

[^75]: https://velog.io/@inhyejeong59/github-actions에서-reusable-workflow사용하기

[^76]: https://docs.github.com/ko/actions/sharing-automations/creating-actions/creating-a-composite-action

[^77]: https://docs.github.com/ko/actions/concepts/workflows-and-actions/about-custom-actions

[^78]: https://www.daleseo.com/github-actions-reusable-workflows/

[^79]: https://deku.posstree.com/en/react/github-actions/prettier-eslint/

[^80]: https://moldstud.com/articles/p-implementing-continuous-integration-and-deployment-for-nextjs-projects

[^81]: https://dev.to/swhabitation/how-to-set-up-eslint-prettier-and-husky-in-nextjs--22pk

[^82]: https://30dayscoding.com/blog/building-and-deploying-nextjs-applications-with-ci-cd

[^83]: https://dmoogi.tistory.com/entry/Nextjs-ESLint와-Prettier-설정하기

[^84]: https://nextjsstarter.com/blog/nextjs-cicd-deployment-guide-2024/

[^85]: https://www.groovyweb.co/blog/how-to-set-up-a-ci-cd-pipeline-for-your-next-js-project/

[^86]: https://velog.io/@sjoleee_/CICD-Github-Actions%EB%A1%9C-CI-%EC%84%B8%ED%8C%85%ED%95%98%EA%B8%B0Prettier-ESLint-TSC

[^87]: https://www.reddit.com/r/nextjs/comments/1cebjjo/proper_cicd_that_mimics_production/

[^88]: https://github.com/nogaz89/Nextjs-template

[^89]: https://www.freecodecamp.org/news/deploy-to-vercel-with-github-actions/

[^90]: https://nextjs.org/docs/pages/guides/ci-build-caching

[^91]: https://velog.io/@seoyeonpp/NX-monorepo에-Eslint-Prettier-Docker-Github-Actions-설정하기

