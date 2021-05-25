정점과 간선의 집합을 Graph라고 한다.   
트리 또한 그래프이며, 그 중 사이클이 허용되지 않는 그래프를 말한다.

## 그래프 관련 용어 정리
### Undirected Graph 와 Directed Graph (Digraph)
말 그대로 정점과 간선의 연결관계에 있어서 방향성이 없는 그래프를 Undirected Graph 라 하고,    
간선에 방향성이 포함되어 있는 그래프를 Directed Graph 라고 한다.

### Degree
Undirected Graph 에서 각 정점(Vertex)에 연결된 Edge 의 개수를 Degree 라 한다.   
Directed Graph 에서는 간선에 방향성이 존재하기 때문에 Degree 가 두 개로 나뉘게 된다.   
각 정점으로부터 나가는 간선의 개수를 Outdegree 라 하고, 들어오는 간선의 개수를 Indegree 라 한다.   

## 그래프를 구현하는 두 방법
### 인접 행렬 (adjacent matrix) : 정방 행렬을 사용하는 방법
해당하는 위치의 value 값을 통해서 vertex 간의 연결 관계를 O(1) 으로 파악할 수 있다.    
Edge 개수와는 무관하게 V^2 의 Space Complexity 를 갖는다.   

### 인접 리스트 (adjacent list) : 연결 리스트를 사용하는 방법
vertex 의 adjacent list 를 확인해봐야 하므로 vertex 간 연결되어있는지 확인하는데 오래 걸린다.   
Space Complexity 는 O(E + V)이다.    

## 그래프 탐색
그래프는 정점의 구성 뿐만 아니라 간선의 연결에도 규칙이 존재하지 않기 때문에 탐색이 복잡하다.   
따라서 그래프의 모든 정점을 탐색하기 위한 방법은 다음의 두 가지 알고리즘을 기반으로 한다.   

### 깊이 우선 탐색 (Depth First Search: DFS)
그래프 상에 존재하는 임의의 한 정점으로부터 연결되어 있는 한 정점으로만 나아간다라는 방법을 우선으로 탐색한다.   
일단 연결된 정점으로 탐색하는 것이다.   
연결할 수 있는 정점이 있을 때까지 계속 연결하다가 더이상 연결되지 않은 정점이 없으면 바로 그 전 단계의 정점으로 돌아가서 연결할 수 있는 정점이 있는지 살펴봐야 할 것이다.   
갔던 길을 되돌아 오는 상황이 존재하는 미로찾기처럼 구성하면 되는 것이다.   
어떤 자료구조를 사용해야할까?   
바로 Stack 이다.   
Time Complexity : O(V+E) … vertex 개수 + edge 개수

### 너비 우선 탐색 (Breadth First Search: BFS)
그래프 상에 존재하는 임의의 한 정점으로부터 연결되어 있는 모든 정점으로 나아간다.    
BFS 에서는 자료구조로 Queue 를 사용한다.   
연락을 취할 정점의 순서를 기록하기 위한 것이다.   
우선, 탐색을 시작하는 정점을 Queue 에 넣는다.(enqueue)   
그리고 dequeue 를 하면서 dequeue 하는 정점과 간선으로 연결되어 있는 정점들을 enqueue 한다.    
즉 vertex 들을 방문한 순서대로 queue 에 저장하는 방법을 사용하는 것이다.   
Time Complexity : O(V+E) … vertex 개수 + edge 개수   
> BFS 로 구한 경로는 최단 경로이다.

## 참고
* [한재엽님 GitHub, Graph](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/DataStructure#graph)