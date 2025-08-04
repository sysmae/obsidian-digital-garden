---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Dockerfile 핸드북

## 핵심 요약

**Dockerfile은 컨테이너 이미지를 “코드로” 정의·구축하는 설계도**로, 일관성·재현성이 뛰어난 개발·배포 환경을 자동화하는 데 필수적이다. Dockerfile의 구조, 주요 명령어, 레이어 개념, 멀티스테이지 빌드, 그리고 최적화·보안 관점의 베스트 프랙티스를 단계별로 상세히 정리하였다.

## 1. Dockerfile 개요

Dockerfile은 Docker 이미지 빌드 과정을 명령어 형태로 텍스트에 기록한 파일이다.

- **역사적 배경**: 가상 머신보다 경량화된 컨테이너 환경이 대두되며, 인프라를 코드(IaC)로 관리하려는 필요성에서 등장.
- **주요 역할**

1. **설계도**: `FROM`으로 베이스 이미지를 지정하고, 필요한 패키지 설치·환경 설정·파일 복사·실행 커맨드를 순서대로 나열.
2. **재현성 보장**: 동일한 Dockerfile로 언제 어디서나 동일한 이미지 생성.
3. **자동화·버전 관리**: CI/CD 파이프라인에 통합하여 빌드 자동화, 변경 이력 기록.


## 2. Dockerfile 구조

Dockerfile은 **파서 디렉티브**, **주요 명령어(INSTRUCTION)**, **주석(Comment)**로 구성된다.

```
# Parser directive (예: syntax=…)
# Comment
INSTRUCTION arguments
```

- **대소문자 구분 없음**(관례상 UPPERCASE)
- **첫 명령은 반드시 FROM**: 베이스 이미지를 선언하지 않으면 빌드 불가[^1].


## 3. 레이어와 캐싱

- 각 명령어는 **읽기 전용 레이어**를 생성하며, 이전 레이어의 상태를 스냅샷으로 이어받음[^2].
- 빌드 과정에서 `RUN`, `COPY`, `ADD` 명령어가 레이어를 추가.
- 레이어 캐싱으로 **변경이 없으면 재사용** → 빌드 속도 최적화.
- **레이어 수 최소화**: `&&`로 명령 결합, 불필요 파일 삭제 권장.


## 4. 주요 명령어(INSTRUCTION)

| 명령어         | 기능             | 예시                                              |     |         |
| :---------- | :------------- | :---------------------------------------------- | --- | ------- |
| FROM        | 베이스 이미지 지정     | `FROM ubuntu:22.04`[^1]                         |     |         |
| ARG         | 빌드 시 변수 선언     | `ARG VERSION=1.0`                               |     |         |
| ENV         | 환경 변수 설정       | `ENV APP_ENV=production`                        |     |         |
| WORKDIR     | 작업 디렉토리 변경     | `WORKDIR /app`                                  |     |         |
| COPY        | 호스트 파일 복사      | `COPY src/ /app/src/`                           |     |         |
| ADD         | 파일·URL 추가      | `ADD archive.tar.gz /app/`                      |     |         |
| RUN         | 이미지 빌드 중 명령 실행 | `RUN apt-get update && apt-get install -y curl` |     |         |
| LABEL       | 메타데이터 추가       | `LABEL maintainer="dev@example.com"`            |     |         |
| EXPOSE      | 컨테이너 포트 문서화    | `EXPOSE 80`                                     |     |         |
| USER        | 실행 사용자 지정      | `USER appuser`                                  |     |         |
| ENTRYPOINT  | 실행 진입점 설정      | `ENTRYPOINT ["npm","start"]`                    |     |         |
| CMD         | 기본 실행 명령 지정    | `CMD ["python","app.py"]`                       |     |         |
| VOLUME      | 볼륨 마운트 지점 선언   | `VOLUME ["/data"]`                              |     |         |
| HEALTHCHECK | 컨테이너 상태 검사     | `HEALTHCHECK CMD curl -f http://localhost/      |     | exit 1` |
| ONBUILD     | 하위 이미지 빌드시 트리거 | `ONBUILD COPY . /src`                           |     |         |
| STOPSIGNAL  | 종료 시그널 설정      | `STOPSIGNAL SIGTERM`                            |     |         |
| SHELL       | 기본 셸 변경        | `SHELL ["/bin/bash","-c"]`                      |     |         |

## 5. 멀티스테이지 빌드(Multi-Stage Builds)

여러 `FROM`을 사용해 **빌드·런타임 단계를 분리**하여 최종 이미지를 슬림화[^3]:

```dockerfile
# 빌드 단계
FROM golang:1.24 AS builder
WORKDIR /src
COPY . .
RUN go build -o app

# 런타임 단계
FROM alpine:3.17
WORKDIR /app
COPY --from=builder /src/app .
CMD ["./app"]
```

- **장점**: 빌드 툴·중간 산출물 제거, 최종 이미지 경량화
- **단계별 이름 지정**(`AS <name>`)으로 복사 시 안정성 확보


## 6. Dockerfile 최적화 및 보안 베스트 프랙티스

### 6.1 이미지 경량화

- **불필요 패키지 제거**: `RUN apt-get purge -y … && rm -rf /var/lib/apt/lists/*`
- **단일 RUN**: `&&`로 여러 명령 결합하여 레이어 축소
- **.dockerignore** 사용으로 빌드 컨텍스트 경량화


### 6.2 보안 강화

- **비루트 사용자** 생성·사용 (`USER appuser`)
- **신뢰된 베이스 이미지** 선택, 정기 업데이트[^4]
- **비밀정보 제거**: 환경변수에 노출 금지, 빌드 인수(`ARG`) 활용
- **이미지 스캔·린팅**: HADOLINT, Trivy 등 도구로 CI 파이프라인 통합


### 6.3 유지보수성

- **메타데이터 라벨** 추가: 버전, 연락처, 설명 등[^4]
- **가독성**: 논리적 섹션 분리, 주석 활용
- **ONBUILD 최소화**: 예측 불가 트리거 주의


## 7. 사용 예시

1. **간단한 Node.js 앱**

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
USER node
CMD ["node","index.js"]
```

2. **Python ML 환경**

```dockerfile
FROM python:3.11-slim
WORKDIR /workspace
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
ENTRYPOINT ["python","train.py"]

각 명령어 상세 설명:

1. WORKDIR  
- 기능: 컨테이너 내부에서 이후 RUN·CMD·ENTRYPOINT·COPY·ADD 명령이 실행될 작업 디렉토리를 설정.  
- 동작  
  • 디렉토리가 없으면 자동 생성  
  • 상대 경로 지정 시 이전 WORKDIR 기준으로 해석  
  • ENV로 설정된 환경변수 치환 가능  
- 예시  
  WORKDIR /app  
  이후 COPY . . 은 /app 이하로 복사됨.

2. COPY  
- 기능: 빌드 컨텍스트 또는 지정된 빌드 스테이지(–from)에서 파일/디렉토리를 이미지에 복사.  
- 양식  
  COPY [--from=<stage>] [--chown=<user>:<group>] <src>… <dest>  
- 주요 옵션  
  • --from: 멀티스테이지 빌드 시 다른 단계에서 복사  
  • --chown: 복사 대상 소유자 변경 (UID:GID)  
  • 와일드카드·여러 src 지원, dest가 절대경로(/)면 루트 기준, 아니면 WORKDIR 기준  
- 예시  
  COPY package*.json ./      # 현재 WORKDIR에 package*.json 복사

3. RUN npm ci --only=production  
- 기능: 이미지 빌드 중 셸 명령 실행 후 결과 레이어 커밋  
- Shell 폼: RUN <cmd>… (쉘 해석, 변수 확장·&& 사용 가능)  
- Exec 폼: RUN ["cmd","arg"] (JSON 형태, 쉘 미사용)  
- 캐시: 이전과 동일 명령 시 레이어 캐시 재사용

4. USER  
- 기능: 이후 명령(RUN·CMD·ENTRYPOINT) 실행 사용자/그룹 지정  
- 형식: USER <username|UID>[:<group>|<GID>]  
- 보안: 비루트 사용자로 전환해 최소 권한 실행 권장  
- 예시  
  USER node               # node 사용자로 변경

5. CMD ["node","index.js"] / ENTRYPOINT ["python","train.py"]  
- CMD: 컨테이너 실행 시 기본 인수 지정, docker run 시 덮어쓰기 가능  
- ENTRYPOINT: 컨테이너 진입점 설정, CMD는 인수 역할  
- 둘 결합: ENTRYPOINT 고정 + CMD로 디폴트 인수 제공  
- Shell vs Exec: ENTRYPOINT exec 폼 권장

6. RUN pip install --no-cache-dir -r requirements.txt  
- 기능: Python 패키지 설치  
- --no-cache-dir: 캐시 저장 안 해 이미지 용량 최소화

7. ENTRYPOINT ["python","train.py"]  
- 기능: 컨테이너 시작 진입점 지정  
- CMD vs ENTRYPOINT 차이: ENTRYPOINT 고정, CMD만 덮어쓰기


```

3. **멀티스테이지 Go 앱**
위 5장 예시 참조.

## 8. 활용 가이드

1. **버전 핀 고정**: `FROM ubuntu:22.04` 처럼 태그·다이제스트로 잠그기.
2. **빌드·런타임 분리**: 멀티스테이지 활용해 용량·보안 강화.
3. **자동화 파이프라인**: CI에서 린팅·스캔·주기적 재빌드 시행.
4. **문서화·공유**: Git 저장소에 Dockerfile 표준 템플릿 제공.

이 핸드북을 참고하여 Dockerfile을 체계적으로 설계·작성하면, 일관된 개발환경 제공과 운영 효율성 극대화, 보안 강화라는 세 마리 토끼를 잡을 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://docs.docker.com/reference/dockerfile/

[^2]: https://qiita.com/marumeru/items/5b9d19508fa649d060a7

[^3]: https://docs.docker.com/build/building/multi-stage/

[^4]: https://sysdig.com/learn-cloud-native/dockerfile-best-practices/

[^5]: https://qiita.com/iaoiui/items/bd88509dc513f7a1a8f4

[^6]: https://www.issoh.co.jp/tech/details/3315/

[^7]: https://www.designet.co.jp/faq/term/?id=RG9ja2VyZmlsZQ

[^8]: https://docs.docker.jp/engine/reference/commandline/history.html

[^9]: https://aiacademy.jp/media/?p=1386

[^10]: https://www.kagoya.jp/howto/cloud/container/dockerimage/

[^11]: https://docs.docker.jp/develop/develop-images/dockerfile_best-practices.html

[^12]: https://qiita.com/hahta3/items/c883504a8dfbc5662b14

[^13]: https://docs.docker.jp/engine/reference/builder.html

[^14]: https://qiita.com/suzu12/items/c4bc47c0df6ec9b9290b

[^15]: https://zenn.dev/supersatton/articles/bcc7b57fbd53e7

[^16]: https://www.cloudbees.com/blog/what-is-a-dockerfile

[^17]: https://alterbo.jp/blog/ryu2-2106/

[^18]: https://docs.docker.jp/engine/reference/commandline/image_history.html

[^19]: https://qiita.com/ikemura-ren/items/3debbe2ba8997a95e6b5

[^20]: https://scrapbox.io/sunabako/docker_image%E3%81%8B%E3%82%89Dockerfile%E3%82%92%E5%BE%A9%E5%85%83%E3%81%99%E3%82%8B%E6%99%82%E3%81%AB%E4%BD%BF%E3%81%86docker_history

[^21]: https://knowledge.sakura.ad.jp/15253/

[^22]: https://zenn.dev/suzuki_hoge/books/2022-03-docker-practice-8ae36c33424b59/viewer/2-8-dockerfile

[^23]: https://github.com/dnaprawa/dockerfile-best-practices

[^24]: https://qiita.com/SNobu/items/8cbcad0edbe41cd34d27

[^25]: https://matsuand.github.io/docs.docker.jp.onthefly/develop/develop-images/multistage-build/

[^26]: https://dev.to/soutoigor/dockerfile-main-commands-and-instructions-2l2h

[^27]: https://zenn.dev/hakshu/articles/docker-multi-stage-build

[^28]: https://qiita.com/minamijoyo/items/711704e85b45ff5d6405

[^29]: https://docs.docker.jp/engine/articles/dockerfile_best-practice.html

[^30]: https://kapeli.com/cheat_sheets/Dockerfile.docset/Contents/Resources/Documents/index

[^31]: https://qiita.com/come2ry/items/98b0bb70e29821b52ba0

[^32]: https://zenn.dev/mutex_inc/articles/nodejs-ts-docker-best-practice

[^33]: https://docs.docker.jp/develop/develop-images/multistage-build.html

[^34]: https://zenn.dev/forcia_tech/articles/20210716_docker_best_practice

[^35]: https://qiita.com/FumiyaShibusawa/items/a0be39d28139a044157d

[^36]: https://blog.adglobe.co.jp/entry/2023/06/23/100000

[^37]: https://speakerdeck.com/devops_vtj/jin-sarawen-kenaidockerru-men-dockerfilenobesutopurakuteisubian

