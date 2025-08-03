---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 4.1 위젯(Widgets) - 확장 가이드

## 개요

Flutter에서 **위젯(Widgets)은 UI 구성 요소의 기본 단위**로, 앱의 모든 시각적 요소를 구성하는 불변(immutable) 객체이다[^1][^2]. "Everything is a widget"이라는 Flutter의 핵심 철학에 따라, 텍스트, 버튼, 레이아웃, 애니메이션까지 모든 UI 요소가 위젯으로 표현된다[^3][^2].

## 위젯의 핵심 특성

### 불변성(Immutability)

모든 위젯은 **불변 객체**로, 한 번 생성되면 수정할 수 없다[^1][^4]. 상태가 변경될 때는 기존 위젯을 폐기하고 새로운 위젯 인스턴스를 생성하여 위젯 트리를 재구성한다[^1][^5].

### 위젯 트리 구조

위젯들은 **계층적 트리 구조**를 형성하며, 부모-자식 관계를 통해 복잡한 UI를 구성한다[^5][^2]. 각 위젯은 `build()` 메서드를 통해 자신의 하위 위젯들을 정의한다[^1][^6].

## StatelessWidget vs StatefulWidget

| 구분        | StatelessWidget            | StatefulWidget                     |
| :-------- | :------------------------- | :--------------------------------- |
| **상태 변경** | 불가능 - 생성 후 고정[^7][^8]      | 가능 - `setState()` 호출로 재렌더링[^9][^7] |
| **생명주기**  | `build()` 한 번만 호출[^6][^10] | 복잡한 생명주기 단계 존재[^9][^11]            |
| **성능**    | 가벼움, 빠른 렌더링[^8][^12]       | 상대적으로 무거움[^7][^12]                 |
| **사용 사례** | 정적 UI(텍스트, 아이콘)[^8][^13]   | 동적 UI(폼, 카운터, 애니메이션)[^8][^14]      |
| **구조**    | 단일 클래스[^14][^4]            | 위젯 클래스 + State 클래스[^14][^4]        |

### StatelessWidget 특징

- **정적 UI 요소**에 최적화된 위젯으로, 내부 상태를 갖지 않는다[^7][^13]
- 부모 위젯으로부터 전달받은 데이터가 변경되면 자동으로 재빌드된다[^6][^4]
- 생성자와 `build()` 메서드만으로 구성되는 간단한 구조[^6][^10]


### StatefulWidget 생명주기

StatefulWidget은 다음과 같은 **복잡한 생명주기**를 갖는다[^9][^15][^11]:

1. **Constructor** → **createState()** → **initState()** → **didChangeDependencies()** → **build()**
2. **상태 변경 시**: **setState()** → **build()** 재호출[^9][^16]
3. **업데이트 시**: **didUpdateWidget()** → **build()**[^16][^6]
4. **소멸 시**: **deactivate()** → **dispose()**[^16][^15]

## 위젯 성능 최적화

### 불필요한 재빌드 방지

- **StatelessWidget 우선 사용**: 가능한 경우 StatelessWidget을 선택하여 성능 향상[^12][^17]
- **위젯 분할**: 큰 위젯을 작은 단위로 나누어 필요한 부분만 재빌드[^12][^17]
- **const 생성자 활용**: 컴파일 타임에 결정되는 위젯은 `const`로 선언하여 메모리 최적화[^18]


### 상태 관리 최적화

- **setState() 범위 최소화**: 변경이 필요한 최소 범위에서만 `setState()` 호출[^19][^17]
- **Provider, Riverpod, Bloc** 등 상태 관리 라이브러리 활용으로 전역 상태 효율적 관리[^19][^20]


## 렌더링 파이프라인과 위젯

Flutter의 **렌더링 파이프라인**에서 위젯은 다음 과정을 거친다[^21][^22]:

1. **위젯 트리 생성**: 사용자 입력이나 상태 변경 시 위젯 계층 구조 생성
2. **Element 트리 변환**: 위젯을 Element 객체로 변환하여 상태 관리
3. **RenderObject 생성**: 실제 레이아웃과 페인팅을 담당하는 렌더 객체 생성
4. **Layer 트리 구성**: GPU 렌더링을 위한 레이어 구조 생성
5. **GPU 래스터화**: 최종적으로 화면에 픽셀로 렌더링[^21][^23]

## 커스텀 위젯 개발

### 커스텀 위젯의 이점

- **재사용성**: 한 번 작성하여 여러 곳에서 활용[^24][^25]
- **캡슐화**: 복잡한 로직을 하나의 위젯으로 묶어 코드 정리[^24][^26]
- **일관성**: 앱 전체에 걸쳐 통일된 디자인 시스템 구축[^24][^25]


### 커스텀 위젯 생성 과정

1. **요구사항 정의**: 위젯의 목적과 기능 명확화[^24][^27]
2. **StatelessWidget/StatefulWidget 선택**: 상태 필요 여부에 따라 결정[^24][^28]
3. **생성자 매개변수 설계**: 외부에서 전달받을 속성들 정의[^26][^28]
4. **build() 메서드 구현**: 실제 UI 구조 반환[^28][^27]
5. **테스트 및 최적화**: 다양한 시나리오에서 동작 검증[^24][^27]

## 위젯 카탈로그와 활용

Flutter는 **풍부한 기본 위젯 라이브러리**를 제공한다[^3][^29]:

### 기본 위젯 카테고리

- **레이아웃**: Container, Row, Column, Stack, Flex[^30][^31]
- **텍스트**: Text, RichText[^29][^30]
- **입력**: TextField, Button, Checkbox[^29][^31]
- **내비게이션**: AppBar, Drawer, BottomNavigationBar[^31]
- **스크롤**: ListView, GridView, SingleChildScrollView[^29][^31]


### Material \& Cupertino 디자인 시스템

- **Material 3**: Android 스타일의 디자인 가이드라인 구현[^3][^31]
- **Cupertino**: iOS Human Interface Guidelines 준수[^3]

**핵심 권장사항:**
위젯 선택 시 **성능과 유지보수성**을 고려하여 StatelessWidget을 우선 사용하고, 상태 관리가 필요한 경우에만 StatefulWidget을 활용한다. 복잡한 UI는 **작은 위젯으로 분할**하여 재사용성과 가독성을 높이며, 커스텀 위젯 개발을 통해 **일관된 디자인 시스템**을 구축할 것을 권장한다.

<div style="text-align: center">⁂</div>

[^1]: https://docs.flutter.dev/ui

[^2]: https://docs.flutter.dev/get-started/fundamentals/widgets

[^3]: https://docs.flutter.dev/ui/widgets

[^4]: https://onlyfor-me-blog.tistory.com/950

[^5]: https://www.f22labs.com/blogs/what-is-flutter-widget-tree-a-comprehensive-guide/

[^6]: https://juwon-yun.tistory.com/84

[^7]: https://couldi.tistory.com/43

[^8]: https://itwise.tistory.com/29

[^9]: https://ifhead.tistory.com/entry/Flutter-Stateless-Widget과-Stateful-Widget-차이-생명주기

[^10]: https://parkjh7764.tistory.com/entry/Flutter-위젯-라이프사이클-StatelessWidget-StatefulWidget-LifeCycle-함수-메서드-정리

[^11]: https://jaceshim.github.io/2019/01/28/flutter-study-stateful-widget-lifecycle/

[^12]: https://somniosoftware.com/blog/13-best-practices-for-flutter-performance-optimization

[^13]: https://here4you.tistory.com/220

[^14]: https://juntcom.tistory.com/301

[^15]: https://hyeonsdevlog.tistory.com/entry/Flutter-lifecycle-플러터-생명주기

[^16]: https://ayoung0073.tistory.com/entry/flutter-app-lifecycle

[^17]: https://docs.flutter.dev/perf/best-practices

[^18]: https://velog.io/@false90/Flutter-Study-1주차Module-Summary

[^19]: https://docs.flutter.dev/get-started/fundamentals/state-management

[^20]: https://docs.flutter.dev/data-and-backend/state-mgmt/simple

[^21]: https://theprogrammingway.com/flutters-rendering-pipeline/

[^22]: https://velog.io/@tygerhwang/Flutter-렌더링-원리와-과정-화면은-이렇게-그려진다

[^23]: https://etst.tistory.com/387

[^24]: https://www.techaheadcorp.com/blog/creating-custom-widgets-flutter-extending-functionality/

[^25]: https://www.freecodecamp.org/news/how-to-build-a-custom-widget-in-flutter/

[^26]: https://www.geeksforgeeks.org/flutter/flutter-custom-widgets/

[^27]: https://www.dhiwise.com/post/how-to-achieve-optimal-code-efficiency-with-custom-widget

[^28]: https://dev.to/harsh8088/building-a-custom-flutter-widget-from-scratch-335i

[^29]: https://www.getwidget.dev/blog/top-flutter-widgets-catalog/

[^30]: https://docs.flutter.dev/ui/widgets/basics

[^31]: https://docs.flutter.dev/ui/widgets/material

[^32]: https://apexive.com/post/guide-to-widget-composition-in-flutter

[^33]: https://velog.io/@realryankim/Flutter-LifeCycle생명주기

[^34]: https://devwithflutter.hashnode.dev/flutter-widgets-demystified-a-comprehensive-guide

[^35]: https://velog.io/@yeogaeng/StatelessWidget-vs.-StatefulWidget

[^36]: https://github.com/academind/flutter-complete-guide-course-resources/blob/main/Code Snapshots/01 Getting Started/01 First App/lib/main.dart

[^37]: https://pub.dev/packages/flutter_widget_catalogue

[^38]: https://docs.flutterflow.io/concepts/custom-code/custom-widgets/

[^39]: https://pieces.app/blog/top-10-widgets-in-the-flutter-widget-catalog

[^40]: https://www.widgetbook.io

[^41]: https://flutterbook.dev/article/Understanding_Flutter_Widgets_A_Comprehensive_Guide.html

[^42]: https://liudonghua123.github.io/flutter_website/ui/widgets/

[^43]: https://www.youtube.com/watch?v=XuVB2fWAbFg

[^44]: https://blog.stackademic.com/understanding-flutters-rendering-pipeline-become-a-widget-master-35791e56278e

[^45]: https://www.geeksforgeeks.org/flutter/flutter-state-management/

[^46]: https://10clouds.com/flutter-application-improvement/

[^47]: https://dev.to/elisaray/building-custom-render-objects-in-flutter-power-beneath-the-widgets-4d5e

[^48]: https://sparcs.org/blog/flutter-state-management/

[^49]: https://docs.flutter.dev/perf/rendering-performance

[^50]: https://seosh817.tistory.com/63

[^51]: https://uxcam.com/blog/flutter-performance-optimization/

[^52]: https://docs.flutter.dev/resources/architectural-overview

[^53]: https://docs.flutter.dev/data-and-backend/state-mgmt/options

[^54]: https://blog.flutter.wtf/how-to-improve-flutter-app-performance/

[^55]: https://hwanstory.kr/@kim-hwan/posts/Flutter-Architectural-Overview-Rendering-And-Layout

[^56]: https://www.social.plus/tutorials/state-management-in-dart-flutter

