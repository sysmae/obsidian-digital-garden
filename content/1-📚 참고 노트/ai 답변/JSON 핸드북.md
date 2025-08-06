---
{"publish":true,"created":"2025-08-05T05:19:59.478+09:00","modified":"2025-08-06T22:35:13.275+09:00","cssclasses":""}
---


<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# JSON 핸드북

**핵심 요약:**
JSON(JavaScript Object Notation)은 Douglas Crockford가 2001년에 웹 애플리케이션 간 실시간 데이터 교환을 위해 고안한 *경량·텍스트 기반의 데이터 교환 포맷*이다[^1]. 간결한 문법(객체·배열·원시값 6종)과 언어 독립성, 풍부한 활용 사례(API, 설정 파일, NoSQL 등)를 바탕으로 현재 모든 주요 프로그래밍 언어에서 지원되며, 웹·모바일·서버 애플리케이션의 *표준* 데이터 형식으로 자리잡았다[^2][^3].

## 1. 탄생 배경 및 역사

- **기원:** 2001년, Crockford와 Chip Morningstar가 브라우저 플러그인(Flash·Java Applet) 없이 HTTP 기반으로 양방향 세션을 유지하는 경량 프로토콜이 필요해 개발[^1].
- **이름 및 표준화:** JSML(JS Markup Language) vs JSON 토의 끝에 ‘JavaScript Object Notation’으로 채택. 2006년 RFC 4627로 소개, 2013년 ECMA-404·ISO/IEC 21778로 국제 표준화[^4].
- **철학:** *최소주의(minimal)*, *텍스트(textual)*, *JavaScript의 부분집합(subset)*이라는 설계 원칙을 고수하며 불필요한 기능 확장을 배제[^5].


## 2. 문법과 구조

### 2.1 기본 데이터 타입 6종

| 타입           | JSON 표기법       | 설명                  | 대응 언어 객체            |
| :----------- | :------------- | :------------------ | :------------------ |
| 객체(Object)   | `{}`           | 키-값 쌍의 집합           | dict (Python) 등     |
| 배열(Array)    | `[]`           | 순서 있는 값의 목록         | list (Python) 등     |
| 문자열(String)  | `"..."`        | UTF-8 인코딩 문자열       | str (Python) 등      |
| 숫자(Number)   | `123`, `3.14`  | 정수·실수 지원 (NaN·∞ 불가) | int·float (Python)  |
| 불리언(Boolean) | `true`/`false` | 논리값                 | True/False (Python) |
| 널(Null)      | `null`         | 값 부재                | None (Python)       |

### 2.2 문법 규칙

- **키·문자열**은 반드시 **쌍따옴표**로 감싸야 함.
- **마지막 요소 뒤**의 **콤마 금지**.
- **네스팅(nesting)**은 객체·배열 간 자유롭게 허용하나, 가독성·성능 고려 필요[^6].


### 2.3 예시

```json
{
  "user": {
    "id": 1001,
    "name": "홍길동",
    "roles": ["admin", "editor"],
    "profile": null
  }
}
```


## 3. 활용 사례

### 3.1 웹 API 데이터 교환

- **REST/GraphQL API** 요청·응답에 표준 포맷으로 사용.
- 자바스크립트 `JSON.parse()`, `JSON.stringify()`를 통해 객체 ↔ JSON 문자열 상호 변환이 용이[^2].


### 3.2 설정 파일 및 데이터 저장

- 애플리케이션 설정(config.json), 환경 변수, 매니페스트(manifest.json) 등에 적용.
- 가독성이 높아 DevOps·CI/CD 파이프라인에서 광범위하게 채택[^3].


### 3.3 NoSQL 문서 데이터베이스

- MongoDB·CouchDB 등 JSON 문서 지향 데이터베이스에서 네이티브 포맷으로 사용.
- RDBMS(MySQL·PostgreSQL)에서도 JSON 컬럼 타입 지원.


### 3.4 JSON Schema를 통한 검증·문서화

- 스키마 정의 언어(JSON Schema)로 데이터 구조·유효성 검사, UI 자동 생성, API 계약 명세 등에 활용[^7].
- 스키마 예시:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "title": "Product",
  "type": "object",
  "properties": {
    "id": { "type": "integer" },
    "name": { "type": "string" },
    "price": { "type": "number", "minimum": 0 }
  },
  "required": ["id", "name", "price"]
}
```


## 4. 심화 주제

### 4.1 보안 및 성능 고려사항

- **JSON Hijacking**, **XSS** 등 클라이언트 보안 취약점 주의.
- 대용량 데이터 전송 시 **스트리밍(JSON Stream)**, **압축** 기법 활용.


### 4.2 확장 포맷

- **JSON-LD**: 링크드 데이터 연동을 위한 시맨틱 확장.
- **BSON**: 바이너리 직렬화 형식, MongoDB 내부 포맷.
- **NDJSON**: Newline-delimited JSON, 로그 처리 등에 적합.


### 4.3 JSON 처리 도구

- **파싱/시리얼라이저:** RapidJSON, Jackson, Gson, Serde(Json) 등.
- **검증 라이브러리:** Ajv, Newtonsoft.Json.Schema 등.
- **문서화:** Swagger/OpenAPI, AsyncAPI 등과 연계.


### 결론

JSON은 **가독성, 경량성, 범용성**을 모두 갖춘 데이터 교환 포맷으로, 웹·모바일·서버·데이터베이스 등 현대 소프트웨어 전 영역에서 핵심 인프라로 자리매김했다. JSON의 구조와 사용법을 완벽히 이해하고, JSON Schema를 사용한 검증·문서화, 보안·성능 최적화 기법을 숙지하면 안정적이고 효율적인 데이터 처리 시스템을 구축할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/JSON

[^2]: https://www.w3schools.com/js/js_json_intro.asp

[^3]: https://www.oracle.com/jp/database/what-is-json/

[^4]: https://www.nic.ad.jp/ja/basics/terms/json.html

[^5]: https://www.wearedevelopers.com/en/magazine/481/douglas-crockford---json-and-beyond

[^6]: https://note.com/gorogoro_ch/n/nccaba08b722c

[^7]: https://json-schema.org/learn/json-schema-examples

[^8]: https://help.mabl.com/hc/ja/articles/19078205331348-JSONの構造と構文

[^9]: https://www.ibm.com/docs/en/datapower-gateway/10.6.0?topic=json-examples

[^10]: https://www.microfocus.com/documentation/silk-performer/195/en/silkperformer-195-webhelp-en/GUID-6AFC32B4-6D73-4FBA-AD36-E42261E2D77E.html

[^11]: https://json-5.com/what-is-json

[^12]: https://qiita.com/inokage/items/7ff5d5a41a960a75bfb7

[^13]: https://json-structure.org

[^14]: https://opensource.adobe.com/Spry/samples/data_region/JSONDataSetSample.html

[^15]: https://products.sint.co.jp/siob/blog/json

[^16]: https://help.rapid7.com/insightidr/content/json/overview.html

[^17]: https://dev.classmethod.jp/articles/concrete-example-of-json/

[^18]: https://datamix.co.jp/media/datascience/introduction-to-json/

[^19]: https://json.org/example.html

[^20]: https://www.hostinger.com/tutorials/what-is-json

[^21]: https://ja.wikipedia.org/wiki/JSON

[^22]: https://developer.mozilla.org/en-US/docs/Learn_web_development/Core/Scripting/JSON

[^23]: https://avato.co/developers/pros-cons-json-vs-xml/

[^24]: https://json-schema.org/learn/getting-started-step-by-step

[^25]: https://www.geekboots.com/story/json-with-advantage-and-disadvantage

[^26]: https://docs.pydantic.dev/latest/concepts/json_schema/

[^27]: https://hackernoon.com/the-history-of-json-and-the-people-that-created-it

[^28]: https://ezeelive.com/json-advantages-disadvantages/

[^29]: https://www.tohoho-web.com/ex/json-schema.html

[^30]: https://www.microsoft.com/en-us/research/video/the-json-saga/

[^31]: https://www.reddit.com/r/gamemaker/comments/ol364q/pros_and_cons_of_using_json_for_saving_loading/

[^32]: https://json-schema.org/overview/use-cases

[^33]: https://www.youtube.com/watch?v=-C-JoyNuQJs

[^34]: https://www.turing.com/kb/what-is-json

[^35]: https://qiita.com/g0e/items/9a4f886897fd46f107a8

[^36]: https://en.wikipedia.org/wiki/Douglas_Crockford

[^37]: https://coresignal.com/blog/json-vs-csv/

[^38]: https://zenn.dev/shibata/articles/6e17b79af4f819

[^39]: https://www.youtube.com/watch?v=xZdNGfFh5BY

[^40]: https://builtin.com/software-engineering-perspectives/yaml-vs-json

