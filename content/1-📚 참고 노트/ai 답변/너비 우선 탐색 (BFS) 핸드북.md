---
publish: true
---

너비 우선 탐색 BFS. 파이썬 코딩 테스트

# 너비 우선 탐색 (BFS) 핸드북

## 1. BFS란 무엇인가?

**너비 우선 탐색(Breadth-First Search, BFS)**은 그래프나 트리를 탐색하는 가장 기본적이고 중요한 알고리즘 중 하나입니다[^1][^2]. BFS는 시작 노드에서 출발하여 **인접한 모든 노드들을 먼저 방문한 후**, 그 다음 레벨의 노드들을 방문하는 방식으로 작동합니다[^3][^4].

BFS의 핵심 특징은 **레벨별 탐색**입니다. 시작점에서 가까운 노드들부터 차례대로 방문하며, 마치 물에 돌을 던졌을 때 파문이 퍼져나가는 것과 같은 패턴으로 탐색을 진행합니다[^5][^6].

### BFS의 작동 원리

BFS는 **큐(Queue) 자료구조**를 사용하여 구현됩니다[^7]. 큐의 로 인해 먼저 발견된 노드가 먼저 처리되어 레벨별 탐색이 가능합니다[^8][^9].

BFS 알고리즘의 기본 단계는 다음과 같습니다[^1][^4]:

1. **초기화**: 시작 노드를 큐에 넣고 방문 표시
2. **탐색**: 큐가 빌 때까지 반복
    - 큐에서 노드를 꺼내 현재 노드로 설정
    - 현재 노드의 미방문 인접 노드들을 모두 큐에 추가
    - 추가된 노드들을 방문 표시
3. **종료**: 큐가 빌 때까지 반복

## 2. BFS 구현 방법

### 2.1 인접 리스트를 이용한 구현

```python
from collections import deque

def bfs_adjacency_list(graph, start):
    """
    인접 리스트를 사용한 BFS 구현
    시간복잡도: O(V + E)
    공간복잡도: O(V)
    """
    visited = set()
    queue = deque([start])
    result = []
    
    while queue:
        node = queue.popleft()
        if node not in visited:
            visited.add(node)
            result.append(node)
            
            # 미방문 인접 노드들을 큐에 추가
            for neighbor in graph[node]:
                if neighbor not in visited:
                    queue.append(neighbor)
    
    return result
```


### 2.2 인접 행렬을 이용한 구현

```python
def bfs_adjacency_matrix(matrix, start):
    """
    인접 행렬을 사용한 BFS 구현
    시간복잡도: O(V²)
    공간복잡도: O(V)
    """
    n = len(matrix)
    visited = [False] * n
    queue = deque([start])
    result = []
    
    visited[start] = True
    
    while queue:
        node = queue.popleft()
        result.append(node)
        
        # 모든 인접 노드 확인
        for i in range(n):
            if matrix[node][i] == 1 and not visited[i]:
                visited[i] = True
                queue.append(i)
    
    return result
```


### 2.3 핵심 구현 요소

**큐(Queue) 사용**: BFS는 반드시 큐 자료구조를 사용해야 합니다[^7][^10]. 파이썬에서는 `collections.deque`를 사용하여 효율적으로 구현할 수 있습니다[^11][^12].

**방문 체크**: 사이클을 방지하고 무한 루프를 막기 위해 방문한 노드를 추적해야 합니다[^6][^12]. 이는 `set`이나 `boolean` 배열을 사용하여 구현할 수 있습니다.

**그래프 표현**: 인접 리스트나 인접 행렬로 그래프를 표현할 수 있으며, 각각의 시간복잡도가 다릅니다[^13][^14].

## 3. 시간 및 공간 복잡도

### 3.1 [[5-💎 영구 노트/시간 복잡도]]

BFS의 시간복잡도는 그래프 표현 방식에 따라 달라집니다[^15][^16]:

- **인접 리스트**: O(V + E) - 각 정점과 간선을 한 번씩만 방문[^17]
- **인접 행렬**: O(V²) - 모든 정점 쌍을 확인해야 함

여기서 V는 정점의 수, E는 간선의 수입니다[^18][^19].

### 3.2 공간 복잡도

BFS의 공간복잡도는 **O(V)**입니다[^15][^17]. 이는 다음 요소들로 구성됩니다:

- **큐 저장공간**: 최악의 경우 모든 정점이 큐에 저장될 수 있음
- **방문 배열**: 모든 정점의 방문 상태를 저장
- **기타 보조 자료구조**: 결과 저장 등을 위한 추가 공간


## 4. BFS vs DFS 비교

![BFS vs DFS: Key Differences and Applications](https://ppl-ai-code-interpreter-files.s3.amazonaws.com/web/direct-files/2f31871e9306c6cb17dc9a0c294e14f8/ffd63c18-8a0a-4190-a64e-c1e56f7b232b/02a4769c.png)

BFS vs DFS: Key Differences and Applications

BFS와 DFS의 선택 기준[^20][^21]:

- **BFS 사용**: 최단 경로 찾기, 레벨별 처리, 해가 시작점 근처에 있을 때[^22][^23]
- **DFS 사용**: 경로 탐색, 사이클 감지, 해가 깊숙이 있을 때[^20][^21]


## 5. BFS의 주요 응용 분야

### 5.1 최단 경로 찾기

BFS는 **가중치가 없는 그래프에서 최단 경로를 찾는 가장 효율적인 방법**입니다[^22][^24]. 레벨별로 탐색하는 특성상 처음 목표에 도달하는 경로가 항상 최단 경로입니다[^6][^23].

```python
def bfs_shortest_path(graph, start, end):
    """BFS를 이용한 최단 경로 찾기"""
    if start == end:
        return [start], 0
    
    visited = set()
    queue = deque([(start, [start])])
    visited.add(start)
    
    while queue:
        node, path = queue.popleft()
        
        for neighbor in graph[node]:
            if neighbor not in visited:
                new_path = path + [neighbor]
                if neighbor == end:
                    return new_path, len(new_path) - 1
                
                visited.add(neighbor)
                queue.append((neighbor, new_path))
    
    return None, -1
```


### 5.2 레벨 순서 트리 순회

**이진 트리의 레벨 순서 순회**는 BFS의 대표적인 응용 사례입니다[^25][^26]. 트리의 각 레벨을 순서대로 방문하여 계층적 구조를 파악할 수 있습니다[^27][^28].

```python
def bfs_level_order_traversal(root):
    """이진 트리의 레벨 순서 순회"""
    if not root:
        return []
    
    result = []
    queue = deque([root])
    
    while queue:
        level_size = len(queue)
        current_level = []
        
        for _ in range(level_size):
            node = queue.popleft()
            current_level.append(node.val)
            
            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)
        
        result.append(current_level)
    
    return result
```


### 5.3 연결 요소 찾기

BFS는 **그래프의 연결 요소(Connected Components)**를 찾는 데 효과적으로 사용됩니다[^29][^30]. 각 연결 요소는 서로 도달 가능한 노드들의 집합입니다[^31][^32].

### 5.4 미로 해결

BFS는 미로에서 최단 경로를 찾는 데 널리 사용됩니다[^33][^34]. 2차원 격자에서 시작점부터 목표점까지의 최소 이동 거리를 계산할 수 있습니다[^35][^36].

### 5.5 실제 응용 사례

- **GPS 네비게이션**: 최단 경로 찾기[^37][^38]
- **소셜 네트워크**: 친구 추천, 관계 분석[^37]
- **웹 크롤링**: 링크를 따라 페이지 탐색[^39][^37]
- **네트워크 브로드캐스팅**: 데이터 전파 경로 최적화[^37]


## 6. 코딩 테스트에서의 BFS 패턴

### 6.1 주요 문제 패턴

BFS는 코딩 테스트에서 **가장 높은 ROI(투자 대비 효과)**를 가지는 알고리즘 중 하나입니다[^40][^41]. 다음과 같은 패턴의 문제에서 자주 출제됩니다:

- **최단 거리/최소 이동 횟수** 문제[^42][^22]
- **레벨별 처리** 문제[^25][^26]
- **연결 요소/그룹 찾기** 문제[^29][^30]
- **격자/미로** 문제[^33][^34]


### 6.2 문제 해결 접근법

BFS 문제를 인식하는 키워드[^40][^43]:

- "최단", "최소", "shortest"
- "level", "레벨별"
- "동시에", "한 번에"
- "flood fill", "전파"


### 6.3 구현 팁

1. **큐 사용**: `collections.deque` 활용[^11][^12]
2. **방문 체크**: `set` 또는 `boolean` 배열 사용
3. **레벨 구분**: 필요시 레벨별로 노드 개수 추적
4. **격자 문제**: 방향 벡터 활용으로 인접 셀 탐색

## 7. BFS의 특성과 제한사항

### 7.1 장점

- **최단 경로 보장**: 가중치 없는 그래프에서 최적해 보장[^22][^23]
- **완전성**: 해가 존재하면 반드시 찾을 수 있음[^18][^23]
- **레벨별 처리**: 계층적 구조 파악에 유리[^25][^27]


### 7.2 제한사항

- **메모리 사용**: 넓은 그래프에서 많은 메모리 필요[^15][^17]
- **가중치 그래프**: 가중치가 있는 그래프에서는 최적해 보장 안 됨[^24]
- **깊은 해**: 해가 깊은 곳에 있으면 비효율적[^20][^21]


## 8. 결론

BFS는 그래프 탐색의 기초가 되는 중요한 알고리즘으로, 최단 경로 찾기, 레벨 순서 순회, 연결 요소 찾기 등 다양한 분야에서 활용됩니다[^1][^2]. 특히 코딩 테스트에서는 높은 출제 빈도를 보이며, 큐 자료구조를 기반으로 한 체계적인 구현이 핵심입니다[^40][^41].

BFS의 O(V + E) 시간복잡도와 O(V) 공간복잡도를 이해하고, 문제의 특성에 따라 DFS와 적절히 구분하여 사용하는 것이 중요합니다[^16][^17]. 실제 구현에서는 큐, 방문 체크, 그래프 표현 방식을 정확히 이해하여 다양한 문제 상황에 대응할 수 있어야 합니다[^11][^12].

<div style="text-align: center">⁂</div>

[^1]: https://www.geeksforgeeks.org/dsa/breadth-first-search-or-bfs-for-a-graph/

[^2]: https://celerdata.com/glossary/breadth-first-search-bfs

[^3]: https://www.programiz.com/dsa/graph-bfs

[^4]: https://www.hackerearth.com/practice/algorithms/graphs/breadth-first-search/tutorial/

[^5]: https://www.simplilearn.com/tutorials/data-structure-tutorial/bfs-algorithm

[^6]: https://www.interviewcake.com/concept/java/bfs

[^7]: https://www.youtube.com/watch?v=oOlN-qWJzzA

[^8]: https://stephan-osterburg.gitbook.io/coding/coding/leetcode/queue-and-stack/queue-and-bfs

[^9]: https://library.fiveable.me/data-structures/unit-11/breadth-first-search-bfs-algorithm/study-guide/3ykxmer0DnvoLRNV

[^10]: https://stackoverflow.com/questions/39731498/why-does-the-bfs-algorithm-use-a-queue

[^11]: https://www.datacamp.com/tutorial/breadth-first-search-in-python

[^12]: https://www.geeksforgeeks.org/python/python-program-for-breadth-first-search-or-bfs-for-a-graph/

[^13]: https://www.geeksforgeeks.org/c/c-program-for-breadth-first-search-or-bfs-for-a-graph/

[^14]: https://www.geeksforgeeks.org/dsa/implementation-of-bfs-using-adjacency-matrix/

[^15]: https://ds1-iiith.vlabs.ac.in/exp/breadth-first-search/analysis/time-and-space-complexity-of-bfs.html

[^16]: https://www.geeksforgeeks.org/dsa/time-and-space-complexity-of-dfs-and-bfs-algorithm/

[^17]: https://www.geeksforgeeks.org/dsa/time-and-space-complexity-of-breadth-first-search-bfs/

[^18]: https://en.wikipedia.org/wiki/Breadth-first_search

[^19]: https://www.thecodeofnumbers.com/post/unraveling-the-mathematical-foundations-of-breadth-first-search-bfs

[^20]: https://stackoverflow.com/questions/3332947/what-are-the-practical-factors-to-consider-when-choosing-between-depth-first-sea

[^21]: https://www.geeksforgeeks.org/dsa/when-to-use-dfs-or-bfs-to-solve-a-graph-problem/

[^22]: https://codesignal.com/learn/courses/mastering-graphs-in-python/lessons/finding-the-shortest-path-in-graphs-with-bfs-algorithm

[^23]: https://library.fiveable.me/introduction-algorithms/unit-8/breadth-first-search-bfs-algorithm-applications/study-guide/qLi7YOuQAJt0zkHC

[^24]: https://courses.grainger.illinois.edu/cs374al1/sp2023/slides/17-shortest-paths.pdf

[^25]: https://www.enjoyalgorithms.com/blog/level-order-traversal-of-binary-tree/

[^26]: https://www.geeksforgeeks.org/dsa/level-order-tree-traversal/

[^27]: https://www.digitalocean.com/community/tutorials/level-order-traversal-in-a-binary-tree

[^28]: https://www.geeksforgeeks.org/dsa/print-level-order-traversal-line-line/

[^29]: https://math.stackexchange.com/questions/2699333/finding-connected-components-in-a-graph-using-bfs

[^30]: https://www.geeksforgeeks.org/dsa/connected-components-in-an-undirected-graph/

[^31]: https://mobuk.tistory.com/120

[^32]: https://gmlwjd9405.github.io/2018/08/16/algorithm-connected-component.html

[^33]: https://www.geeksforgeeks.org/dsa/count-number-of-ways-to-reach-destination-in-a-maze-using-bfs/

[^34]: https://github.com/efraimG21/BFS-Maze-Solver

[^35]: https://stackoverflow.com/questions/16366448/maze-solving-with-breadth-first-search

[^36]: https://www.youtube.com/watch?v=4wgRlNqIKqM

[^37]: https://www.iquanta.in/blog/applications-of-bfs-or-breadth-first-search/

[^38]: https://www.geeksforgeeks.org/dsa/applications-of-breadth-first-traversal/

[^39]: https://www.scaler.com/topics/breadth-first-search-python/

[^40]: https://www.linkedin.com/pulse/dfs-bfs-coding-interview-3-steps-success-jimmy-zhang-hldgf

[^41]: https://algo.monster/problems/stats

[^42]: https://igotanoffer.com/blogs/tech/breadth-first-search-interview-questions

[^43]: https://www.reddit.com/r/leetcode/comments/1deb3yi/dfs_and_bfs_3_steps_to_success/

[^44]: https://www.youtube.com/watch?v=oDqjPvD54Ss

[^45]: https://takeuforward.org/graph/breadth-first-search-bfs-level-order-traversal/

[^46]: https://docs.aws.amazon.com/ko_kr/neptune-analytics/latest/userguide/bfs-algorithms.html

[^47]: https://www.youtube.com/watch?v=xlVX7dXLS64

[^48]: https://www.w3schools.com/dsa/dsa_algo_graphs_traversal.php

[^49]: https://www.youtube.com/watch?v=HZ5YTanv5QE

[^50]: https://www.wscubetech.com/resources/dsa/bfs-algorithm

[^51]: https://visualgo.net/en/dfsbfs

[^52]: http://www.aistudy.com/heuristic/breadth-first_search.htm

[^53]: https://www.tutorialspoint.com/data_structures_algorithms/breadth_first_traversal.htm

[^54]: https://www.youtube.com/watch?v=rTk9dewULy8

[^55]: https://dad-rock.tistory.com/644

[^56]: https://ko.wikipedia.org/wiki/너비_%EC%9A%B0%EC%84%A0_%ED%83%90%EC%83%89

[^57]: https://favtutor.com/blogs/breadth-first-search-python

[^58]: https://casa-de-feel.tistory.com/33

[^59]: https://www.youtube.com/watch?v=A1UG1Fc-TVc

[^60]: https://github.com/hassanzadehmahdi/BFS-and-DFS-Algorithms

[^61]: https://dev.to/theramoliya/python-implement-breadth-first-search-bfs-for-graph-and-tree-traversal-566b

[^62]: https://www.youtube.com/watch?v=CnECo0rUbzo

[^63]: https://www.geeksforgeeks.org/breadth-first-search-or-bfs-for-a-graph/

[^64]: https://itholic.github.io/python-bfs-dfs/

[^65]: https://leetcode.com/problem-list/breadth-first-search/

[^66]: https://www.educative.io/answers/how-to-implement-a-breadth-first-search-in-python

[^67]: https://interviewing.io/breadth-first-search-interview-questions

[^68]: https://codesignal.com/learn/courses/getting-deep-into-complex-algorithms-for-interviews-with-python/lessons/graph-algorithms-implementation-breadth-first-search-bfs

[^69]: https://gist.github.com/tamzidpeace/7c6ae5718a5615ac1c7af0a2712750dd

[^70]: https://stackoverflow.com/questions/58119635/implementation-of-bfs-using-queue-and-adjacency-list-in-c

[^71]: https://shacoding.com/2021/06/01/data-structure-bfs-adjacency-list-adjacency-matrix/

[^72]: https://sueaty.tistory.com/99

[^73]: https://choiiis.github.io/algorithm/practice-bfs/

[^74]: https://backstreetcoder.com/breadth-first-search-bfs/

[^75]: https://www.puppygraph.com/blog/depth-first-search-vs-breadth-first-search

[^76]: https://www.geeksforgeeks.org/dsa/bfs-using-vectors-queue-per-algorithm-clrs/

[^77]: https://github.com/BruceWeng/Leetcode-Python/blob/master/BFS and DFS template.py

[^78]: https://www.youtube.com/watch?v=nKnlhhz2huE

[^79]: https://labuladong.online/algo/en/problem-set/bfs-ii/

[^80]: https://security-guide.tistory.com/202

[^81]: https://stackoverflow.com/questions/23576746/what-is-the-difference-between-breadth-first-searching-and-level-order-traversal

[^82]: https://qkqhxla1.tistory.com/1103

[^83]: https://www.geeksforgeeks.org/dsa/top-50-graph-coding-problems-for-interviews/

[^84]: https://velog.io/@wodnr_09/LeetCode-Binary-Tree-Level-Order-Traversal

[^85]: https://www.youtube.com/watch?v=81qgDPMgxpo

[^86]: https://www.techinterviewhandbook.org/algorithms/graph/

[^87]: https://velog.io/@hyecircle/자료구조BFS-Level-Order-Traversal

[^88]: https://memgraph.com/blog/graph-algorithms-cheat-sheet-for-coding-interviews

[^89]: https://daniel-park.tistory.com/57

[^90]: https://leetcode.com/problems/binary-tree-level-order-traversal/discuss/1219538/python-simple-bfs-explained

[^91]: https://www.geeksforgeeks.org/dsa/difference-between-bfs-and-dfs/

[^92]: https://www.reddit.com/r/algorithms/comments/nm3yzm/is_there_a_reason_to_use_bfs_instead_of_dfs_and_a/

[^93]: https://www.codecademy.com/article/bfs-vs-dfs

[^94]: https://arxiv.org/abs/2410.17226

[^95]: https://hypermode.com/blog/depth-first-search-vs-breadth-first-search

[^96]: https://www.reddit.com/r/leetcode/comments/uhiitd/when_to_use_bfs_vs_dfs_in_graphs/

[^97]: https://en.wikipedia.org/wiki/Maze-solving_algorithm

[^98]: https://www.cs.ucr.edu/~ygu/papers/ALENEX25/cbfs.pdf

[^99]: https://www.youtube.com/watch?v=cS-198wtfj0

[^100]: https://www.finalroundai.com/articles/binary-tree-level-order-traversal

[^101]: https://chanhuiseok.github.io/posts/ds-2/

[^102]: https://velog.io/@kjh107704/그래프-연결-요소

[^103]: https://www.youtube.com/watch?v=lleJzy4g6Wc

[^104]: https://leetcode.com/problems/binary-tree-level-order-traversal/

[^105]: https://www.youtube.com/watch?v=7gv3aEHcs2U

[^106]: https://hackernoon.com/14-patterns-to-ace-any-coding-interview-question-c5bb3357f6ed

[^107]: https://engineercoding.tistory.com/199

