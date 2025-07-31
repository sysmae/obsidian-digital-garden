---
publish:  true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 힙(Heap) 자료구조 핸드북

## 주요 권고사항

힙은 **최대값 또는 최소값**을 효율적으로 찾아야 하는 상황에서 필수적인 자료구조로, 우선순위 큐의 기본 구현 방식이자 힙 정렬 알고리즘의 핵심이다. 삽입과 삭제 연산 모두 $O(\log n)$ 시간에 수행되므로, 대규모 데이터 처리 및 실시간 스케줄링에 유리하다.

## 1. 탄생 배경

1.1. **힙 정렬(Heapsort)의 필요성**
- 1964년 J. W. J. Williams가 제안한 힙 정렬은 배열 기반 정렬 방식 중 비교 연산 횟수를 최소화하기 위해 고안되었으며, 최악·평균·최선 모두 $O(n \log n)$ 시간 복잡도를 보장한다[^1].
1.2. **우선순위 큐(Priority Queue)의 효율적 구현**
- 우선순위 큐는 “가장 높은(또는 낮은) 우선순위 요소”를 반복적으로 추출해야 하는 상황에 사용되며, 힙을 활용하면 추출·삽입 연산을 모두 $O(\log n)$에 처리할 수 있다[^1].
1.3. **그래프 알고리즘에서의 활용**
- 다익스트라(Dijkstra)·프림(Prim)·A\* 등의 최단 경로·최소 신장 트리 알고리즘에서 우선순위 큐 구현체로 사용되어 전체 시간 복잡도를 크게 단축시킨다[^2].


## 2. 구조

2.1. **완전 이진 트리(Complete Binary Tree)**

- 힙은 레벨 순서(level order)로 노드를 채워 넣는 완전 이진 트리 형태를 띠며, 깊이가 $\lfloor \log_a N\rfloor$로 최소화되어 있다[^1].
2.2. **힙 속성(Heap Property)**
- Max-Heap: 부모 노드의 키 ≥ 자식 노드의 키
- Min-Heap: 부모 노드의 키 ≤ 자식 노드의 키[^1]

2.3. **배열 기반 구현**

- 트리 구조를 배열로 표현하며, 인덱스 관계는 다음과 같다[^3]:
    - 부모 인덱스: $\lfloor(i-1)/2\rfloor$
    - 왼쪽 자식: $2i + 1$
    - 오른쪽 자식: $2i + 2$


## 3. 주요 연산

| 연산          | 설명                                           | 시간복잡도       |
| :---------- | :------------------------------------------- | :---------- |
| 힙 생성        | 주어진 배열을 힙 구조로 변환 (heapify)                   | $O(n)$      |
| 삽입(insert)  | 배열 끝에 요소 추가 후 “up-heap” (또는 sift-up)으로 속성 복원 | $O(\log n)$ |
| 삭제(extract) | 루트 요소 교체 및 “down-heap”(sift-down)으로 힙 속성 복원  | $O(\log n)$ |
| 최대/최소 조회    | getMax()/getMin()로 루트 요소 반환                  | $O(1)$      |
|             |                                              |             |

## 4. 상세 구현

4.1. **힙화(Heapify) 알고리즘**

```
MaxHeapify(A, i, n):
    largest ← i
    l ← 2i + 1
    r ← 2i + 2
    if l < n and A[l] > A[largest]:
        largest ← l
    if r < n and A[r] > A[largest]:
        largest ← r
    if largest ≠ i:
        swap A[i], A[largest]
        MaxHeapify(A, largest, n)
```

4.2. **힙 생성 (Build-Max-Heap)**

```
Build-Max-Heap(A):
    for i from ⌊n/2⌋ − 1 down to 0:
        MaxHeapify(A, i, n)
```

4.3. **삽입과 삭제**

- 삽입: 새 노드를 배열 끝에 추가 → 부모와 비교하며 위치 교환
- 삭제: 루트와 마지막 노드 교환 후 배열 길이 감소 → down-heap으로 복원


## 5. 활용 사례

- 우선순위 큐: 작업 스케줄링·이벤트 시뮬레이션[^2]
- 정렬: 힙 정렬(Heapsort)[^1]
- 그래프: 다익스트라·프림 알고리즘[^2]
- 데이터 압축: 허프만 코딩(Huffman Coding)[^2]
- 자원 할당·로드 밸런싱: 서버 부하 분산 및 메모리 블록 관리[^2]
- 통계·스트리밍: 실시간 중앙값(Median) 계산[^2]


## 6. 고급 변형

6.1. **피보나치 힙(Fibonacci Heap)**

- 삽입·최소값 조회·병합 연산이 $O(1)$ 상수 시간(상환) 보장, 삭제는 $O(\log n)$[^4].
6.2. **이항 힙(Binomial Heap)**
- 힙을 여러 개의 이항 트리로 구성하여 병합 연산을 $O(\log n)$에 처리 가능.


## 7. 예제 코드 (Python)

```python
class MaxHeap:
    def __init__(self):
        self.A = []

    def insert(self, key):
        self.A.append(key)
        i = len(self.A) - 1
        while i > 0 and self.A[(i-1)//2] < self.A[i]:
            self.A[(i-1)//2], self.A[i] = self.A[i], self.A[(i-1)//2]
            i = (i-1)//2

    def extract_max(self):
        if not self.A:
            return None
        max_val = self.A[^0]
        self.A[^0] = self.A.pop()
        self._heapify(0)
        return max_val

    def _heapify(self, i):
        n = len(self.A)
        largest = i
        l, r = 2*i+1, 2*i+2
        if l < n and self.A[l] > self.A[largest]:
            largest = l
        if r < n and self.A[r] > self.A[largest]:
            largest = r
        if largest != i:
            self.A[i], self.A[largest] = self.A[largest], self.A[i]
            self._heapify(largest)
```


## 8. 결론

힙 자료구조는 **효율성**, **단순성**, **다양성** 측면에서 우선순위 큐·정렬·그래프 알고리즘 등 광범위한 분야에서 핵심 역할을 수행한다. 특히 대규모 데이터 처리나 실시간 시스템에서 성능 병목을 줄이기 위한 필수 도구로 자리 잡고 있다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Heap_(data_structure)

[^2]: https://www.geeksforgeeks.org/dsa/applications-of-heap-data-structure/

[^3]: https://last9.io/blog/heaps-in-java/

[^4]: https://ja.wikipedia.org/wiki/ヒープ

[^5]: https://www.programiz.com/dsa/heap-data-structure

[^6]: https://www.heap.io

[^7]: https://runestone.academy/ns/books/published/pythonds/Trees/BinaryHeapImplementation.html

[^8]: https://learn.microsoft.com/ja-jp/windows/win32/memory/heap-functions

[^9]: https://builtin.com/articles/heap-data-structure

[^10]: https://takeuforward.org/heap/binary-heap-implementation/

[^11]: https://ejje.weblio.jp/content/application+heap

[^12]: https://www.geeksforgeeks.org/dsa/heap-data-structure/

[^13]: https://www.geeksforgeeks.org/dsa/binary-heap/

[^14]: https://www.architectalgos.com/heap-data-structures-explained-applications-problem-solving-patterns-and-real-world-examples-6256e4b8b600

[^15]: https://www.educative.io/blog/data-structure-heaps-guide

[^16]: https://fobi.ashisuto.co.jp/tech/technical/techmemo/post-8668/

[^17]: https://web.stanford.edu/class/archive/cs/cs161/cs161.1168/lecture4.pdf

[^18]: https://it-trend.jp/development_tools/article/32-0041

[^19]: https://www.ibm.com/docs/ja/samfess/8.2.0?topic=server-configuring-heap-size-application

[^20]: https://stackoverflow.com/questions/749199/when-would-i-want-to-use-a-heap

