---
publish: true
---



jenkins 에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘. 초보~중급
# Jenkins 핸드북: 초보자부터 중급자까지

## 1. Jenkins란 무엇인가?

Jenkins는 2004년 Sun Microsystems의 Kohsuke Kawaguchi가 개발한 오픈소스 자동화 서버입니다[^1]. 처음에는 Hudson이라는 이름으로 시작되었지만, 2011년 Oracle과의 분쟁으로 인해 Jenkins로 개명되었습니다[^2][^3]. Jenkins는 **지속적 통합(CI)과 지속적 배포(CD)**를 위한 핵심 도구로, 소프트웨어 개발 생명주기 전반에 걸쳐 빌드, 테스트, 배포 과정을 자동화합니다[^1][^4].

### Jenkins가 해결하는 문제

개발자들이 코드를 커밋할 때마다 빌드가 실패하는 상황을 겪었던 Kohsuke Kawaguchi는 "팀원들의 분노를 피하기 위해" Jenkins를 만들었습니다[^5][^6]. Jenkins는 다음과 같은 문제들을 해결합니다:

- **수동 빌드와 테스트의 비효율성**: 개발자가 수동으로 코하는 시간 소모
- **통합 오류의 늦은 발견**: 코드 변경사항이 메인 브랜치에 통합된 후에야 발견되는 문제들
- **배포 과정의 복잡성**: 여러 환경에 일관되게 배포하는 어려움


## 2. Jenkins 아키텍처

Jenkins는 **Master-Agent(또는 Controller-Node) 아키텍처**를 기반으로 합니다[^7][^8][^9].

### 2.1 Jenkins Controller (Master)

Jenkins Controller는 전체 시스템의 두뇌 역할을 합니다[^8][^9]:

- **작업 스케줄링**: 빌드 작업을 예약하고 관리
- **에이전트 모니터링**: 연결된 에이전트들의 상태를 추적
- **웹 인터페이스 제공**: 사용자가 작업을 구성하고 모니터링할 수 있는 UI
- **플러그인 관리**: 플러그인 설치 및 관리
- **구성 저장**: 모든 주요 구성 정보 보관


### 2.2 Jenkins Agent (Slave)

Agent는 실제 작업을 수행하는 워커 노드입니다[^7][^8]:

- **작업 실행**: Controller가 할당한 빌드, 테스트, 배포 작업 수행
- **환경 격리**: 서로 다른 운영체제나 환경에서 작업 실행 가능
- **확장성 제공**: 더 많은 Agent를 추가하여 처리 능력 향상


### 2.3 통신 방식

Controller와 Agent 간 통신은 다음 방식을 사용합니다[^10]:

- **SSH (Secure Shell)**: Linux/Unix 노드에 주로 사용
- **JNLP (Java Network Launch Protocol)**: Windows 노드나 방화벽 뒤의 노드에 사용


### 2.4 데이터 저장 구조

Jenkins는 파일 시스템을 사용하여 데이터를 저장합니다[^11][^12]:

```
JENKINS_HOME/
├── config.xml (Jenkins 루트 구성)
├── jobs/
│   └── [JOB_NAME]/
│       ├── config.xml (작업 구성 파일)
│       └── builds/
│           └── [BUILD_ID]/
│               ├── build.xml
│               └── log
├── workspace/ (작업 공간)
├── plugins/ (플러그인 저장소)
└── secrets/ (보안 정보)
```


## 3. Jenkins 설치 및 설정

### 3.1 시스템 요구사항

**최소 요구사항**[^13]:

- RAM: 256MB (권장: 4GB+)
- 디스크 공간: 1GB (권장: 50GB+)
- Java: Java 17 또는 21


### 3.2 Windows 설치

**MSI 인스톨러 사용법**[^14][^15][^13]:

1. **Jenkins MSI 다운로드**: jenkins.io에서 LTS 버전 다운로드
2. **Java 설치**: JDK 8 이상 설치 및 환경변수 설정
3. **설치 실행**:
    - MSI 파일 실행
    - 설치 경로 선택 (기본: Program Files)
    - 서비스 계정 설정 (로컬 또는 도메인 사용자 권장)
    - 포트 설정 (기본: 8080)
4. **초기 설정**:
    - 브라우저에서 `http://localhost:8080` 접속
    - 초기 관리자 비밀번호 입력 (`JENKINS_HOME/secrets/initialAdminPassword`)
    - 권장 플러그인 설치
    - 관리자 계정 생성

### 3.3 기본 구성

**시스템 구성 주요 항목**[^16][^17]:

- **Home Directory**: Jenkins 루트 디렉토리 위치
- **Executors 수**: 동시 실행 가능한 작업 수
- **Quiet Period**: 트리거 후 작업 시작 전 대기 시간
- **SCM Checkout Retry Count**: 소스 코드 체크아웃 재시도 횟수


## 4. Jenkins 사용법

### 4.1 Job/Project 생성

Jenkins에서는 다양한 유형의 프로젝트를 생성할 수 있습니다[^18]:

**Freestyle Project**: 가장 기본적인 프로젝트 유형

1. "New Item" 클릭
2. 프로젝트 이름 입력
3. "Freestyle project" 선택
4. 소스 코드 관리 설정 (Git, SVN 등)
5. 빌드 트리거 설정
6. 빌드 단계 추가 (Shell 스크립트, Maven, Gradle 등)

### 4.2 Pipeline 사용법

**Pipeline이란?**
Pipeline은 코드로서의 파이프라인(Pipeline as Code) 개념을 구현합니다[^19][^20]. Jenkinsfile을 통해 전체 CI/CD 프로세스를 정의할 수 있습니다.

**기본 Pipeline 구조**[^21][^22]:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                echo 'Building...'
                // 빌드 명령어
            }
        }
        
        stage('Test') {
            steps {
                echo 'Testing...'
                // 테스트 명령어
            }
        }
        
        stage('Deploy') {
            steps {
                echo 'Deploying...'
                // 배포 명령어
            }
        }
    }
}
```

**Pipeline 생성 방법**[^20]:

1. **Blue Ocean 사용**: 시각적 편집기를 통한 Pipeline 생성
2. **Classic UI**: 웹 인터페이스에서 직접 스크립트 입력
3. **SCM**: Jenkinsfile을 소스 코드 저장소에 저장

### 4.3 Multibranch Pipeline

여러 브랜치를 자동으로 감지하고 각각에 대해 별도의 Pipeline을 생성합니다[^23]:

1. "New Item" → "Multibranch Pipeline" 선택
2. 소스 저장소 설정 (GitHub, GitLab 등)
3. 브랜치 소스 구성
4. 빌드 구성 (Jenkinsfile 기반)

## 5. 플러그인 생태계

Jenkins의 강력함은 **1,800개 이상의 플러그인**에서 나옵니다[^4][^24].

### 5.1 플러그인 설치

**웹 UI를 통한 설치**[^25]:

1. "Manage Jenkins" → "Plugins" 이동
2. "Available" 탭에서 플러그인 검색
3. 원하는 플러그인 선택
4. "Install without restart" 클릭

### 5.2 주요 플러그인

**필수 플러그인들**[^26]:

- **Git Plugin**: Git 저장소 연동
- **Pipeline Plugin**: Pipeline 기능 제공
- **Docker Pipeline Plugin**: Docker 컨테이너 활용
- **Blue Ocean**: 현대적인 UI 제공
- **SonarQube Scanner**: 코드 품질 분석
- **Kubernetes Plugin**: Kubernetes 연동


### 5.3 플러그인 관리 모범 사례

**보안과 안정성을 위한 권장사항**[^27]:

- 신뢰할 수 있는 개발자의 플러그인만 설치
- 정기적인 플러그인 업데이트
- 사용하지 않는 플러그인 제거
- 플러그인 호환성 확인 후 설치


## 6. 보안 설정

### 6.1 인증 (Authentication)

Jenkins는 다양한 인증 방식을 지원합니다[^28][^29][^30]:

**내장 사용자 데이터베이스**:

- Jenkins 자체 사용자 관리 시스템
- 소규모 팀에 적합
- "Manage Jenkins" → "Security"에서 설정

**외부 인증 시스템**:

- **LDAP**: 기업 환경에서 주로 사용
- **Active Directory**: Windows 환경 통합
- **OAuth**: Google, GitHub 등과 연동
- **SSO**: 단일 로그인 시스템


### 6.2 권한 부여 (Authorization)

**권한 부여 전략**[^28][^31]:

**Matrix Authorization Strategy**:

- 사용자별/그룹별 세부 권한 설정
- 읽기, 빌드, 관리 등 권한 구분

**Project-based Matrix Authorization**:

- 프로젝트별 권한 설정
- 민감한 프로젝트에 추가 보안 레이어 제공

**Role-based Authorization Strategy**:

- 역할 기반 접근 제어
- 권한 관리 단순화


### 6.3 보안 강화

**주요 보안 설정**[^28]:

- **CSRF 보호**: 사이트 간 요청 위조 방지
- **TLS/SSL 암호화**: HTTPS 통신 활성화
- **스크립트 승인**: Pipeline 스크립트 실행 전 승인
- **불필요한 기능 비활성화**: CLI, 원격 API 등


## 7. 모니터링 및 성능 관리

### 7.1 주요 모니터링 메트릭

**시스템 상태 메트릭**[^32]:

- **disk-space**: 디스크 사용량 모니터링
- **plugins**: 플러그인 실행 상태
- **thread-deadlock**: 스레드 데드락 감지
- **temporary-space**: 임시 공간 사용량

**JVM 메트릭**[^32]:

- **system.cpu.load**: CPU 사용률
- **vm.memory.heap.usage**: 힙 메모리 사용률
- **vm.uptime.milliseconds**: JVM 가동 시간
- **vm.deadlocks**: 데드락 스레드 수


### 7.2 모니터링 도구

**내장 모니터링**[^33]:

- Jenkins Monitoring Plugin (JavaMelody 기반)
- HTTP 응답 시간, 메모리 사용량 차트 제공
- `/monitoring` 엔드포인트를 통한 접근

**외부 모니터링 솔루션**[^34][^35]:

- **Prometheus + Grafana**: 오픈소스 모니터링 스택
- **Site24x7**: 통합 모니터링 플랫폼
- **IBM Instana**: 애플리케이션 성능 모니터링


## 8. 문제 해결 (Troubleshooting)

### 8.1 일반적인 문제들

**Jenkins 시작 실패**[^36][^37]:

- **원인**: 포트 충돌, 권한 부족, 구성 파일 손상
- **해결**: 로그 확인 (`/var/log/jenkins/jenkins.log`), 설정 복구

**메모리 부족 오류**[^36]:

- **원인**: JVM 힙 메모리 부족
- **해결**: `JAVA_ARGS`에서 `-Xmx2g` 등으로 힙 크기 증가

**플러그인 호환성 문제**[^36]:

- **원인**: Jenkins 버전과 플러그인 비호환
- **해결**: 플러그인을 호환 버전으로 업데이트


### 8.2 성능 문제

**디스크 공간 부족**[^36][^38]:

- 불필요한 빌드 아티팩트 정리
- 로그 파일 로테이션 설정
- 디스크 용량 확장

**과부하 상태**[^36]:

- Agent 노드 추가로 부하 분산
- 동시 실행 작업 수 조정
- 리소스 사용량 모니터링


### 8.3 권한 문제

**파일 접근 권한**[^38]:

- Jenkins 프로세스의 파일 시스템 권한 확인
- 홈 디렉토리 소유권 및 권한 설정
- SELinux/AppArmor 정책 검토


## 9. 모범 사례

### 9.1 Pipeline 설계

**파이프라인 구조화**[^23]:

- 단계별 명확한 분리 (빌드, 테스트, 배포)
- 병렬 실행 활용으로 시간 단축
- 실패 시 빠른 피드백 제공

**코드로서의 파이프라인**[^22]:

- Jenkinsfile을 소스 코드와 함께 버전 관리
- 선언적 파이프라인 문법 사용 권장
- 공유 라이브러리 활용으로 재사용성 증대


### 9.2 보안 모범 사례

**최소 권한 원칙**:

- 사용자에게 필요한 최소한의 권한만 부여
- 정기적인 권한 검토 및 정리
- 서비스 계정 사용 시 별도 계정 생성

**정기적인 업데이트**:

- Jenkins 코어 및 플러그인 정기 업데이트
- 보안 패치 즉시 적용
- 취약점 스캔 정기 실행


### 9.3 확장성 고려사항

**분산 아키텍처**:

- 워크로드에 따른 Agent 노드 확장
- 클라우드 기반 동적 Agent 활용
- 컨테이너 기반 빌드 환경 구성


## 10. Jenkins의 미래와 대안

### 10.1 현재 트렌드

Jenkins는 여전히 가장 널리 사용되는 CI/CD 도구 중 하나로, 개발자의 54%가 사용하고 있습니다[^9]. 하지만 **클라우드 네이티브** 환경과 **컨테이너 기반** 워크플로우에 대한 요구가 증가하면서 새로운 도구들과의 경쟁이 치열해지고 있습니다[^39][^40].

### 10.2 주요 대안 도구들

**클라우드 네이티브 솔루션**[^39][^41]:

- **GitHub Actions**: GitHub과 긴밀한 통합
- **GitLab CI/CD**: GitLab 플랫폼 내장 CI/CD
- **CircleCI**: 클라우드 기반 확장성
- **Azure DevOps**: Microsoft 생태계 통합

**Jenkins의 장점**[^39]:

- 오픈소스이며 무료
- 방대한 플러그인 생태계
- 높은 커스터마이징 가능성
- 강력한 커뮤니티 지원

**Jenkins의 한계**[^40]:

- 복잡한 설정과 유지보수
- 플러그인 관리의 어려움
- 클라우드 네이티브 환경에 대한 제한적 지원
- Groovy 기반의 복잡한 구성 언어


## 결론

Jenkins는 2004년 출시 이후 지속적으로 발전해온 성숙한 CI/CD 플랫폼입니다. 강력한 플러그인 생태계와 유연한 아키텍처를 통해 다양한 개발 환경에 적응할 수 있으며, 대규모 엔터프라이즈부터 소규모 팀까지 폭넓게 활용되고 있습니다.

초보자부터 중급자까지 Jenkins를 효과적으로 활용하기 위해서는:

1. **기본 개념 이해**: CI/CD 개념과 Jenkins 아키텍처
2. **점진적 학습**: Freestyle 프로젝트에서 시작하여 Pipeline으로 발전
3. **보안 중시**: 처음부터 적절한 보안 설정 적용
4. **모니터링 습관**: 성능과 안정성을 위한 지속적인 모니터링
5. **커뮤니티 활용**: 풍부한 문서와 플러그인 생태계 활용

Jenkins는 여전히 강력하고 유용한 도구이지만, 프로젝트의 요구사항과 팀의 역량을 고려하여 적절한 CI/CD 솔루션을 선택하는 것이 중요합니다. 클라우드 네이티브 환경으로의 전환을 고려한다면 대안 도구들도 함께 검토해보는 것을 권장합니다.

<div style="text-align: center">⁂</div>

[^1]: https://www.simplilearn.com/tutorials/jenkins-tutorial/what-is-jenkins

[^2]: https://en.wikipedia.org/wiki/Jenkins_(software)

[^3]: https://www.linkedin.com/pulse/what-jenkins-history-features-advantages-how-nokia-getting-shaikh

[^4]: https://www.cloudbees.com/jenkins/what-is-jenkins

[^5]: https://www.reddit.com/r/programming/comments/1c1slh3/jenkins_was_invented_bc_an_engineer_got_tired_of/

[^6]: https://www.upgrad.com/blog/what-is-jenkins/

[^7]: https://cloudinfrastructureservices.co.uk/jenkins-architecture-with-diagrams-tutorial-explained/

[^8]: https://www.linkedin.com/pulse/jenkins-architecture-overview-dr-swati-saxena-57juf

[^9]: https://devopscube.com/jenkins-architecture-explained/

[^10]: https://notes.kodekloud.com/docs/Certified-Jenkins-Engineer/Jenkins-Setup-and-Interface/Jenkins-Architecture

[^11]: https://wiki.jenkins-ci.org/JENKINS/Architecture.html

[^12]: https://stackoverflow.com/questions/6131114/where-does-jenkins-store-configuration-files-for-the-jobs-it-runs

[^13]: https://www.jenkins.io/doc/book/installing/windows/

[^14]: https://www.youtube.com/watch?v=Zdxko2bPAAw

[^15]: https://www.geeksforgeeks.org/devops/installing-jenkins-on-windows/

[^16]: https://minholee93.tistory.com/entry/Jenkins-Basic-Configurations

[^17]: https://userguide.mystsoftware.com/build/ci/jenkins/

[^18]: https://www.tutorialspoint.com/jenkins/jenkins_setup_build_jobs.htm

[^19]: https://www.jenkins.io/doc/book/pipeline/

[^20]: https://www.jenkins.io/doc/book/pipeline/getting-started/

[^21]: https://www.jenkins.io/doc/pipeline/tour/hello-world/

[^22]: https://devopscube.com/jenkins-pipeline-as-code/

[^23]: https://www.jenkins.io/doc/book/using/best-practices/

[^24]: https://www.jenkins.io

[^25]: https://www.jenkins.io/doc/book/managing/plugins/

[^26]: https://www.opsera.io/blog/ace-your-devops-game-with-this-ultimate-list-of-plugins-in-jenkins

[^27]: https://buildkite.com/resources/blog/best-practices-for-managing-jenkins-plugins/

[^28]: https://jeevisoft.com/blogs/2025/01/securing-your-jenkins-pipeline-core-security-concepts-you-need-to-know/

[^29]: https://notes.kodekloud.com/docs/Jenkins-For-Beginners/Automation-and-Security/Jenkins-Authentication

[^30]: https://www.geeksforgeeks.org/devops/managing-security-settings-and-access-controls-in-jenkins/

[^31]: https://www.jenkins.io/doc/book/security/access-control/

[^32]: https://www.site24x7.com/learn/jenkins-performance-monitoring.html

[^33]: https://plugins.jenkins.io/monitoring/

[^34]: https://www.cloudraft.io/blog/monitoring-jenkins

[^35]: https://grafana.com/grafana/dashboards/9964-jenkins-performance-and-health-overview/

[^36]: https://www.linkedin.com/pulse/troubleshooting-jenkins-resolving-common-issues-harish-raj-qkasc

[^37]: https://devops-cicd.hashnode.dev/jenkins-troubleshooting-in-realtime

[^38]: https://www.site24x7.com/learn/jenkins-troubleshooting-guide.html

[^39]: https://buildkite.com/resources/blog/alternatives-to-jenkins/

[^40]: https://spacelift.io/blog/jenkins-alternatives

[^41]: https://www.theserverside.com/tip/5-Jenkins-alternatives-for-Java-developers

[^42]: https://www.jenkins.io/doc/pipeline/tour/getting-started/

[^43]: https://www.jenkins.io/doc/book/installing/

[^44]: https://karandeepsingh.ca/posts/jenkins-versions-evolution-journey/

[^45]: https://www.jenkins.io/doc/developer/architecture/

[^46]: https://www.jenkins.io/doc/book/installing/linux/

[^47]: https://velog.io/@manarc/Architecture-Jenkins-Automation

[^48]: https://notes.kodekloud.com/docs/Jenkins/Jenkins-Plugins-and-Integrations/Installing-a-Plugin

[^49]: https://contradicto-lee.tistory.com/76

[^50]: https://www.lambdatest.com/blog/jenkins-pipeline-tutorial/

[^51]: https://www.youtube.com/watch?v=hgUGblYj-JQ

[^52]: https://www.youtube.com/watch?v=WkP5dt7dLXg

[^53]: https://news.ycombinator.com/item?id=14048860

[^54]: https://www.jenkins.io/doc/tutorials/

[^55]: https://www.jenkins.io/doc/book/managing/system-configuration/

[^56]: https://tuxcare.com/blog/what-is-jenkins/

[^57]: https://www.browserstack.com/guide/circleci-vs-jenkins

[^58]: https://www.jenkins.io/solutions/

[^59]: https://www.blazemeter.com/blog/jenkins-automation

[^60]: https://www.devopsschool.com/blog/what-is-jenkins-and-use-cases-of-jenkins/

[^61]: https://www.reddit.com/r/jenkinsci/comments/spomc1/real_world_examples_of_using_jenkins/

[^62]: https://www.functionize.com/blog/how-jenkins-supports-test-automation

[^63]: https://www.payoda.com/software-development-jenkins/

[^64]: https://www.linkedin.com/pulse/jenkins-industry-use-cases-benefits-harsh-srivastava

[^65]: https://www.netclues.com/blog/jenkins-vs-ansible

[^66]: https://www.browserstack.com/guide/jenkins-pipeline

[^67]: https://apiumhub.com/tech-blog-barcelona/advantages-of-jenkins/

[^68]: https://www.reddit.com/r/devops/comments/1blmktc/jenkins_vs_other_cicd_platforms/

[^69]: https://logz.io/blog/jenkins-in-a-nutshell/

[^70]: https://www.mdpi.com/1424-8220/24/18/6002

[^71]: https://automatenow.io/jenkins-vs-top-competitors/

[^72]: https://www.softlogicsys.in/jenkins-common-issues-and-solutions-for-beginners/

[^73]: https://www.ibm.com/docs/en/instana-observability/1.0.299?topic=technologies-monitoring-jenkins

[^74]: https://devopsvoyager.hashnode.dev/understanding-authentication-and-authorization-in-jenkins

[^75]: https://www.almtoolbox.com/jenkins-monitoring.php

[^76]: https://www.jenkins.io/doc/book/troubleshooting/

[^77]: https://www.jenkins.io/doc/book/security/managing-security/

[^78]: https://moldstud.com/articles/p-troubleshooting-jenkins-common-issues-and-effective-developer-solutions

[^79]: https://cycode.com/blog/jenkins-security-best-practices/

[^80]: https://www.jenkins.io/doc/book/system-administration/monitoring/

