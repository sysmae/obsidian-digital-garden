---
publish:  true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Git Flow 핸드북 (초급-중급용)

**핵심 메시지**
Git Flow는 배포가 명확히 구분되는 소프트웨어(·패키지·펌웨어 등)에 적합한 “릴리즈 지향” 브랜치 전략이다. `main`·`develop` 두 주축 브랜치와 세 보조 브랜치(`feature/*`,`release/*`,`hotfix/*`)가 협력해 “병렬 개발 ↔ 안정 릴리즈”를 분리한다.

## 1. Git Flow가 탄생한 이유

1. 여러 버전을 동시에 유지해야 하는 **버전드(Versioned) 제품** 지원
2. 기능 병합보다 **릴리즈 안정성**을 우선시하는 워크플로 필요
3. Git merge 기능을 적극 활용해 **충돌 해소·롤백 용이**

Vincent Driessen는 2010년 블로그 글에서 모델을 공개했고, 2020년 회고 글에서 “웹앱 CD(지속 배포)에는 과하니 문맥에 맞게 쓰라”고 조언했다[^1][^2].

## 2. 브랜치 구조 한눈에 보기

![Git Flow 브랜치 다이어그램](https://user-gen-media-assets.s3.amazonaws.com/gpt4o_images/de40af91-ac06-4b9e-b850-c0cd3bb57ddd.png)

Git Flow 브랜치 다이어그램

- **main**: 항상 프로덕션 코드. 태그로 버전 표시
- **develop**: 다음 릴리즈 후보가 누적되는 통합 브랜치
- **feature/**: 단일 기능·이슈 작업용 단기 브랜치
- **release/**: 릴리즈 준비(버전 bump, QA, 경미한 버그 픽스)
- **hotfix/**: 운영 긴급 수정 전용, main에서 직접 분기


## 3. 표준 작업 플로우

| 단계 | 담당 브랜치 | 주요 명령어 (깃 플러그인 사용 예) | 설명 |
| :-- | :-- | :-- | :-- |
| 초기 설정 | main, develop | `git flow init` | 기본 브랜치 등록·접두사 설정 |
| 기능 개발 | feature | `git flow feature start 로그인`<br>`…코딩…`<br>`git flow feature finish 로그인` | develop로 자동 병합·브랜치 삭제 |
| 릴리즈 준비 | release | `git flow release start 1.2.0`<br>`…QA…`<br>`git flow release finish 1.2.0` | main·develop 양쪽 병합 + 태그 `v1.2.0` |
| 긴급 패치 | hotfix | `git flow hotfix start critical`<br>`…패치…`<br>`git flow hotfix finish critical` | main·develop 동시 반영 + 패치 태그 |

### 커맨드 없이 수동으로 할 때

```bash
# feature 예
git checkout -b feature/login develop
# 작업 후
git checkout develop
git merge --no-ff feature/login
git branch -d feature/login
```


## 4. 브랜치 네이밍 컨벤션

- 소문자·하이픈 사용: `feature/user-auth`
- 업무 티켓 번호 포함 권장: `feature/ISSUE-231-chat`
- release/hotfix는 버전·이슈명 직접 명시: `release/2.3.0`,`hotfix/payment-404`


## 5. 충돌 관리 팁

1. feature 브랜치를 **짧게 유지**하고 자주 `git pull --rebase origin develop`
2. hotfix 병합 뒤 develop에 **즉시 back-merge** 해 이중 수정 방지[^3]
3. release ↔ develop 병합 순서를 그림처럼 고정해 “버전 누락” 문제 예방[^4].

## 6. Git Flow + CI/CD 통합

- **자동 빌드 행**
– develop 푸시 → 스테이징 환경 배포
– release 브랜치 → UAT(사용자 수용 테스트)
– main 병합/태그 → 프로덕션 배포 + 변경 로그 생성
- **파이프라인 가드레일**: main 직접 푸시 차단, 병합 전 테스트 필수[^5].


## 7. 장단점 비교

| 항목 | 장점 | 단점 |
| :-- | :-- | :-- |
| 구조 | 릴리즈·핫픽스 단계별 명확[^6] | 브랜치 수 많아 학습 곡선↑[^5] |
| 팀 규모 | 대규모·규제 프로젝트 적합[^5] | 소규모·CD 팀엔 과도[^7] |
| 버전 관리 | 다중 버전 장기 지원 용이[^5] | 긴 릴리즈 사이클→피드백 늦음[^8] |

## 8. 언제 Git Flow를 선택할까?

1. **버전 태그로 납품**하는 임베디드·SDK·온프레미스 SW
2. 고객사별 **장기 LTS 브랜치**를 유지해야 하는 기업형 제품
3. 출시 전 QA·보안 인증 등 **규제 절차**가 필요한 환경

CD 중심 웹앱·모바일 서비스라면 GitHub Flow나 trunk-based가 더 민첩하다.

## 9. 도입 체크리스트

1. 팀원에게 **git-flow CLI** 설치 교육
2. 브랜치 보호·파이프라인 정책 수립
3. 릴리즈·패치 절차를 **문서화 \& 자동화**
4. 분기·병합 시각화 툴(Sourcetree, GitKraken) 활용

## 10. 빠른 레퍼런스 (치트시트)

```
# Feature
git flow feature start <name>
git flow feature finish <name>

# Release
git flow release start <ver>
git flow release finish <ver>

# Hotfix
git flow hotfix start <ver>
git flow hotfix finish <ver>
```

세 줄만 익히면 Git Flow 작업 플로우를 현장에서 바로 활용할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://velog.io/@couchcoding/충돌-없는-Git을-위해-Git-Flow에-대해-알아보자

[^2]: https://www.datacamp.com/tutorial/gitflow

[^3]: https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-git-branch-approach/advantages-and-disadvantages-of-the-git-hub-flow-strategy.html

[^4]: https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow

[^5]: https://stackoverflow.com/questions/73641987/git-branch-between-hotfix-and-develop

[^6]: https://hackernoon.com/from-git-flow-to-cicd-a-practical-guide-to-implement-git-workflow

[^7]: https://tech.mfort.co.kr/blog/2022-08-05-trunk-based-development/

[^8]: https://velog.io/@king_nono_1030/git-git-flow-strategy

[^9]: https://docs.aws.amazon.com/prescriptive-guidance/latest/choosing-git-branch-approach/advantages-and-disadvantages-of-the-gitflow-strategy.html

[^10]: https://danielkummer.github.io/git-flow-cheatsheet/index.ko_KR.html

[^11]: https://taptorestart.tistory.com/entry/Q-git-flow란-git-flow-활용-방법은

[^12]: https://www.reddit.com/r/git/comments/f9du5a/what_drawbacks_have_you_experienced_on_projects/

[^13]: https://wookkl.tistory.com/57

[^14]: https://framecreator.tistory.com/25

[^15]: https://www.glukhov.org/post/2025/06/gitflow-steps-and-alternatives/

[^16]: https://dogfeet.github.io/articles/2011/a-successful-git-branching-model.html

[^17]: https://blog.hwahae.co.kr/all/tech/9507

[^18]: https://georgestocker.com/2020/03/04/please-stop-recommending-git-flow/

[^19]: https://ykh6242.tistory.com/entry/Git-flow-소개

[^20]: https://wonsss.github.io/etc/git-branch-strategy/

[^21]: https://www.cnblogs.com/Leo_wl/p/14440150.html

[^22]: https://www.linkedin.com/posts/kedar-joshi-5648b44b_a-successful-git-branching-model-activity-7119024217278337024-eeEN

[^23]: https://nvie.com/posts/a-successful-git-branching-model/

[^24]: https://news.ycombinator.com/item?id=37415677

[^25]: https://news.ycombinator.com/item?id=22496724

[^26]: https://jtm0609.tistory.com/179

[^27]: https://velog.io/@geon-2/Git-WorkFlow

[^28]: https://hudi.blog/git-branch-strategy/

