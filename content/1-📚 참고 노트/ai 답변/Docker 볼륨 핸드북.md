---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Docker 볼륨 핸드북

이 핸드북은 **Docker 볼륨**의 개념, 구조, 생성 및 관리 방법, 활용 사례와 모범 사례를 종합적으로 정리하여, Docker 환경에서 데이터를 안전하고 효율적으로 관리하기 위한 참고서로 작성되었다.

## 1. 개요 및 생성 배경

Docker 컨테이너의 파일 시스템은 **에페멀(ephemeral)** 특성을 지녀, 컨테이너가 중지·삭제될 때 내부에 저장된 데이터도 함께 사라진다.

- **문제점**: 데이터베이스 파일, 로그, 사용자 업로드 콘텐츠 등 컨테이너 라이프사이클을 넘어 보존해야 하는 데이터를 직접 이미지나 컨테이너 내부에 저장할 수 없음.
- **해결책**: Docker가 관리하는 독립된 저장소인 **볼륨(volume)**을 사용하여, 컨테이너 재시작·재배포 시에도 데이터 지속성(persistence) 보장[^1].


## 2. 볼륨의 종류와 구조

Docker 볼륨은 크게 네 가지 유형으로 구분된다.


| 구분 | 설명 | 호스트 경로 | 사용 예시 |
| :-- | :-- | :-- | :-- |
| 이름 있는 볼륨 (Named volume) | Docker가 `/var/lib/docker/volumes/<이름>/_data`에 생성·관리 | 자동 생성 | 데이터베이스, 애플리케이션 설정 파일 |
| 익명 볼륨 (Anonymous volume) | 이름 없이 컨테이너 생성 시 자동 부여 | `/var/lib/docker/volumes/<랜덤ID>/_data` | 테스트 용 임시 저장소 |
| 바인드 마운트 (Bind mount) | 호스트 파일 시스템의 특정 디렉토리를 컨테이너에 직접 연결 | 사용자 지정 경로 | 개발 중 코드 공유, 호스트 로그 분석 |
| 임시 메모리 볼륨 (tmpfs) | 호스트 메모리에만 존재, 컨테이너 종료 시 삭제 | 메모리 상 | 민감 정보 처리, 고속 I/O 캐시 |

> 볼륨은 Docker 엔진이 관리하는 저장 영역이며, 컨테이너의 쓰기 계층을 우회하여 호스트 파일 시스템에 직접 기록된다[^1].

## 3. 생성·마운트·관리 절차

### 3.1 생성

- CLI: `docker volume create [옵션] <볼륨명>`
    - 드라이버 지정: `--driver local --opt o=type=nfs,...`
- Docker Compose:

```yaml
volumes:
  app_data:
    driver: local
```


### 3.2 마운트

- `-v` 단축: `docker run -v <볼륨명>:<컨테이너경로>[:ro] 이미지`
- `--mount` 상세:

```
docker run \
  --mount type=volume,source=<볼륨명>,target=/data,readonly \
  이미지
```

    - `type=volume|bind|tmpfs`
    - `source`(또는 `src`), `target`(또는 `dst`)
    - 읽기 전용: `readonly=true` 또는 `:ro`


### 3.3 조회 및 삭제

- 목록: `docker volume ls`
- 상세: `docker volume inspect <볼륨명>`
- 개별 삭제: `docker volume rm <볼륨명>`
- 미사용 볼륨 일괄 정리: `docker volume prune`


## 4. 고급 설정 및 드라이버 활용

- **NFS/CIFS 마운트**:

```
docker volume create \
  --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.1.1,rw \
  nfs_volume
```

- **크기 제한**(지원 드라이버 한정):

```
docker volume create \
  --opt size=10g limited_volume
```

- **분산 파일 시스템**을 위한 서드파티 플러그인 연동 가능


## 5. 활용 사례 및 모범 사례

### 5.1 활용 사례

1. **상태 저장 애플리케이션**
    - MySQL, Postgres 데이터베이스 파일 저장
2. **로그 수집**
    - 컨테이너 로그 디렉토리 마운트 후 호스트에서 Aggregation 도구 실행
3. **CI/CD 파이프라인**
    - 빌드 산출물을 공유 디렉토리에 저장하여 후속 단계 재사용
4. **다중 컨테이너 공유 데이터**
    - 웹 서버와 워커 간 파일 공유

### 5.2 모범 사례

- **이름 있는 볼륨 사용 권장**: 관리·백업·마이그레이션 용이
- **`--mount` 구문** 선호: 명시적이고 가독성 우수
- **볼륨 네이밍 규칙** 수립: 환경·서비스 식별자 포함 (`prod_db_data` 등)
- **정기 정리**: `docker volume prune`로 불필요 볼륨 제거하여 디스크 공간 확보
- **백업 전략**:
    - 볼륨을 다른 컨테이너에 마운트하여 `tar` 백업
    - 외부 스토리지 백업 툴 통합


## 6. 참고 명령어 요약

| 구분 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 생성 | `docker volume create myvol` | 기본 로컬 드라이버로 볼륨 생성 |
| 생성(드라이버) | `docker volume create --driver local --opt type=nfs … nfsvol` | NFS 타입 볼륨 생성 |
| 목록 | `docker volume ls` | 볼륨 목록 조회 |
| 상세 | `docker volume inspect myvol` | 볼륨 메타데이터 확인 |
| 삭제 | `docker volume rm myvol` | 단일 볼륨 삭제 |
| 정리 | `docker volume prune` | 미사용 볼륨 일괄 삭제 |
| 마운트 | `docker run --mount type=volume,source=myvol,target=/data img` | 볼륨 마운트하여 컨테이너 실행 |

## 7. 결론 및 추천

Docker 볼륨은 **컨테이너와 데이터의 수명주기를 분리**하여 영속성을 보장하는 핵심 수단이다.

- **이름 있는 볼륨**과 **`--mount` 구문** 사용을 기본으로,
- **정기 백업·정리**를 통해 안정적인 운영 환경을 유지할 것을 권장한다.

이 핸드북을 토대로 Docker 볼륨을 체계적으로 설계·운영하여, 컨테이너 기반 인프라의 데이터 내구성과 관리 효율성을 극대화하기 바란다.

---
_sources: Docker 공식 문서[^1], Dev.to 가이드[^2], LabEx 튜토리얼[^3]._

<div style="text-align: center">⁂</div>

[^1]: https://docs.docker.com/engine/storage/volumes/

[^2]: https://dev.to/doziestar/a-comprehensive-guide-to-docker-volumes-4d9h

[^3]: https://labex.io/ja/tutorials/docker-how-to-use-docker-volumes-effectively-392904

[^4]: https://www.divio.com/blog/docker-volumes-a-comprehensive-guide/

[^5]: https://qiita.com/mtgto/items/aabc212a1d3f99878828

[^6]: https://docs.docker.com/build/building/best-practices/

[^7]: https://spacelift.io/blog/docker-volumes

[^8]: https://www.resumy.ai/tech-posts/43b97641-d6e6-4147-bc70-1a7a9f07cfda

[^9]: https://qiita.com/masterpiecehack/items/8568ba4acb8916fd61fb

[^10]: https://docs.docker.jp/storage/volumes.html

[^11]: https://docs.docker.jp/develop/develop-images/dockerfile_best-practices.html

[^12]: https://www.docker.com/ja-jp/blog/top-tips-and-use-cases-for-managing-your-volumes/

[^13]: https://zenn.dev/forcia_tech/articles/20210716_docker_best_practice

[^14]: https://docs.docker.jp/engine/userguide/dockervolumes.html

[^15]: https://refine.dev/blog/docker-volumes/

[^16]: https://zenn.dev/fdnsy/articles/e2024d73ec2bda

[^17]: https://matsuand.github.io/docs.docker.jp.onthefly/develop/dev-best-practices/

[^18]: https://qiita.com/aki_55p/items/63c47214cab7bcb027e0

[^19]: https://matsuand.github.io/docs.docker.jp.onthefly/storage/volumes/

[^20]: https://blog.cloudsmith.co.jp/2021/08/549/

