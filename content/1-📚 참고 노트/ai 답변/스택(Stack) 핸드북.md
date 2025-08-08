---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 스택(Stack) 핸드북

## 주요 개요 및 생성 배경

스택은 **Last-In-First-Out (LIFO)** 원칙에 따라 작동하는 추상 자료형(ADT)으로, 가장 나중에 추가된 요소를 가장 먼저 추출한다. 이 개념은 물리적 ‘접시 더미(stack of plates)’에 비유된다[^1].
역사적으로, 1945년 콘라드 주제의 Z4 기계에서 2단계 스택이 구현되었고, 1946년 앨런 튜링은 서브루틴 호출·복귀를 위해 ‘bury’·‘unbury’를 사용했다. 1955년 바우어(Bauer)와 자멜손(Samelson)이 *Operationskeller*를 제안·1957년 특허를 획득했으며, 찰스 햄블린(Hamblin)도 독자 개발하였다[^1][^2][^3].

## 1. 스택의 정의 및 구조

스택은 다음 세 가지 기본 연산을 제공하는 ADT이다[^1]:

- **Push**: 요소를 스택의 최상단에 추가
- **Pop**: 최상단 요소를 제거 및 반환
- **Peek(Top)**: 최상단 요소를 제거 없이 조회

추가 연산으로 **Size**, **IsEmpty**, **Dup**(최상단 요소 복제), **Min/Max 스택**(최솟값·최댓값 추적) 등이 있다. 스택은 단일 종단(Top)에서만 연산이 발생하며, 하단(Bottom)은 고정된다.

## 2. 구현 방식과 시간·공간 복잡도

| 구현 방식 | 장점 | 단점 | 시간 복잡도 | 메모리 오버헤드 |
| :--: | :-- | :-- | :--: | :--: |
| 배열(Array)[^4] | 간단 구현, 캐시 친화적 | 초기 크기 고정 또는 동적 배열 확장 시 O(n) 복사 필요 | Push/Pop: O(1) (앰모타이즈드)[^5] | 낮음 (연속 메모리) |
| 연결 리스트(Linked List)[^4] | 크기 제한 없음, 동적 메모리 사용 | 포인터 오버헤드, 캐시 비친화적 | Push/Pop: O(1) | 높음 (노드당 포인터 포함) |

- **오버플로우(Overflow)**: 배열이 가득 찼을 때에 발생.
- **언더플로우(Underflow)**: 비어 있는 스택에서 Pop 시도 시 발생.


## 3. 주요 사용 사례

1. **함수 호출 관리(콜 스택)**: 실행 중인 함수의 복귀 주소와 지역 변수를 저장·복원[^6].
2. **재귀 처리**: 재귀 호출 상태 탐색·복귀에 활용.
3. **브라우저 내비게이션**: 뒤로/앞으로 이동 기록 저장[^7].
4. **Undo/Redo 기능**: 텍스트 편집·이미지 편집기 등에서 작업 이력 관리[^8].
5. **식(Expression) 평가**: 중위표기식→후위표기식 변환 및 계산(역폴란드 표기법)[^9].
6. **구문 분석(Syntax Parsing)**: 컴파일러의 괄호 짝 검사, 문법 트리 구성.
7. **푸시다운 오토마타(PDA)**: 형식 언어 인식 자동자에서 구조적·재귀적 패턴 처리[^10].
8. **백트래킹(Backtracking)**: 미로 탐색·퍼즐 풀이 시 상태 되돌리기.

## 4. 심화 개념

- **추상 자료형 vs. 자료구조**: ADT는 연산 정의에 집중하며, 배열·연결 리스트 구현은 자료구조 관점의 구체화이다[^1][^11].
- **Stack Machine**: 스택 기반 가상 기계 모델로, 대부분 언어 인터프리터·컴파일러 백엔드에서 사용된다[^2].
- **Min/Max 스택**: 보조 스택을 이용해 최솟값·최댓값 추적, Push/Pop을 O(1)에 달성.
- **다중 스택 구현**: 하나의 배열로 여러 스택을 구현, 고정 분할 또는 가변 분할 기법 활용.


## 5. 구현 예시 (Python)

```python
class Stack:
    def __init__(self):
        self._items = []
    def push(self, item):
        self._items.append(item)
    def pop(self):
        if not self.is_empty():
            return self._items.pop()
        raise IndexError("Pop from empty stack")
    def peek(self):
        if not self.is_empty():
            return self._items[-1]
        raise IndexError("Peek from empty stack")
    def is_empty(self):
        return len(self._items) == 0
    def size(self):
        return len(self._items)
```


## 6. 결론 및 활용 팁

스택은 **LIFO 특성** 덕분에 *최신 상태 관리*, *재귀·함수 호출*, *식 평가*, *이력 추적* 등 다양한 분야에서 필수적이다. 구현 시

- **성능 우선**: 메모리 연속성이 중요한 경우 배열
- **크기 유동성**: 크기 예측이 어려운 경우 연결 리스트
를 선택하면 된다. 기본 연산 모두 상수 시간(O(1))이며, 스택 오버플로우·언더플로우를 고려해야 한다.

핸드북을 통해 스택의 기원, 구조, 구현, 활용, 심화 개념을 종합적으로 이해하여 실무 및 학습에 활용하길 바란다.

(인용: [^1], [^2], [^10], [^7], [^5], [^3], [^4], [^9])

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Stack_(abstract_data_type)

[^2]: https://simple.wikipedia.org/wiki/Stack_(data_structure)

[^3]: https://www.sigcis.org/files/A brief history.pdf

[^4]: https://cstaleem.com/difference-between-stack-using-array-and-linked-list

[^5]: https://www.geeksforgeeks.org/dsa/introduction-to-stack-data-structure-and-algorithm-tutorials/

[^6]: https://trends.codecamp.jp/blogs/media/terminology524

[^7]: https://it-life.net/term/stack/

[^8]: https://www.enjoyalgorithms.com/blog/application-of-stack-data-structure-in-programming/

[^9]: https://www.geeksforgeeks.org/dsa/applications-advantages-and-disadvantages-of-stack/

[^10]: https://cs.pomona.edu/classes/cs62/history/stack\&queue/

[^11]: https://en.wikipedia.org/wiki/Abstract_data_type

[^12]: https://www.ei.fukui-nct.ac.jp/tag/lifo/

[^13]: https://www.pickl.ai/blog/abstract-data-types/

[^14]: https://qiita.com/jay_42tokyo/items/509601884a5fab2a9eb3

[^15]: https://www.cs.toronto.edu/~david/course-notes/csc110-111/10-abstraction/05-stacks.html

[^16]: https://cs.nyu.edu/~joannakl/cs102_s17/notes/lecture03_ListsStacksQueues_notes.pdf

[^17]: https://shikaku.pepenoheya.blog/basic_information_stack/

[^18]: https://dev.to/emmanuelayinde/understanding-stack-data-structure-a-step-by-step-guide-to-implementing-stack-in-javascript-3f62

[^19]: https://moderndata101.substack.com/p/evolution-of-the-data-stack-the-story

[^20]: https://e-words.jp/w/LIFO.html

[^21]: https://retrocomputing.stackexchange.com/questions/25850/what-motivated-stack-being-invented-originally

[^22]: https://hidekazu-blog.com/stack-lifo-filo/

[^23]: https://www.geeksforgeeks.org/dsa/stack-data-structure/

[^24]: https://blog.heycoach.in/time-complexity-of-stack-operations/

[^25]: https://www.tutorchase.com/answers/ib/computer-science/what-are-the-main-applications-of-stacks-in-computing

[^26]: https://stackoverflow.com/questions/65467752/time-complexity-for-iteration-over-a-stack

[^27]: https://www.reddit.com/r/leetcode/comments/ye58iw/when_do_we_use_linked_lists_and_when_arrays_as/

[^28]: https://www.baeldung.com/cs/complexity-stack-queue-deque-set

[^29]: https://cs.stackexchange.com/questions/149543/array-vs-linked-list-implementation-of-stacks-in-js

[^30]: https://iq.opengenus.org/time-complexity-of-stack/

[^31]: https://www.geeksforgeeks.org/dsa/linked-list-vs-array/

[^32]: https://www.upgrad.com/blog/stack-example-in-real-life/

[^33]: https://www.geeksforgeeks.org/dsa/time-and-space-complexity-analysis-of-stack-operations/

[^34]: https://stackoverflow.com/questions/12056842/why-stack-is-implemented-as-a-linked-list-what-is-the-need-why-array-implemen

[^35]: https://www.reddit.com/r/learnprogramming/comments/1i8z1ox/real_life_uses_of_stacks/

[^36]: https://pages.cs.wisc.edu/~siff/CS367/Notes/stacks.html

[^37]: https://library.fiveable.me/data-structures/unit-3/implementation-stacks-queues-arrays-linked-lists/study-guide/LS7Lm3X6o7rEEAlH

[^38]: https://dev.to/amoabakelvin/deep-dive-into-stacks-a-practical-guide-for-software-engineers-5h7p

[^39]: https://www.shiksha.com/online-courses/articles/all-that-you-need-to-know-about-stack/

[^40]: https://www.wscubetech.com/resources/dsa/array-vs-linked-list

