---
publish:  true
---

'깃허브 액션'에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘. 초보~중급. nextjs

# GitHub Actions 완전 핸드북: 초보자부터 중급자까지

## GitHub Actions가 만들어진 이유

### 개발 워크플로우 자동화의 필요성

GitHub Actions는 **개발자의 반복적인 작업을 자동화**하기 위해 2018년 10월에 출시되었습니다[^1]. Microsoft에 인수된 이후 GitHub이 야심차게 선보인 이 서비스는 기존의 Jenkins, Circle CI, Travis CI 등의 CI/CD 시장에 지각변동을 일으키고 있습니다[^2].

GitHub의 플랫폼 책임자 Sam Lambert는 이를 "GitHub 역사상 가장 큰 변화"라고 표현했으며, iOS의 shortcuts와 유사하지만 훨씬 더 유연한 도구로 설명했습니다[^1]. 개발자들이 단순히 코드를 저장하고 공유하는 것을 넘어서, **코드를 실행하고 전체 개발 워크플로우를 자동화**할 수 있게 되었습니다.

### 기존 문존에는 다음과 같은 문제들이 있었습니다:

- **수동 배포 과정**으로 인한 인적 오류 발생
- **반복적인 테스트 작업**으로 인한 시간 낭비
- **CI/CD 도구들의 복잡한 설정**과 높은 진입장벽
- **DevOps 엔지니어의 도움** 없이는 CI/CD를 구축하기 어려움[^2]

GitHub Actions는 이러한 문제들을 해결하여 **일반 개발자들도 스스로 CI/CD 설정**을 할 수 있게 만들었습니다.

## GitHub Actions의 핵심 구조

### 계층적 구조 이해

GitHub Actions는 계층적 구조로 이루어져 있으며, 다음과 같은 4가지 핵심 구성요소가 있습니다[^3][^4]:

**Workflow (워크플로우)** → **Job (작업)** → **Step (단계)** → **Action (액션)**

### 1. Workflow (워크플로우)

워크플로우는 **자동화된 프로세스의 정의**로, 가장 상위 개념입니다[^5][^2].

**주요 특징:**

- `.github/workflows` 디렉터리에 YAML 파일로 저장[^5][^2]
- 특정 이벤트에 의해 트리거됨
- 하나의 저장소에 여러 워크플로우 생성 가능
- 각각 다른 작업 수행 (빌드, 테스트, 배포 등)

**기본 구조:**

```yaml
name: 워크플로우 이름
on: [이벤트]
jobs:
  작업명:
    runs-on: 실행환경
    steps:
      - 단계들...
```


### 2. Job (작업)

작업은 **동일한 러너에서 실행되는 단계들의 집합**입니다[^3][^6].

**주요 특징:**

- 기본적으로 **병렬 실행**됨
- `needs` 키워드로 순차 실행 가능
- 각 작업은 독립된 가상머신에서 실행
- `runs-on`으로 실행 환경 지정


### 3. Step (단계)

단계는 **작업 내에서 순차적으로 실행되는 개별 명령**입니다[^3][^7].

**두 가지 유형:**

- **`run`**: 셸 명령어 실행
- **`uses`**: 액션 사용

**단계 간 데이터 전달:**

```yaml
- id: step1
  run: echo "output=hello" >> $GITHUB_OUTPUT
- run: echo ${{ steps.step1.outputs.output }}
```


### 4. Action (액션)

액션은 **재사용 가능한 코드 단위**로, 복잡한 작업을 캡슐화합니다[^5][^8].

**세 가지 유형:**

- **Docker Container Actions**: 일관된 환경 제공
- **JavaScript Actions**: 빠른 실행, 모든 플랫폼 지원
- **Composite Actions**: 여러 단계를 하나로 묶음


## 이벤트와 트리거

### 주요 이벤트 유형

GitHub Actions는 다양한 이벤트에 반응할 수 있습니다[^9][^10]:

**1. Push 이벤트**

```yaml
on:
  push:
    branches: [main, develop]
    paths: ['src/**']
```

**2. Pull Request 이벤트**

```yaml
on:
  pull_request:
    types: [opened, synchronize]
    branches: [main]
```

**3. 스케줄 이벤트**

```yaml
on:
  schedule:
    - cron: '0 2 * * *'  # 매일 오전 2시
```

**4. 수동 트리거**

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        description: '배포 환경'
        required: true
        default: 'staging'
```


## 러너 환경

### GitHub-hosted Runners

GitHub에서 제공하는 가상머신으로, **유지보수가 자동으로 처리**됩니다[^11][^12].

**지원 운영체제:**

- Ubuntu Linux (ubuntu-latest, ubuntu-20.04, ubuntu-18.04)
- Windows (windows-latest, windows-2019)
- macOS (macos-latest, macos-11, macos-10.15)

**특징:**

- 각 작업마다 새로운 가상머신 제공
- 사전 설치된 도구와 패키지 포함
- 월 2,000분 무료 (Public 저장소는 무제한)


### Self-hosted Runners

직접 관리하는 서버에서 실행되는 러너입니다[^13][^14].

**장점:**

- 하드웨어와 소프트웨어 완전 제어
- 사용량에 따른 비용 없음
- 기존 인프라 활용 가능
- 보안 요구사항 충족

**설치 방법:**

1. GitHub 저장소 Settings → Actions → Runners
2. "New self-hosted runner" 클릭
3. 제공된 스크립트 실행

## GitHub Actions Marketplace

### Marketplace 활용

GitHub Marketplace에는 **20,000개 이상의 액션**이 등록되어 있습니다[^15][^16].

**카테고리:**

- **AI Assisted**: AI 기반 도구들
- **Code Quality**: 코드 품질 관리
- **Deployment**: 배포 자동화
- **Security**: 보안 검사
- **Testing**: 테스트 자동화

**액션 사용법:**

```yaml
- name: 체크아웃
  uses: actions/checkout@v4
  
- name: Node.js 설정
  uses: actions/setup-node@v4
  with:
    node-version: '18'
```

**주의사항:**

- **신뢰할 수 있는 액션** 선택
- **정확한 버전 태그** 사용
- **소스코드 검토** 권장


## Next.js 프로젝트 실전 활용

### 기본 CI/CD 파이프라인

```yaml
# .github/workflows/ci.yml
name: Next.js CI/CD Pipeline
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
    - name: 체크아웃
      uses: actions/checkout@v4
      
    - name: Node.js 설정  
      uses: actions/setup-node@v4
      with:
        node-version: '18'
        cache: 'npm'
        
    - name: 의존성 설치
      run: npm ci
      
    - name: 린트 검사
      run: npm run lint
      
    - name: 테스트 실행
      run: npm test
      
    - name: 빌드
      run: npm run build
```


### 프로덕션 배포 파이프라인

```yaml
# .github/workflows/deploy.yml
name: Next.js 배포
on:
  push:
    branches: [main]

env:
  NODE_VERSION: '18'

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: 소스코드 체크아웃
      uses: actions/checkout@v4
      
    - name: Node.js 환경 설정
      uses: actions/setup-node@v4
      with:
        node-version: ${{ env.NODE_VERSION }}
        cache: 'npm'
        
    - name: 의존성 설치
      run: npm ci
      
    - name: 환경변수 설정
      run: |
        echo "NEXT_PUBLIC_API_URL=${{ secrets.API_URL }}" >> .env.production
        echo "DATABASE_URL=${{ secrets.DATABASE_URL }}" >> .env.production
        
    - name: 프로덕션 빌드
      run: npm run build
      
    - name: 서버 배포
      uses: appleboy/ssh-action@v0.1.5
      with:
        host: ${{ secrets.HOST }}
        username: ${{ secrets.USERNAME }}
        key: ${{ secrets.SSH_PRIVATE_KEY }}
        script: |
          cd /var/www/myapp
          git pull origin main
          npm ci --production
          npm run build
          pm2 restart next-app
```


### GitHub Pages 배포

```yaml
# .github/workflows/pages.yml
name: GitHub Pages 배포
on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - name: 체크아웃
      uses: actions/checkout@v4
      
    - name: Node.js 설정
      uses: actions/setup-node@v4
      with:
        node-version: '18'
        cache: 'npm'
        
    - name: 의존성 설치
      run: npm ci
      
    - name: Next.js 빌드 (정적 내보내기)
      run: npm run build
      
    - name: GitHub Pages 설정
      uses: actions/configure-pages@v3
      
    - name: 아티팩트 업로드
      uses: actions/upload-pages-artifact@v2
      with:
        path: './out'
        
    - name: GitHub Pages 배포
      uses: actions/deploy-pages@v2
```


## 보안 모범 사례

### Secrets 관리

**GitHub Secrets 사용:**[^17][^18]

```yaml
steps:
  - name: 환경변수 설정
    env:
      API_KEY: ${{ secrets.API_KEY }}
      DATABASE_URL: ${{ secrets.DATABASE_URL }}
    run: echo "환경변수가 안전하게 설정됨"
```

**보안 원칙:**[^19][^20]

- **하드코딩 금지**: 민감한 정보를 코드에 직접 입력하지 않음
- **최소 권한 원칙**: 필요한 최소한의 권한만 부여
- **정기적 로테이션**: 시크릿을 주기적으로 변경
- **마스킹 처리**: `::add-mask::` 명령어로 로그에서 숨김


### 써드파티 액션 보안

**안전한 액션 사용:**[^21][^20]

- **정확한 커밋 SHA 사용**: `uses: actions/checkout@a81bbbf8298c0fa03ea29cdc473d45769f953675`
- **소스코드 검토**: 액션의 코드를 직접 확인
- **검증된 크리에이터**: GitHub Marketplace의 "Verified creator" 배지 확인
- **정기적 업데이트**: Dependabot을 활용한 자동 업데이트


## 성능 최적화 팁

### 캐싱 활용

**의존성 캐싱:**

```yaml
- name: Node.js 설정
  uses: actions/setup-node@v4
  with:
    node-version: '18'
    cache: 'npm'  # 자동 캐싱
```

**커스텀 캐싱:**

```yaml
- name: 캐시 복원
  uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```


### 병렬 처리

**매트릭스 전략:**

```yaml
strategy:
  matrix:
    node-version: [16, 18, 20]
    os: [ubuntu-latest, windows-latest]
    
runs-on: ${{ matrix.os }}
steps:
  - uses: actions/setup-node@v4
    with:
      node-version: ${{ matrix.node-version }}
```


## 모니터링과 디버깅

### 워크플로우 실행 확인

1. **Actions 탭**에서 실행 상태 확인
2. **로그 분석**을 통한 오류 파악
3. **아티팩트 다운로드**로 빌드 결과물 확인
4. **재실행 기능**으로 일시적 오류 해결

### 디버깅 기법

**디버그 로깅 활성화:**

```yaml
- name: 디버그 정보 출력
  run: |
    echo "현재 디렉토리: $(pwd)"
    echo "파일 목록: $(ls -la)"
    echo "환경변수: $NODE_ENV"
  env:
    ACTIONS_STEP_DEBUG: true
```


## 시작하기

### 첫 번째 워크플로우 만들기

1. **저장소에 디렉토리 생성**: `.github/workflows/`
2. **YAML 파일 생성**: `first-workflow.yml`
3. **기본 워크플로우 작성**:
```yaml
name: 첫 번째 워크플로우
on: [push]
jobs:
  hello:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Hello, GitHub Actions!"
```

4. **커밋 후 푸시**: 자동으로 워크플로우 실행
5. **Actions 탭에서 결과 확인**

GitHub Actions는 현대적인 개발 워크플로우에서 필수적인 도구가 되었습니다. 이 핸드북을 통해 기본기를 탄탄히 다지고, 점진적으로 고급 기능들을 활용해보시기 바랍니다. 지속적인 학습과 실습을 통해 더욱 효율적인 개발 환경을 구축할 수 있을 것입니다.

<div style="text-align: center">⁂</div>

[^1]: https://techcrunch.com/2018/10/16/github-launches-actions-its-workflow-automation-tool/

[^2]: https://www.daleseo.com/github-actions-basics/

[^3]: https://kotlinworld.com/384

[^4]: https://thinkingtool.tistory.com/entry/GitHub-Actions의-구조와-활용법

[^5]: https://docs.github.com/articles/getting-started-with-github-actions

[^6]: https://notes.kodekloud.com/docs/GitHub-Actions/GitHub-Actions-Core-Concepts/GitHub-Action-Core-Components

[^7]: https://www.daleseo.com/github-actions-steps/

[^8]: https://docs.github.com/actions/creating-actions/about-custom-actions

[^9]: https://codefresh.io/learn/github-actions/github-actions-triggers-5-ways-to-trigger-a-workflow-with-code/

[^10]: https://www.daleseo.com/github-actions-triggers/

[^11]: https://docs.github.com/en/actions/concepts/runners/about-github-hosted-runners

[^12]: https://docs.github.com/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners

[^13]: https://docs.github.com/en/actions/concepts/runners/about-self-hosted-runners

[^14]: https://devocean.sk.com/blog/techBoardDetail.do?ID=164362

[^15]: https://www.youtube.com/watch?v=JYOGmLzMbpM

[^16]: https://github.com/marketplace

[^17]: https://www.neovasolutions.com/2025/02/06/github-actions-how-to-secure-secrets-and-credentials-in-ci-cd/

[^18]: https://docs.github.com/en/actions/concepts/security/secrets

[^19]: https://blog.gitguardian.com/github-actions-security-cheat-sheet/

[^20]: https://docs.github.com/en/actions/how-tos/security-for-github-actions/security-guides/security-hardening-for-github-actions

[^21]: https://docs.github.com/en/actions/reference/secure-use-reference

[^22]: https://dev.to/alexmercedcoder/a-deep-dive-into-github-actions-from-software-development-to-data-engineering-bki

[^23]: https://octopus.com/devops/github-actions/

[^24]: https://docs.github.com/en/actions/how-tos/sharing-automations/creating-actions

[^25]: https://www.geeksforgeeks.org/git/introduction-to-github-actions/

[^26]: https://docs.github.com/en/actions/how-tos/create-and-publish-actions

[^27]: https://github.com/features/actions

[^28]: http://bergel.eu/MyPapers/Vale22a-GitHubActionWorkflows.pdf

[^29]: https://docs.github.com/en/actions/reference/contexts-reference

[^30]: https://docs.github.com/actions/quickstart

[^31]: https://stackoverflow.com/questions/59319281/github-action-different-between-release-created-and-published

[^32]: https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions

[^33]: https://docs.github.com/ko/enterprise-cloud@latest/actions/get-started/understanding-github-actions

[^34]: https://varunsridharan.hashnode.dev/the-why-how-and-creation-of-github-actions

[^35]: https://stackoverflow.com/questions/57921401/push-to-origin-from-github-action

[^36]: https://docs.github.com/actions

[^37]: https://tech.kakao.com/posts/516

[^38]: https://docs.github.com/en/actions/reference/metadata-syntax-for-github-actions

[^39]: https://docs.github.com/ko/enterprise-cloud@latest/actions/sharing-automations/creating-actions/metadata-syntax-for-github-actions

[^40]: https://www.korgithub.com/Ch4.GitHub Actions/02.workflow/02.workflow_yaml_syntax.html

[^41]: https://codefresh.io/learn/github-actions/github-actions-workflows-basics-examples-and-a-quick-tutorial/

[^42]: https://stackoverflow.com/questions/75572493/github-actions-script-error-in-yaml-syntax

[^43]: https://every-up.tistory.com/69

[^44]: https://docs.github.com/en/actions/reference/workflow-commands-for-github-actions

[^45]: https://velog.io/@sung_hyuki/Github-Action

[^46]: https://docs.github.com/actions/using-workflows/about-workflows

[^47]: https://zzsza.github.io/development/2020/06/06/github-action/

[^48]: https://docs.github.com/en/actions/writing-workflows/about-workflows

[^49]: https://j-k4keye.tistory.com/46

[^50]: https://codefresh.io/learn/github-actions/github-actions-tutorial-and-examples/

[^51]: https://docs.github.com/en/actions/tutorials/creating-an-example-workflow

[^52]: https://www.dotenv.org/docs/frameworks/nextjs/github-actions

[^53]: https://docs.github.com/ko/enterprise-cloud@latest/actions/get-started/quickstart

[^54]: https://github.com/bhall2001/serverless-nextjs-github-ci-cd

[^55]: https://www.freecodecamp.org/news/learn-to-use-github-actions-step-by-step-guide/

[^56]: https://www.daleseo.com/github-actions-first-workflow/

[^57]: https://seonlog.tistory.com/62

[^58]: https://spacelift.io/blog/github-actions-tutorial

[^59]: https://docs.github.com/ko/actions/writing-workflows/workflow-syntax-for-github-actions

[^60]: https://javascript.plainenglish.io/nextjs-github-actions-deployment-b14dea6cfed5

[^61]: https://www.youtube.com/watch?v=ljINpvCvHnQ

[^62]: https://www.youtube.com/watch?v=JXGljLzvL2w

[^63]: https://www.youtube.com/watch?v=R8_veQiYBjI

[^64]: https://velog.io/@ggong/Github-Action에-대한-소개와-사용법

[^65]: https://www.youtube.com/watch?v=fkzpywlJcMA

[^66]: https://www.youtube.com/watch?v=ylEy4eLdhFs

[^67]: https://velog.io/@tt8784/Nextjs를-github-action-EC2로-배포하기1

[^68]: https://dev.to/kalkwst/workflow-triggering-events-and-event-actions-5cec

[^69]: https://www.stepsecurity.io/blog/implement-internal-github-actions-marketplace-with-stepsecurity

[^70]: https://docs.github.com/ko/actions/writing-workflows/choosing-when-your-workflow-runs/triggering-a-workflow

[^71]: https://somaz.tistory.com/323

[^72]: https://docs.github.com/actions/learn-github-actions/events-that-trigger-workflows

[^73]: https://github.com/actions/runner

[^74]: https://docs.github.com/en/actions/how-tos/writing-workflows/choosing-what-your-workflow-does/using-pre-written-building-blocks-in-your-workflow

[^75]: https://neon.com/guides/gihub-actions-self-hosted-runners

[^76]: https://velog.io/@gidskql6671/Github-Actions-Trigger-Event

[^77]: https://github.com/actions/actions-runner-controller/issues/1135

[^78]: https://staticmania.com/blog/how-to-deploy-next-js-application-with-github-action

[^79]: https://www.datree.io/resources/github-actions-best-practices

[^80]: https://magdongsani.tistory.com/6

[^81]: https://jonghoonpark.com/2024/10/29/next-js-github-hosting-pnpm

[^82]: https://exercism.org/docs/building/github/gha-best-practices

[^83]: https://www.stepsecurity.io/blog/github-actions-secrets-management-best-practices

[^84]: https://gist.github.com/officialrajdeepsingh/3714f17122ada14e399b8e43c19384b1

[^85]: https://www.stepsecurity.io/blog/github-actions-security-best-practices

[^86]: https://www.wiz.io/blog/github-actions-security-guide

[^87]: https://dev.to/yousufbasir/automating-nextjs-application-deployment-with-github-actions-38ac

[^88]: https://codefresh.io/learn/github-actions/deployment-with-github-actions/

[^89]: https://docs.github.com/ko/actions/security-for-github-actions/security-guides/using-secrets-in-github-actions

[^90]: https://docs.github.com/en/actions/guides

