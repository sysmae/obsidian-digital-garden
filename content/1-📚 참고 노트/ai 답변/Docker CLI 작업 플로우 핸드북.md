---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Docker CLI 작업 플로우 핸드북

## 1. 초기 설정

Docker 데몬과 클라이언트가 정상 작동하도록 환경을 구성하는 단계이다.

1.1. Docker 상태 및 버전 확인

```bash
docker version              # 클라이언트·서버 버전 확인  
docker info                 # 데몬 설정·리소스 현황 조회  
```

1.2. 권한 설정

- Linux: `sudo groupadd docker` → `sudo usermod -aG docker $USER` 명령으로 현재 사용자를 docker 그룹에 추가.
- macOS/Windows: Docker Desktop 설치 후 로그인.

1.3. 기본 컨텍스트 설정

```bash
docker context ls           # 사용 가능한 컨텍스트 조회  
docker context use default  # 기본 컨텍스트 선택  
```

1.4. 도움말 및 구성 파일

```bash
docker help                 # 전체 명령어 목록  
docker <command> --help     # 개별 명령어 옵션 확인  
# 구성 파일 위치: ~/.docker/config.json
```


## 2. 이미지 관리 워크플로우

이미지를 **획득(Pull) → 생성(Build) → 태깅(Tag) → 배포(Push) → 정리(Remove)** 순으로 운영.

2.1. 레지스트리에서 이미지 가져오기

```bash
docker pull <레포지토리>/<이미지>:<태그>   # 이미지 다운로드  
docker search <키워드>                     # Docker Hub 검색  
```

2.2. Dockerfile로 이미지 빌드

```bash
docker build --no-cache -t <이미지>:<태그> <Dockerfile_경로>  
```

- `--pull` : 베이스 이미지 최신화
- 멀티스테이지 빌드 사용 권장

2.3. 이미지 태깅 및 푸시

```bash
docker tag <이미지>:<태그> <레포지토리>/<이름>:<태그>  
docker push <레포지토리>/<이름>:<태그>  
```

- CI/CD 파이프라인 내에서 자동 태깅(예: Git SHA)

2.4. 로컬 이미지 정리

```bash
docker images --filter dangling=true    # 태그 없는 이미지 조회  
docker rmi $(docker images -q --filter dangling=true)  
```


## 3. 컨테이너 관리 워크플로우

컨테이너를 **생성(Create) → 실행(Run) → 모니터링 → 테스트/디버그 → 종료(Stop) → 삭제(Remove)** 한다.

3.1. 컨테이너 생성 및 실행

```bash
# 포트 매핑, 볼륨 마운트, 환경 변수 설정 예시
docker run -d \
  --name webapp \
  -p 8080:80 \
  -v webdata:/var/www/html \
  -e NODE_ENV=production \
  <이미지>:<태그>
```

- `-d` : 백그라운드 모드
- `--rm` : 종료 시 자동 삭제

3.2. 라이브 모니터링

```bash
docker ps --format "table {{.Names}}\t{{.Status}}\t{{.Ports}}"  
docker logs -f <컨테이너>  
docker stats <컨테이너>               # 리소스 사용량  
```

3.3. 진단 및 디버깅

```bash
docker exec -it <컨테이너> /bin/bash  # 셸 진입  
docker inspect <컨테이너>             # 상세 메타데이터  
docker top <컨테이너>                 # 프로세스 목록  
```

3.4. 컨테이너 중지·재시작·삭제

```bash
docker stop <컨테이너>  
docker start <컨테이너>  
docker restart <컨테이너>  
docker rm <컨테이너>  
```


## 4. 네트워크 구축 워크플로우

컨테이너 간 통신을 위해 **네트워크 생성 → 컨테이너 연결 → 정책 설정 → 모니터링 → 정리** 과정을 따른다.

4.1. 네트워크 생성

```bash
docker network create \
  --driver bridge \
  --subnet 172.20.0.0/16 \
  app-network
```

4.2. 컨테이너 연결

```bash
docker network connect app-network webapp  
docker network disconnect app-network webapp  
```

4.3. 네트워크 정책 및 검사

```bash
docker network inspect app-network  
docker run --network=app-network <이미지>  
```

4.4. 네트워크 삭제

```bash
docker network rm app-network  
```


## 5. 데이터 퍼시스턴스 워크플로우

데이터 보존을 위해 **볼륨 생성 → 볼륨 마운트 → 백업/복원 → 정리** 처리.

5.1. 볼륨 생성 및 마운트

```bash
docker volume create dbdata  
docker run -d \
  --name db \
  -v dbdata:/var/lib/mysql \
  mysql:latest
```

5.2. 데이터 백업·복원

```bash
# 백업
docker run --rm \
  -v dbdata:/data \
  -v $(pwd):/backup \
  ubuntu \
  tar czf /backup/dbdata.tar.gz -C /data .

# 복원
docker run --rm \
  -v dbdata:/data \
  -v $(pwd):/backup \
  ubuntu \
  tar xzf /backup/dbdata.tar.gz -C /data
```

5.3. 볼륨 확인 및 삭제

```bash
docker volume ls  
docker volume inspect dbdata  
docker volume rm dbdata  
```


## 6. 오케스트레이션 워크플로우 (Compose)

여러 컨테이너를 **정의(Define) → 빌드(Build) → 배포(Up) → 스케일링 → 종료(Down) → 정리** 한다.

6.1. Compose 파일 예시 (`docker-compose.yml`)

```yaml
version: '3.8'
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
  api:
    build: ./api
    environment:
      - DB_HOST=db
  db:
    image: mysql:5.7
    volumes:
      - dbdata:/var/lib/mysql
volumes:
  dbdata:
```

6.2. 서비스 배포 및 관리

```bash
docker-compose up -d         # 빌드 + 실행  
docker-compose ps            # 서비스 상태  
docker-compose logs -f       # 전체 로그 모니터링  
docker-compose scale api=3   # api 서비스 3개 스케일링  
docker-compose down          # 모든 서비스 정리  
```


## 7. 시스템 유지·관리 워크플로우

장기 운영을 위해 **리소스 점검 → 정리 → 업그레이드 → 모니터링** 단계를 반복.

7.1. 리소스 현황

```bash
docker system df             # 디스크 사용량 요약  
docker system info           # 전체 리소스 현황  
```

7.2. 불필요 리소스 정리

```bash
docker system prune --volumes  # 중지 컨테이너·미사용 이미지·볼륨 삭제  
```

7.3. Docker 데몬 제어

```bash
sudo systemctl restart docker  
sudo journalctl -u docker -f   # 데몬 로그 실시간 모니터링  
```

7.4. 보안 업데이트

- OS 패치 → Docker 엔진 최신 버전으로 업그레이드
- 이미지 취약점 스캔: `docker scan <이미지>`


### 부록: 작업 플로우별 체크리스트

| 단계 | 주요 명령어 | 주요 옵션/파일 | 비고 |
| :-- | :-- | :-- | :-- |
| 초기 설정 | docker version, context use | ~/.docker/config.json | 권한·컨텍스트 확인 |
| 이미지 관리 | docker pull/build/tag/push | Dockerfile, 레포지토리 | CI/CD 통합 |
| 컨테이너 관리 | docker run/exec/logs | -p, -v, --rm | 디버깅·모니터링 |
| 네트워크 | docker network create/connect | bridge 드라이버 | 서비스 격리 |
| 볼륨 | docker volume create/inspect | 백업 스크립트 | 데이터 보존 |
| Compose | docker-compose up/scale/down | docker-compose.yml | 다중 컨테이너 |
| 시스템 관리 | docker system df/prune | systemctl | 리소스 정리 |

이 핸드북을 따라 작업 플로우별로 단계화된 명령어와 패턴을 숙지하면, **일관되고 효율적인 Docker 운영 환경**을 구현할 수 있다.

