---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Docker Compose 파일을 통한 YAML 문법 설명 핸드북

## 1. 최상위 구조

Docker Compose의 YAML 파일(`docker-compose.yml`)은 다음 **최상위(top-level) 요소**로 구성된다.

- **version**: Compose 파일 명세 버전 (예: `'3.8'`)
- **services**: 컨테이너 단위의 서비스 정의
- **networks**: 서비스 간 통신 네트워크 정의
- **volumes**: 데이터 지속성을 위한 볼륨 정의
- **configs**, **secrets**: 설정·비밀값 관리(옵션)

```yaml
version: '3.8'

services:
  # 서비스 정의 블록

networks:
  # 네트워크 정의 블록

volumes:
  # 볼륨 정의 블록

configs:
  # 설정(configs) 정의

secrets:
  # 비밀(secrets) 정의
```


## 2. version

- 문자열로 명시 (`'2.4'`, `'3.9'` 등)
- 최신 기능을 사용하려면 3.x 계열 권장

```yaml
version: '3.8'
```


## 3. services

각 서비스는 컨테이너 하나를 나타내며, 서비스 이름으로 블록을 시작한다.

```yaml
services:
  web:
    image: nginx:latest        # 이미지 이름
    ports:
      - "80:80"                 # 호스트:컨테이너 포트 매핑
    environment:
      - ENV=production         # 환경변수
    volumes:
      - web-data:/usr/share/nginx/html
    networks:
      - front-net

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydb        # key: value 형태도 가능
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - back-net
```

- **image** 또는 **build**(경로/객체) 중 하나 필수
- **ports**, **environment**, **volumes**, **networks**, **depends_on** 등 다양한 옵션 지원


## 4. networks

서비스 간 통신 채널을 정의한다.

```yaml
networks:
  front-net:
    driver: bridge
  back-net:
    driver: bridge
```

- 기본 드라이버는 `bridge`
- `external: true`로 외부 네트워크 참조 가능


## 5. volumes

컨테이너 재시작·삭제 시에도 데이터 유지용 볼륨 정의.

```yaml
volumes:
  web-data:
  db-data:
```

- 서비스의 `volumes` 키에서 참조
- 드라이버·옵션 지정 가능


## 6. configs \& secrets

### configs

Swarm 모드에서 설정 파일 단일 관리

```yaml
configs:
  app-config:
    file: ./config/app.conf

services:
  web:
    configs:
      - source: app-config
        target: /etc/app.conf
```


### secrets

민감 정보(비밀번호·키) 파일로 주입

```yaml
secrets:
  db_pass:
    file: ./secrets/db_pass.txt

services:
  db:
    image: postgres:13
    secrets:
      - db_pass
```


## 7. YAML 문법 포인트

- **들여쓰기**: 스페이스만, 탭 금지
- **시퀀스**: `- item` 또는 `[a, b]`
- **매핑**: `key: value` 또는 `{k: v}`
- **스칼라 표기**: 따옴표 생략·`'single'`·`"double"`
- **주석**: `# 설명`
- **멀티 문서**: Compose에선 사용하지 않음


## 8. 예시 전체 구조

```yaml
version: '3.8'

services:
  web:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - web-data:/usr/share/nginx/html
    networks:
      - front-net

  db:
    image: postgres:13
    environment:
      POSTGRES_DB: mydb
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
    volumes:
      - db-data:/var/lib/postgresql/data
    networks:
      - back-net
    secrets:
      - db_pass

networks:
  front-net:
    driver: bridge
  back-net:
    driver: bridge

volumes:
  web-data:
  db-data:

secrets:
  db_pass:
    file: ./secrets/db_pass.txt
```

이 예시를 통해 Docker Compose 파일에서 **최상위 요소**, **서비스 옵션**, **네트워크·볼륨 정의**, **configs·secrets 관리**를 포함한 YAML 문법 활용법을 한눈에 파악할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://www.geeksforgeeks.org/devops/docker-compose-yaml-explained-a-deep-dive-into-configuration/

[^2]: https://qiita.com/zembutsu/items/9e9d80e05e36e882caaa

[^3]: https://docs.docker.com/compose/how-tos/use-secrets/

[^4]: https://docs.divio.com/reference/docker-docker-compose/

[^5]: https://matsuand.github.io/docs.docker.jp.onthefly/compose/compose-file/

[^6]: https://matsuand.github.io/docs.docker.jp.onthefly/compose/reference/

[^7]: https://qiita.com/gon0821/items/77369def082745d19c38

[^8]: https://docs.docker.jp/v1.12/compose/compose-file.html

[^9]: https://stackoverflow.com/questions/78096903/docker-compose-file-set-keys-from-environment

[^10]: https://docs.docker.com/compose/

[^11]: https://www.okteto.com/docs/reference/docker-compose/

[^12]: https://docs.docker.jp/v1.9/compose/compose-file.html

[^13]: https://docs.docker.com/reference/compose-file/

[^14]: https://matsuand.github.io/docs.docker.jp.onthefly/compose/compose-file/compose-file-v3/

[^15]: https://qiita.com/ryuichi1208/items/8b5adc88a93319cb76a2

[^16]: https://docs.docker.com/compose/gettingstarted/

[^17]: https://docs.docker.com/reference/compose-file/services/

[^18]: https://qiita.com/takkii1010/items/7ef76673cfe04135c39b

[^19]: https://docs.docker.jp/compose/compose-file/compose-file-v3.html

[^20]: https://dexall.co.jp/articles/?p=3376

