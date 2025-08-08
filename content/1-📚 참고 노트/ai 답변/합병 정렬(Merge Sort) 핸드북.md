---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 합병 정렬(Merge Sort) 핸드북

## 핵심 요약

**합병 정렬**은 **분할 정복(divide and conquer)** 전략을 따르는 안정적인 정렬 알고리즘으로, 최악·평균·최선 경우 모두 시간 복잡도가 **O(n log n)**인 것이 특징이다. 배열이나 리스트를 반으로 나누어 재귀적으로 정렬한 뒤, 두 부분을 병합하여 최종적으로 정렬된 배열을 얻는다.

## 1. 합병 정렬이 만들어진 이유

합병 정렬은 **대용량 데이터** 처리 시에도 **예측 가능한 성능**을 보장하고, **안정 정렬(stable sort)**을 유지해야 할 때 고안되었다[^1].

- 분할 정복 알고리즘의 대표주자로, 여러 하위 문제를 병렬적으로 처리하기 용이
- 최악의 시간 복잡도가 O(n log n)으로 보장되어, 입력 분포에 상관없이 일정한 성능


## 2. 알고리즘 구조

### 2.1 분할 단계(Divide)

1. 배열을 반으로 나눈다.
2. 더 이상 나눌 수 없을 때까지(하위 배열 크기가 1이 될 때까지) 재귀 호출한다.

### 2.2 정복 단계(Conquer)

각 하위 배열이 단일 요소일 때 해당 하위 배열은 이미 정렬된 상태이므로, 별도의 처리 없이 그대로 반환한다[^2].

### 2.3 병합 단계(Merge)

두 정렬된 하위 배열을 하나의 정렬된 배열로 합치는 과정이다.

1. 왼쪽과 오른쪽 배열의 첫 요소를 비교해 작은 값을 결과 배열에 추가
2. 선택된 배열의 인덱스를 한 칸 옮김
3. 어느 한쪽 배열이 비면 남은 요소를 한 번에 결과 배열에 이어 붙임
4. 병합이 완료된 결과 배열을 반환

## 3. Python 구현 예제

```python
def merge(left, right):
    merged = []
    i, j = 0, 0
    while i < len(left) and j < len(right):
        if left[i] <= right[j]:
            merged.append(left[i])
            i += 1
        else:
            merged.append(right[j])
            j += 1
    # 남은 요소 이어 붙이기
    merged.extend(left[i:])
    merged.extend(right[j:])
    return merged

def merge_sort(arr):
    if len(arr) <= 1:
        return arr
    mid = len(arr) // 2
    left = merge_sort(arr[:mid])
    right = merge_sort(arr[mid:])
    return merge(left, right)

# 사용 예시
data = [38, 27, 43, 3, 9, 82, 10]
sorted_data = merge_sort(data)
print(sorted_data)  # [3, 9, 10, 27, 38, 43, 82]
```

위 코드는 **재귀적 분할**과 **병합 함수**를 분리하여 구현한 방식으로, 가독성과 재사용성을 높였다[^3].

## 4. 성능 분석

| 구분  | 시간 복잡도     | 공간 복잡도 | 안정성 |
| :-- | :--------- | :----- | :-- |
| 최선  | O(n log n) | O(n)   | 안정적 |
| 평균  | O(n log n) | O(n)   | 안정적 |
| 최악  | O(n log n) | O(n)   | 안정적 |

- **시간 복잡도**: 재귀 깊이가 log n이고, 병합 과정이 n을 선형적으로 처리하므로 O(n log n)[^2][^1]
- **공간 복잡도**: 추가 배열을 사용한 병합 과정에서 O(n)의 보조 메모리 필요[^4]
- **안정 정렬**: 동일한 키의 원소 순서를 보존


## 5. 사용법 및 응용

### 5.1 배열 정렬

- 대용량 배열 정렬 시 예측 가능한 성능이 필요할 때 사용
- 외부 정렬(external sort)에서 분할-병합 방식으로 대용량 데이터를 블록 단위로 처리


### 5.2 연결 리스트 정렬

- 연결 리스트는 임의 접근(random access)이 비효율적이므로, 삽입·병합만으로 처리 가능한 합병 정렬이 유리[^5]


### 5.3 분산·병렬 처리

- 분할된 하위 문제를 독립적으로 처리 가능하므로, 멀티스레드 혹은 분산 환경에서 **병렬 합병 정렬**로 확장


## 6. 장단점 및 변형

### 장점

- 모든 경우에 O(n log n)을 보장
- 안정 정렬로, 키가 동일한 데이터 순서 보존
- 분할된 하위 배열 병렬 처리에 적합


### 단점

- 추가 메모리 O(n) 필요, 메모리 제약 환경에서 비효율
- 작은 크기 정렬 시 오버헤드로 인해 다른 알고리즘보다 느릴 수 있음


### 변형

- **Bottom-Up 합병 정렬**: 재귀 대신 반복(iterative)으로 병합 구간 크기를 1, 2, 4,… 순으로 확장하며 수행
- **In-Place 병합**: 보조 메모리를 O(1)로 줄이려는 연구가 있으나 구현이 복잡


## 7. 결론

합병 정렬은 **안정성**과 **일관된 성능**이 필요한 정렬 문제에 적합하며, **대규모 데이터** 처리 또는 **병렬 환경**에서 특히 강점을 가진다. Python으로 구현할 때는 재귀 분할과 명확한 **merge 함수** 분리가 유지보수성과 이해도를 높인다. 작은 데이터셋에는 삽입 정렬 등 다른 알고리즘과의 하이브리드 접근을 고려하는 것이 좋다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Merge_sort

[^2]: https://www.geeksforgeeks.org/dsa/merge-sort/

[^3]: https://www.codecademy.com/learn/sorting-algorithms-python/modules/merge-sort-python/cheatsheet

[^4]: https://www.geeksforgeeks.org/time-and-space-complexity-analysis-of-merge-sort/

[^5]: https://www.simplilearn.com/tutorials/data-structure-tutorial/merge-sort-algorithm

[^6]: https://www.codecademy.com/article/time-complexity-of-merge-sort

[^7]: https://www.w3schools.com/dsa/dsa_algo_mergesort.php

[^8]: https://www.geeksforgeeks.org/python/python-program-for-merge-sort/

[^9]: https://library.fiveable.me/introduction-algorithms/unit-4/merge-sort-algorithm-analysis/study-guide/TiRfUka9L1TjHZSw

[^10]: https://www.youtube.com/watch?v=-3u1C1URNZY

[^11]: https://www.w3schools.com/python/python_dsa_mergesort.asp

[^12]: https://czasopisma.uwm.edu.pl/index.php/ts/article/download/2714/2073/4029

[^13]: https://www.datacamp.com/tutorial/python-merge-sort-tutorial

[^14]: https://www.bbc.co.uk/bitesize/guides/zjdkw6f/revision/5

[^15]: https://hostman.com/tutorials/merge-sort-algorithm-java-c-and-python-implementation/

[^16]: https://www.khanacademy.org/computing/computer-science/algorithms/merge-sort/a/analysis-of-merge-sort

[^17]: https://qiita.com/Yuya-Shimizu/items/ac2b489f4585bd8ad042

[^18]: https://www.ime.usp.br/~pf/algorithms/chapters/mergesort.html

[^19]: https://developer.nvidia.com/blog/merge-sort-explained-a-data-scientists-algorithm-guide/

[^20]: https://gist.github.com/RichmondAlake/36548222f2ba4ffb9b0cf9db1cb11850

