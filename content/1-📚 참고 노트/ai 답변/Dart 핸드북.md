---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# Dart 핸드북

**핵심 요약**
Dart는 **구글**이 2011년 개발한 **클라이언트 최적화** 프로그래밍 언어로, **웹·모바일·데스크톱·서버** 전반에 걸친 크로스 플랫폼 애플리케이션 개발을 지원합니다. **클래스 기반** 객체지향 구조와 **사운드 널 안전성**, **JIT/AOT 컴파일**, **Hot Reload** 등을 통해 **생산성**과 **퍼포먼스**를 동시에 확보할 수 있습니다.

## 1. 탄생 배경과 역사

2011년 10월 구글이 GOTO 컨퍼런스(덴마크 오르후스)에서 발표한 Dart는, 자바스크립트의 성능·규모 확장 한계를 극복하고자 설계되었다[^1].

- ECMA-408 표준 제정(2014년)으로 정식 언어 명세화[^1].
- Dart 1.0 출시(2013년) 이후 Chrome 내장 VM 계획 포기, JS 트랜스파일 중심 전환(2015년)
- Dart 2.0(2018년): **정적 타이핑 강화**, **sound null safety** 예고
- Dart 3.0(2023년): **sound null safety 의무화**, **레코드**, **패턴 매칭**, **클래스 수정자** 추가
- 최신 버전 3.8(2025년 5월): **null-aware 요소** 및 포맷터 개선[^2].


## 2. 언어 구조 및 문법

### 2.1 기본 문법

- **C 스타일** 구문: 세미콜론, 중괄호
- **main()** 진입점, `print()`로 콘솔 출력[^3]
- **타입 추론(var)**, 명시적 타입 지정 가능
- `final` / `const`로 불변 변수 선언


### 2.2 클래스 기반 객체지향

- **클래스**, **추상 클래스**, **인터페이스**, **믹스인**
- **생성자**: 기본, 명명, 이니셜라이저 리스트
- **getter/setter**, **연산자 오버로딩** 지원


### 2.3 제네릭과 기능형 요소

- **재ified generics** (런타임에도 타입 정보 유지)
- **고차 함수**, **람다**, **이터러블**, **스트림**, **async/await**, **yield** 등 비동기/순회 기능 풍부


### 2.4 사운드 널 안전성 (Sound Null Safety)

- Dart 2.12 도입, Dart 3.0부터 의무화
- **nonnull** 변수는 절대 null 불허, 컴파일 시점에 검증[^4]


## 3. 컴파일 및 실행 구조

### 3.1 트랜스파일러 (JavaScript)

- **dart2js**: 전통적 JS 생산용 컴파일러
- **dartdevc**: 개발용 디버깅 컴파일러
- `webdev serve` (개발), `webdev build` (프로덕션) 명령어 통합


### 3.2 네이티브 컴파일

- **AOT(사전 컴파일)**: `dart compile aot-snapshot` → 플랫폼별 최적화된 바이너리
- **JIT(실행 시 컴파일)**: `dart compile jit-snapshot` → 빠른 컴파일, VM 필요
- **Self-contained exe**: 종속 라이브러리·런타임 포함 실행 파일 생성[^1]


### 3.3 WebAssembly 지원

- Dart 3.4부터 WasmGC 기능 활용 가능, Chrome 119 이상 지원


## 4. 런타임 모델과 동시성

- **Isolate**: 메모리 공유 없는 독립 실행체, 메시지 패싱으로 통신(Actor 모델)[^1]
- 웹 플랫폼에서는 Web Worker 권장


## 5. 개발 생태계

### 5.1 Flutter

- **UI 툴킷** Flutter의 기본 언어로 채택
- **Hot Reload**: 코드 수정 즉시 UI 반영, 생산성 극대화[^4]


### 5.2 도구 및 에디터

- **Dart SDK**: 포맷터, 분석기, 테스트 프레임워크 포함
- **DartPad**: 웹 기반 실습 환경
- **IDE 플러그인**: IntelliJ, Android Studio, VS Code, WebStorm 등 풍부


### 5.3 패키지 관리

- **pub**: 공식 패키지 매니저, 의존성 관리 및 배포 지원


## 6. 주요 특징 및 장점

- **크로스 플랫폼**: 단일 코드 베이스로 모바일·웹·데스크톱 모두 지원
- **고성능**: AOT 컴파일로 네이티브 실행, JS 최적화 트랜스파일
- **생산성**: Hot Reload, 풍부한 언어 기능, 표준화된 툴체인
- **안정성**: 사운드 널 안전성, 정적 타입 시스템
- **확장성**: 믹스인, 제네릭, 추상화 등 대규모 앱 설계에 적합


## 7. 사용 사례

| 분야 | 예시 프레임워크/툴 | 설명 |
| :-- | :-- | :-- |
| 모바일 앱 | Flutter | iOS/Android 크로스 플랫폼 UI 개발에 널리 사용 |
| 웹 애플리케이션 | AngularDart | 구글이 개발한 Dart 기반 웹 프레임워크 (현재 유지보수 중단) |
| 서버/백엔드 | Aqueduct, Conduit, Serverpod | Dart로 작성된 서버 사이드 프레임워크, gRPC·REST API 구축 지원 |
| 데스크톱 네이티브 | Flutter Desktop | Windows, macOS, Linux 네이티브 앱 빌드 |

## 8. 학습 및 활용 가이드

### 8.1 입문

1. DartPad로 Hello World 실습
2. 변수·제어문·함수·클래스 기본 문법 학습

### 8.2 심화

1. async/await, 스트림, 제네릭 활용법
2. Dart SDK 도구(publish, analyze, format) 익히기
3. Flutter 튜토리얼로 UI 구축 실습

### 8.3 프로젝트 적용

- CI/CD 파이프라인에 AOT 빌드 통합
- 정적 분석(Analyzer)으로 코드 품질 관리
- 패키지 관리(pub.dev 활용) 및 테스트 자동화


## 9. 결론 및 전망

Dart는 **웹 한정**을 넘어 **모바일·데스크톱·서버** 전 영역을 아우르는 **올인원** 언어로 자리매김하고 있습니다. 특히 Flutter와 결합 시 **생산성**과 **퍼포먼스**를 모두 잡을 수 있어, 앞으로도 **크로스 플랫폼 개발**의 중심 언어로 성장할 것으로 기대됩니다.

---

[^1] [Dart (programming language) - Wikipedia]
[^3] [Introduction to Dart]
[^2] [Dart language evolution]
[^4] [Dart overview]

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Dart_(programming_language)

[^2]: https://dart.dev/resources/language/evolution

[^3]: https://dart.dev/language

[^4]: https://dart.dev/overview

[^5]: https://www.reddit.com/r/dartlang/comments/11xe6sz/why_isnt_dart_used_more/

[^6]: https://www.scribd.com/document/727170362/History-of-Dart

[^7]: https://aptrust.github.io/dart-docs/developers/architecture/

[^8]: https://redmonk.com/jgovernor/2022/05/16/flutter-propels-dart-frameworks-language-adoption-and-cross-platform-development/

[^9]: https://www.deusinmachina.net/p/the-dart-programming-language-is

[^10]: https://docs.flutter.dev/resources/architectural-overview

[^11]: https://flutterwire.com/why-flutter-uses-dart-an-in-depth-look-at-the-benefits-of-the-programming-language/

[^12]: https://github.com/dart-lang/language

[^13]: https://www.aubergine.co/insights/dart-vs-kotlin-which-one-is-better-in-2023

[^14]: https://futurbyte.co/blog/dart-programming-language/

[^15]: https://velog.io/@sucream/FLUS-스터디-2주차-Dart-기초-Flutters-Architecture-and-Widgets플러터의-아키텍처와-위젯

[^16]: https://news.ycombinator.com/item?id=18603328

[^17]: https://github.com/dart-lang/sdk/wiki/Supported-Architectures

[^18]: https://www.sonatype.com/products/language-support/dart

[^19]: https://awjunaid.com/dart/history-and-evolution-of-dart-programming-language/?amp=1

[^20]: https://dl.acm.org/doi/fullHtml/10.1145/3660829.3661027

