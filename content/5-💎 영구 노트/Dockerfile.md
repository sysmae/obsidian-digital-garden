---
publish: true
---
#2025-08-05 04:23

Status: 

Tags: [[3-🏷️ 태그/도커]]

# Dockerfile
- [[5-💎 영구 노트/도커 이미지]] 를 만들때 사용하는 파일
- 도커 이미지 빌드 과정을 명령어 형태로 텍스트에 기록한 파일
- [[5-💎 영구 노트/컨테이너]] [[5-💎 영구 노트/도커 이미지]]를 코드로 정의, 구축 하는 설계도
- 파일 만들고 나서 docker build 명령어로 도커 이미지 생성 가능
## 주요 명령어
- FROM : 베이스 이미지 생성
- COPY : 파일 복사(이동) : 호스트 컴퓨터 파일 복사 -> 컨테이너 전달
	- COPY {호스트 컴퓨터에 있는 복사할 파일의 경로} {컨테이너에서 파일이 위치할 경로}
- ENTRYPOINT : '컨테이너가 시작'('컨테이너 생성 직후')할 때 실행되는 명령어
- RUN : '이미지를 생성하는 과정'에서 사용할 명령문 실행 
- WORKDIR: 작업 디렉토리 지정
- EXPOSE: 컨테이너 내부 사용중 포트 문서화(실제 작동 영향X)

| 명령어         | 기능             | 예시                                              |
| :---------- | :------------- | :---------------------------------------------- |
| FROM        | 베이스 이미지 지정     | `FROM ubuntu:22.04`[^1]                         |
| ARG         | 빌드 시 변수 선언     | `ARG VERSION=1.0`                               |
| ENV         | 환경 변수 설정       | `ENV APP_ENV=production`                        |
| WORKDIR     | 작업 디렉토리 변경     | `WORKDIR /app`                                  |
| COPY        | 호스트 파일 복사      | `COPY src/ /app/src/`                           |
| ADD         | 파일·URL 추가      | `ADD archive.tar.gz /app/`                      |
| RUN         | 이미지 빌드 중 명령 실행 | `RUN apt-get update && apt-get install -y curl` |
| LABEL       | 메타데이터 추가       | `LABEL maintainer="dev@example.com"`            |
| EXPOSE      | 컨테이너 포트 문서화    | `EXPOSE 80`                                     |
| USER        | 실행 사용자 지정      | `USER appuser`                                  |
| ENTRYPOINT  | 실행 진입점 설정      | `ENTRYPOINT ["npm","start"]`                    |
| CMD         | 기본 실행 명령 지정    | `CMD ["python","app.py"]`                       |
| VOLUME      | 볼륨 마운트 지점 선언   | `VOLUME ["/data"]`                              |
| HEALTHCHECK | 컨테이너 상태 검사     | `HEALTHCHECK CMD curl -f http://localhost/      |
| ONBUILD     | 하위 이미지 빌드시 트리거 | `ONBUILD COPY . /src`                           |
| STOPSIGNAL  | 종료 시그널 설정      | `STOPSIGNAL SIGTERM`                            |
| SHELL       | 기본 셸 변경        | `SHELL ["/bin/bash","-c"]`                      |
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci -only=production
COPY . . 
USER node
CMD ["node", "index.js"]
```

### 종료된 컨테이너 들어가서 디버깅
- 컨테이너가 바로 종료되지 않게 일시정지 명령어 걸고 디버깅
```dockerfile 
FROM openjdk:17-jdk 
...
ENTRYPOINT ["/bin/bash", "-c", "sleep 500"] # 500초 동안 시스템을 일시정지 시키는 명령어
```

## References
[[5-💎 영구 노트/도커 명령어]]

[[1-📚 참고 노트/ai 답변/Dockerfile 핸드북]]