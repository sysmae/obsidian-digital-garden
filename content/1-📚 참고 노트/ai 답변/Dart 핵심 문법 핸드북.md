---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Dart 핵심 문법 핸드북

**핵심 요약**
Dart는 **구글**이 개발한 클래스 기반 객체지향 언어로, **정적 타입 추론**, **사운드 널 안전성**, **고급 비동기 처리**를 통해 생산성과 안정성을 동시에 제공합니다. 본 핸드북은 Dart의 기초 문법과 구조를 3단계 더 깊이 있게 살펴봅니다.

## 1. 기본 구조 및 진입점

Dart 애플리케이션은 최상위 `main()` 함수에서 시작합니다.

```dart
void main(List<String> args) {
  print('Hello, Dart!');  // 콘솔 출력
}
```

- `void`: 반환값이 없음을 명시
- `List<String> args`: 커맨드라인 인자 수신[^1]


## 2. 변수와 타입

### 2.1 타입 선언 방식

1. **명시적 타입**

```dart
String name = 'Voyager';
int year = 1977;
double diameter = 3.7;
bool isLaunchSuccess = true;
```

2. **타입 추론 (`var`)**

```dart
var mission = 'Mars Rover';  // String
var count = 3;              // int
```

3. **불변 변수**
    - `final`: 런타임에 단 한 번만 할당
    - `const`: 컴파일 시점에 상수화

```dart
final launchDate = DateTime(2020, 7, 30);
const pi = 3.14159;
```

4. **널러블 타입**
    - `String? nickname;`  // null 허용
    - 비널러블 타입에 `!`로 강제 언래핑 가능

```dart
String? codeName;
print(codeName!.length);  // 런타임 오류 발생 가능
```


### 2.2 컬렉션

- **리스트**

```dart
List<String> planets = ['Mercury', 'Venus'];
var moons = <String>['Io','Europa'];
```

- **맵**

```dart
Map<String,int> scores = {'Alice': 90, 'Bob': 85};
var config = <String, dynamic>{'debug': true};
```

- **세트**

```dart
var unique = <int>{1,2,3};
```


## 3. 제어 흐름

### 3.1 조건문

```dart
if (year >= 2000) {
  print('21st century');
} else if (year >= 1900) {
  print('20th century');
} else {
  print('Earlier');
}
```

- 삼항 연산자: `condition ? expr1 : expr2`
- `switch`–`case`: 다중 분기


### 3.2 반복문

- **for-in**

```dart
for (var planet in planets) {
  print(planet);
}
```

- **전통적 for**

```dart
for (int i = 0; i < 10; i++) { /* ... */ }
```

- **while / do-while**

```dart
while (count < 5) { count++; }
do { process(); } while (check());
```

- `break`, `continue` 지원[^1]


## 4. 함수와 메서드

### 4.1 기본 함수

```dart
int add(int a, int b) {
  return a + b;
}
```


### 4.2 화살표 함수

```dart
int square(int x) => x * x;
```

- **익명 함수**: `list.where((x) => x.isEven).forEach(print);`


### 4.3 매개변수

- **위치 매개변수** (기본)
- **선택적 위치 매개변수**: `foo(int x, [int? y])`
- **이름 붙인 매개변수**:

```dart
void config({bool debug = false, required String env}) { /* ... */ }
```

- **기본값 지정**, **`required`** 키워드[^1]


## 5. 클래스와 객체지향

### 5.1 클래스 선언

```dart
class Spacecraft {
  String name;
  DateTime? launchDate;
  
  // 기본 생성자
  Spacecraft(this.name, this.launchDate);
  
  // 명명 생성자
  Spacecraft.unlaunched(String name) : this(name, null);
  
  // 게터
  int? get launchYear => launchDate?.year;
  
  // 메서드
  void describe() {
    print('Spacecraft: $name');
  }
}
```


### 5.2 상속과 오버라이드

```dart
class Orbiter extends Spacecraft {
  double altitude;
  Orbiter(String name, DateTime? date, this.altitude)
      : super(name, date);
  
  @override
  void describe() {
    super.describe();
    print('Altitude: $altitude km');
  }
}
```


### 5.3 믹스인 \& 인터페이스

- **Mixin** 선언: `mixin Piloted { int crew = 1; }`
- 사용: `class Shuttle extends Spacecraft with Piloted {}`
- **인터페이스**: 모든 클래스는 암묵적 인터페이스 제공
- **추상 클래스**: `abstract class Describable { void describe(); }`[^1]


## 6. 비동기 처리

### 6.1 Future / async-await

```dart
Future<String> fetchData() async {
  await Future.delayed(Duration(seconds:1));
  return 'Data';
}

void main() async {
  var data = await fetchData();
  print(data);
}
```


### 6.2 스트림 생성 (`async*`)

```dart
Stream<int> counter(int to) async* {
  for (int i=1; i<=to; i++) {
    await Future.delayed(Duration(seconds:1));
    yield i;
  }
}
```

- 예외 처리: `try` / `on IOException catch (e)`[^1]


## 7. 예외 처리

```dart
void risky() {
  try {
    throw StateError('Error!');
  } on StateError catch (e) {
    print('Caught: $e');
  } finally {
    print('Done');
  }
}
```

- `throw`, `try`–`catch`–`finally`, `rethrow` 지원[^1]


## 8. 식별자 및 기타 개념

- 식별자: 문자 또는 `_` 시작, 숫자 포함 가능
- 모든 것은 객체: 숫자·함수·null 포함
- 접근 제한: `_private` → 라이브러리 단위 비공개
- 컴파일 타임 검사 vs 런타임 예외 분리[^1]

**참고**
[^1] Dart 언어 공식 문서, “Introduction to Dart” 페이지 (2025-02-13)

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Dart_(programming_language)

