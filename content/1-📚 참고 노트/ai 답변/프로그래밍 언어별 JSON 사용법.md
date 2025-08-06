---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" style="height:64px;margin-right:32px"/>

# 프로그래밍 언어별 JSON 사용법

## JavaScript

JavaScript는 JSON의 출발점 언어로, 언어 내장 객체인 `JSON`을 통해 직렬화·역직렬화를 지원한다.

- 직렬화: `JSON.stringify(value[, replacer, space])`
    - `replacer` 매개변수로 키 필터링 또는 값 변환 제어
    - `space` 매개변수로 들여쓰기 공백 수 지정
- 역직렬화: `JSON.parse(text[, reviver])`
    - `reviver` 콜백으로 파싱된 값별 추가 가공 가능

```javascript
const data = { x:10, y:20, tag: "point" };
const json = JSON.stringify(data, null, 2);
// 결과: "{\n  \"x\":10,\n  \"y\":20,\n  \"tag\":\"point\"\n}"
const obj  = JSON.parse(json, (key, val) => 
  typeof val==="number" ? val*2 : val
);
// obj.x ===20, obj.y ===40[^28]
```


## Python

Python 표준 라이브러리 `json` 모듈로 지원하며, 파일 입출력과 메모리 처리를 모두 제공[^1].

- 직렬화
    - `json.dumps(obj, ensure_ascii=False, indent=2)` → 문자열
    - `json.dump(obj, fp, ensure_ascii=False)` → 파일
- 역직렬화
    - `json.loads(text)` → 객체
    - `json.load(fp)` → 파일

```python
import json

data = {"name":"Bob","scores":[80,90,100]}
# 메모리에 직렬화
json_str = json.dumps(data, ensure_ascii=False, indent=4)
# 파일에 직렬화
with open("data.json","w",encoding="utf-8") as f:
    json.dump(data, f, ensure_ascii=False)

# 메모리 역직렬화
restored = json.loads(json_str)
# 파일 역직렬화
with open("data.json","r",encoding="utf-8") as f:
    loaded = json.load(f)[^24]
```


## Java

Jackson과 Gson 라이브러리를 통해 가장 널리 사용된다[^2]:

- **Jackson(ObjectMapper)**
    - 직렬화: `new ObjectMapper().writeValueAsString(obj)`
    - 역직렬화: `mapper.readValue(json, TypeReference)`
- **Gson**
    - 직렬화: `new Gson().toJson(obj)`
    - 역직렬화: `gson.fromJson(json, MyClass.class)`

```java
// Jackson 예시
ObjectMapper mapper = new ObjectMapper();
String json = mapper.writeValueAsString(person);
Person p = mapper.readValue(json, Person.class);

// Gson 예시
Gson gson = new GsonBuilder().setPrettyPrinting().create();
String text = gson.toJson(person);
Person p2 = gson.fromJson(text, Person.class)[^26];
```


## C\# (.NET)

.NET 5 이상에서는 `System.Text.Json`이 권장되며, 기존 `Newtonsoft.Json`(Json.NET)도 널리 사용된다[^3]:

- **System.Text.Json**
    - 직렬화: `JsonSerializer.Serialize(obj, options)`
    - 역직렬화: `JsonSerializer.Deserialize<T>(json, options)`
    - 옵션: `new JsonSerializerOptions { WriteIndented = true }`
- **Newtonsoft.Json**
    - 직렬화: `JsonConvert.SerializeObject(obj, Formatting.Indented)`
    - 역직렬화: `JsonConvert.DeserializeObject<T>(json)`

```csharp
// System.Text.Json 예시
var opts = new JsonSerializerOptions { WriteIndented = true };
string json = JsonSerializer.Serialize(user, opts);
User u = JsonSerializer.Deserialize<User>(json, opts);

// Newtonsoft.Json 예시
string json2 = JsonConvert.SerializeObject(user, Formatting.Indented);
User u2 = JsonConvert.DeserializeObject<User>(json2)[^21].
```


## 기타 언어

- **Go**: `encoding/json` 패키지
    - 직렬화: `json.Marshal(v)`
    - 역직렬화: `json.Unmarshal(data, &v)`
- **Ruby**: `require "json"`
    - `JSON.generate(obj)`/`JSON.parse(str)`
- **PHP**: `json_encode($obj)`/`json_decode($str, true)`
- **Dart(Flutter)**: `dart:convert`
    - `jsonEncode(obj)`/`jsonDecode(str)`
    - `json_serializable` 패키지로 코드 생성 지원[^4]

각 언어는 JSON 표준(rfc8259)에 기반해 쉽게 사용할 수 있도록 내장 함수 또는 표준 라이브러리를 제공하며, 대부분의 환경에서 **키–값 구조, 배열, 중첩 객체**를 그대로 변환하여 사용한다. 추가로 필드 이름 매핑, 커스텀 컨버터, 스트리밍 API 등을 이용해 복잡한 요구 사항도 충족할 수 있다.

<div style="text-align: center">⁂</div>

[^1]: https://stackoverflow.com/questions/3316762/what-is-deserialize-and-serialize-in-json

[^2]: https://blogs.oracle.com/javamagazine/post/java-json-serialization-jackson

[^3]: https://learn.microsoft.com/ja-jp/dotnet/standard/serialization/system-text-json/how-to

[^4]: https://docs.flutter.dev/data-and-backend/serialization/json

