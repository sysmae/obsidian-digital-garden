---
publish: true
---

<img src="https://r2cdn.perplexity.ai/pplx-full-logo-primary-dark%402x.png" class="logo" width="120"/>

# 깊이 우선 탐색(Depth-First Search) 핸드북

## 1. 개요 및 탄생 배경

깊이 우선 탐색(DFS)은 그래프나 트리 구조에서 한 갈래 경로를 가능한 한 깊게 탐색하고, 더 이상 진행할 수 없을 때 이전 분기점으로 되돌아와 다른 경로를 탐색하는 알고리즘이다. 19세기 프랑스 수학자 샤를 트레모(Charles Pierre Trémaux)가 미로 해결 전략으로 고안한 초기 개념이 현대 컴퓨터 과학의 그래프 탐색 알고리즘으로 발전되었다[^1].

## 2. 알고리즘 구조

### 2.1 기본 원리

1) 시작 정점 선택
2) 현재 정점을 방문 처리
3) 미방문 인접 정점을 재귀(또는 스택)로 깊이 탐색
4) 더 이상 인접 정점이 없으면 한 단계 되돌아가기(백트래킹)
5) 모든 정점 방문 시 종료

### 2.2 재귀(Implicit Stack) vs. 명시적 스택(Explicit Stack)

- 재귀 방식: 코드가 간결하나 호출 깊이에 따라 스택 오버플로우 위험
- 명시적 스택 방식: 재귀 호출 대신 스택 자료구조 사용, 깊은 그래프에서도 안정적

```python
# 재귀 구현 (Python)
def dfs(v, visited, adj):
    visited[v] = True  # 현재 노드를 방문 표시
    # 처리 작업 (예: 출력, 계산 등)
    for u in adj[v]:  # 인접한 모든 노드에 대해
        if not visited[u]:  # 방문하지 않은 노드만
            dfs(u, visited, adj)  # 재귀 호출로 깊이 탐색
```

```python
# 스택 구현 (Python)
def dfs_iterative(start, adj):
    visited = set()  # 방문한 노드 집합
    stack = [start]  # 명시적 스택 (리스트로 구현)
    visited.add(start)  # 시작 노드 방문 표시
    
    while stack:  # 스택이 비어있지 않은 동안
        v = stack.pop()  # 스택에서 노드 꺼내기
        # 처리 작업 (예: 출력, 계산 등)
        
        for u in adj[v]:  # 인접한 모든 노드에 대해
            if u not in visited:  # 방문하지 않은 노드만
                visited.add(u)  # 방문 표시
                stack.append(u)  # 스택에 추가
```


## 3. 시간·공간 복잡도

- [[5-💎 영구 노트/시간 복잡도]]: O(V + E)
    - V: 정점 수, E: 간선 수. 모든 정점과 간선을 한 번씩만 조사하므로 선형 시간.
- 공간 복잡도:
    - 재귀 호출 스택 또는 명시적 스택 크기: 최악 깊이 = O(V)
    - 방문 여부 기록 배열: O(V)


## 4. 주요 변형 및 심화 기법

### 4.1 순회 순서별 분류

- 전위 순회(Pre-order): 정점 방문 후 자식 탐색
- 후위 순회(Post-order): 자식 탐색 후 정점 방문
- 중위 순회(In-order, 이진트리 전용): 왼쪽 하위트리→정점→오른쪽 하위트리


### 4.2 깊이 제한 탐색(Depth-Limited Search)

- 최대 깊이 `L` 설정 후 DFS 수행
- 무한 탐색 방지, 자원 제약이 있을 때 유용


### 4.3 반복 심화 깊이 우선 탐색(IDDFS)

- `L=0,1,2,…` 순으로 깊이 제한 DFS 반복 실행
- 최적해(가장 얕은 깊이) 보장, BFS 메모리 절약판[^2]


## 5. 활용 사례 및 응용

| 응용 분야 | 설명 |
| :-- | :-- |
| 사이클 탐지 | 재귀 스택 상태로 백 엣지(back edge) 발견 시 사이클 존재 판단[^3] |
| 경로 탐색 | 출발점→목적지 단일 경로 탐색, 스택 내 경로 구성 |
| 연결 요소(Connected Components) 확인 | 미방문 정점마다 DFS 호출로 구성 요소 개별 탐색 |
| 위상 정렬(Topological Sort) | DFS 종료 시간(exit time) 내림차순 정점 정렬 |
| 강결합 성분(SCC) 분할 | Kosaraju·Tarjan 알고리즘의 핵심 서브루틴 |
| 미로 생성·해결 | 경로 생성 및 백트래킹 기반 미로 알고리즘 |
| 웹 크롤러 | 링크 깊이 탐색, 순환 방지를 위한 방문 체크 |
| 모델 검사(Model Checking) | 상태 공간 탐색 및 속성 검증 |

## 6. 구현 시 유의사항 및 최적화

- **재귀 깊이 제어**: Python 등에서 `sys.setrecursionlimit` 조정
- **이웃 정점 순서**: 탐색 순서 결과에 영향, 문제별 우선순위 결정 전략 필요
- **메모리 관리**: 불필요 자료구조 제거, 인접 리스트 활용 권장
- **스택 오버플로우 대책**: 깊이 큰 그래프엔 명시적 스택 선호


## 7. 샘플 코드 통합 예시

```python
import sys
sys.setrecursionlimit(10**7)

def dfs(v, visited, adj):
    visited[v] = True
    print(v, end=' ')           # 정점 처리 예시
    for u in adj[v]:
        if not visited[u]:
            dfs(u, visited, adj)

# 입력 처리 및 그래프 구축
n, e = map(int, input().split())
adj = [[] for _ in range(n)]
for _ in range(e):
    a, b = map(int, input().split())
    adj[a].append(b)
    adj[b].append(a)

start = int(input())
visited = [False]*n
dfs(start, visited, adj)
```


## 8. 결론 및 권장 사항

깊이 우선 탐색은 **메모리 효율이 높고**, **손쉬운 백트래킹**이 가능해 다양한 그래프 문제에 적합하다. 구현 시 재귀 깊이와 스택 사용을 고려해 안정적인 탐색을 설계하고, 문제 특성에 맞는 DFS 변형(IDDFS, 깊이 제한 DFS 등)을 선택하는 것이 핵심이다.

<div style="text-align: center">⁂</div>

[^1]: https://en.wikipedia.org/wiki/Depth-first_search

[^2]: https://en.wikipedia.org/wiki/Iterative_deepening_depth-first_search

[^3]: https://www.geeksforgeeks.org/dsa/applications-of-depth-first-search/

[^4]: https://profound.academy/ja/algorithms-data-structures/dfs-Hfp3FggTTjqlsuiDf6az

[^5]: https://www.datacamp.com/tutorial/depth-first-search-in-python

[^6]: https://www.programiz.com/dsa/graph-dfs

[^7]: https://e-words.jp/w/深さ優先探索.html

[^8]: https://www.tutorialspoint.com/data_structures_algorithms/depth_first_traversal.htm

[^9]: https://www.youtube.com/watch?v=Urx87-NMm6c

[^10]: https://qiita.com/drken/items/4a7869c5e304883f539b

[^11]: https://www.youtube.com/watch?v=by93qH4ACxo

[^12]: https://www.numberanalytics.com/blog/depth-first-search-detailed-exploration

[^13]: https://algo-logic.info/dfs/

[^14]: https://www.youtube.com/watch?v=PMMc4VsIacU

[^15]: https://brilliant.org/wiki/depth-first-search-dfs/

[^16]: https://zenn.dev/convers39/articles/1c315cd96a991f

[^17]: https://www.hackerearth.com/practice/algorithms/graphs/depth-first-search/tutorial/

[^18]: https://www.simplilearn.com/tutorials/data-structure-tutorial/dfs-algorithm

[^19]: https://ja.wikipedia.org/wiki/深さ優先探索

[^20]: https://www.geeksforgeeks.org/dsa/depth-first-search-or-dfs-for-a-graph/

[^21]: https://qiita.com/drken/items/a803d4fc4a727e02f7ba

[^22]: https://neo4j.com/docs/graph-data-science/current/algorithms/dfs/

[^23]: https://acim.nidec.com/drives/control-techniques/-/media/Project/Nidec/ControlTechniques/Documents/Datasheets/DFS-Series-Datasheet.pdf

[^24]: https://cp-algorithms.com/graph/depth-first-search.html

[^25]: https://ai2-iiith.vlabs.ac.in/exp/iterative-deepening-dfs/theory.html

[^26]: https://www.numberanalytics.com/blog/unlocking-depth-first-search-dfs

[^27]: https://www.baeldung.com/cs/iterative-deepening-vs-depth-first-search

[^28]: https://youcademy.org/graph-dfs-applications/

[^29]: https://acim.nidec.com/en/drives/control-techniques/Products/High-Performance-Drives/DFS-High-Power-Freestanding-Drives

[^30]: https://www.puppygraph.com/blog/graph-traversal

[^31]: https://academy.finxter.com/python-iterative-deepening-depth-first-search-dfs-algorithm/

[^32]: https://scigate.com.sg/products/23-control-techniques/6658-unidrive-dfs-series-variants

[^33]: https://robertsweeneyblanco.github.io/Programming_for_Mathematical_Applications/content/Graphs/Graph_Algorithms.html

[^34]: https://www.geeksforgeeks.org/dsa/iterative-deepening-searchids-iterative-deepening-depth-first-searchiddfs/

[^35]: https://memgraph.com/blog/graph-algorithms-applications

[^36]: https://www.youtube.com/watch?v=BK8cEWKHCkY

[^37]: https://www.geeksforgeeks.org/dsa/graph-data-structure-and-algorithms/

[^38]: https://cs.stanford.edu/people/eroberts/courses/soco/projects/2003-04/intelligent-search/inter.html

[^39]: https://library.fiveable.me/introduction-algorithms/unit-8/depth-first-search-dfs-algorithm-applications/study-guide/fDGXBDTWc0KZUTM8

