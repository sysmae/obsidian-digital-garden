---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# GitHub Flow 핸드북

GitHub Flow는 GitHub에서 개발한 단순하고 효율적인 브랜치 기반 워크플로우입니다[^1]. 복잡한 Git Flow에 비해 간단한 구조를 가지고 있어 초보자부터 중급자까지 쉽게 적용할 수 있으며, 지속적 통합과 배포(CI/CD)에 최적화되어 있습니다[^2][^3].

## GitHub Flow의 탄생 배경

### 개발 이유와 역사적 맥락

GitHub Flow는 2011년 Scott Chacon이 GitHub에서 복잡한 Git Flow의 문제점을 해결하기 위해 고안한 브랜치 전략입니다[^1][^4]. 당시 Vincent Driessen이 2010년에 제 대규모 프로젝트에서는 효과적이었지만, GitHub와 같은 지속적 배포 환경에서는 너무 복잡하고 번거로웠습니다[^5][^1].

Git Flow의 주요 문제점들은 다음과 같았습니다[^1]:

- **과도한 복잡성**: 5개의 브랜치(master, develop, feature, release, hotfix)로 인한 관리 부담
- **느린 개발 사이클**: release 브랜치를 통한 복잡한 배포 과정
- **GUI 도구 제약**: 명령줄에서만 완전히 활용 가능한 헬퍼 스크립트

GitHub Flow는 이러한 문제를 해결하기 위해 **"항상 배포 가능한 master 브랜치"**라는 핵심 원칙을 기반으로 설계되었습니다[^1][^6]. GitHub에서 하루에도 여러 번 배포하는 환경에 맞춰 단순성과 속도를 중시했습니다[^5].

### 설계 철학

GitHub Flow의 설계 철학은 다음과 같습니다[^2][^1]:

- **단순성**: 복잡한 규칙 대신 간단하고 직관적인 프로세스
- **지속적 배포**: 언제든지 배포 가능한 상태 유지
- **협업 중심**: Pull Request를 통한 효과적인 코드 리뷰

Vincent Driessen 자신도 2020년 3월 5일 자신의 Git Flow 원문을 업데이트하며 웹 애플리케이션 개발에는 GitHub Flow와 같은 더 간단한 워크플로우를 채택하라고 권장했습니다[^7].

## GitHub Flow의 구조와 구성 요소

### 브랜치 구조

GitHub Flow는 매우 단순한 브랜치 구조를 가지고 있습니다[^8][^9]:

#### Main 브랜치 (Master/Main)

- **역할**: 프로덕션 환경에 배포되는 안정된 코드 저장소
- **특징**:
    - 항상 배포 가능한 상태(stable state) 유지[^10]
    - 모든 커밋은 빌드와 테스트를 통과해야 함[^10]
    - 새로운 브랜치의 시작점 역할[^9]


#### Feature 브랜치 (기능 브랜치)

- **역할**: 새로운 기능 개발, 버그 수정, 실험적 작업
- **특징**:
    - main 브랜치에서 분기[^9][^11]
    - 단기간 존재하는 임시 브랜치[^12]
    - 작업 완료 후 삭제[^4]
    - 명확한 이름 규칙 필요[^9]

**브랜치 명명 규칙 예시**[^11]:

- `feature/123456_MS_Implement_Feature_A`
- `bugfix/123456_MS_Fix_Problem_A`
- `feature/user-login-system`


### GitHub Flow vs Git Flow 비교

![GitHub Flow와 Git Flow 브랜치 전략의 주요 특성 비교 레이더 차트](https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/c2fefdbfc73b3f6d167c6ec5bffd2f16/d3829095-29f1-4070-8e89-369eeb408552/dfd53a00.png)

GitHub Flow와 Git Flow 브랜치 전략의 주요 특성 비교 레이더 차트

위 차트에서 볼 수 있듯이, GitHub Flow는 Git Flow에 비해 훨씬 단순하고 빠른 특성을 가지고 있습니다.


| 구분 | GitHub Flow | Git Flow |
| :-- | :-- | :-- |
| **브랜치 수** | 2개 (main + feature)[^8] | 5개 (master, develop, feature, release, hotfix)[^13] |
| **복잡도** | 낮음[^4] | 높음[^13] |
| **배포 주기** | 지속적 배포[^12] | 계획적 릴리즈[^13] |
| **적합한 환경** | 웹 애플리케이션, CI/CD 환경[^3] | 대규모 프로젝트, 엔터프라이즈[^13] |
| **학습 난이도** | 쉬움[^14] | 어려움[^14] |
| **릴리즈 관리** | 없음[^15] | 체계적[^13] |

## GitHub Flow 워크플로우 단계별 가이드

![GitHub Flow의 8단계 워크플로우 과정을 보여주는 플로우차트](https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/c2fefdbfc73b3f6d167c6ec5bffd2f16/4ef1e93c-0aea-4909-996e-b64c2c3a7ed2/b3705592.png)

GitHub Flow의 8단계 워크플로우 과정을 보여주는 플로우차트

GitHub Flow의 워크플로우는 8개의 주요 단계로 구성됩니다[^6][^16]:

### 1단계: 브랜치 생성 (Create a Branch)

**목적**: main 브랜치에 영향을 주지 않고 새로운 작업 환경 구성[^2][^6]

**실행 방법**:

```bash
# main 브랜치에서 최신 상태로 업데이트
git checkout main
git pull origin main

# 새로운 feature 브랜치 생성
git checkout -b feature/login-system
```

**중요 원칙**[^6][^17]:

- 모든 브랜치는 main에서 분기
- 브랜치 이름은 작업 내용을 명확히 표현
- 서술적이고 구체적인 이름 사용


### 2단계: 코드 작성 및 커밋 (Make Commits)

**목적**: 작업 진행 상황을 체계적으로 기록[^6][^16]

**실행 방법**:

```bash
# 파일 수정 후 스테이징
git add .

# 의미 있는 커밋 메시지 작성
git commit -m "Add user authentication validation logic"
```

**Best Practice**[^6][^18]:

- **자주 커밋**: 작은 단위로 나누어 커밋[^6]
- **원자적 커밋**: 하나의 논리적 변경사항만 포함[^18]
- **명확한 커밋 메시지**: 변경 사항과 이유를 설명[^6][^18]


### 3단계: 원격 저장소에 푸시 (Push to Remote)

**목적**: 작업 내용을 원격 저장소에 백업하고 공유[^7][^19]

**실행 방법**:

```bash
# 원격 저장소에 브랜치 푸시
git push origin feature/login-system

# 처음 푸시하는 경우 upstream 설정
git push --set-upstream origin feature/login-system
```

**GitHub Flow의 특징**[^19]:

- Git Flow와 달리 수시로 푸시 권장
- 하드웨어 문제나 작업 유실 방지
- 팀원들과 진행 상황 공유


### 4단계: Pull Request 생성 (Open a Pull Request)

**목적**: 코드 변경사항에 대한 논의와 검토 시작[^6][^20]

**GitHub에서 Pull Request 생성**[^21]:

1. GitHub 저장소 페이지로 이동
2. "Compare \& pull request" 버튼 클릭
3. base 브랜치를 main으로 설정
4. 제목과 설명 작성
5. "Create Pull Request" 클릭

**Pull Request 작성 가이드**[^20]:

- **명확한 제목**: 변경사항을 간결하게 요약
- **상세한 설명**: 변경 이유, 구현 방법, 테스트 결과 포함
- **관련 이슈 연결**: 해결하는 이슈가 있다면 링크


### 5단계: 코드 리뷰 및 논의 (Review and Discuss)

**목적**: 코드 품질 향상과 지식 공유[^14][^20]

**리뷰 프로세스**[^20]:

1. **리뷰어 추가**: PR 생성 후 적절한 리뷰어 지정
2. **코드 검토**: "Files changed" 탭에서 변경사항 확인
3. **피드백 제공**: 라인별 코멘트로 구체적 의견 제시
4. **논의 진행**: 개선사항이나 질문사항 토론

**리뷰 승인 과정**[^20]:

- **Approve**: 변경사항에 동의하고 병합 승인
- **Request Changes**: 수정이 필요한 부분 지적
- **Comment**: 승인 없이 의견만 제시


### 6단계: 자동화된 테스트 실행 (Automated Testing)

**목적**: 코드 품질과 기능 무결성 검증[^14][^22]

**CI/CD 파이프라인 통합**[^22][^12]:

- Pull Request 생성 시 자동 테스트 실행
- 빌드 성공 여부 확인
- 코드 커버리지 측정
- 정적 분석 도구 실행

**테스트 통과 조건**[^12]:

- 모든 단위 테스트 통과
- 통합 테스트 성공
- 코드 스타일 검사 통과
- 보안 취약점 검사 통과


### 7단계: Main 브랜치에 병합 (Merge to Main)

**목적**: 검증된 코드를 프로덕션 브랜치에 통합[^6][^12]

**병합 조건**[^12]:

- 모든 리뷰어 승인 완료
- 자동화된 테스트 통과
- 충돌 해결 완료

**병합 방법**[^6]:

```bash
# GitHub UI에서 "Merge pull request" 버튼 클릭
# 또는 명령줄에서
git checkout main
git merge feature/login-system
git push origin main
```


### 8단계: 자동 배포 (Deploy)

**목적**: 변경사항을 프로덕션 환경에 반영[^22][^12]

**배포 방식**[^12][^3]:

- **지속적 배포**: main 브랜치 병합 시 자동 배포
- **수동 배포**: 필요에 따라 수동으로 배포 트리거
- **단계적 배포**: 개발 → 스테이징 → 프로덕션 순서

**배포 후 작업**:

```bash
# feature 브랜치 삭제
git branch -d feature/login-system
git push origin --delete feature/login-system
```


## GitHub Flow의 장점과 단점

### 주요 장점

#### 1. 단순성과 이해 용이성

GitHub Flow는 직관적이고 간단한 구조로 인해 Git 초보자도 쉽게 이해할 수 있습니다[^14][^1]. 복잡한 브랜치 규칙이 없어 학습 곡선이 낮고, 팀 전체가 빠르게 적응할 수 있습니다[^14].

#### 2. 빠른 개발 사이클

지속적 배포에 최적화되어 있어 기능 개발부터 배포까지의 시간을 대폭 단축할 수 있습니다[^22][^13]. 복잡한 release 프로세스가 없어 개발 속도가 향상됩니다[^13].

#### 3. 효과적인 협업

Pull Request 중심의 워크플로우로 코드 리뷰와 지식 공유가 자연스럽게 이루어집니다[^14][^22]. 팀원 간 의사소통과 코드 품질 향상에 기여합니다[^14].

#### 4. CI/CD 통합

지속적 통합과 배포 파이프라인과 완벽하게 호환됩니다[^22][^12]. 자동화된 테스트와 배포 프로세스를 쉽게 구축할 수 있습니다[^22].

#### 5. 유연성

프로젝트 요구사항에 따라 쉽게 적응할 수 있는 유연한 구조를 제공합니다[^14]. 다양한 개발 환경과 팀 규모에 맞춰 조정 가능합니다[^14].

### 주요 단점

#### 1. 제한적인 릴리즈 관리

체계적인 버전 관리나 릴리즈 계획이 필요한 프로젝트에는 부적합합니다[^14][^15]. release 브랜치가 없어 복잡한 릴리즈 프로세스를 관리하기 어렵습니다[^15].

#### 2. 대규모 프로젝트의 한계

복잡한 코드베이스와 여러 장기 기능 브랜치가 필요한 대규모 프로젝트에서는 효과적이지 않을 수 있습니다[^14][^15]. 더 구조화된 워크플로우가 필요할 수 있습니다[^15].

#### 3. 장기 릴리즈 계획 어려움

단기 기능 브랜치에 초점을 맞춰 설계되어 장기적인 릴리즈 계획이나 복잡한 기능 의존성 관리가 어렵습니다[^14][^15].

#### 4. 빈번한 병합 충돌 위험

자주 발생하는 브랜치 생성과 병합으로 인해 merge conflict가 발생할 가능성이 높습니다[^14][^15]. 이를 해결하는 기술과 경험이 필요합니다[^14].

#### 5. 안정성 위험

main 브랜치에 직접적이고 빈번한 병합으로 인해 불안정한 코드가 프로덕션에 배포될 위험이 있습니다[^14][^15]. 엄격한 코드 리뷰와 테스트가 필수적입니다[^14].

## 실제 구현 및 사용법

### GitHub에서의 실제 설정

#### Pull Request 규칙 설정

GitHub에서 브랜치 보호 규칙을 설정하여 GitHub Flow를 강제할 수 있습니다[^23]:

1. **Settings** → **Branches** 이동
2. **Add rule** 클릭
3. main 브랜치에 대한 보호 규칙 설정:
    - Require pull request reviews before merging
    - Require status checks to pass before merging
    - Require branches to be up to date before merging

#### 자동화 설정

GitHub Actions를 활용한 CI/CD 파이프라인 구성 예시[^24]:

```yaml
name: GitHub Flow CI/CD
on:
  pull_request:
    branches: [main]
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Run tests
        run: npm test
      
  deploy:
    if: github.ref == 'refs/heads/main'
    needs: test
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to production
        run: npm run deploy
```


### 팀 협업을 위한 Best Practices

#### 브랜치 명명 규칙

효과적인 브랜치 관리를 위한 명명 규칙[^9][^10]:

- **Feature**: `feature/사용자-로그인-시스템`
- **Bug Fix**: `bugfix/로그인-오류-수정`
- **Hotfix**: `hotfix/보안-패치`
- **Improvement**: `improvement/성능-최적화`


#### 커밋 메시지 가이드라인

명확하고 일관된 커밋 메시지 작성[^6][^18]:

```
타입: 간단한 설명 (50자 이내)

상세한 설명 (선택사항)
- 변경 이유
- 구현 방법
- 영향 범위

관련 이슈: #123
```


#### 코드 리뷰 체크리스트

효과적인 코드 리뷰를 위한 체크포인트[^20]:

- **기능성**: 요구사항을 올바르게 구현했는가?
- **가독성**: 코드가 이해하기 쉬운가?
- **성능**: 성능상 문제는 없는가?
- **보안**: 보안 취약점은 없는가?
- **테스트**: 적절한 테스트가 포함되어 있는가?


### 도구와 통합

#### 추천 도구들

GitHub Flow 구현을 위한 유용한 도구들[^12][^24]:

- **GitHub Desktop**: 시각적 Git 관리 도구
- **VSCode with GitLens**: 강력한 Git 통합 기능
- **GitHub CLI**: 명령줄에서 GitHub 작업 수행
- **Conventional Commits**: 일관된 커밋 메시지 작성


#### CI/CD 도구 통합

다양한 CI/CD 도구와의 연동[^22][^24]:

- **GitHub Actions**: GitHub 네이티브 CI/CD
- **Jenkins**: 온프레미스 CI/CD 서버
- **Travis CI**: 클라우드 기반 CI 서비스
- **AWS CodePipeline**: AWS 환경 CI/CD


## 적용 시나리오와 권장사항

### 적합한 프로젝트 유형

#### 1. 웹 애플리케이션

GitHub Flow는 웹 애플리케이션 개발에 가장 적합합니다[^3][^25]:

- 빠른 기능 배포 필요
- 사용자 피드백 기반 반복 개발
- 지속적 업데이트와 개선


#### 2. 스타트업과 애자일 팀

소규모 팀이나 스타트업 환경에서 효과적입니다[^14][^25]:

- 빠른 시장 출시 필요
- 유연한 개발 프로세스 요구
- 제한된 리소스로 효율성 추구


#### 3. 오픈소스 프로젝트

GitHub 기반 오픈소스 프로젝트에 최적화되어 있습니다[^2][^25]:

- 다양한 기여자들의 협업
- Pull Request 기반 기여 시스템
- 투명한 개발 프로세스


#### 4. 마이크로서비스 아키텍처

개별 서비스의 독립적 배포에 적합합니다[^25]:

- 서비스별 독립적 개발 사이클
- 빠른 배포와 롤백 필요
- 팀 간 의존성 최소화


### 부적합한 환경

#### 1. 엔터프라이즈 대규모 프로젝트

복잡한 릴리즈 관리가 필요한 환경에는 부적합합니다[^14][^15]:

- 여러 버전 동시 유지보수
- 엄격한 품질 관리 프로세스
- 복잡한 승인 체계


#### 2. 규제가 심한 산업

금융, 의료 등 규제가 엄격한 분야에서는 제한적입니다[^15]:

- 상세한 감사 추적 필요
- 복잡한 승인 프로세스
- 엄격한 변경 관리


#### 3. 긴 개발 사이클 프로젝트

장기간에 걸친 대규모 기능 개발에는 부적합합니다[^15]:

- 분기별 또는 연간 릴리즈
- 복잡한 기능 의존성
- 장기 브랜치 유지 필요


### 도입 가이드라인

#### 팀 준비사항

GitHub Flow 도입 전 팀이 준비해야 할 사항들[^18][^26]:

1. **Git 기본 지식**: 모든 팀원의 Git 사용 능력
2. **코드 리뷰 문화**: 건설적 피드백 문화 구축
3. **자동화 환경**: CI/CD 파이프라인 구축
4. **테스트 전략**: 충분한 자동화된 테스트

#### 단계적 도입 전략

점진적 도입을 위한 단계별 전략[^27][^26]:

1. **1단계**: 브랜치 전략 교육과 훈련
2. **2단계**: 파일럿 프로젝트에서 시험 적용
3. **3단계**: CI/CD 파이프라인 구축
4. **4단계**: 전체 프로젝트로 확대 적용

#### 성공을 위한 핵심 요소

GitHub Flow 성공적 적용을 위한 핵심 요소들[^22][^18]:

- **자동화된 테스트**: 충분한 테스트 커버리지
- **빠른 피드백**: 신속한 코드 리뷰와 테스트 결과
- **명확한 가이드라인**: 일관된 브랜치 명명과 커밋 규칙
- **지속적 개선**: 프로세스의 지속적 모니터링과 개선


## 다른 브랜치 전략과의 비교

### Git Flow와의 상세 비교

| 측면 | GitHub Flow | Git Flow |
| :-- | :-- | :-- |
| **브랜치 구조** | main + feature[^8] | master + develop + feature + release + hotfix[^13] |
| **릴리즈 관리** | 지속적 배포[^12] | 계획적 릴리즈[^13] |
| **복잡도** | 낮음 - 2개 브랜치[^4] | 높음 - 5개 브랜치[^13] |
| **배포 주기** | 수시 배포[^3] | 주기적 배포[^13] |
| **버전 관리** | 단순[^15] | 체계적[^13] |
| **팀 크기** | 소규모 팀[^14] | 대규모 팀[^13] |
| **학습 곡선** | 완만[^14] | 가파름[^13] |
| **CI/CD 통합** | 우수[^22] | 보통[^13] |

### GitLab Flow와의 비교

GitLab Flow는 GitHub Flow의 단순함을 유지하면서 환경별 브랜치를 추가한 전략입니다[^25]:

**GitLab Flow 특징**[^25]:

- main + feature + environment 브랜치 (staging, production)
- CI/CD 파이프라인과 긴밀한 통합
- 환경별 배포 관리

**선택 기준**[^25]:

- **GitHub Flow**: 단일 환경, 빠른 배포
- **GitLab Flow**: 다중 환경, 체계적 배포 관리


### Trunk-based Development와의 비교

Trunk-based Development는 모든 개발자가 단일 브랜치에서 작업하는 전략입니다[^7]:

**주요 차이점**[^7]:

- **브랜치 사용**: Trunk-based는 브랜치 최소화, GitHub Flow는 기능별 브랜치
- **통합 주기**: Trunk-based는 매일 통합, GitHub Flow는 기능 완성 시
- **적용 난이도**: Trunk-based는 높은 숙련도 필요


## 문제 해결과 팁

### 자주 발생하는 문제들

#### Merge Conflict 해결

빈번한 병합 충돌을 최소화하는 방법[^14][^18]:

1. **자주 동기화**: 정기적으로 main 브랜치와 동기화
2. **작은 단위 작업**: 큰 기능을 작은 단위로 분할
3. **커뮤니케이션**: 팀원 간 작업 영역 사전 조율
```bash
# main 브랜치 최신 상태로 업데이트
git checkout main
git pull origin main

# feature 브랜치에 main 변경사항 반영
git checkout feature/my-feature
git rebase main
```


#### Pull Request 관리

효과적인 PR 관리 방법[^20]:

- **작은 PR**: 리뷰하기 쉬운 크기로 분할
- **명확한 설명**: 변경 사항과 이유를 상세히 기술
- **신속한 리뷰**: 24시간 내 리뷰 완료 목표


### 성능 최적화 팁

#### 브랜치 정리

불필요한 브랜치 정리로 저장소 최적화[^18]:

```bash
# 병합된 브랜치 자동 삭제
git branch --merged main | grep -v main | xargs -n 1 git branch -d

# 원격 브랜치 정리
git remote prune origin
```


#### 커밋 히스토리 관리

깔끔한 커밋 히스토리 유지[^9]:

- **Squash Merge**: 여러 커밋을 하나로 합치기
- **Rebase**: 선형적인 히스토리 유지
- **의미 있는 커밋**: 각 커밋이 독립적인 의미 가지도록


## GitHub Flow의 미래와 발전 방향

### 현재 트렌드

GitHub Flow는 DevOps와 지속적 배포의 확산과 함께 더욱 널리 채택되고 있습니다[^22][^28]. 특히 클라우드 네이티브 개발과 마이크로서비스 아키텍처의 증가로 인해 그 중요성이 더욱 커지고 있습니다[^22].

### 도구의 진화

GitHub Flow를 지원하는 도구들이 지속적으로 발전하고 있습니다[^22]:

- **AI 기반 코드 리뷰**: 자동화된 코드 품질 검사
- **고급 CI/CD**: 더 정교한 배포 파이프라인
- **협업 도구**: 향상된 팀 협업 기능


### 모범 사례의 확산

GitHub Flow의 성공 사례가 다양한 산업과 프로젝트 유형으로 확산되고 있으며, 각 환경에 맞는 변형된 형태들이 개발되고 있습니다[^27][^22].

GitHub Flow는 현대적인 소프트웨어 개발 환경에서 단순성과 효율성을 추구하는 팀들에게 이상적인 선택입니다. 적절한 도구와 프로세스를 갖춘 환경에서 GitHub Flow를 올바르게 적용한다면, 개발 생산성과 코드 품질을 동시에 향상시킬 수 있는 강력한 워크플로우가 될 것입니다[^2][^1][^22].

<div style="text-align: center">⁂</div>

[^1]: https://githubflow.github.io

[^2]: https://roachhd.gitbooks.io/github-guides/flow/flow.html

[^3]: https://rupijun.tistory.com/entry/GitHub-Flow-웹-개발의-효율적-브랜치-관리-전략

[^4]: https://parkstate.tistory.com/33

[^5]: https://blog.ull.im/engineering/2019/06/25/git-workflow-for-ci-cd.html

[^6]: https://www.w3schools.com/git/git_github_flow.asp?remote=github

[^7]: https://uzzam.dev/3

[^8]: https://www.heropy.dev/p/6hdJi6

[^9]: https://dongminyoon.tistory.com/39

[^10]: https://ltr2006.tistory.com/50

[^11]: https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-git-branch-approach/branches-in-a-git-hub-flow-strategy.html

[^12]: https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-git-branch-approach/github-flow-branching-strategy.html

[^13]: https://escapefromcoding.tistory.com/746

[^14]: https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-git-branch-approach/advantages-and-disadvantages-of-the-git-hub-flow-strategy.html

[^15]: https://codiin.com/mastering-git-flow-vs-github-flow-which-should-you-choose/

[^16]: https://www.iv-lead.com/hubspot-by-iv-lead/github-flow-a-quick-5-step-process

[^17]: https://trailhead.salesforce.com/content/learn/modules/git-and-git-hub-basics/work-with-the-git-hub-workflow

[^18]: https://stackademic.com/blog/git-workflow-best-practices-for-a-smooth-and-efficient-development-process

[^19]: https://youngtoad.tistory.com/46

[^20]: https://graphite.dev/guides/github-pull-request-review-workflow

[^21]: https://docs.github.com/articles/creating-a-pull-request

[^22]: https://www.statsig.com/perspectives/gitflow-vs-github-flow

[^23]: https://www.youtube.com/watch?v=rDcswW9NOnc

[^24]: https://blog.cloudacode.com/코드-관리-전략-git-flow-github-flow-d5dc2a191039

[^25]: https://dev-miyeon.tistory.com/7

[^26]: https://magicbox.readthedocs.io/en/latest/admin/github-workflow/

[^27]: https://betterprogramming.pub/a-simple-git-repository-strategy-93a0c7450f23

[^28]: https://www.reddit.com/r/git/comments/1972njp/git_workflows_best_practices_branching_strategies/

[^29]: https://sjevie.tistory.com/entry/적용기-서로-다른-프로젝트에-다른-Branch-전략-적용-후기Git-Flow-Github-Flow

[^30]: https://sihyung92.oopy.io/architecture/gitflow-vs-githubflow

[^31]: https://docs.github.com/en/get-started/using-github/github-flow

[^32]: https://techblog.woowahan.com/2553/

[^33]: https://inpa.tistory.com/entry/GIT-⚡️-github-flow-git-flow-📈-브랜치-전략

[^34]: https://oris9.tistory.com/45

[^35]: https://github.com/NiceFortune/happy_saver/issues/8

[^36]: https://blog.honeybomb.kr/4

[^37]: https://ujuc.github.io/2015/12/16/git-flow-github-flow-gitlab-flow/

[^38]: https://www.harness.io/blog/github-flow-vs-git-flow-whats-the-difference

[^39]: https://www.abtasty.com/blog/git-branching-strategies/

[^40]: https://co-no.tistory.com/entry/Git-브랜치branch-전략-git-flow-방식-github-flow-방식-git-lab-방식

[^41]: https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/choosing-git-branch-approach/github-flow-branching-strategy.html

[^42]: https://docs.github.com/actions/using-workflows/manually-running-a-workflow

[^43]: https://github.com/vaadin/flow-components

[^44]: https://docs.github.com/ko/actions/writing-workflows/workflow-syntax-for-github-actions

[^45]: https://www.youtube.com/watch?v=x-b_ij22vWg

[^46]: https://github.com/langflow-ai/langflow/issues/8081

[^47]: https://www.daleseo.com/github-actions-steps/

[^48]: https://sunrise-min.tistory.com/entry/Git-Flow-vs-Github-Flow-브랜치-전략

[^49]: https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/choosing-git-branch-approach/advantages-and-disadvantages-of-the-git-hub-flow-strategy.html

[^50]: https://leejincha.tistory.com/290

[^51]: https://builter-stack.tistory.com/entry/Git-Flow-and-GitHub-Flow

[^52]: https://devocean.sk.com/blog/techBoardDetail.do?ID=165571\&boardType=techBlog

[^53]: https://github.com/Piwigo/Piwigo/wiki/Git-workflow-and-best-pratices

[^54]: https://github.com/arquizade/git-flow-implementation

[^55]: https://gitversion.net/docs/learn/branching-strategies/githubflow/

[^56]: https://www.youtube.com/watch?v=GgjIvUrOpmg

[^57]: https://docs.github.com/articles/about-pull-requests

[^58]: https://github.com/a-a-ron/Github-Flow

[^59]: https://docs.aws.amazon.com/prescriptive-guidance/latest/patterns/implement-a-github-flow-branching-strategy-for-multi-account-devops-environments.html

[^60]: https://docs.aws.amazon.com/ko_kr/prescriptive-guidance/latest/patterns/implement-a-github-flow-branching-strategy-for-multi-account-devops-environments.html

[^61]: https://docs.wpvip.com/development-workflow/github-pr-reviews/

