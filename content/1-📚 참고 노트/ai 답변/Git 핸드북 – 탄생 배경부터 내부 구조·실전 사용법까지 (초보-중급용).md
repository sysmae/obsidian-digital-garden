---
publish: true
---

# Git 핸드북 – 탄생 배경부터 내부 구조·실전 사용법까지 (초보-중급용)
git 에 관해 만들어진 이유에서 구조, 사용법 등을 통합한 하나의 핸드북 형식의 요약 만들어줘. 초보~중급

**요약 · 핵심**  
Git은 5년 BitKeeper 라이선스 문제로 인해 리누스 토르발스가 “대규모 · 분산 · 무결성”을 목표로 10일 만에 만든 분산 버전 관리 시스템(DVCS)이다[1](https://en.wikipedia.org/wiki/Git)[2](https://www.linuxfoundation.org/blog/blog/10-years-of-git-an-interview-with-git-creator-linus-torvalds)​. **모든 개발자가 전체 이력을 보유**하며, 로컬-오프라인 작업이 빠르고 안전하다[3](https://en.wikipedia.org/wiki/Distributed_version_control)​. Git 내부는 ① 작업 디렉터리 ② 스테이징 영역(index) ③ 로컬 저장소의 **3-계층 구조**로 동작하고[4](https://nulab.com/learn/software-development/git-tutorial/git-basics/what-is-git/git-architecture/)[5](https://learn.microsoft.com/en-us/archive/msdn-magazine/2017/august/devops-git-internals-architecture-and-index-files)​, 저장소 안에서는 **blob-tree-commit-tag** 객체 DAG로 데이터 무결성을 보장한다[6](https://ahhda.github.io/git/architecture/2020/04/19/git-internal-architecture.html)[7](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)​.

## 1. 왜 Git이 만들어졌을까?

|문제|BitKeeper 해약 전 CVS·SVN 한계|Git 설계 목표|
|---|---|---|
|중앙 서버 장애 시 작업 중단|CVS/SVN은 중앙집중식이어서 단일 장애점|**완전 분산**(모든 노드가 백업)[3](https://en.wikipedia.org/wiki/Distributed_version_control)|
|느린 패치·병합|30 초 이상 걸리던 패치 적용[1](https://en.wikipedia.org/wiki/Git)|**3 초 이내 패치, 초고속** 작업[1](https://en.wikipedia.org/wiki/Git)|
|히스토리 조작·무결성 취약|메타데이터 손상 가능|**SHA-1 해시**로 객체 불변·검증[7](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)|

## 2. 내부 구조 한눈에 보기

**작업 흐름**

```
Working Directory ── git add ─▶ Index(Stage) ── git commit ─▶ Local Repository
     ▲                                            │
     │                                            ▼
     └────── git checkout / git restore ◀───────── Objects (blob/tree/commit)

```

1. **작업 디렉터리**: 수정·삭제·신규 파일을 만드는 공간
    
2. **스테이징 영역**: 커밋 후보 파일을 임시로 보관, 부분 커밋 가능[4](https://nulab.com/learn/software-development/git-tutorial/git-basics/what-is-git/git-architecture/)
    
3. **로컬 저장소 (.git)**:
    
    - objects/ 폴더에 `blob, tree, commit, tag` 저장[6](https://ahhda.github.io/git/architecture/2020/04/19/git-internal-architecture.html)
        
    - refs/ 에 브랜치·태그 포인터 저장
        
    - HEAD → 현재 작업 브랜치를 가리키는 특수 참조[6](https://ahhda.github.io/git/architecture/2020/04/19/git-internal-architecture.html)
        

## 3. 필수 명령 & 개념 정리

| 범주        | 핵심 명령                               | 설명                                                                                                          |
| --------- | ----------------------------------- | ----------------------------------------------------------------------------------------------------------- |
| **시작**    | `git init`                          | 새 로컬 저장소 초기화[8](https://www.geeksforgeeks.org/git/useful-git-commands-and-basic-concepts/)                  |
|           | `git clone <url>`                   | 원격 저장소 복제[9](https://www.freecodecamp.org/news/10-important-git-commands-that-every-developer-should-know/) |
| **저장·이동** | `git status`                        | 변경 상태 확인[8](https://www.geeksforgeeks.org/git/useful-git-commands-and-basic-concepts/)                      |
|           | `git add <file>`                    | 스테이징 영역에 등록[10](https://docs.gitlab.com/topics/git/commands/)                                               |
|           | `git commit -m "msg"`               | 스냅숏 기록, SHA-1 생성[7](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)                               |
| **브랜치**   | `git branch` / `-d`                 | 목록·생성·삭제[9](https://www.freecodecamp.org/news/10-important-git-commands-that-every-developer-should-know/)  |
|           | `git checkout -b feat`              | 브랜치 생성+전환[10](https://docs.gitlab.com/topics/git/commands/)                                                 |
| **통합**    | `git merge`                         | 브랜치 병합 → 머지 커밋 생성[11](https://coding-by-head.tistory.com/entry/git-advanced)                                |
|           | `git rebase`                        | 커밋을 선형 재배치, 깔끔한 히스토리[11](https://coding-by-head.tistory.com/entry/git-advanced)                             |
| **협업**    | `git remote add origin <url>`       | 원격 별칭 등록[10](https://docs.gitlab.com/topics/git/commands/)                                                  |
|           | `git pull` / `git push`             | 원격과 동기화[8](https://www.geeksforgeeks.org/git/useful-git-commands-and-basic-concepts/)                       |
| **임시저장**  | `git stash` / `stash pop`           | 작업 보관 후 다른 브랜치 이동[12](https://geunuk.tistory.com/464)                                                       |
| **디버깅**   | `git log`, `git diff`, `git bisect` | 히스토리·차이·버그 추적[10](https://docs.gitlab.com/topics/git/commands/)                                             |

## 4. 브랜치 & 워크플로 전략

|전략|특징|상황|
|---|---|---|
|**Feature Branch (GitHub Flow)**|main에서 기능 브랜치 → PR → 머지, CI 간단[13](https://lazyops.hashnode.dev/understanding-the-git-workflow)|스타트업·소규모 팀|
|**Git Flow**|`main` + `develop` + release/hotfix 브랜치 체계적[14](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)|릴리스 주기가 분명한 서비스|
|**Trunk-Based**|모든 기능을 작은 PR로 빠르게 main에 합치고 feature flag 사용|데브옵스·배포 자동화 환경|

병합 방식 선택

- **merge** – 흐름 보존, 커밋·브랜치 내역이 명확[11](https://coding-by-head.tistory.com/entry/git-advanced)
    
- **rebase** – 히스토리 선형, 리뷰·bisect 용이. 공개 원격 브랜치에 rebase 금지 원칙 준수.
    

## 5. 스테이징·리베이스 고급 사용

1. **인터랙티브 리베이스**  
    `git rebase -i HEAD~5` → `pick / reword / squash / fixup` 로 커밋 메시지 수정·합치기[11](https://coding-by-head.tistory.com/entry/git-advanced)[15](https://aeju.tistory.com/783)
    
2. **autosquash**  
    `git commit --fixup <SHA>` → `git rebase -i --autosquash` 로 자동 정리[16](https://nochoco-lee.tistory.com/153)
    
3. **자동 stash**  
    `git pull --rebase --autostash` 옵션으로 충돌 최소화[17](https://github.com/fork-dev/Tracker/issues/2034)
    

## 6. 원격 협업 체크리스트

1. **항상 `git pull --rebase`** 로 최신 main 선적용 후 작업 이어가기
2. 리뷰 전 **인터랙티브 리베이스**로 커밋 정리 → 작은 단위 커밋 유지
3. 공개 브랜치에서 **force push 금지**: `--force-with-lease` 예외적 사용
4. **태그**(`git tag -a v1.0 -m "release"`)로 릴리스 스냅숏 고정
5. CI / CD 파이프라인과 hook(`.git/hooks`) 활용해 테스트 자동화
    

## 7. 자주 묻는 질문(FAQ)

| 질문                   | 간단 답변                                                                 |
| -------------------- | --------------------------------------------------------------------- |
| **Git과 GitHub 차이?**  | Git은 로컬 VCS, GitHub는 Git 호스팅 서비스·웹 UI                                 |
| **파일을 실수로 삭제했어요.**   | `git checkout -- <file>` 또는 `git restore <file>`                      |
| **커밋 메시지 수정?**       | 직전 커밋: `git commit --amend`; 과거: `git rebase -i`                      |
| **대용량 파일 푸시가 안 돼요.** | Git LFS 사용 또는 `.gitignore`로 제외                                        |
| **충돌 해결 팁?**         | ```<<<<<<<`, `=======`, `>>>>>>>` 구분선 편집 후 `git add` → `git commit``` |
|                      |                                                                       |

## 8. 더 배우기 위한 추천 자료

- 공식 Pro Git e-Book (git-scm.com)
    
- `git help <command>` – 내장 매뉴얼[18](https://git-scm.com/docs)
    
- 무료 실습 사이트: learngitbranching.js.org
    

**이 핸드북으로 기본기를 다졌다면** 작은 개인 프로젝트부터 Git 워크플로를 직접 적용해 보자. 실습이 최고의 학습이다!

1. [https://en.wikipedia.org/wiki/Git](https://en.wikipedia.org/wiki/Git)
2. [https://www.linuxfoundation.org/blog/blog/10-years-of-git-an-interview-with-git-creator-linus-torvalds](https://www.linuxfoundation.org/blog/blog/10-years-of-git-an-interview-with-git-creator-linus-torvalds)
3. [https://en.wikipedia.org/wiki/Distributed_version_control](https://en.wikipedia.org/wiki/Distributed_version_control)
4. [https://nulab.com/learn/software-development/git-tutorial/git-basics/what-is-git/git-architecture/](https://nulab.com/learn/software-development/git-tutorial/git-basics/what-is-git/git-architecture/)
5. [https://learn.microsoft.com/en-us/archive/msdn-magazine/2017/august/devops-git-internals-architecture-and-index-files](https://learn.microsoft.com/en-us/archive/msdn-magazine/2017/august/devops-git-internals-architecture-and-index-files)
6. [https://ahhda.github.io/git/architecture/2020/04/19/git-internal-architecture.html](https://ahhda.github.io/git/architecture/2020/04/19/git-internal-architecture.html)
7. [https://git-scm.com/book/en/v2/Git-Internals-Git-Objects](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)
8. [https://www.geeksforgeeks.org/git/useful-git-commands-and-basic-concepts/](https://www.geeksforgeeks.org/git/useful-git-commands-and-basic-concepts/)
9. [https://www.freecodecamp.org/news/10-important-git-commands-that-every-developer-should-know/](https://www.freecodecamp.org/news/10-important-git-commands-that-every-developer-should-know/)
10. [https://docs.gitlab.com/topics/git/commands/](https://docs.gitlab.com/topics/git/commands/)
11. [https://coding-by-head.tistory.com/entry/git-advanced](https://coding-by-head.tistory.com/entry/git-advanced)
12. [https://geunuk.tistory.com/464](https://geunuk.tistory.com/464)
13. [https://lazyops.hashnode.dev/understanding-the-git-workflow](https://lazyops.hashnode.dev/understanding-the-git-workflow)
14. [https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
15. [https://aeju.tistory.com/783](https://aeju.tistory.com/783)
16. [https://nochoco-lee.tistory.com/153](https://nochoco-lee.tistory.com/153)
17. [https://github.com/fork-dev/Tracker/issues/2034](https://github.com/fork-dev/Tracker/issues/2034)
18. [https://git-scm.com/docs](https://git-scm.com/docs)
19. [https://www.linkedin.com/pulse/from-linus-github-fun-fascinating-history-git-michael-baker](https://www.linkedin.com/pulse/from-linus-github-fun-fascinating-history-git-michael-baker)
20. [https://about.gitlab.com/topics/version-control/benefits-distributed-version-control-system/](https://about.gitlab.com/topics/version-control/benefits-distributed-version-control-system/)
21. [https://www.linuxjournal.com/content/git-origin-story](https://www.linuxjournal.com/content/git-origin-story)
22. [https://velog.io/@chillihc/Git-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0](https://velog.io/@chillihc/Git-%EA%B0%9C%EB%85%90-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0)
23. [https://bansalanuj.com/git-internal-architecture](https://bansalanuj.com/git-internal-architecture)
24. [https://github.blog/open-source/git/git-turns-20-a-qa-with-linus-torvalds/](https://github.blog/open-source/git/git-turns-20-a-qa-with-linus-torvalds/)
25. [https://atelier-chez-moi.tistory.com/3](https://atelier-chez-moi.tistory.com/3)
26. [https://git-scm.com](https://git-scm.com/)
27. [https://sjp38.github.io/ko/post/git_origin_story_ko/](https://sjp38.github.io/ko/post/git_origin_story_ko/)
28. [https://ko.wikipedia.org/wiki/%EA%B9%83_(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4)](https://ko.wikipedia.org/wiki/%EA%B9%83_\(%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4\))
29. [https://angular.love/becoming-a-git-pro-part-1-internal-git-architecture](https://angular.love/becoming-a-git-pro-part-1-internal-git-architecture)
30. [https://github.com/torvalds/linux](https://github.com/torvalds/linux)
31. [https://encode-nyoung.tistory.com/70](https://encode-nyoung.tistory.com/70)
32. [https://apxml.com/courses/getting-started-with-git/chapter-4-branching-merging-basics/practice-branching-merging](https://apxml.com/courses/getting-started-with-git/chapter-4-branching-merging-basics/practice-branching-merging)
33. [https://stackoverflow.com/questions/41725917/is-it-possible-to-git-stash-an-interactive-rebase-in-progress](https://stackoverflow.com/questions/41725917/is-it-possible-to-git-stash-an-interactive-rebase-in-progress)
34. [https://www.reddit.com/r/git/comments/zuq54x/a_beginners_guide_to_git_a_comprehensive/](https://www.reddit.com/r/git/comments/zuq54x/a_beginners_guide_to_git_a_comprehensive/)
35. [https://www.youtube.com/watch?v=Q1kHG842HoI](https://www.youtube.com/watch?v=Q1kHG842HoI)
36. [https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows](https://git-scm.com/book/en/v2/Git-Branching-Branching-Workflows)
37. [https://www.atlassian.com/git/glossary](https://www.atlassian.com/git/glossary)
38. [https://vntgcorp.github.io/Git/](https://vntgcorp.github.io/Git/)
39. [https://education.github.com/git-cheat-sheet-education.pdf](https://education.github.com/git-cheat-sheet-education.pdf)
40. [https://velog.io/@jus1234/Git-Branch-Workflow](https://velog.io/@jus1234/Git-Branch-Workflow)