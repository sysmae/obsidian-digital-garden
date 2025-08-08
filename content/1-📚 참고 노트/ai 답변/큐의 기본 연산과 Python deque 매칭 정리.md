---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 큐의 기본 연산과 Python deque 매칭 정리

## 큐(Queue)와 deque 개념

**큐(Queue)**는 FIFO(First In First Out) 원칙을 따르는 선형 자료구조로, 먼저 들어간 요소가 먼저 나오는 구조입니다[^1][^2]. **Python의 deque(Double-Ended Queue)**는 양방향에서 요소의 삽입과 삭제가 가능한 자료구조로, 일반적인 큐보다 더 유연한 기능을 제공합니다[^3][^4].

## 기본 연산 매칭

![큐의 기본 연산과 Python deque 메서드 매칭 비교표](https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/e5a758c5eafd22f8428976ed2d6dc50d/07fd49a6-dfa9-4662-b27f-9ea7ac3ce146/e945af02.png)

큐의 기본 연산과 Python deque 메서드 매칭 비교표

### 1. 핵심 큐 연산과 deque 메서드 대응

**enqueue(x) ↔ append(x)**

- 큐의 뒤쪽(rear)에 요소를 삽입하는 연산
- deque에서는 `append(x)` 메서드로 구현
- 시간복잡도: O(1)[^1][^3]

**dequeue() ↔ popleft()**

- 큐의 앞쪽(front)에서 요소를 제거하고 반환
- deque에서는 `popleft()` 메서드로 구현
- 시간복잡도: O(1)[^1][^3]

**front()/peek() ↔ d**

- 앞쪽 요소를 제거하지 않고 확인
- deque에서는 인덱스 접근 `d`으로 구현
- 시간복잡도: O(1)[^1][^3]

**rear()/back() ↔ d[-1]**

- 뒤쪽 요소를 제거하지 않고 확인
- deque에서는 인덱스 접근 `d[-1]`으로 구현
- 시간복잡도: O(1)[^1][^3]


### 2. 상태 확인 연산

**isEmpty() ↔ len(d) == 0**

- 큐가 비어있는지 확인
- deque에서는 길이를 확인하여 구현[^1][^3]

**size() ↔ len(d)**

- 큐의 크기 반환
- deque에서는 `len()` 함수 사용[^1][^3]


## deque의 추가 기능

![deque 자료구조의 양방향 연산 시각화](https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/e5a758c5eafd22f8428976ed2d6dc50d/126e2e27-a93e-4a2d-b2e9-d3e64bf8f1d3/815eca35.png)

deque 자료구조의 양방향 연산 시각화

### 양방향 연산의 장점

deque는 일반적인 큐와 달리 양쪽 끝에서 모두 삽입과 삭제가 가능합니다[^5][^6]:

**appendleft(x)** - 왼쪽 끝에 요소 삽입 (O(1))
**pop()** - 오른쪽 끝에서 요소 제거 (O(1))
**extend(iterable)** - 여러 요소를 오른쪽에 추가
**extendleft(iterable)** - 여러 요소를 왼쪽에 추가 (역순)

### 특별한 기능들

**rotate(n)** - deque를 n만큼 회전[^5][^7]

- 양수: 오른쪽으로 회전
- 음수: 왼쪽으로 회전

**count(x)** - 특정 값의 개수 반환
**remove(x)** - 첫 번째 해당 값 제거
**reverse()** - 순서 뒤집기

## 성능상의 장점

deque는 일반 리스트보다 양쪽 끝에서의 연산이 훨씬 효율적입니다[^4][^8]:

- **리스트의 pop(0)**: O(n) - 모든 요소를 한 칸씩 이동
- **deque의 popleft()**: O(1) - 직접적인 삭제

벤치마크 결과에 따르면 deque는 Queue보다 약 20배 빠른 성능을 보입니다[^9].

## 실제 사용 예제

```python
from collections import deque

# 큐로 사용
queue = deque()
queue.append(1)    # enqueue
queue.append(2)
queue.append(3)

front = queue[^0]   # peek
item = queue.popleft()  # dequeue

# deque만의 기능
queue.appendleft(0)    # 왼쪽에 삽입
queue.rotate(1)        # 회전
```


## 용도별 권장사항

**일반적인 큐 구현**: deque를 사용하여 효율적인 FIFO 구조 구현
**스택 구현**: append()와 pop()을 사용하여 LIFO 구조 구현
**슬라이딩 윈도우**: maxlen 매개변수로 고정 크기 버퍼 구현[^4][^8]
**멀티스레드 환경**: thread-safe한 통신이 필요한 경우 queue.Queue 사용[^9]

deque는 큐의 모든 기본 연산을 지원하면서도 추가적인 양방향 기능을 제공하므로, Python에서 큐를 구현할 때 가장 효율적인 선택입니다.
## 전체 코드

```python
"""
큐(Queue)의 기본 연산과 Python deque 메서드 매칭 예제
작성자: (예시)
설명: 큐의 FIFO 동작을 deque로 구현하면서,
      deque 고유의 양방향 기능(appendleft, pop, extend, rotate 등)까지
      한눈에 볼 수 있도록 모든 주요 메서드를 시연합니다.
"""
from collections import deque

# ------------------------------------------------------------------
# 1) deque 초기화 (빈 큐)
# ------------------------------------------------------------------
print("# 1. 초기화 ------------------------------------------------------")
q = deque()          # 빈 deque 생성 → 큐처럼 사용
print("초기 상태:", q)            # deque([])

# ------------------------------------------------------------------
# 2) enqueue  ─ append()
# ------------------------------------------------------------------
print("\n# 2. enqueue(x) – 뒤쪽에 요소 삽입 ------------------------------")
for x in (1, 2, 3):
    q.append(x)      # 큐의 rear에 삽입
    print(f"append({x}) 실행 후:", q)

# ------------------------------------------------------------------
# 3) front/peek ─ q[0]
# ------------------------------------------------------------------
print("\n# 3. front()/peek() – 앞쪽 요소 확인 ---------------------------")
print("front 요소:", q[0])        # 제거하지 않고 확인

# ------------------------------------------------------------------
# 4) rear/back ─ q[-1]
# ------------------------------------------------------------------
print("\n# 4. rear()/back() – 뒤쪽 요소 확인 ---------------------------")
print("rear 요소 :", q[-1])

# ------------------------------------------------------------------
# 5) dequeue  ─ popleft()
# ------------------------------------------------------------------
print("\n# 5. dequeue() – 앞쪽 요소 제거 후 반환 ------------------------")
item = q.popleft()
print("제거된 요소:", item)
print("큐 상태   :", q)

# ------------------------------------------------------------------
# 6) isEmpty ─ len(q) == 0
# ------------------------------------------------------------------
print("\n# 6. isEmpty() – 비어 있는지 확인 -----------------------------")
print("비어 있음?", len(q) == 0)

# ------------------------------------------------------------------
# 7) size ─ len(q)
# ------------------------------------------------------------------
print("\n# 7. size() – 크기 확인 ---------------------------------------")
print("큐 크기   :", len(q))

# ------------------------------------------------------------------
# 8) clear ─ clear()
# ------------------------------------------------------------------
print("\n# 8. clear() – 모든 요소 제거 ----------------------------------")
q.clear()
print("clear() 후:", q)

# ------------------------------------------------------------------
# 9) deque 고유 기능 시연 --------------------------------------------
# ------------------------------------------------------------------
print("\n# 9. deque 고유 메서드 ----------------------------------------")
d = deque([1, 2, 3])          # 예시용 새 deque

# (1) appendleft(x) ─ 왼쪽에 삽입
d.appendleft(0)
print("appendleft(0):", d)

# (2) pop() ─ 오른쪽 끝 요소 제거
removed = d.pop()
print("pop() →", removed, "   결과:", d)

# (3) extend(iterable) ─ 오른쪽에 여러 요소 추가
d.extend([4, 5])
print("extend([4,5]):", d)

# (4) extendleft(iterable) ─ 왼쪽에 여러 요소 추가(역순으로 들어감)
d.extendleft([-2, -1])
print("extendleft([-2,-1]):", d)

# (5) rotate(n) ─ 회전 (양수: 오른쪽, 음수: 왼쪽)
d.rotate(2)        # 오른쪽으로 2칸
print("rotate(2):", d)
d.rotate(-2)       # 왼쪽으로 2칸
print("rotate(-2):", d)

# (6) count(x) ─ 특정 값 개수
print("count(2):", d.count(2))

# (7) remove(x) ─ 첫 번째 x 제거
d.remove(2)
print("remove(2):", d)

# (8) reverse() ─ 순서 뒤집기 (제자리)
d.reverse()
print("reverse():", d)

# (9) index(x) ─ 첫 번째 x의 인덱스 찾기
idx = d.index(4)
print("index(4):", idx, "   현재 deque:", d)

# ------------------------------------------------------------------
# 10) maxlen 옵션으로 고정 크기 큐 만들기 -----------------------------
# ------------------------------------------------------------------
print("\n# 10. deque(maxlen=N) – 고정 길이 슬라이딩 윈도우 --------------")
window = deque(maxlen=3)   # 최대 3개 유지
for i in range(5):
    window.append(i)
    print(f"append({i}) →", window)   # 3개 초과 시 자동으로 앞 요소가 제거

```
<div style="text-align: center">⁂</div>

[^1]: https://www.geeksforgeeks.org/dsa/basic-operations-for-queue-in-data-structure/

[^2]: https://sparkbyexamples.com/python/python-deque-methods/

[^3]: https://www.dsavisualizer.in/visualizer/queue/operations/enqueue-dequeue

[^4]: https://devopedia.org/queue-data-structure

[^5]: https://www.codecademy.com/resources/docs/python/deque

[^6]: https://www.geeksforgeeks.org/queue-in-c/

[^7]: https://techvidvan.com/tutorials/queue-in-data-structure/

[^8]: https://zetcode.com/python/deque/

[^9]: https://www.freecodecamp.org/news/queue-data-structure-definition-and-java-example-code/

[^10]: https://www.w3schools.com/dsa/dsa_data_queues.php

[^11]: https://www.geeksforgeeks.org/python/deque-in-python/

[^12]: https://en.wikipedia.org/wiki/Queue_(abstract_data_type)

[^13]: https://www.programiz.com/dsa/queue

[^14]: https://docs.python.org/3/library/collections.html

[^15]: https://www.youtube.com/watch?v=NXHJm-XLDJ0

[^16]: https://www.geeksforgeeks.org/dsa/queue-data-structure/

[^17]: https://realpython.com/python-deque/

[^18]: https://www.tutorialspoint.com/data_structures_algorithms/dsa_queue.htm

[^19]: https://velog.io/@aonee/Python-Queue-사용하기

[^20]: https://www.ccbp.in/blog/articles/operations-of-queue-in-data-structure

[^21]: https://www.geeksforgeeks.org/dsa/difference-between-queue-and-deque-queue-vs-deque/

[^22]: https://www.happycoders.eu/algorithms/java-queue-vs-deque/

[^23]: https://asxpyn.tistory.com/72

[^24]: https://www.geeksforgeeks.org/cpp/difference-between-queue-and-deque-in-c/

[^25]: https://docs.python.org/3/tutorial/datastructures.html

[^26]: https://straw961030.tistory.com/174

[^27]: https://velog.io/@gouz7514/자료구조파이썬-Queue-vs-deque

[^28]: https://wikidocs.net/104977

[^29]: https://letzgorats.tistory.com/entry/deque-vs-deque-뭐가-맞는-방식이지

[^30]: https://www.alexomegapy.com/post/queue-deque-and-priority-queue-key-data-structures-explained

[^31]: https://excelsior-cjh.tistory.com/96

[^32]: https://stackoverflow.com/questions/717148/queue-queue-vs-collections-deque

[^33]: https://www.tutorialspoint.com/difference-between-queue-and-deque-in-cplusplus

[^34]: https://ooeunz.tistory.com/31

[^35]: https://docs.python.org/ko/3.13/library/collections.html

[^36]: https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/e5a758c5eafd22f8428976ed2d6dc50d/d96148e2-47cf-44dd-be87-a45c8f4ed2d5/6d7f3044.csv

[^37]: https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/e5a758c5eafd22f8428976ed2d6dc50d/d96148e2-47cf-44dd-be87-a45c8f4ed2d5/978ba23a.csv

