---
publish: true
---

'깃 내부 구조'에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘. 개발자용

# Git 내부 구조 핸드북: 개발자를 위한 완전 분석 가이드

## 개요

Git의 내부 구조는 현대 소프트웨어 개발의 핵심을 이루는 정교한 시스템입니다. 이 핸드북은 Git이 어떻게 데이터를 저장하고 관리하는지, 왜 이러한 구조를 선택했는지, 그리고 개발자가 이를 어떻게 활용할 수 있는지를 심층적으로 다룹니다. Git의 내부 동작 원리를 이해함으로써 버전 관리 시스템의 본질을 파악하고, 더 효과적인 Git 사용법을 습득할 수 있습니다.

## 1. Git 내부 구조의 철학과 설계 원칙

### 분산 시스템으로서의 Git

Git은 **분산 버전 관리 시스템(DVCS)**으로 설계되었습니다[^1]. 리누스 토르발즈가 2005년 개발한 Git의 핵심 설계 목표는 다음과 같습니다[^1]:

- **속도**: 대용량 저장소와 수많은 커밋을 효율적으로 처리
- **분산 아키텍처**: 개발자마다 완전한 저장소 복사본 보유
- **데이터 무결성**: 암호학적 해시안 보장
- **비선형 개발 지원**: 복잡한 브랜치와 병합 워크플로우 처리


### 콘텐츠 주소 지정 시스템

Git의 가장 중요한 특징 중 하나는 **콘텐츠 주소 지정(Content-Addressable) 시스템**입니다[^2]. 모든 객체는 SHA-1 해시 값으로 식별되며, 이는 데이터 무결성과 중복 제거를 동시에 보장합니다[^2].

## 2. .git 디렉토리의 완전 구조 분석

### 디렉토리 구조 개요

Git 저장소의 핵심인 `.git` 디렉토리는 다음과 같이 구성됩니다[^3][^4]:

```
.git/
├── objects/              # Git 객체 데이터베이스
│   ├── [0-9a-f][0-9a-f]/ # SHA-1 해시로 분류된 객체들
│   ├── pack/             # 압축된 packfile들
│   └── info/             # 객체 메타데이터
├── refs/                 # 참조 정보 저장
│   ├── heads/            # 로컬 브랜치 참조
│   ├── tags/             # 태그 참조
│   └── remotes/          # 원격 저장소 참조
├── logs/                 # Reflog 데이터
│   ├── HEAD              # HEAD 이동 기록
│   └── refs/             # 브랜치별 이동 기록
├── hooks/                # Git 훅 스크립트들
├── HEAD                  # 현재 브랜치 포인터
├── index                 # 스테이징 영역 데이터
├── config                # 저장소별 설정
├── description           # 저장소 설명
├── packed-refs           # 압축된 참조들
└── info/
    └── exclude           # 로컬 gitignore
```


### 핵심 구성 요소 상세 분석

#### Objects 디렉토리: Git의 심장부

**Objects 디렉토리**는 Git의 핵심 데이터베이스입니다[^5]. 모든 Git 객체가 여기에 저장되며, SHA-1 해시의 첫 2자리를 디렉토리명으로, 나머지 38자리를 파일명으로 사용합니다[^3][^5].

**구조적 특징:**

- **분산 저장**: 256개의 하위 디렉토리로 객체를 분산하여 파일 시스템 성능 최적화[^3]
- **압축 저장**: zlib 압축을 통한 공간 효율성[^6]
- **내용 기반 중복 제거**: 동일한 내용은 하나의 객체로만 저장[^7]


#### Refs 디렉토리: 참조 관리 시스템

**Refs 디렉토리**는 Git의 참조 시스템을 구현합니다[^8][^5]:

- **heads/**: 로컬 브랜치들이 가리키는 커밋 해시 저장
- **tags/**: 태그가 가리키는 객체 해시 저장
- **remotes/**: 원격 저장소의 브랜치 상태 추적

각 참조 파일은 단순히 40자리 SHA-1 해시를 텍스트로 저장합니다[^8].

#### Index 파일: 스테이징 영역의 실체

**Index 파일**은 Git의 스테이징 영역을 구현하는 바이너리 파일입니다[^9]. 이 파일에는 다음 정보가 포함됩니다:

- 파일 경로와 모드 정보
- SHA-1 해시 값
- 파일 크기와 타임스탬프
- 스테이지 상태 정보


## 3. Git 객체 모델: 4가지 핵심 객체

### Blob 객체: 파일 내용의 순수 저장소

**Blob(Binary Large Object)**은 파일의 내용만을 저장하는 객체입니다[^7][^10]:

**특징:**

- **메타데이터 없음**: 파일명, 권한, 경로 정보는 저장하지 않음[^7]
- **내용 기반 식별**: 동일한 내용의 파일은 같은 Blob 객체 공유[^7]
- **형식 무관성**: 텍스트, 이미지, 바이너리 파일 모두 동일하게 처리[^7]

```bash
# Blob 객체 생성 예시
echo "Hello, Git!" | git hash-object -w --stdin
# 출력: a0423896973644771497bdc03eb99d5281615b51
```


### Tree 객체: 디렉토리 구조의 스냅샷

**Tree 객체**는 디렉토리 구조를 표현합니다[^7][^11]:

**구성 요소:**

- **파일 모드**: 100644(일반 파일), 100755(실행 파일), 040000(디렉토리)[^5]
- **객체 타입**: blob 또는 tree
- **SHA-1 해시**: 참조하는 객체의 식별자
- **파일/디렉토리명**: 실제 이름 정보

```bash
# Tree 객체 내용 조회
git cat-file -p <tree-hash>
# 100644 blob a0423896... README.md  
# 040000 tree 5a4d8e2f... src/
```


### Commit 객체: 프로젝트 상태의 스냅샷

**Commit 객체**는 특정 시점의 프로젝트 전체 상태를 나타냅니다[^7][^10]:

**포함 정보:**

- **Tree 참조**: 해당 커밋 시점의 루트 트리 객체
- **부모 커밋**: 이전 커밋들의 SHA-1 해시
- **메타데이터**: 작성자, 커미터, 타임스탬프
- **커밋 메시지**: 변경사항에 대한 설명

```bash
# Commit 객체 구조
git cat-file -p HEAD
# tree 5a4d8e2f...
# parent 3c5a9d4b...
# author John Doe <john@example.com> 1640995200 +0900
# committer John Doe <john@example.com> 1640995200 +0900
#
# Initial commit
```


### Tag 객체: 특별한 참조점

**Tag 객체**는 특정 커밋을 영구적으로 표시하는 객체입니다[^7]:

**종류:**

- **Lightweight 태그**: 단순한 커밋 참조
- **Annotated 태그**: 메타데이터가 포함된 객체


## 4. Git의 3-Tree 아키텍처

### 아키텍처 개념

Git의 **3-Tree 아키텍처**는 버전 관리의 핵심을 이룹니다[^12][^13][^14]:

1. **Working Directory**: 실제 파일들이 존재하는 작업 공간
2. **Index (Staging Area)**: 다음 커밋을 준비하는 중간 영역
3. **HEAD**: 현재 브랜치가 가리키는 커밋

### 각 트리의 역할과 상호작용

#### Working Directory (작업 디렉토리)

- **역할**: 파일 편집, 생성, 삭제가 일어나는 실제 작업 공간[^14]
- **특징**: 압축되지 않은 형태로 직접 접근 가능[^14]
- **상태**: Git이 추적하지 않는 변경사항 포함 가능


#### Index (스테이징 영역)

- **역할**: 다음 커밋에 포함될 변경사항들을 임시 저장[^15][^9]
- **구현**: `.git/index` 파일에 바이너리 형태로 저장[^9]
- **기능**: 선택적 스테이징을 통한 논리적 커밋 구성[^16]


#### HEAD (현재 커밋)

- **역할**: 현재 체크아웃된 커밋을 가리키는 참조[^17]
- **내용**: 일반적으로 `ref: refs/heads/branch-name` 형태[^8]
- **변화**: 커밋, 체크아웃, 리셋 시 자동 업데이트


### 워크플로우 예시

```bash
# 1. 파일 생성 (Working Directory)
echo "Hello" > file.txt

# 2. 스테이징 (Working Directory → Index)  
git add file.txt

# 3. 커밋 (Index → HEAD)
git commit -m "Add file.txt"

# 각 단계에서의 상태 확인
git status           # 트리 간 차이점 표시
git diff             # Working Directory vs Index
git diff --staged    # Index vs HEAD
git diff HEAD        # Working Directory vs HEAD
```


## 5. SHA-1 해시 시스템과 보안

### SHA-1의 역할과 한계

Git은 모든 객체를 **SHA-1 해시**로 식별합니다[^2]. 하지만 2017년 SHA-1 충돌 공격이 성공하면서 보안 우려가 제기되었습니다[^18].

**충돌 공격의 영향:**

- **실제 위험성**: 현재까지 Git에 직접적인 영향은 없음[^19]
- **방어 조치**: GitHub은 SHA-1DC를 통한 충돌 탐지 구현[^18]
- **미래 대응**: SHA-256으로의 전환 작업 진행 중[^20]


### SHA-256 전환 계획

Git은 SHA-256으로의 전환을 준비하고 있습니다[^20][^21][^22]:

**현재 상태:**

- Git 2.42부터 SHA-256 저장소 생성 시 경고 완화[^20]
- 로컬 작업은 대부분 정상 동작[^23]
- 원격 저장소 호환성은 아직 제한적[^23]

**전환 과정의 과제:**

- SHA-1과 SHA-256 저장소 간 상호 운용성 부족[^23]
- 주요 Git 호스팅 서비스(GitHub, GitLab)의 지원 필요[^22]
- 기존 도구들의 호환성 문제 해결 필요


## 6. 압축과 최적화: Packfile 시스템

### Packfile의 필요성

Git의 초기 저장 방식은 **loose objects**로, 각 객체를 개별 파일로 저장합니다[^24]. 하지만 저장소가 커질수록 다음 문제들이 발생합니다:

- 파일 시스템의 inodes 부족
- 네트워크 전송 시 비효율성
- 디스크 공간 낭비


### Delta 압축 알고리즘

Git은 **Delta 압축**을 통해 저장 효율성을 크게 향상시킵니다[^25][^26]:

**작동 원리:**

1. **기준 객체 선택**: 원본 파일을 기준으로 설정
2. **차이점 계산**: 새 버전과의 차이점만 저장
3. **명령어 생성**: Copy와 Insert 명령어로 복원 방법 기록

**압축 효과:**

- 100회 업데이트 시: 스냅샷 방식 1GB vs Delta 방식 ~10MB[^25]
- 텍스트 파일에서 특히 높은 압축률 달성[^25]


### Packfile 구조

**Packfile**은 여러 객체를 하나로 압축한 파일입니다[^26]:

```
.git/objects/pack/
├── pack-<hash>.pack    # 실제 압축된 객체들
└── pack-<hash>.idx     # 빠른 검색을 위한 인덱스
```

**최적화 과정:**

```bash
# 수동 가비지 컬렉션
git gc                  # 기본 최적화
git gc --aggressive     # 더 적극적인 압축
git repack -a -d       # 모든 객체 재압축
```


## 7. 참조 시스템과 Reflog

### 참조(References)의 종류

Git의 참조 시스템은 SHA-1 해시를 인간이 읽기 쉬운 이름으로 매핑합니다[^27][^28]:

**브랜치 참조:**

- **로컬 브랜치**: `.git/refs/heads/`에 저장
- **원격 브랜치**: `.git/refs/remotes/`에 저장
- **추적 관계**: 로컬과 원격 브랜치 간 연결 정보

**특수 참조:**

- **HEAD**: 현재 체크아웃된 브랜치/커밋
- **ORIG_HEAD**: 위험한 작업 전의 HEAD 상태
- **MERGE_HEAD**: 병합 중인 브랜치의 HEAD


### Reflog: Git의 안전망

**Reflog(Reference Log)**는 참조의 변경 이력을 기록하는 Git의 안전 장치입니다[^27][^29][^30]:

**저장 위치:**

- `.git/logs/HEAD`: HEAD의 모든 이동 기록
- `.git/logs/refs/heads/<branch>`: 각 브랜치별 기록
- `.git/logs/refs/stash`: stash 작업 기록

**주요 활용:**

```bash
# Reflog 조회
git reflog                    # HEAD 기록
git reflog show main          # main 브랜치 기록

# 손실된 커밋 복구
git reflog                    # 손실 지점 찾기
git checkout HEAD@{2}         # 특정 시점으로 이동
git branch recovery HEAD@{2}  # 복구 브랜치 생성
```

**보존 기간:**

- **도달 가능한 항목**: 기본 90일[^27]
- **도달 불가능한 항목**: 기본 30일[^27]
- **설정 변경**: `gc.reflogExpire` 옵션으로 조정 가능


## 8. 스테이징 영역(Index)의 심층 구조

### Index 파일의 내부 구조

**.git/index** 파일은 바이너리 형태로 스테이징 정보를 저장합니다[^9]:

**저장 내용:**

- **파일 경로**: 상대 경로 정보
- **파일 모드**: 권한 및 파일 타입
- **SHA-1 해시**: 파일 내용의 해시값
- **파일 크기**: 바이트 단위 크기
- **타임스탬프**: mtime과 ctime
- **스테이지 번호**: 충돌 해결 시 사용 (0-3)


### 스테이징의 고급 활용

**부분 스테이징:**

```bash
git add -p file.txt    # 대화형 부분 스테이징
git add -e file.txt    # 에디터를 통한 정밀 스테이징
```

**스테이지 상태 조회:**

```bash
git ls-files --stage   # 인덱스 내용 상세 보기
git status --porcelain # 스크립트 친화적 상태 출력
```


### Index와 성능 최적화

Git은 Index를 통해 성능을 크게 향상시킵니다:

- **빠른 상태 확인**: 타임스탬프 비교로 변경 감지
- **선택적 커밋**: 원하는 변경사항만 포함 가능
- **충돌 해결**: 3-way 병합 시 중간 상태 관리


## 9. 가비지 컬렉션과 저장소 최적화

### 가비지 컬렉션의 필요성

Git 저장소는 시간이 지나면서 다양한 "가비지"를 축적합니다[^31]:

- **분리된 커밋**: 히스토리 수정으로 접근 불가능해진 커밋
- **중복 객체**: 압축되지 않은 loose objects
- **만료된 reflog**: 오래된 참조 기록


### GC 작업의 단계별 과정

**자동 GC 조건:**

```bash
# 기본 임계값 확인
git config gc.auto          # loose objects 개수 (기본: 6700개)
git config gc.autopacklimit # packfile 개수 (기본: 50개)
```

**수동 GC 실행:**

```bash
git gc                    # 기본 가비지 컬렉션
git gc --aggressive       # 더 적극적인 최적화
git gc --prune=now        # 즉시 정리
```

**GC 과정의 상세 단계:**

1. **Reflog 정리**: 만료된 항목 제거
2. **객체 압축**: loose objects를 packfile로 변환
3. **참조 압축**: `.git/packed-refs` 파일 생성
4. **불가접근 객체 제거**: prune 작업 수행

### 성능 모니터링

```bash
# 저장소 상태 확인
git count-objects -v      # 객체 통계
du -sh .git/              # 디스크 사용량
git fsck --full           # 무결성 검사
```


## 10. 훅(Hooks) 시스템의 구조와 활용

### 훅 시스템의 아키텍처

Git 훅은 특정 Git 이벤트 발생 시 자동으로 실행되는 스크립트입니다[^32]:

**저장 위치:**

- **기본 경로**: `.git/hooks/`
- **사용자 정의**: `core.hooksPath` 설정으로 변경 가능

**실행 환경:**

- **작업 디렉토리**: 저장소 루트 또는 `.git` 디렉토리[^33]
- **환경 변수**: `GIT_DIR`, `GIT_WORK_TREE` 등 자동 설정[^32]


### 주요 훅의 종류와 활용

**클라이언트 사이드 훅:**

- **pre-commit**: 커밋 전 코드 품질 검사
- **prepare-commit-msg**: 커밋 메시지 템플릿 생성
- **commit-msg**: 커밋 메시지 형식 검증
- **post-commit**: 커밋 후 알림이나 배포 작업

**서버 사이드 훅:**

- **pre-receive**: 푸시된 내용 전체 검증
- **update**: 각 참조별 개별 검증
- **post-receive**: 푸시 완료 후 작업 (배포, 알림 등)


### 실용적인 훅 예시

**pre-commit 훅 (코드 품질 검사):**

```bash
#!/bin/sh
# .git/hooks/pre-commit

# ESLint 검사
npm run lint || exit 1

# 테스트 실행
npm test || exit 1

# 커밋 메시지 제한
if git diff --cached --name-only | grep -q "\.js$"; then
    echo "JavaScript files detected, running additional checks..."
fi
```


## 11. Git 내부 구조 디버깅과 문제 해결

### 저수준 명령어 활용

**객체 검사:**

```bash
git cat-file -t <hash>    # 객체 타입 확인
git cat-file -s <hash>    # 객체 크기 확인  
git cat-file -p <hash>    # 객체 내용 출력
```

**저장소 구조 분석:**

```bash
git ls-tree -r HEAD       # 전체 트리 구조 출력
git rev-list --objects --all  # 모든 객체 나열
git show-branch --all     # 브랜치 관계 시각화
```

**문제 진단:**

```bash
git fsck --full           # 저장소 무결성 검사
git fsck --unreachable    # 접근 불가능한 객체 찾기
git count-objects -v      # 객체 통계 및 문제점 파악
```


### 일반적인 문제와 해결법

**손상된 객체 복구:**

```bash
# 다른 클론에서 객체 복사
cp other-repo/.git/objects/ab/cdef... .git/objects/ab/

# 원격에서 다시 페치
git fetch origin
git reset --hard origin/main
```

**Index 파일 손상:**

```bash
# Index 재구성
rm .git/index
git reset
```


## 12. 고급 최적화 기법

### 대용량 저장소 최적화

**Shallow Clone 활용:**

```bash
git clone --depth 1 <repo>        # 최신 커밋만
git clone --shallow-since="1 year ago" <repo>  # 기간 제한
```

**Sparse Checkout:**

```bash
git config core.sparseCheckout true
echo "src/*" > .git/info/sparse-checkout
git read-tree -m -u HEAD
```


### 네트워크 최적화

**Delta 압축 최적화:**

```bash
git config pack.windowMemory 1g   # 메모리 사용량 증가
git config pack.window 50         # Delta 윈도우 크기
git repack -a -d --window=50      # 재압축 실행
```

**전송 최적화:**

```bash
git config http.postBuffer 524288000   # POST 버퍼 증가
git config pack.packSizeLimit 2g       # Pack 크기 제한
```


## 13. 보안과 무결성 보장

### 데이터 무결성 메커니즘

**SHA-1 체크섬 시스템:**

- 모든 객체는 내용 기반 해시로 식별[^2]
- 데이터 변경 시 즉시 감지 가능
- 네트워크 전송 중 오류 탐지

**충돌 탐지 시스템:**

```bash
# SHA-1 충돌 탐지 상태 확인
git config --global --list | grep sha1
# core.sha1collisiondetection=true 확인
```


### 암호화와 서명

**커밋 서명:**

```bash
git config user.signingkey <key-id>
git config commit.gpgsign true
git commit -S -m "Signed commit"
```

**태그 서명:**

```bash
git tag -s v1.0 -m "Signed release v1.0"
git verify-tag v1.0
```


## 결론

Git의 내부 구조는 현대 소프트웨어 개발의 복잡한 요구사항을 해결하기 위해 정교하게 설계된 시스템입니다. 4가지 핵심 객체(Blob, Tree, Commit, Tag)와 3-Tree 아키텍처를 통해 효율적이고 안전한 버전 관리를 제공합니다.

SHA-1에서 SHA-256으로의 전환, Delta 압축을 통한 저장 공간 최적화, Reflog를 통한 안전망 제공 등 Git의 내부 구조는 계속해서 발전하고 있습니다. 이러한 내부 동작 원리를 이해함으로써 개발자는 Git을 더욱 효과적으로 활용할 수 있으며, 문제 상황에서도 적절한 해결책을 찾을 수 있습니다.

Git의 내부 구조에 대한 깊은 이해는 단순히 명령어를 외우는 것을 넘어서, 버전 관리 시스템의 본질을 파악하고 더 나은 개발 워크플로우를 구축하는 데 필수적인 지식입니다. 앞으로도 Git의 발전과 함께 이러한 내부 구조에 대한 이해를 지속적으로 발전시켜 나가시기 바랍니다.

<div style="text-align: center">⁂</div>

[^1]: https://ahhda.github.io/git/architecture/2020/04/19/git-internal-architecture.html

[^2]: https://shafiul.github.io/gitbook/1_the_git_object_model.html

[^3]: https://githowto.com/git_internals_git_directory

[^4]: https://velog.io/@jinjoa1/Git-저장소-.git-디렉토리-구조

[^5]: https://tecoble.techcourse.co.kr/post/2021-07-08-dot-git/

[^6]: https://gist.github.com/matthewmccullough/2695758

[^7]: https://develop-ssooo.tistory.com/217

[^8]: https://velog.io/@sjhgd107/Git-내부-정리-Part-2-.git-디렉토리-구조-및-참조-시스템

[^9]: https://www.geeksforgeeks.org/git/git-index/

[^10]: https://storycompiler.tistory.com/7

[^11]: https://velog.io/@jihwooon/Git-내부-구조에-대해서-알아보자-Tree와-Blob

[^12]: https://learn.microsoft.com/en-us/archive/msdn-magazine/2017/august/devops-git-internals-architecture-and-index-files

[^13]: https://nulab.com/learn/software-development/git-tutorial/git-basics/what-is-git/git-architecture/

[^14]: https://mint.westdri.ca/git/intro_three_trees.html

[^15]: https://graphite.dev/guides/git-staging-area

[^16]: https://stackoverflow.com/questions/49228209/whats-the-use-of-the-staging-area-in-git

[^17]: https://stackoverflow.com/questions/3689838/git-difference-between-head-working-tree-and-index

[^18]: https://github.blog/news-insights/company-news/sha-1-collision-detection-on-github-com/

[^19]: https://stackoverflow.com/questions/9392365/how-would-git-handle-a-sha-1-collision-on-a-blob

[^20]: https://devclass.com/2023/08/22/git-2-42-released-sha-256-repositories-no-longer-an-experimental-curiosity/

[^21]: https://stackoverflow.com/questions/60087759/git-is-moving-to-new-hashing-algorithm-sha-256-but-why-git-community-settled-on

[^22]: https://about.gitlab.com/blog/sha256-support-in-gitaly/

[^23]: https://lwn.net/Articles/898522/

[^24]: https://github.blog/open-source/git/gits-database-internals-i-packed-object-store/

[^25]: https://www.linkedin.com/pulse/gits-delta-compression-algorithm-technical-deep-dive-maheshwari

[^26]: https://www.geeksforgeeks.org/git/git-packfiles/

[^27]: https://www.atlassian.com/git/tutorials/rewriting-history/git-reflog

[^28]: https://www.siteground.com/kb/git-structures-repository-content/

[^29]: https://dev.to/pragativerma18/git-reflog-rescuing-lost-work-in-gits-timeline-108c

[^30]: https://www.datacamp.com/tutorial/git-reflog

[^31]: https://www.atlassian.com/git/tutorials/git-gc

[^32]: https://git-scm.com/docs/githooks

[^33]: https://stackoverflow.com/questions/7065224/in-a-git-hook-is-the-current-working-directory-guaranteed-to-be-within-the-git-r

[^34]: https://codecreature.tistory.com/69

[^35]: https://git-scm.com/book/en/v2/Git-Internals-Git-Objects

[^36]: https://300-29-1.tistory.com/71

[^37]: https://donotlimityourself.tistory.com/41

[^38]: https://www.freecodecamp.org/news/git-internals-objects-branches-create-repo/

[^39]: https://git-scm.com/book/ko/v2/Git의-내부-Git-개체

[^40]: https://git-scm.com/docs/gitrepository-layout

[^41]: https://angular.love/becoming-a-git-pro-part-1-internal-git-architecture

[^42]: https://cyberx.tistory.com/81

[^43]: https://news.ycombinator.com/item?id=34195895

[^44]: https://www.reddit.com/r/git/comments/1ydvpg/does_git_compress_files_efficiently/

[^45]: https://git-scm.com/about/staging-area

[^46]: https://news.ycombinator.com/item?id=28844142

[^47]: https://www.reddit.com/r/git/comments/2szztw/is_the_index_the_same_as_the_staging_area/

[^48]: https://security.stackexchange.com/questions/225411/how-secure-is-a-git-commit-hash-sha1

[^49]: https://github.com/GitoxideLabs/gitoxide/security/advisories/GHSA-2frx-2596-x5r6

[^50]: https://www.aviator.co/blog/how-git-compresses-files/

[^51]: https://gitolite.com/uses-of-index.html

[^52]: https://spri.kr/posts/view/21757

[^53]: https://stackoverflow.com/questions/52131250/how-does-git-perform-on-compressed-files

[^54]: https://lindevs.com/optimize-local-git-repository-by-running-garbage-collection

[^55]: https://www.codewalnut.com/tutorials/how-to-use-git-reflog-to-view-reference-logs

[^56]: https://www.youtube.com/watch?v=kCo4LHefBnY

[^57]: https://community.atlassian.com/forums/Sourcetree-questions/Hooks-and-lfs-folders-are-created-on-project-root-instead-of/qaq-p/1343948

[^58]: https://drehzr.tistory.com/2069

[^59]: https://www.baeldung.com/ops/git-log-reflog-specific-branch

[^60]: https://wansook0316.github.io/dv/ios/2021/08/08/Git-Structure.html

[^61]: https://github.com/git-lfs/git-lfs/issues/4050

[^62]: https://git-scm.com/docs/git-gc

[^63]: https://stackoverflow.com/questions/17857723/whats-the-difference-between-git-reflog-and-log

[^64]: https://inpa.tistory.com/entry/JAVA-☕-가비지-컬렉션GC-동작-원리-알고리즘-💯-총정리

[^65]: https://git-scm.com/docs/git-reflog

[^66]: https://en.wikipedia.org/wiki/Zlib

[^67]: https://stackoverflow.com/questions/31873479/git-deflate-optimized-zlib

[^68]: https://www.npmjs.com/package/compression-zlib

[^69]: https://www.linkedin.com/pulse/git-concepts-architecture-hung-vo-hoang-manh

[^70]: https://github.com/kevin-cantwell/zlib

[^71]: https://lwn.net/Articles/823768/

[^72]: http://facebook.github.io/zstd/

[^73]: https://github.com/go-gitea/gitea/issues/13794

[^74]: https://github.com/gildor2/fast_zlib

[^75]: https://ohyecloudy.com/pnotes/archives/1949/

[^76]: https://git-scm.com/docs/hash-function-transition

[^77]: https://chromium.googlesource.com/chromium/src/+/HEAD/third_party/zlib/README.chromium

