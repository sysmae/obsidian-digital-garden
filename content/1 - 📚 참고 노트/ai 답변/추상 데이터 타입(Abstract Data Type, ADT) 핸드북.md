---
publish:  true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 추상 데이터 타입(Abstract Data Type, ADT) 핸드북

## 주요 시사점

**추상 데이터 타입(ADT)은** 자료 구조의 개념적 설계도이자 사용자 관점의 수학적 모델로, **데이터의 저장 방식(구현)을 숨기고** 오직 **허용된 연산(인터페이스)만 노출**함으로써 모듈성·재사용성·안정성을 극대화한다.

## 1. ADT가 만들어진 이유

### 1.1. 복잡성 관리

- 대형 소프트웨어 시스템에서 자료 구조 구현 세부 정보를 일일이 파악·수정하는 것은 **유지보수 비용 폭발**의 원인이다.
- 1974년 Barbara Liskov와 Stephen N. Zilles는 CLU 언어 설계 과정에서 **데이터 추상화(data abstraction)** 개념으로 ADT를 제안[^1]하여, 구현 변경이 인터페이스나 클라이언트 코드에 영향을 미치지 않도록 해결했다.


### 1.2. 정보 은닉과 모듈화

- ADT는 내부 상태(데이터 표현)와 구현 알고리즘을 **감추고** 외부에는 **명세된 연산만** 제공한다.
- 이를 통해 개발자는 구현 세부 사항에 대한 이해 없이도 ADT를 사용할 수 있어, **모듈 단위 개발·테스트·교체**가 가능하다[^1].


### 1.3. 재사용성 및 확장성

- 동일한 ADT 인터페이스를 **배열, 연결 리스트, 트리 등** 다양한 자료 구조로 구현하여, 성능·메모리 요구사항에 맞춰 **손쉽게 교체**할 수 있다[^2].
- 새로운 연산 추가 또는 내부 구현 개선 시에도 **외부 의존 코드를 수정할 필요 없음**.


## 2. ADT의 구조

ADT는 크게 **값 집합(carrier set)**과 **연산(operations)** 두 요소로 구성된다[^3].


| 구성 요소 | 정의 및 역할 |
| :-- | :-- |
| 값 집합(values) | 해당 ADT가 취할 수 있는 원소들의 범위. 예: Stack의 경우 빈 상태부터 최대 용량까지의 모든 스택 상태 |
| 연산(operations) | 값 집합 위에서 수행 가능한 함수들. 각 연산은 입력 사양과 출력 동작(사이드 이펙트 포함)을 정의하며, **연산의 동작 방식은 명세만 제공**[^1][^3]. |

### 2.1. 연산 예시

- **Stack ADT**
    - push(x), pop(), peek(), isEmpty(), isFull()
- **Queue ADT**
    - enqueue(x), dequeue(), front(), isEmpty(), size()
- **List ADT**
    - insert(i, x), remove(i), get(i), set(i, x), size(), isEmpty()


### 2.2. 사양과 불변식(invariant)

- **사전 조건(preconditions)**: 연산 호출 전 만족해야 할 조건(예: pop 호출 전 isEmpty() == false)
- **사후 조건(postconditions)**: 연산 완료 후 보장되는 상태(예: push 후 size 증가)
- **불변식**: 모든 연산 수행 전후에 유지되어야 할 속성(예: 스택 내부포인터 범위)


## 3. ADT의 구현(데이터 구조)

ADT는 이론적 개념이며, 실제 구현은 **자료 구조(data structure)**로 이루어진다[^1].


| ADT | 대표 구현 자료 구조 | 장단점 비교 |
| :-- | :-- | :-- |
| Stack | 배열, 연결 리스트 | 배열: 인덱스 접근 O(1)·공간 고정<br>연결 리스트: 동적 크기·삽입·삭제 O(1) |
| Queue | 배열(원형), 단일·이중 연결 리스트 | 원형 배열: 메모리 연속·인덱스 효율<br>연결 리스트: 동적 크기 |
| List | 배열, 단일·이중 연결 리스트 | 배열: 임의 접근 O(1)·삽입 삭제 O(n)<br>연결 리스트: 삽입 삭제 O(1) |
| Priority Queue | 힙(binary heap), 정렬 리스트 | 힙: 삽입·삭제 O(log n)<br>정렬 리스트: 검색 O(log n) |

## 4. ADT 사용법 및 원칙

### 4.1. 모듈화 설계

- ADT 인터페이스와 구현 클래스를 **분리**
- 클라이언트는 **인터페이스만** 참조하도록 설계


### 4.2. 계약 기반 설계(Design by Contract)

- 연산의 사전·사후 조건을 **문서화**
- 예외 처리 전략을 **일관성 있게** 정의


### 4.3. 제네릭(Generic)과 ADT

- 다양한 데이터 타입을 다루기 위해 **제네릭**으로 ADT를 정의
- 예: `interface Stack<T> { void push(T x); T pop(); }`[^4]


## 5. ADT 활용 사례

### 5.1. 표준 라이브러리 컬렉션

- Java `java.util` 패키지: List, Set, Queue, Map 등의 ADT 인터페이스 제공
- C++ STL: `std::vector`, `std::list`, `std::queue` 등 ADT 기반


### 5.2. 도메인 특화 ADT

- 그래프(Graph) ADT: 노드 집합·간선 집합, addEdge(), adjacent(), dfs(), bfs()
- 사전(Dictionary) ADT: key-value 쌍, put(), get(), remove(), containsKey()


## 6. 장·단점 분석

| 구분 | 장점 | 단점 |
| :-- | :-- | :-- |
| **장점** | -  구현 독립성: 내부 변경 시 외부 영향 최소화<br>-  재사용성 증가: 다양한 구현 선택 가능<br>-  모듈성 향상: 유지보수·테스트 용이 | **단점** |
| **단점** | -  성능 오버헤드: 인터페이스 호출 비용 등<br>-  설계 복잡도: 사전·사후 조건 문서화 필요<br>-  초기 학습 곡선 발생 |  |

## 7. 결론 및 권장사항

추상 데이터 타입은 **안정적이고 확장 가능한** 소프트웨어 설계를 위해 필수적이다.

- **인터페이스 우선 설계**: 구현에 앞서 ADT 명세를 정의하고, 문서화에 충실할 것
- **다양한 구현 체험**: 배열·연결 리스트·트리 등 여러 구조로 구현하며 효율·메모리 특성 학습
- **계약 기반 프로그래밍** 도입으로 신뢰성 강화

핸드북을 통해 ADT 개념, 구조, 사용법을 숙지하여 **모듈화·재사용성·유지보수성**이 뛰어난 시스템을 구축할 것을 권장한다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Abstract_data_type

[^2]: https://www.geeksforgeeks.org/dsa/abstract-data-types/

[^3]: https://eng.libretexts.org/Bookshelves/Computer_Science/Databases_and_Data_Structures/Data_Structure_and_Algorithms_(Njoroge)/03:_Basic_Data_Structures_and_Abstract_Data_Types

[^4]: https://www.cs.cornell.edu/courses/cs2110/2024sp/reference/abstract_data_types.html

[^5]: https://www.scholarhat.com/tutorial/datastructures/abstract-data-type

[^6]: https://www.deepchecks.com/glossary/abstract-data-type/

[^7]: https://catalog.citruscollege.edu/disciplines/computer-science/computer-science-adt/

[^8]: https://www.goseeko.com/blog/what-is-the-abstract-data-type-in-data-structure/

[^9]: https://www.savemyexams.com/glossary/a-level/computer-science/abstract-data-type-adt-definition/

[^10]: https://brilliant.org/wiki/abstract-data-types/

[^11]: https://catalog.citruscollege.edu/disciplines/computer-science/computer-science-adt/computer-science-adt.pdf

[^12]: https://runestone.academy/ns/books/published/pythonds/Introduction/WhyStudyDataStructuresandAbstractDataTypes.html

[^13]: https://www.ibm.com/docs/ru/SS6PEW_10.0.0/customization/JSP_Abstract_Data_Type_Def.html?view=kc

[^14]: https://en.wikipedia.org/wiki/History_of_computer_science

[^15]: https://web.mit.edu/6.031/www/sp22/classes/10-abstract-data-types/

[^16]: https://itpfdoc.hitachi.co.jp/manuals/3000/3000635100e/U3510077.HTM

[^17]: https://bulletin.sfsu.edu/colleges/liberal-creative-arts/history/ba-history/adt-roadmap/

[^18]: http://www.cs.man.ac.uk/~pjj/cs211/ho/node18.html

[^19]: https://ja.wikipedia.org/wiki/抽象データ型

[^20]: https://www.barstow.edu/academic-affairs/guided-pathways/science-technology-engineering-and-math/computer-science-adt

