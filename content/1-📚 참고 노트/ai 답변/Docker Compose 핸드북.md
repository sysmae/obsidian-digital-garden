---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Docker Compose 핸드북

## 핵심 요약

**Docker Compose**는 복잡한 멀티컨테이너 애플리케이션을 코드화된 설정 한 파일로 정의·관리하여, 일관된 개발·테스트·배포 환경을 제공하도록 고안된 도구이다.

## 1. 탄생 배경 및 목적

Docker Compose는 단일 컨테이너 실행을 위한 Docker CLI(예: `docker run`)가, 여러 컨테이너를 조합해 하나의 스택으로 운영하기에는 비효율적이라는 점에서 출발했다.

- **목적**:
    - 여러 서비스(예: 웹 서버·DB·캐시)를 하나의 YAML 파일(`docker-compose.yml`)에 선언
    - 단일 명령으로 전체 스택 생성·시작·중지·삭제
    - 개발·테스트·CI/CD·스테이징·프로덕션 환경 간 설정 일관성 유지


## 2. 아키텍처 및 주요 컴포넌트

Docker Compose 실행 시, 내부에서 다음 요소를 자동 구성·관리한다.


| 컴포넌트                             | 설명                                                   |
| :------------------------------- | :--------------------------------------------------- |
| 서비스(Services)                    | 각 컨테이너를 나타내며, 이미지·빌드 컨텍스트·포트·환경변수·볼륨·종속관계 등을 정의[^1]. |
| 네트워크(Networks)                   | 프로젝트별 기본 네트워크를 생성하여 컨테이너 간 호스트명 기반 통신 지원[^1].        |
| 볼륨(Volumes)                      | 데이터 영속화를 위한 스토리지 볼륨을 생성·마운트[^1].                     |
| 프로필(Profiles)                    | 서비스 그룹을 묶어 특정 환경(dev·prod)별 활성화 제어[^2].              |
| 조건·헬스체크(depends_on, healthcheck) | 서비스 시작 순서 및 준비 상태 조건 지정[^2].                         |

## 3. 구성 파일 구조

Compose 스펙 버전(V2·V3·V3.8 등)을 최상단에 선언하고, `services`·`networks`·`volumes`·`configs` 블록을 정의한다[^3].

```yaml
version: "3.8"

services:
  web:
    image: myapp:latest
    build:
      context: .
    ports:
      - "8080:80"
    environment:
      - NODE_ENV=production
    depends_on:
      - db
    profiles:
      - prod

  db:
    image: postgres:14
    volumes:
      - db-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres"]
      interval: 30s

volumes:
  db-data:

networks:
  default:
```

1. **서비스 정의**: 이미지·빌드·포트·환경변수·의존성·프로필 등
2. **볼륨 선언**: 명명된 볼륨을 통해 데이터 영속화
3. **네트워크 선언**: 커스텀 네트워크 옵션 설정 가능

## 4. 기본 사용법

1. **시작**:

```bash
docker compose up -d
```

    - 정의된 모든 서비스 컨테이너를 생성·시작
2. **중지 및 제거**:

```bash
docker compose down
```

    - 네트워크·볼륨(옵션)까지 정리 가능
3. **스케일링**:

```bash
docker compose up --scale web=3 -d
```

    - 서비스별 인스턴스 수 조절
4. **로그 확인**:

```bash
docker compose logs -f
```


## 5. 고급 기능 및 활용

세부 기능을 활용해 환경별·목적별로 유연한 설정 관리가 가능하다.


| 기능 | 설명 |
| :-- | :-- |
| 다중 파일 머지(`-f`) | `compose.yml`+`compose.override.yml` 병합[^4]. |
| 환경별 오버라이드 | `.env` 또는 `env_file`로 민감정보·환경변수 분리[^5]. |
| 프로필 지원 | `profiles`로 dev·test·prod 서비스 그룹 제어[^2]. |
| 변수 치환 | `${VAR}` 구문으로 동적 설정[^1]. |
| 리소스 제한 | `deploy.resources`에 CPU·메모리 한계 지정[^2]. |
| 헬스체크 및 조건 | `healthcheck`, `depends_on.condition`으로 안정적 스타트업[^2]. |

## 6. Compose V2 변화 및 마이그레이션

- **Compose V2**는 Go 언어 기반으로 Docker CLI 플러그인 형태 통합[^6].
- 명령어 통합: `docker-compose` → `docker compose`
- **새 기능**: GPU 지원·클라우드 배포(`docker context`)·빠른 릴리즈[^6].
- 마이그레이션: 기존 `docker-compose.yml` 그대로 호환되며, 추가 설정 불필요[^7].


## 7. 사용 시 유의사항

- **경로 해석**: 머지 시 모든 호스트 파일 경로는 기준 파일 상대경로로 통일[^4].
- **환경변수 보안**: 민감정보는 `.env` 파일이나 외부 시크릿 관리 사용 권장.
- **파일 버전 관리**: Compose 파일도 코드로 간주해 Git 등 VCS에 커밋.
- **프로덕션 전환**: 단순 오케스트레이션용으로는 작지만, 복잡한 프로덕션 환경은 Kubernetes 등 상위 솔루션 검토.


## 8. 결론 및 권장 전략

Docker Compose는 **로컬 개발부터 CI/CD, 경량 프로덕션**까지 다양한 환경에서 빠른 멀티컨테이너 관리와 일관된 배포를 가능케 한다.

- **추천 활용**:
    - **개발·테스트**: 신속한 스택 구성·디버깅
    - **CI/CD 파이프라인**: 동일한 Compose 파일로 자동화
    - **작은 프로덕션**: 단일 호스트 애플리케이션 스택 운영

핸드북을 참고하여 적절한 버전, 오버라이드 전략, 프로필 설정 및 리소스 제한을 적용하면, 더욱 견고하고 유연한 환경을 구축할 수 있다.

---

[^1] [^1] [^2] [^2] [^4] [^4] [^5] [^5] [^6] [^6] [^7] [^7]

<div style="text-align: center">⁂</div>

[^1]: https://spacelift.io/blog/docker-compose

[^2]: https://reintech.io/blog/advanced-docker-compose-features-for-customized-workflows

[^3]: https://docs.docker.jp/compose/index.html

[^4]: https://docs.docker.com/compose/how-tos/multiple-compose-files/merge/

[^5]: https://qiita.com/hoto17296/items/a8a85d5244f46c119278

[^6]: https://www.docker.com/blog/announcing-compose-v2-general-availability/

[^7]: https://docs.docker.com/compose/releases/migrate/

[^8]: https://genee.jp/contents/docker-compose/

[^9]: https://docs.docker.jp/get-started/08_using_compose.html

[^10]: https://blog.devops.dev/docker-compose-overview-663e7ae16e7f

[^11]: https://techblog.nhn-techorus.com/archives/14429

[^12]: https://docs.docker.com/get-started/workshop/08_using_compose/

[^13]: https://www.kagoya.jp/howto/cloud/container/dockercompose/

[^14]: https://www.agent-grow.com/self20percent/2024/04/02/docker-composeを解説/

[^15]: https://docs.docker.com/compose/gettingstarted/

[^16]: https://matsuand.github.io/docs.docker.jp.onthefly/compose/

[^17]: https://qiita.com/toyoyuto618/items/f225e1b2ab22ca5adf33

[^18]: https://qiita.com/kojikaya/items/1410ba4390b6bad30246

[^19]: https://zenn.dev/toshi052312/articles/0c7b49fa765e34

[^20]: https://qiita.com/ryome/items/35df47a01a7fe3ac70ee

[^21]: https://docs.docker.com/compose/

[^22]: https://qiita.com/dev-satoshi/items/3bf77eec6089bcef642c

[^23]: https://www.kagoya.jp/howto/cloud/container/dockercomposeup/

[^24]: https://docs.docker.jp/compose/toc.html

[^25]: https://developer.a-blogcms.jp/blog/custom/docker-arm64.html

[^26]: https://www.linode.com/docs/guides/how-to-use-docker-compose-v2/

[^27]: https://dev.to/rajeshgheware/docker-compose-advanced-techniques-a-comprehensive-guide-to-production-deployments-1goi

[^28]: https://matsuand.github.io/docs.docker.jp.onthefly/compose/cli-command/

[^29]: https://docs.nautobot.com/projects/core/en/stable/development/core/docker-compose-advanced-use-cases/

[^30]: https://qiita.com/TKDS/items/e0b7a2c7af149f1974ad

[^31]: https://docs.nautobot.com/projects/core/en/v1.4.8/development/docker-compose-advanced-use-cases/

[^32]: https://zenn.dev/masinc/articles/df856763a81f80

[^33]: https://qiita.com/zembutsu/items/d82b2ae1a511ebd6a350

[^34]: https://dev.to/code42cate/day-11-advanced-docker-compose-32no

[^35]: https://tekunabe.hatenablog.jp/entry/2020/12/28/docker_compose_override_yaml

[^36]: https://egashira.dev/blog/docker-compose-v2

[^37]: https://tech-couch.com/post/advanced-docker-compose-features

[^38]: https://zenn.dev/yumemi_inc/articles/4c54f980668eec

[^39]: https://docs.docker.jp/compose/extends.html

