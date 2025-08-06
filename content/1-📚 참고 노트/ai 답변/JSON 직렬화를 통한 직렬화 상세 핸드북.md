---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# JSON 직렬화를 통한 직렬화 상세 핸드북

**핵심 요약**
JSON(JavaScript Object Notation) 직렬화는 객체의 상태를 텍스트 기반의 JSON 문자열로 변환해 저장·전송하고, 역직렬화를 통해 원본 객체로 복원하는 과정이다. 경량 포맷 특성 덕분에 웹 API, 모바일 애플리케이션, IoT, 분산 시스템 등 다양한 환경에서 주로 사용된다[^1].

## 1. JSON 기본 구조와 문법

JSON은 여섯 가지 기본 자료형과 두 가지 복합 구조로 이루어진다[^1]:

- 원시값: 숫자(Number), 문자열(String), 불리언(Boolean: true/false), null
- 복합값: 객체(Object: `{}`), 배열(Array: `[]`)

객체는 키–값 쌍으로, 배열은 값들의 순차적 나열로 표현된다.

```json
{
  "id": 123,                  // 숫자
  "name": "Alice",            // 문자열
  "active": true,             // 불리언
  "roles": ["admin","user"],  // 배열
  "profile": {                // 중첩 객체
    "age": 30,
    "email": null
  }
}
```


## 2. 직렬화·역직렬화 과정

1. **직렬화(Serialization)**
    - 프로그래밍 언어 객체 → JSON 문자열
    - 예: `JSON.stringify(obj)` 또는 Python `json.dumps(obj)`[^2][^3]
2. **역직렬화(Deserialization)**
    - JSON 문자열 → 프로그래밍 언어 객체
    - 예: `JSON.parse(text)` 또는 Python `json.loads(text)`[^2][^3]
```javascript
// JavaScript 예시
const obj = { foo: [1,4,7], bar: "baz" };
const jsonStr = JSON.stringify(obj);  // '{"foo":[1,4,7],"bar":"baz"}'
const restored = JSON.parse(jsonStr); // { foo: [1,4,7], bar: "baz" }
```


## 3. 언어별 구현 방식

### 3.1 JavaScript

- 내장 함수: `JSON.stringify()`, `JSON.parse()`
- 커스텀 리플레이서(replacer)·리바이브(reviver) 기능으로 직렬화 제어 가능


### 3.2 Python

- 표준 모듈: `import json`
- `json.dumps()`/`json.dump()` → 문자열/파일
- `json.loads()`/`json.load()` → 메모리/파일[^3]


### 3.3 C\# (.NET)

- `System.Text.Json`: `JsonSerializer.Serialize()`/`JsonSerializer.Deserialize<T>()`[^4]
- `Newtonsoft.Json`(Json.NET) 라이브러리 활용 가능


### 3.4 Java

- Jackson: `ObjectMapper.writeValueAsString()`/`readValue()`[^5]
- Gson: `new Gson().toJson()`/`fromJson()`


### 3.5 Dart(Flutter)

- 내장 `dart:convert`: `jsonEncode()`/`jsonDecode()`
- 자동화: `json_serializable` 코드 생성[^6]


## 4. 고급 기능 및 옵션

### 4.1 포맷 제어

- ** 들여쓰기·개행**
    - JavaScript: `JSON.stringify(obj, null, 2)`
    - .NET: `JsonSerializerOptions.WriteIndented = true`[^4]
- **날짜 형식**
    - ISO 8601 문자열로 저장하거나, 타임스탬프로 변환


### 4.2 커스텀 변환

- **리플레이서(replacer)**: 특정 키·값 필터링
- **리바이브(reviver)**: 파싱 시 타입 변환(JavaScript)
- **커스텀 컨버터**: .NET `JsonConverter` 구현[^7]


### 4.3 스트리밍 직렬화

대용량 데이터 처리 시 전체 문자열 생성 대신 스트림으로 직렬화·역직렬화하여 메모리 사용량 최소화

## 5. 활용 및 주의 사항

### 5.1 활용 사례

- **웹 API**: 클라이언트-서버 간 RESTful 통신
- **구성 파일**: 설정·스크립트 적재
- **로그 및 이벤트**: 비정형 로그 저장
- **모바일 앱**: 네이티브 데이터 교환


### 5.2 성능 최적화

- 작은 페이로드: 기본 `stringify` 방식
- 대용량/실시간: 스트리밍 또는 이진 포맷(protobuf·MessagePack) 고려


### 5.3 보안 취약점

- 역직렬화 공격: `eval` 사용 금지, `JSON.parse` 권장[^1]
- 입력 검증: 외부 입력 JSON 구조·스키마 검증


### 5.4 스키마 정의

- OpenAPI·JSON Schema로 API 계약 문서화
- 자동 검증·문서 생성 지원

**결론**
JSON 직렬화는 가독성과 범용성, 언어 독립성을 갖춘 경량 포맷으로, 다양한 플랫폼·언어에서 표준 기능으로 제공된다. 복잡한 데이터 구조에도 대응 가능하며, 필요에 따라 포맷 제어, 커스텀 변환, 스트리밍 방식을 활용해 성능·보안·유연성을 높일 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://ja.wikipedia.org/wiki/JSON

[^2]: https://www.w3schools.com/js/js_json_intro.asp

[^3]: https://stackoverflow.com/questions/3316762/what-is-deserialize-and-serialize-in-json

[^4]: https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/how-to

[^5]: https://blogs.oracle.com/javamagazine/post/java-json-serialization-jackson

[^6]: https://docs.flutter.dev/data-and-backend/serialization/json

[^7]: https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/converters-how-to

[^8]: https://qiita.com/skm_bnn/items/ecf3d0fe1188e666d079

[^9]: https://qiita.com/rokuosan/items/da6f3c43158859eaa50a

[^10]: https://celerdata.com/glossary/what-is-the-role-of-data-serialization

[^11]: https://kotlinlang.org/docs/serialization.html

[^12]: https://help.mabl.com/hc/ja/articles/19078205331348-JSONの構造と構文

[^13]: https://stackoverflow.com/questions/75164713/how-do-i-make-json-serialization-use-the-same-case-as-the-object-property

[^14]: https://docs.unity3d.com/ja/2018.4/Manual/JSONSerialization.html

[^15]: https://tex2e.github.io/rfc-translater/html/rfc8259.html

[^16]: https://aws.amazon.com/compare/the-difference-between-yaml-and-json/

[^17]: https://datamix.co.jp/media/datascience/introduction-to-json/

[^18]: https://json-5.com/what-is-json

[^19]: https://qiita.com/SY81517/items/1cf6246dd99869f7b9c5

[^20]: https://www.json.org

