---
publish:  true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 삽입 정렬(Insertion Sort) 핸드북

**핵심 요약**
삽입 정렬은 배열을 한 번에 한 요소씩 정렬된 부분에 “삽입”하며 점진적으로 전체를 정렬하는 알고리즘으로, 소규모 또는 거의 정렬된 데이터에 탁월한 성능을 보인다. 평균·최악 [[5-💎 영구 노트/시간 복잡도]]는 O(n²)이지만, 최선의 경우 O(n)의 효율을 가지며, 안정 정렬(stable), 제자리(in-place), 온라인(online) 특성을 갖는다.

## 1. 삽입 정렬을 사용하는 이유

삽입 정렬이 가지는 주요 장점은 다음과 같다 [^1]:

- 구현이 매우 간단하며, 최적화된 의사코드가 5줄 이내로 작성 가능
- 거의 정렬된 데이터나 소규모 데이터셋에서 매우 효율적 (adaptive: O(kn) 성능)
- 안정 정렬(stable)로서 동일 키의 상대적 순서를 보존
- 제자리(in-place) 정렬로 추가 메모리 O(1)만 필요
- 입력을 받으면서 동시에 정렬 가능(online)

이러한 특성으로 인해, 퀵소트나 병합정렬 같은 고급 알고리즘에서 서브배열 크기가 작아지면 삽입 정렬로 전환하는 하이브리드 기법(예: IntroSort, TimSort)에 활용된다.

## 2. 알고리즘 구조 및 의사코드

삽입 정렬의 기본 아이디어는 “현재 요소를 정렬된 부분에서 알맞은 위치에 삽입”하는 것이다.

### 2.1. 기본 반복적 구현

```  
for i ← 1 to n−1 do  
    key ← A[i]  
    j ← i−1  
    while j ≥ 0 and A[j] > key do  
        A[j+1] ← A[j]  
        j ← j−1  
    end while  
    A[j+1] ← key  
end for  
```

- 외부 루프는 두 번째 요소부터 시작하여 앞쪽이 정렬된 부분임을 가정
- 내부 루프는 key보다 큰 요소를 오른쪽으로 한 칸씩 이동


### 2.2. 최적화된 버전

swap 대신 한 번에 자리 이동:

```  
for i ← 1 to n−1 do  
    x ← A[i]  
    j ← i  
    while j > 0 and A[j−1] > x do  
        A[j] ← A[j−1]  
        j ← j−1  
    end while  
    A[j] ← x  
end for  
```

- 비교를 통해 빈 자리를 만든 뒤, 최종 한 번의 할당으로 key를 삽입


### 2.3. 재귀적 구현

```  
function insertionSortR(A, n):  
    if n > 0 then  
        insertionSortR(A, n−1)  
        x ← A[n]  
        j ← n−1  
        while j ≥ 0 and A[j] > x do  
            A[j+1] ← A[j]  
            j ← j−1  
        end while  
        A[j+1] ← x  
    end if  
end function  
```

- 스택 공간 O(n)을 추가로 사용하므로 일반적으론 반복문 구현 선호


## 3. 시간 및 공간 복잡도 분석

| 경우 | 비교 횟수 | 이동/스왑 횟수 | 시간 복잡도 |
| :-- | :-- | :-- | :-- |
| 최선 (이미 정렬됨) | n−1 비교 | 0 이동 | O(n) |
| 평균 | ≈n²/4 비교 + 이동 | ≈n²/4 이동 | O(n²) |
| 최악 (역정렬) | n(n−1)/2 비교 | n(n−1)/2 이동 | O(n²) |

- 공간 복잡도: O(1) [^1]
- 삽입 정렬은 데이터 이동(write) 횟수가 많아, 쓰기 비용이 비싼 환경(플래시 메모리 등)에서는 선택 정렬 대비 불리할 수 있음.


## 4. 응용 및 변형

### 4.1. 하이브리드 정렬

- **TimSort**: 작은 서브배열을 삽입 정렬로 처리 후 병합
- **IntroSort**: 깊이가 일정 수준 이상일 때 힙 정렬 대신 삽입 정렬로 전환


### 4.2. 개선된 삽입 정렬

- **이진 삽입 정렬(Binary Insertion Sort)**: 삽입 위치 탐색에 이진 탐색 활용 → 비교 횟수 O(n log n), 전체 복잡도 여전히 O(n²) [^1]
- **도서관 정렬(Library Sort)**: 요소 사이에 간격(gaps) 삽입 → 평균 O(n log n) 성능


### 4.3. 연결 리스트 삽입 정렬

- 링크드 리스트 활용 시 삽입 자체는 O(1)이나 위치 탐색은 O(n) → 전체 O(n²)
- 스킵 리스트나 힙 같은 구조 사용 시 O(n log n)로 개선 가능


## 5. 사용 가이드

1. **데이터 크기**: n ≤ 약 30 이하의 소규모일 때 우선 고려
2. **정렬 상태**: 대부분 이미 정렬된 배열에 이상적
3. **안정성**: 동일 키 상대 순서 유지가 필요할 때
4. **추가 메모리**: 제자리 정렬이 필수인 경우
5. **하이브리드**: 퀵소트·병합정렬 등 고급 알고리즘의 작은 서브문제 처리 시

## 6. 예제 절차

정수 배열 [^2][^3][^1][^4][^5][^6][^7][^8]을 오름차순 정렬하는 단계:

```
3 7 4 9 5 2 6 1
3* 7 4 9 5 2 6 1
3 7* 4 9 5 2 6 1
3 4* 7 9 5 2 6 1
3 4 7* 9 5 2 6 1
3 4 7 9* 5 2 6 1
3 4 5* 7 9 2 6 1
2* 3 4 5 7 9 6 1
2 3 4 5 6* 7 9 1
1* 2 3 4 5 6 7 9
```

- 밑줄(*)로 표시된 요소가 삽입된 값이며, 매 단계마다 왼쪽 부분이 정렬됨 [^1].

**핸드북 요약**
삽입 정렬은 단순하지만, 작은 규모 또는 거의 정렬된 데이터를 처리할 때 최상의 선택이다. 안정성·제자리·온라인 특성을 겸비하며, 하이브리드 정렬 알고리즘의 핵심 구성 요소로 널리 사용된다. 적절한 상황에서 활용하면 복잡도가 높은 알고리즘보다 더 효율적일 수 있다.

### 파이썬 코드
```python
def insertion_sort(arr):
    """
    삽입 정렬 함수
    입력: 정수 또는 비교 가능한 요소의 리스트 arr
    출력: 오름차순으로 정렬된 리스트
    """
    # 리스트를 직접 수정(in-place)
    for i in range(1, len(arr)):
        key = arr[i]           # 삽입할 요소
        j = i - 1
        # 정렬된 부분(0…i-1)에서 key보다 큰 요소를 한 칸씩 오른쪽으로 이동
        while j >= 0 and arr[j] > key:
            arr[j + 1] = arr[j]
            j -= 1
        # 빈 자리에 key 삽입
        arr[j + 1] = key

# 사용 예시
if __name__ == "__main__":
    data = [3, 7, 4, 9, 5, 2, 6, 1]
    print("정렬 전:", data)
    insertion_sort(data)
    print("정렬 후:", data)

```
1. `for` 문으로 두 번째 요소부터 끝까지 순회하며 `key`에 저장
2. `while` 문으로 정렬된 앞부분에서 `key`보다 큰 값을 만나면 오른쪽으로 이동
3. 반복이 끝난 지점에 `key`를 삽입하여 해당 단계까지 왼쪽 부분이 정렬된 상태 유지 

이렇게 작성된 `insertion_sort` 함수는 입력 리스트를 제자리(in-place)로 정렬하며, 리스트 길이가 n일 때 최악·평균 O(n²), 최선 O(n)의 시간 복잡도를 가집니다.
<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Insertion_sort

[^2]: https://www.geeksforgeeks.org/dsa/time-and-space-complexity-of-insertion-sort-algorithm/

[^3]: https://www.interviewcake.com/concept/java/insertion-sort

[^4]: https://www.cs.auckland.ac.nz/compsci220s1c/lectures/2016S1C/CS220-Lecture08.pdf

[^5]: https://youcademy.org/insertion-sort-pseudocode/

[^6]: https://www.reddit.com/r/computerscience/comments/1397hvc/help_understanding_this_pseudocode_for_the/

[^7]: https://www.w3schools.com/dsa/dsa_timecomplexity_insertionsort.php

[^8]: https://brilliant.org/wiki/insertion/

[^9]: https://www.savemyexams.com/a-level/computer-science/ocr/17/revision-notes/8-algorithms/8-1-algorithms/insertion-sort/

[^10]: https://www.hackerearth.com/practice/algorithms/sorting/insertion-sort/tutorial/

[^11]: https://www.ee.torontomu.ca/~courses/coe428/sorting/insertionsort.html

[^12]: https://www.math.umd.edu/~immortal/CMSC351/notes/insertionsort.pdf

[^13]: https://www.w3schools.com/dsa/dsa_algo_insertionsort.php

[^14]: https://pmt.physicsandmathstutor.com/download/Computer-Science/A-level/Notes/OCR/2.3-Algorithms-AS/Intermediate/2.3.3. Sorting Algorithms.pdf

[^15]: https://www.khanacademy.org/computing/computer-science/algorithms/insertion-sort/a/analysis-of-insertion-sort

[^16]: https://www.geeksforgeeks.org/dsa/insertion-sort-algorithm/

[^17]: https://www.khanacademy.org/computing/computer-science/algorithms/insertion-sort/a/insertion-sort-pseudocode

[^18]: https://www.youtube.com/watch?v=s9fmGjFY1v0

[^19]: https://dev.to/emmanuelayinde/understanding-insertion-sort-algorithm-beginners-guide-with-leetcode-problems-1pll

[^20]: https://www.tutorialspoint.com/data_structures_algorithms/insertion_sort_algorithm.htm

