# DS09-图（Graph）
## 若干定义
1. 简单路径：路径中间没有重复经过的顶点
2. 环(loop)：图含有一条从一个顶点到它自身的边
3. 圈(cycle)：路径的起点和终点是同一个顶点
4. 无圈的(acyclic)
5. 有向无圈图(DAG)
6. 连通的(connected)：图的每一个顶点到其他每个顶点都存在一条路径
7. 完全的(complete)：图的每对顶点之间都存在一条边
## 邻接矩阵与邻接表
1. 邻接矩阵(adjacency matrix)：适用于稠密图，空间需求V^2
2. 邻接表(adjacency list)：适用于稀疏图，空间需求V+E
3. 使用邻接表时，无向图每条边会出现在两个表中，因此会使用双倍的空间：V+2E。不过，在存储有向图时，也往往会多写一份逆邻接表来记录顶点的"进"的情况
## 拓扑排序
1. 拓扑排序仅适用于有向无环图  
2. 排序结果唯一：每次循环中入度为0的节点有且仅有一个  
3. 以下是一种时间复杂度为O(|E|+|V|)的拓扑排序算法
```c
//Kahn算法：
//1.找到入度为0的顶点加入栈中，然后提出栈顶元素，成为拓扑排序的一部分
//2.除去与之相邻所有边并减少其度数，再次加入入度数为0的点循环往复，如果某一次栈已经空了而且没有入读为0的点，那就是存在有向环。
Void TopSort()
{
    Queue Q;
	  int Counter=0;
    Vertex V,W;
    
    Q=CreateQueue(NumVertex);
	  MakeEmpty(Q);
	  for each vertex V
		  if(InDegree[V]==0)
		      Enqueue(V,Q);
    While(!isEmpty(Q))
    {
        V=Dequeue(Q);
        TopoNum[V]=++Counter;
		  for each W adjacent to V
				if(--InDegree[W]==0)
					  Enqueue(W,Q);
	  }
	  if(Counter != NumVertex)
		  Error("Graph has a cycle!");
	  DisposeQueue(Q);    //free memory
```

## 最短路径算法
### 单源最短路径
1. 如果存在负值圈(negative-cost cycle)，最短路径问题就是不确定的
### 无权最短路径
1. 使用广度优先搜索(breadth-first search, BFS)，按层处理顶点。下图是一种时间复杂度为O(|E|+|V|)的BFS算法（使用邻接表时）
![](DS09-%E5%9B%BE%EF%BC%88Graph%EF%BC%89/IMG_0364.JPG)

### 有权最短路径/Dijkstra算法
1. 贪婪算法(greedy algorithm)：分阶段求解一个问题，在每个阶段都把当前出现的当作是最好的去处理。但贪婪算法不是总能够成功的
2. 以下是Dijkstra算法的一段伪代码：
```cpp
void Dijkstra(Table T)
{
	Vertex V,W;
	while(true){
		V=smallest unknown distance vertex;
		if(V==NotAVertex)
			break;
		T[V].Known=true;
		for each W adjacent to V
			if(!T[W].Known)
				if(T[V].Dist+Cvw<T[W].Dist{
					Decrease(T[W].Dist to T[V]+Cvw);
					T[W].Path=V;
				}	//Cvw is the cost of edge(v,w)
	}
}
```
3. 对于这种贪婪算法，只要没有负值边，该算法总能顺利完成
4. 算法的时间复杂度取决于图是稠密的还是稀疏的：
	- 稠密图：使用邻接矩阵——T=O(|V|^2+E)
	- 稀疏图：使用邻接表+优先队列（最小堆）——T=O(|E|*log|V|)
## 最小生成树(minimum spanning tree)
- 一个无向图的最小生成树是由该图的那些连接G的所有顶点的总花费最低的边构成的树(A spanning tree of a graph G is a tree which consists of V( G ) and a subset of E( G ))
- 在最小生成树中，边的条数为|V|-1
### Prim算法
- 选定一个节点作为根，并在其上添加边，每一步均是如此，最终使生成树一步步长成
- 算法在每一阶段都可以选择边(u,v)，其中u在生成树上而v不在
- Prim算法基本上和求最短路径的Dijkstra算法一样，只不过Prim算法在无向图上进行，因此每一条边都要放在两个邻接表中
- 时间复杂度：
	- 不用堆时（适合稠密图）：O(|V|^2)
	- 用二叉堆（适合稀疏图）：O(|E| log|V|)
### Kruskal算法：
- 连续的按照最小的权选定边，并且当所选边不产生圈时将它作为选定边
- 适用不相交集的Union/Find算法
- 下面是Kruskal算法的为代码
```cpp
void Kruskal(Graph G)
{
  int EdgesAccepted;
  DisjSet S;
  PriorityQueue H;
  Vertex U,V;
  SetType Uset,Vset;
  Edge E;
  
  Initialize(S);
  ReadGraphIntoHeapArray(G, H)
  BulidHear(H);
  
  EdgeAccepted=0;
  while(EdgeAcceptedE < NumVertex-1)
  {
    E=DeleteMin(H);
    Uset=Find(U,S);
    Vset=Find(V,S);
    if(Uset!=Vset){
      EdgeAccepted++;
      UnionByHeight(S,S,Uset,Vset);
    }
  }
```
- 最坏运行时间是O(|E| log|E|)——由于|E|=O(|V|^2)，因此实际时间是O(|E| log|V|)
## 深度优先搜索(DFS)
- 深度优先搜索是对先序遍历的推广，模板如下：
```cpp
void DFS(Vertex V)
{
	Visited[V]=True;
	//do something
	for each W adjacent to V
		if(!Visited[W])
			DFS(W);
}
```
- 该算法保证了每条边只访问一次，所以只要是使用了链接表，执行遍历的总时间就是O(|E|+|V|)
### 无向图 · 深度优先生成树（森林）
- 在对无向图进行深度优先搜索时，如果对于一条边，处理(v, w)时发现w已被标记，处理(w, v)时发现v也已被标记，那么用一条虚线的**背向边(back edge)**来表示
### 双连通性
- 如果一个连通的无向图中的任一顶点删除后，剩下的图仍然连通，那么这样的无向连通图就是**双连通的(bioconnected)**
- 如果一个图不是双连通的，那么，将其删除后图将不再连通的那些顶点叫做**割点(articulation point)**
### 欧拉回路
- 算法所需的时间为O(|E|+|V|)
