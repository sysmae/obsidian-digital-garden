---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Docker 명령어 핸드북

**핵심 요약:** Docker 명령어는 **이미지 관리**, **컨테이너 관리**, **네트워크 관리**, **볼륨 관리**, **레지스트리 상호작용**, **시스템 관리**, **Docker Compose** 등 주요 기능별로 구분된다. 각 명령어는 컨테이너화된 환경 구축과 운영을 간소화하며, 일관성 있는 배포·확장을 가능하게 한다.

## 1. 개요 및 준비

Docker CLI는 터미널에서 Docker 데몬과 상호작용하기 위한 기본 도구이다.

- **도구 설치 확인**:

```bash
docker version           # 클라이언트·서버 버전 확인  
docker info              # 실행 환경 및 리소스 현황 조회  
```

- **도움말 조회**:

```bash
docker help              # 전체 명령어 목록  
docker <command> --help  # 특정 명령어 상세 옵션  
```


## 2. 이미지 관리

이미지는 애플리케이션 실행 환경의 청사진으로, 빌드·태깅·삭제 단계로 구성된다.


| 기능 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 이미지 목록 | `docker images` | 로컬에 저장된 모든 이미지 표시[^1] |
| 이미지 빌드 | `docker build -t <이름>:<태그> <경로>` | Dockerfile로부터 이미지 생성[^1] |
| 이미지 태깅 | `docker tag <원본>:<태그> <레포지토리>/<이름>:<태그>` | 레포지토리용 태그 지정[^1] |
| 이미지 삭제 | `docker rmi <이미지>` | 하나 이상의 이미지 제거[^1] |

**심화 팁:**

- `--no-cache` 옵션으로 레이어 캐시 무시
- `--pull` 옵션으로 베이스 이미지 최신화


## 3. 컨테이너 관리

컨테이너는 이미지를 실행한 인스턴스로, 생성·실행·종료·삭제 단계로 분류된다.


| 기능 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 컨테이너 생성 | `docker create <이미지>` | 실행은 하지 않고 컨테이너만 생성[^1] |
| 컨테이너 실행 | `docker run [옵션] <이미지> [명령]` | 새 컨테이너 생성 후 명령 실행[^1] |
| 실행 중 컨테이너 조회 | `docker ps` | 동작 중인 컨테이너 리스트[^1] |
| 전체 컨테이너 조회 | `docker ps -a` | 중지된 컨테이너 포함 모든 리스트[^1] |
| 컨테이너 시작 | `docker start <컨테이너>` | 중지된 컨테이너 재시작[^1] |
| 컨테이너 중지 | `docker stop <컨테이너>` | 실행 중인 프로세스에 SIGTERM 전송 후 종료[^1] |
| 컨테이너 삭제 | `docker rm <컨테이너>` | 중지된 컨테이너 제거[^1] |
| 명령 실행 | `docker exec -it <컨테이너> <명령>` | 실행 중 컨테이너에 명령 전달[^1] |
| 로그 조회 | `docker logs <컨테이너>` | STDOUT/STDERR 로그 스트림 출력[^1] |

**주요 옵션:**

- `-d`: 백그라운드(detached) 모드
- `-p`: 호스트<->컨테이너 포트 매핑
- `-v`: 볼륨 마운트


## 4. 네트워크 관리

컨테이너 간 통신·격리를 위해 네트워크 생성·연결·검사·삭제가 필요하다.


| 기능 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 네트워크 목록 | `docker network ls` | 구성된 네트워크 표시[^1] |
| 네트워크 생성 | `docker network create <네트워크>` | 새 네트워크 생성[^1] |
| 네트워크 연결 | `docker network connect <네트워크> <컨테이너>` | 컨테이너를 네트워크에 연결[^1] |
| 네트워크 분리 | `docker network disconnect <네트워크> <컨테이너>` | 컨테이너 분리[^1] |
| 네트워크 삭제 | `docker network rm <네트워크>` | 네트워크 제거[^1] |
| 네트워크 상세 | `docker network inspect <네트워크>` | 구성·정책 정보 출력[^1] |

## 5. 볼륨 관리

데이터 지속성·공유를 위해 볼륨을 생성·마운트·삭제한다.


| 기능 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 볼륨 목록 | `docker volume ls` | 모든 볼륨 표시[^1] |
| 볼륨 생성 | `docker volume create <이름>` | 새 볼륨 생성[^1] |
| 볼륨 마운트 | `docker run -v <볼륨>:<경로> <이미지>` | 컨테이너에 볼륨 연결[^1] |
| 볼륨 정보 | `docker volume inspect <볼륨>` | 경로·드라이버 등 상세 정보[^1] |
| 볼륨 삭제 | `docker volume rm <볼륨>` | 사용되지 않는 볼륨 제거[^1] |

## 6. 레지스트리 상호작용

이미지 공유를 위한 레지스트리 로그인·푸시·풀·검색 기능 포함.


| 기능 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 로그인 | `docker login [레지스트리]` | 인증 정보 저장[^1] |
| 로그아웃 | `docker logout [레지스트리]` | 인증 정보 제거[^1] |
| 이미지 푸시 | `docker push <레포>/<이미지>:<태그>` | 레지스트리에 이미지 업로드[^1] |
| 이미지 풀 | `docker pull <이미지>:<태그>` | 레지스트리에서 이미지 다운로드[^1] |
| 이미지 검색 | `docker search <키워드>` | Hub에서 이미지 검색[^1] |

## 7. 시스템 관리

불필요 리소스 정리·정보 조회·데몬 제어를 통해 환경을 최적화한다.


| 기능 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 시스템 정보 | `docker system info` | 총 컨테이너·이미지·볼륨 현황[^1] |
| 리소스 정리 | `docker system prune` | 중지 컨테이너·미사용 네트워크·단일 이미지 삭제[^1] |
| 데몬 시작 | `sudo systemctl start docker` | Docker 서비스 시작[^1] |
| 데몬 상태 | `sudo systemctl status docker` | 서비스 상태 확인[^1] |
| 데몬 재시작 | `sudo systemctl restart docker` | 설정 변경 후 재시작[^1] |

## 8. Docker Compose

다중 컨테이너 애플리케이션 정의·실행 자동화를 지원한다.


| 기능 | 명령어 | 설명 |
| :-- | :-- | :-- |
| 서비스 시작 | `docker-compose up` | `docker-compose.yml` 기반 서비스 실행[^1] |
| 백그라운드 실행 | `docker-compose up -d` | 데몬 모드로 실행[^1] |
| 서비스 중지 | `docker-compose down` | 모든 서비스 종료·네트워크 삭제[^1] |
| 서비스 목록 | `docker-compose ps` | 현재 실행 서비스 표시[^1] |

## 9. 고급 사용법 및 팁

- **환경 변수**: `DOCKER_API_VERSION`, `DOCKER_HOST` 등을 설정해 CLI 동작 커스터마이징 가능.
- **멀티 스테이지 빌드**: 이미지 크기 최적화를 위해 여러 빌드 스테이지 활용.
- **헬스체크**: `HEALTHCHECK`으로 컨테이너 상태 자동 감시.
- **플러그인**: `docker plugin` 명령으로 확장 기능 설치·관리.

**핸드북 활용법:**
각 장별로 **예제**, **주요 옵션**, **베스트 프랙티스**를 익혀, 상황에 맞는 명령어 조합으로 운영 자동화·효율성을 극대화할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://www.grootan.com/blogs/docker-cli-commands-handbook-a-reference-cheatsheet/

[^2]: https://matsuand.github.io/docs.docker.jp.onthefly/engine/reference/commandline/docker/

[^3]: https://qiita.com/h-umatani/items/4eb2e106e9aa9d8cb602

[^4]: https://qiita.com/supaiku2452/items/da44b05c4ba4ec13bccf

[^5]: https://docs.docker.com/reference/

[^6]: https://matsuand.github.io/docs.docker.jp.onthefly/engine/reference/commandline/cli/

[^7]: https://spacelift.io/blog/docker-commands-cheat-sheet

[^8]: https://docs.docker.jp/engine/reference/commandline/index.html

[^9]: https://docs.docker.jp/v19.03/engine/reference/commandline/cli.html

[^10]: https://qiita.com/WebSysRider/items/585a90acd4d5abd27d69

[^11]: https://unclesnote.com/ja/240215120315/command_line_cli_handbook_for_using_docker

[^12]: https://www.kagoya.jp/howto/cloud/container/dockercommand/

[^13]: https://docs.docker.com/reference/cli/docker/

[^14]: https://sol-satoru.hatenablog.com/entry/2022/01/04/162855

[^15]: https://www.miraiserver.ne.jp/column/about_docker_command/

[^16]: https://docs.docker.jp/engine/reference/commandline/cli.html

[^17]: https://docs.docker.jp/engine/reference/index.html

[^18]: https://offers.jp/media/programming/a_4105

[^19]: https://docs.docker.com/get-started/docker_cheatsheet.pdf

[^20]: https://www.freecodecamp.org/news/the-docker-handbook/

