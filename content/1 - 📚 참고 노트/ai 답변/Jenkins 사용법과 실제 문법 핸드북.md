---
publish:  true
---

젠킨스 사용법과 실제 문법에 초점을 두고 어떻게 설계되고 사용하는지 핸드북 형식으로 요약 정리. nodejs
# Jenkins 사용법과 실제 문법 핸드북

## 목차

1. [Jenkins Pipeline 기본 구조](#1-jenkins-pipeline-%EA%B8%B0%EB%B3%B8-%EA%B5%AC%EC%A1%B0)
2. [Declarative Pipeline 문법](#2-declarative-pipeline-%EB%AC%B8%EB%B2%95)
3. [Node.js 프로젝트 실제 예제](#3-nodejs-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%8B%A4%EC%A0%9C-%EC%98%88%EC%A0%9C)
4. [주요 Directive 상세 문법](#4-%EC%A3%BC%EC%9A%94-directive-%EC%83%81%EC%84%B8-%EB%AC%B8%EB%B2%95)
5. [조건부 실행과 병렬 처리](#5-%EC%A1%B0%EA%B1%B4%EB%B6%80-%EC%8B%A4%ED%96%89%EA%B3%BC-%EB%B3%91%EB%A0%AC-%EC%B2%98%EB%A6%AC)
6. [환경 변수와 자격 증명](#6-%ED%99%98%EA%B2%BD-%EB%B3%80%EC%88%98%EC%99%80-%EC%9E%90%EA%B2%A9-%EC%A6%9D%EB%AA%85)
7. [에러 처리와 후처리](#7-%EC%97%90%EB%9F%AC-%EC%B2%98%EB%A6%AC%EC%99%80-%ED%9B%84%EC%B2%98%EB%A6%AC)
8. [Docker 통합](#8-docker-%ED%86%B5%ED%95%A9)
9. [모범 사례와 보안](#9-%EB%AA%A8%EB%B2%94-%EC%82%AC%EB%A1%80%EC%99%80-%EB%B3%B4%EC%95%88)
10. [Shared Library 활용](#10-shared-library-%ED%99%9C%EC%9A%A9)

## 1. Jenkins Pipeline 기본 구조

### 1.1 Declarative Pipeline의 기본 틀

모든 유효한 Declarative Pipeline은 `pipeline` 블록 내에 정의되어야 합니다[^1][^2].

```groovy
pipeline { {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
```


### 1.2 필수 구성 요소

Declarative Pipeline은 다음 필수 요소들을 포함해야 합니다[^3]:

- **agent**: 파이프라인이 실행될 위치 지정
- **stages**: 하나 이상의 stage 블록 포함
- **stage**: 개별 작업 단위 정의
- **steps**: stage 내에서 실행될 구체적인 명령어들


## 2. Declarative Pipeline 문법

### 2.1 Pipeline 블록 구조

```groovy
pipeline {
    /* Declarative Pipeline 내용 */
}
```

기본 규칙[^2]:

- 최상위 레벨은 반드시 `pipeline { }` 블록이어야 함
- 세미콜론 없음 (각 문장은 별도 줄에 작성)
- 블록은 Sections, Directives, Steps, 또는 할당문으로만 구성


### 2.2 Agent 지시문

Agent는 Jenkins 환경에서 파이프라인이 실행될 위치를 지정합니다[^4][^5]:

```groovy
pipeline {
    agent any                    // 사용 가능한 모든 agent
    // 또는
    agent none                   // 글로벌 agent 없음 (각 stage에서 정의)
    // 또는
    agent {
        label 'linux-machine'    // 특정 라벨의 agent
    }
    // 또는
    agent {
        docker {
            image 'node:18'      // Docker 컨테이너에서 실행
        }
    }
}
```


### 2.3 Stages와 Steps

```groovy
pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/username/repository.git'
            }
        }
        stage('Build') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        stage('Test') {
            steps {
                sh 'npm test'
            }
        }
    }
}
```


## 3. Node.js 프로젝트 실제 예제

### 3.1 기본 Node.js Pipeline

Node.js 프로젝트를 위한 완전한 파이프라인 예제입니다[^6][^7]:

```groovy
pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-18'  // Jenkins에서 설정한 Node.js 도구명
    }
    
    environment {
        CI = 'true'
        NODE_ENV = 'production'
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/username/nodejs-app.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }
        
        stage('Lint') {
            steps {
                sh 'npm run lint'
            }
        }
        
        stage('Test') {
            steps {
                sh 'npm test'
            }
            post {
                always {
                    publishTestResults testResultsPattern: 'test-results.xml'
                }
            }
        }
        
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        
        stage('Archive Artifacts') {
            steps {
                archiveArtifacts artifacts: 'dist/**/*', 
                                allowEmptyArchive: true
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
```


### 3.2 Docker를 활용한 Node.js Pipeline

```groovy
pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            args '-v /tmp/.npm:/tmp/.npm'  // npm 캐시 마운트
        }
    }
    
    environment {
        HOME = '.'  // Docker 권한 문제 해결
        NPM_CONFIG_CACHE = '/tmp/.npm'
    }
    
    stages {
        stage('Install & Test') {
            parallel {
                stage('Install Dependencies') {
                    steps {
                        sh 'npm install'
                    }
                }
                stage('Security Audit') {
                    steps {
                        sh 'npm audit --audit-level moderate'
                    }
                }
            }
        }
        
        stage('Build & Package') {
            steps {
                sh 'npm run build'
                sh 'npm pack'
            }
        }
    }
}
```


## 4. 주요 Directive 상세 문법

### 4.1 Environment 지시문

환경 변수를 정의합니다[^8][^9]:

```groovy
pipeline {
    agent any
    
    environment {
        // 글로벌 환경 변수
        APP_NAME = 'my-node-app'
        VERSION = '1.0.0'
        DATABASE_URL = credentials('db-url')  // 자격 증명 사용
    }
    
    stages {
        stage('Deploy') {
            environment {
                // 스테이지별 환경 변수
                DEPLOY_ENV = 'production'
                API_KEY = credentials('api-key')
            }
            steps {
                sh 'echo "Deploying ${APP_NAME} version ${VERSION}"'
                sh 'echo "Environment: ${DEPLOY_ENV}"'
            }
        }
    }
}
```


### 4.2 Tools 지시문

자동으로 설치하거나 PATH에 추가할 도구를 정의합니다[^10]:

```groovy
pipeline {
    agent any
    
    tools {
        nodejs 'NodeJS-18'     // NodeJS 플러그인에서 설정한 이름
        maven 'Maven-3.8'      // Maven 도구
        jdk 'JDK-11'          // JDK 도구
    }
    
    stages {
        stage('Build') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'mvn --version'
                sh 'java --version'
            }
        }
    }
}
```


### 4.3 Parameters 지시문

사용자 입력을 받는 파라미터를 정의합니다[^4][^11]:

```groovy
pipeline {
    agent any
    
    parameters {
        string(
            name: 'BRANCH_NAME',
            defaultValue: 'main',
            description: 'Git branch to build'
        )
        choice(
            name: 'ENVIRONMENT',
            choices: ['dev', 'staging', 'production'],
            description: 'Target environment'
        )
        booleanParam(
            name: 'SKIP_TESTS',
            defaultValue: false,
            description: 'Skip test execution'
        )
        password(
            name: 'API_TOKEN',
            description: 'API token for deployment'
        )
    }
    
    stages {
        stage('Build') {
            steps {
                echo "Building branch: ${params.BRANCH_NAME}"
                echo "Target environment: ${params.ENVIRONMENT}"
                script {
                    if (!params.SKIP_TESTS) {
                        sh 'npm test'
                    }
                }
            }
        }
    }
}
```


### 4.4 Options 지시문

파이프라인 옵션을 설정합니다[^5]:

```groovy
pipeline {
    agent any
    
    options {
        timeout(time: 1, unit: 'HOURS')           // 1시간 타임아웃
        retry(3)                                  // 실패 시 3회 재시도
        skipDefaultCheckout()                     // 기본 체크아웃 건너뛰기
        buildDiscarder(                          // 빌드 기록 관리
            logRotator(
                daysToKeepStr: '30',
                numToKeepStr: '10'
            )
        )
        disableConcurrentBuilds()                // 동시 빌드 비활성화
        timestamps()                             // 로그에 타임스탬프 추가
    }
    
    stages {
        stage('Build') {
            steps {
                echo 'Building with timeout protection'
            }
        }
    }
}
```


## 5. 조건부 실행과 병렬 처리

### 5.1 When 지시문 - 조건부 실행

특정 조건에서만 stage를 실행합니다[^12]:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Deploy to Production') {
            when {
                branch 'main'  // main 브랜치에서만 실행
            }
            steps {
                sh 'npm run deploy:prod'
            }
        }
        
        stage('Run Integration Tests') {
            when {
                expression { 
                    return env.BRANCH_NAME != 'main' 
                }
            }
            steps {
                sh 'npm run test:integration'
            }
        }
        
        stage('Deploy to Staging') {
            when {
                allOf {
                    branch 'develop'
                    environment name: 'DEPLOY_STAGING', value: 'true'
                }
            }
            steps {
                sh 'npm run deploy:staging'
            }
        }
        
        stage('Feature Branch Build') {
            when {
                anyOf {
                    branch 'feature/*'
                    changeRequest()
                }
            }
            steps {
                sh 'npm run build:feature'
            }
        }
    }
}
```


### 5.2 Parallel 지시문 - 병렬 처리

여러 stage를 동시에 실행합니다[^13][^14]:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Parallel Testing') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        sh 'npm run test:unit'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        sh 'npm run test:integration'
                    }
                }
                stage('Lint Check') {
                    steps {
                        sh 'npm run lint'
                    }
                }
                stage('Security Scan') {
                    steps {
                        sh 'npm audit'
                    }
                }
            }
        }
        
        stage('Cross-Platform Build') {
            parallel {
                stage('Linux Build') {
                    agent {
                        label 'linux'
                    }
                    steps {
                        sh 'npm run build:linux'
                    }
                }
                stage('Windows Build') {
                    agent {
                        label 'windows'
                    }
                    steps {
                        bat 'npm run build:windows'
                    }
                }
            }
        }
    }
}
```


### 5.3 실패 시 빠른 중단

```groovy
pipeline {
    agent any
    
    stages {
        stage('Quality Checks') {
            parallel failFast: true {  // 하나라도 실패하면 모든 병렬 작업 중단
                stage('Test Coverage') {
                    steps {
                        sh 'npm run test:coverage'
                    }
                }
                stage('Code Quality') {
                    steps {
                        sh 'npm run sonar'
                    }
                }
                stage('Performance Test') {
                    steps {
                        sh 'npm run test:performance'
                    }
                }
            }
        }
    }
}
```


## 6. 환경 변수와 자격 증명

### 6.1 환경 변수 사용법

Jenkins는 다양한 내장 환경 변수를 제공합니다[^8][^9]:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Environment Info') {
            steps {
                echo "Job Name: ${env.JOB_NAME}"
                echo "Build Number: ${env.BUILD_NUMBER}"
                echo "Build URL: ${env.BUILD_URL}"
                echo "Workspace: ${env.WORKSPACE}"
                echo "Git Commit: ${env.GIT_COMMIT}"
                echo "Git Branch: ${env.BRANCH_NAME}"
                
                // 모든 환경 변수 출력
                sh 'printenv | sort'
            }
        }
    }
}
```


### 6.2 자격 증명 관리

Jenkins Credentials를 안전하게 사용하는 방법입니다[^15][^16]:

```groovy
pipeline {
    agent any
    
    environment {
        // credentials() 함수 사용
        DOCKER_CREDS = credentials('docker-hub-credentials')
        API_KEY = credentials('api-key-secret')
    }
    
    stages {
        stage('Login & Deploy') {
            steps {
                // Username/Password 자격 증명
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker-hub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push myapp:latest'
                }
                
                // SSH 키 사용
                withCredentials([
                    sshUserPrivateKey(
                        credentialsId: 'deployment-ssh-key',
                        keyFileVariable: 'SSH_KEY',
                        usernameVariable: 'SSH_USER'
                    )
                ]) {
                    sh '''
                        ssh -i $SSH_KEY $SSH_USER@server.com '
                            pm2 restart myapp
                        '
                    '''
                }
                
                // Secret Text 사용
                withCredentials([
                    string(
                        credentialsId: 'github-token',
                        variable: 'GITHUB_TOKEN'
                    )
                ]) {
                    sh 'gh auth login --with-token <<< $GITHUB_TOKEN'
                }
            }
        }
    }
}
```


### 6.3 동적 환경 변수 설정

```groovy
pipeline {
    agent any
    
    stages {
        stage('Set Dynamic Variables') {
            steps {
                script {
                    // 동적으로 환경 변수 설정
                    env.BUILD_VERSION = sh(
                        script: "npm version --json | jq -r '.version'",
                        returnStdout: true
                    ).trim()
                    
                    env.GIT_SHORT_COMMIT = sh(
                        script: "git rev-parse --short HEAD",
                        returnStdout: true
                    ).trim()
                    
                    env.DOCKER_TAG = "${env.BUILD_VERSION}-${env.GIT_SHORT_COMMIT}"
                }
                
                echo "Version: ${env.BUILD_VERSION}"
                echo "Docker Tag: ${env.DOCKER_TAG}"
            }
        }
    }
}
```


## 7. 에러 처리와 후처리

### 7.1 Post 지시문

파이프라인 실행 후 조건별 작업을 정의합니다[^17][^18]:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                sh 'npm run build'
            }
            post {
                success {
                    echo 'Build stage completed successfully'
                }
                failure {
                    echo 'Build stage failed'
                    archiveArtifacts artifacts: 'logs/**/*', allowEmptyArchive: true
                }
                always {
                    echo 'Build stage finished'
                }
            }
        }
    }
    
    post {
        always {
            echo 'Pipeline finished'
            cleanWs()  // 워크스페이스 정리
        }
        success {
            emailext (
                subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Good news! The build succeeded.",
                to: "${env.CHANGE_AUTHOR_EMAIL}"
            )
        }
        failure {
            emailext (
                subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
                body: "Build failed. Please check the console output.",
                to: "${env.CHANGE_AUTHOR_EMAIL}"
            )
        }
        unstable {
            echo 'Pipeline marked as unstable'
        }
        changed {
            echo 'Pipeline result changed from previous run'
        }
    }
}
```


### 7.2 CatchError - 에러 처리

특정 stage가 실패해도 파이프라인을 계속 진행합니다[^19][^20]:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Optional Tests') {
            steps {
                catchError(
                    buildResult: 'SUCCESS',
                    stageResult: 'FAILURE',
                    message: 'Tests failed but continuing pipeline'
                ) {
                    sh 'npm run test:optional'
                }
            }
        }
        
        stage('Critical Build') {
            steps {
                catchError(
                    buildResult: 'FAILURE',
                    stageResult: 'FAILURE'
                ) {
                    sh 'npm run build'
                }
            }
        }
        
        stage('Continue Anyway') {
            steps {
                echo 'This stage runs even if previous stages failed'
            }
        }
    }
}
```


### 7.3 Try-Catch 블록

더 세밀한 에러 처리가 필요한 경우:

```groovy
pipeline {
    agent any
    
    stages {
        stage('Complex Error Handling') {
            steps {
                script {
                    try {
                        sh 'npm run risky-command'
                        currentBuild.result = 'SUCCESS'
                    } catch (Exception e) {
                        echo "Error occurred: ${e.getMessage()}"
                        
                        // 에러 유형별 처리
                        if (e.getMessage().contains('timeout')) {
                            echo 'Handling timeout error'
                            currentBuild.result = 'UNSTABLE'
                        } else {
                            echo 'Handling general error'
                            currentBuild.result = 'FAILURE'
                        }
                        
                        // 에러 정보 저장
                        writeFile file: 'error.log', text: e.getMessage()
                        archiveArtifacts artifacts: 'error.log'
                    } finally {
                        echo 'Cleanup operations'
                        sh 'npm run cleanup'
                    }
                }
            }
        }
    }
}
```


## 8. Docker 통합

### 8.1 Docker Agent 사용

Docker 컨테이너에서 파이프라인 실행[^21][^22]:

```groovy
pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            label 'docker'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    
    stages {
        stage('Build in Container') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'npm install'
                sh 'npm run build'
            }
        }
    }
}
```


### 8.2 다단계 Docker 빌드

```groovy
pipeline {
    agent any
    
    environment {
        DOCKER_REGISTRY = 'your-registry.com'
        IMAGE_NAME = 'myapp'
        DOCKER_BUILDKIT = '1'
    }
    
    stages {
        stage('Build Application') {
            agent {
                docker {
                    image 'node:18'
                    args '-v /tmp/.npm:/root/.npm'
                }
            }
            steps {
                sh 'npm ci --only=production'
                sh 'npm run build'
                stash includes: 'dist/**/*,package*.json', name: 'built-app'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                unstash 'built-app'
                script {
                    def image = docker.build("${IMAGE_NAME}:${env.BUILD_NUMBER}")
                    docker.withRegistry("https://${DOCKER_REGISTRY}", 'docker-registry-credentials') {
                        image.push()
                        image.push("latest")
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                sh """
                    docker run -d --name myapp-${env.BUILD_NUMBER} \
                        -p 3000:3000 \
                        ${DOCKER_REGISTRY}/${IMAGE_NAME}:${env.BUILD_NUMBER}
                """
            }
        }
    }
}
```


### 8.3 Multi-stage Dockerfile 활용

```groovy
// Dockerfile
/*
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine AS runtime
WORKDIR /app
COPY --from=builder /app/node_modules ./node_modules
COPY . .
EXPOSE 3000
CMD ["npm", "start"]
*/

pipeline {
    agent any
    
    stages {
        stage('Build Multi-stage Image') {
            steps {
                script {
                    def image = docker.build(
                        "myapp:${env.BUILD_NUMBER}",
                        "--target runtime ."
                    )
                    
                    // 이미지 취약점 스캔
                    sh "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock \
                        aquasec/trivy image myapp:${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
```


## 9. 모범 사례와 보안

### 9.1 보안 모범 사례

Jenkins 파이프라인 보안을 위한 핵심 사항들입니다[^23][^24]:

```groovy
pipeline {
    agent {
        label 'restricted-agent'  // 제한된 agent 사용
    }
    
    options {
        skipDefaultCheckout()     // 불필요한 체크아웃 방지
        timeout(time: 30, unit: 'MINUTES')  // 타임아웃 설정
    }
    
    environment {
        // 민감한 정보는 credentials() 함수 사용
        DB_PASSWORD = credentials('database-password')
        API_KEY = credentials('api-key')
    }
    
    stages {
        stage('Secure Checkout') {
            steps {
                // 특정 브랜치와 깊이 제한
                git branch: env.BRANCH_NAME,
                    url: 'https://github.com/company/secure-repo.git',
                    credentialsId: 'github-token',
                    depth: 1
            }
        }
        
        stage('Security Scans') {
            parallel {
                stage('Dependency Check') {
                    steps {
                        sh 'npm audit --audit-level moderate'
                        sh 'npm run security:scan'
                    }
                }
                stage('Code Security Analysis') {
                    steps {
                        sh 'npm run lint:security'
                        // SonarQube 보안 분석
                        withSonarQubeEnv('SonarQube') {
                            sh 'sonar-scanner'
                        }
                    }
                }
            }
        }
        
        stage('Secure Build') {
            steps {
                // 빌드 중 네트워크 접근 제한
                sh '''
                    export NODE_ENV=production
                    npm ci --only=production
                    npm run build
                '''
            }
        }
    }
    
    post {
        always {
            // 민감한 파일 정리
            sh 'rm -f .env .env.local'
            cleanWs()
        }
        failure {
            // 보안 관련 실패 시 알림
            emailext (
                subject: "Security Issue in ${env.JOB_NAME}",
                body: "Security scan failed. Please review immediately.",
                to: "security-team@company.com"
            )
        }
    }
}
```


### 9.2 성능 최적화

```groovy
pipeline {
    agent any
    
    options {
        // 빌드 기록 관리로 디스크 공간 절약
        buildDiscarder(logRotator(
            numToKeepStr: '10',
            daysToKeepStr: '30'
        ))
        // 동시 빌드 제한
        disableConcurrentBuilds()
    }
    
    stages {
        stage('Optimized Build') {
            steps {
                // npm 캐시 활용
                sh '''
                    export NPM_CACHE_DIR=/tmp/npm-cache-${BUILD_NUMBER}
                    mkdir -p $NPM_CACHE_DIR
                    npm ci --cache $NPM_CACHE_DIR
                    npm run build
                '''
                
                // 병렬 처리로 시간 단축
                parallel (
                    "Unit Tests": {
                        sh 'npm run test:unit'
                    },
                    "Lint Check": {
                        sh 'npm run lint'
                    },
                    "Type Check": {
                        sh 'npm run type-check'
                    }
                )
            }
        }
    }
    
    post {
        always {
            // 캐시 정리
            sh 'rm -rf /tmp/npm-cache-${BUILD_NUMBER}'
        }
    }
}
```


### 9.3 코드 품질 체크

```groovy
pipeline {
    agent any
    
    stages {
        stage('Quality Gates') {
            steps {
                script {
                    // 테스트 커버리지 체크
                    sh 'npm run test:coverage'
                    def coverage = readFile('coverage/coverage-summary.json')
                    def coverageJson = readJSON text: coverage
                    def linesCoverage = coverageJson.total.lines.pct
                    
                    if (linesCoverage < 80) {
                        error("Code coverage ${linesCoverage}% is below threshold 80%")
                    }
                    
                    echo "Code coverage: ${linesCoverage}%"
                }
                
                // 복잡도 분석
                sh 'npm run complexity:check'
                
                // 보안 취약점 검사
                sh 'npm audit --level moderate'
            }
            post {
                always {
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'coverage/lcov-report',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
    }
}
```


## 10. Shared Library 활용

### 10.1 Shared Library 기본 구조

Jenkins Shared Library는 공통 코드를 재사용하기 위한 강력한 도구입니다[^25][^26]:

```groovy
// vars/buildNodeApp.groovy
def call(Map config) {
    pipeline {
        agent any
        
        tools {
            nodejs config.nodeVersion ?: 'NodeJS-18'
        }
        
        stages {
            stage('Checkout') {
                steps {
                    git branch: config.branch ?: 'main',
                        url: config.repoUrl
                }
            }
            
            stage('Install Dependencies') {
                steps {
                    sh 'npm ci'
                }
            }
            
            stage('Test') {
                when {
                    expression { config.skipTests != true }
                }
                steps {
                    sh 'npm test'
                }
                post {
                    always {
                        publishTestResults testResultsPattern: 'test-results.xml'
                    }
                }
            }
            
            stage('Build') {
                steps {
                    sh 'npm run build'
                }
            }
            
            stage('Deploy') {
                when {
                    expression { config.deploy == true }
                }
                steps {
                    deployApp(config.deployTarget)
                }
            }
        }
        
        post {
            always {
                cleanWs()
            }
            success {
                notifySuccess(config.notificationChannel)
            }
            failure {
                notifyFailure(config.notificationChannel)
            }
        }
    }
}
```


### 10.2 Shared Library 사용 예제

```groovy
// Jenkinsfile
@Library('company-shared-library@v1.0') _

// 간단한 사용법
buildNodeApp([
    repoUrl: 'https://github.com/company/my-app.git',
    branch: 'develop',
    nodeVersion: 'NodeJS-18',
    skipTests: false,
    deploy: true,
    deployTarget: 'staging',
    notificationChannel: '#deployments'
])
```


### 10.3 고급 Shared Library 함수

```groovy
// vars/deployToKubernetes.groovy
def call(String environment, Map config = [:]) {
    def namespace = config.namespace ?: environment
    def image = config.image ?: "${env.IMAGE_NAME}:${env.BUILD_NUMBER}"
    def replicas = config.replicas ?: 3
    
    script {
        withKubeConfig([credentialsId: "k8s-${environment}"]) {
            // Deployment 업데이트
            sh """
                kubectl set image deployment/${config.appName} \
                    ${config.appName}=${image} \
                    --namespace=${namespace}
            """
            
            // Rollout 상태 확인
            sh """
                kubectl rollout status deployment/${config.appName} \
                    --namespace=${namespace} \
                    --timeout=300s
            """
            
            // 헬스 체크
            def healthCheck = sh(
                script: """
                    kubectl get pods -l app=${config.appName} \
                        --namespace=${namespace} \
                        --field-selector=status.phase=Running \
                        --no-headers | wc -l
                """,
                returnStdout: true
            ).trim() as Integer
            
            if (healthCheck < replicas) {
                error("Deployment failed: only ${healthCheck}/${replicas} pods are running")
            }
            
            echo "Successfully deployed ${config.appName} to ${environment}"
        }
    }
}

// vars/notifySlack.groovy
def call(String message, String color = 'good') {
    slackSend(
        channel: '#deployments',
        color: color,
        message: """
            *${env.JOB_NAME}* - Build #${env.BUILD_NUMBER}
            ${message}
            
            <${env.BUILD_URL}|View Build> | <${env.GIT_URL}|Repository>
        """
    )
}
```


### 10.4 실제 프로젝트에서 Shared Library 활용

```groovy
// Jenkinsfile
@Library('company-pipeline-library@main') _

pipeline {
    agent any
    
    environment {
        APP_NAME = 'my-node-api'
        DOCKER_REGISTRY = 'registry.company.com'
    }
    
    stages {
        stage('Build & Test') {
            steps {
                // Shared Library 함수 사용
                buildNodeApplication([
                    nodeVersion: '18',
                    testCommand: 'npm run test:ci',
                    buildCommand: 'npm run build:prod'
                ])
            }
        }
        
        stage('Docker Build') {
            steps {
                script {
                    def image = buildDockerImage([
                        imageName: "${DOCKER_REGISTRY}/${APP_NAME}",
                        tag: env.BUILD_NUMBER,
                        dockerfile: 'Dockerfile.prod'
                    ])
                    
                    // 이미지 보안 스캔
                    scanDockerImage(image)
                    
                    // 레지스트리에 푸시
                    pushDockerImage(image, 'docker-registry-creds')
                }
            }
        }
        
        stage('Deploy') {
            parallel {
                stage('Deploy to Staging') {
                    when {
                        branch 'develop'
                    }
                    steps {
                        deployToKubernetes('staging', [
                            appName: env.APP_NAME,
                            image: "${DOCKER_REGISTRY}/${APP_NAME}:${env.BUILD_NUMBER}",
                            namespace: 'staging',
                            replicas: 2
                        ])
                    }
                }
                
                stage('Deploy to Production') {
                    when {
                        branch 'main'
                    }
                    steps {
                        // 승인 프로세스
                        input message: 'Deploy to production?', 
                              submitter: 'admin,deploy-team'
                        
                        deployToKubernetes('production', [
                            appName: env.APP_NAME,
                            image: "${DOCKER_REGISTRY}/${APP_NAME}:${env.BUILD_NUMBER}",
                            namespace: 'production',
                            replicas: 5
                        ])
                    }
                }
            }
        }
    }
    
    post {
        success {
            notifySlack("✅ Deployment successful!", 'good')
        }
        failure {
            notifySlack("❌ Deployment failed!", 'danger')
        }
        always {
            archiveArtifacts artifacts: 'logs/**/*', allowEmptyArchive: true
            cleanWs()
        }
    }
}
```

이 핸드북은 Jenkins Pipeline의 실제 문법과 사용법을 Node.js 프로젝트를 중심으로 종합적으로 다뤘습니다. 기본 구조부터 고급 기능인 Shared Library까지, 실무에서 바로 활용할 수 있는 실용적인 예제들을 포함하고 있습니다. 각 섹션의 코드 예제들을 참고하여 프로젝트에 맞는 파이프라인을 구성해보시기 바랍니다.

<div style="text-align: center">⁂</div>

[^1]: https://www.jenkins.io/doc/book/pipeline/getting-started/

[^2]: https://www.jenkins.io/doc/book/pipeline/syntax/

[^3]: https://www.blazemeter.com/blog/jenkins-declarative-pipeline

[^4]: https://www.lambdatest.com/blog/jenkins-declarative-pipeline-examples/

[^5]: https://waspro.tistory.com/554

[^6]: https://cloud-centric.hashnode.dev/jenkins-pipeline-for-automating-the-deployment-of-a-nodejs-application

[^7]: https://www.linkedin.com/pulse/cicd-pipeline-nodejs-jenkins-docker-windows-linux-gantyada-nx6wc

[^8]: https://spacelift.io/blog/jenkins-environment-variables

[^9]: https://every-up.tistory.com/23

[^10]: https://www.youtube.com/watch?v=HaGeSq-SB9E

[^11]: https://devopscube.com/declarative-pipeline-parameters/

[^12]: https://stackoverflow.com/questions/37690920/conditional-step-stage-in-jenkins-pipeline

[^13]: https://www.baeldung.com/ops/running-stages-in-parallel-jenkins-workflow-pipeline

[^14]: https://dev.to/vandana_babshetti_91df8eb/parallel-stages-with-declarative-pipeline-in-jenkins-cicd-11fk

[^15]: https://stackoverflow.com/questions/64875989/how-to-get-a-jenkins-credential-in-a-pipeline-based-on-username-part-of-the-desi

[^16]: https://passwd.tistory.com/entry/Jenkins-Pipeline-Credentials-사용-2

[^17]: https://kiranpawar.hashnode.dev/harnessing-post-build-actions-in-jenkins-pipelines

[^18]: https://docs.cloudbees.com/docs/cloudbees-ci/latest/pipeline-syntax-reference-guide/declarative-pipeline

[^19]: https://ynlee1.github.io/jenkins/jenkins-catch-error/

[^20]: https://stackoverflow.com/questions/66705021/jenkins-catch-error-to-skip-to-next-stage-but-still-show-errored-stage-has-fai

[^21]: https://www.jenkins.io/doc/book/pipeline/docker/

[^22]: https://weezip.treefeely.com/post/building-with-docker-for-jenkins-pipeline

[^23]: https://cycode.com/blog/jenkins-security-best-practices/

[^24]: https://www.geeksforgeeks.org/devops/jenkins-security-best-practices/

[^25]: https://tomd.xyz/jenkins-shared-library/

[^26]: https://www.jenkins.io/doc/book/pipeline/shared-libraries/

[^27]: https://dev.to/msfaizi/how-to-write-jenkinsfile-a-comprehensive-guide-for-beginners-58d2

[^28]: https://devopscube.com/jenkins-pipeline-as-code/

[^29]: https://www.jenkins.io/doc/book/pipeline/jenkinsfile/

[^30]: https://www.c-sharpcorner.com/article/an-in-depth-guide-to-jenkinsfile-syntax/

[^31]: https://dzone.com/refcardz/declarative-pipeline-with-jenkins

[^32]: https://tg360.tistory.com/entry/Jenkins-Declarative-입문하기

[^33]: https://www.youtube.com/watch?v=8jd3G07yGrI

[^34]: https://codefresh.io/learn/jenkins/jenkins-pipeline-examples-usage-and-best-practices/

[^35]: https://www.browserstack.com/guide/jenkins-file

[^36]: https://blog.voidmainvoid.net/104

[^37]: https://github.com/jfrog/project-examples/blob/master/jenkins-examples/pipeline-examples/declarative-examples/README.md

[^38]: https://www.jenkins.io/doc/book/pipeline/

[^39]: https://www.youtube.com/watch?v=7KCS70sCoK0

[^40]: https://octopus.com/devops/jenkins/jenkins-pipeline/

[^41]: https://plugins.jenkins.io/nodejs/

[^42]: https://github.com/MansoorMajeed/devops-from-scratch/blob/master/episodes/27-create-real-life-end-to-end-jenkins-pipeline.md

[^43]: https://enumclass.tistory.com/19

[^44]: https://aws.plainenglish.io/building-a-ci-cd-pipeline-for-node-js-application-with-jenkins-and-docker-23dafcee93a7

[^45]: https://www.jenkins.io/doc/tutorials/build-a-node-js-and-react-app-with-npm/

[^46]: https://sg-choi.tistory.com/213

[^47]: https://blog.devops.dev/setting-up-jenkins-pipeline-for-a-node-js-project-with-docker-sonarqube-and-docker-compose-on-aws-7f2f6f516c66

[^48]: https://www.couchbase.com/blog/enable-continuous-deployment-nodejs-jenkins/

[^49]: https://deep-dive-dev.tistory.com/58

[^50]: https://withhamit.tistory.com/285

[^51]: https://velog.io/@wonn23/Jenkins-활용-프로젝트

[^52]: https://anasmansuri.hashnode.dev/deploy-nodejs-app-with-docker-and-jenkins-a-complete-tutorial

[^53]: https://velog.io/@snghyun331/Docker-Docker와-Jenkins로-NodeJs-프로젝트의-CICD-Pipeline-구축하기-with-AWS-EC2-Ubuntu-2

[^54]: https://dev.to/sharker3312/automating-cicd-setting-up-a-nodejs-pipeline-with-jenkins-docker-and-aws-1jb5

[^55]: https://www.jenkins.io/doc/pipeline/tour/hello-world/

[^56]: https://jeinie-developer.tistory.com/21

[^57]: https://stackoverflow.com/questions/78294340/how-can-i-async-jenkins-parallel-stages

[^58]: https://stackoverflow.com/questions/76980696/jenkins-declarative-pipeline-stage-post-action-goes-to-failure-block-if-previou

[^59]: https://community.jenkins.io/t/different-post-actions-being-called-at-the-same-time/14947

[^60]: https://yeonyeon.tistory.com/90

[^61]: https://code-maven.com/jenkins-pipeline-parallel-stages

[^62]: https://www.youtube.com/watch?v=XM0nuRJMglI

[^63]: https://www.jenkins.io/blog/2017/09/25/declarative-1/

[^64]: https://itnext.io/jenkins-tutorial-part-4-post-actions-e5d0ef1e3c39

[^65]: https://www.incredibuild.com/blog/jenkins-parallel-builds-jenkins-distributed-builds

[^66]: https://stackoverflow.com/questions/45136379/how-to-retrieve-build-job-status-success-error-for-using-in-post-build-steps-i/45145796

[^67]: https://velog.io/@cyeongy/jenkins-parallel-문법

[^68]: https://www.youtube.com/watch?v=AsQhJiDvcp4

[^69]: https://blog.dineshcloud.in/step-by-step-java-cicd-pipeline-setup-using-jenkins-maven-and-docker

[^70]: https://www.youtube.com/watch?v=tLR3VKlMwFI

[^71]: https://configu.com/blog/jenkins-environment-variables-practical-guide-with-code-examples/

[^72]: https://stackoverflow.com/questions/53519831/docker-jenkins-automatically-installed-maven-open-java-not-working-together

[^73]: https://passwd.tistory.com/entry/Jenkins-Pipeline-Credentials-사용-1

[^74]: https://stackoverflow.com/questions/69092116/how-to-set-an-environment-variable-from-jenkinsfile

[^75]: https://forums.docker.com/t/dead-stuck-jenkins-is-not-able-to-read-the-correct-path-to-the-docker/140203

[^76]: https://www.jenkins.io/doc/book/using/using-credentials/

[^77]: https://www.youtube.com/watch?v=qz9I3v0PDeY

[^78]: https://phoenixnap.com/kb/jenkins-environment-variables

[^79]: https://www.jenkins.io/doc/tutorials/build-a-java-app-with-maven/

[^80]: https://dev.to/soumya14041987/devsecops-fundamentals-security-in-the-jenkins-pipeline-20n

[^81]: https://stackoverflow.com/questions/73236129/how-to-add-error-handling-code-to-catcherror-in-jenkins/73236130

[^82]: https://github.com/GSA/jenkins-shared-library-examples

[^83]: https://www.jenkins.io/doc/book/security/

[^84]: https://shenxianpeng.github.io/2023/12/jenkins-catch-error/

[^85]: https://www.youtube.com/watch?v=BLQ0PDjgN8w

[^86]: https://www.jenkins.io/doc/book/pipeline/pipeline-best-practices/

[^87]: https://www.youtube.com/watch?v=u-1lyoas3ys

[^88]: https://bgradecoding.tistory.com/33

[^89]: https://www.aquasec.com/cloud-native-academy/supply-chain-security/jenkins-security/

[^90]: https://www.jenkins.io/doc/pipeline/steps/workflow-basic-steps/

[^91]: https://www.tutorialworks.com/jenkins-shared-library/

[^92]: https://www.addwebsolution.com/blog/jenkins-pipeline-comprehensive-guide

[^93]: https://ford.tistory.com/61

