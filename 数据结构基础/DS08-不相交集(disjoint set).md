# DS08-不相交集
## 等价关系
- 等价关系(euqivalence)是满足一下三个性质的关系R：
	1. 自反性：对于所有的a∈S，aRa
	2. 对称性：aRb当且仅当bRa
	3. 传递性：若aRb且bRc，则aRc
## 动态等价性
### 等价类(euqivalence class)
- 对于一个元素a∈S，其等价类是S的一个子集，包含所有与a有关系的元素。
- 等价类形成对S的一个**划分**：S的每一个成员恰好出现在一个等价类中
- 为确定是否有a～b，只需验证a和b是否都在一个等价类之中。
### 不相交集的Union/Find算法：
- 若要添加关系a～b，首先需要对a、b执行**Find**来检验是否处在同一个等价类中；如果不是，进一步执行**Find**操作
- 上条目的算法是一个动态(dynamic)算法，同时也是联机(on-line)操作
## 基本数据结构
- 用一棵棵树来表示每一个等价类集合，这些树被非显示地存储在一个数组中：数组的每个成员_P[i]_表示元素_i_的父亲，如果_i_是根，那么_P[i]_=0
- 数组最开始全部为0
### Find(X)
- 对元素X的一次Find(X)操作通过返回包含X的树的根完成，花费的时间与表示X的节点的深度成正比
```cpp
SetType Find（ElementType X， DisjSet S）
{
	if（S[X] <= 0)
    	return X;
    else
    	return Find(S[X], S);
}
```

### Union
- 为了尽量降低树的深度，提高Find()的小效率，在Union时应当采用**按大小求并(union-by-size)**的策略，使得总让较小的树成为较大的树的子树
- 使用按大小求并的策略后，所有树的深度最多是O(log N)，也就是说，Find的运行时间是O(log N)
- 另外一种策略是**按高度求并(union-by-height)**，同样保证了所有树的深度最多是O(log N)。这种算法还是一种平缓的算法，只有当两个相等深度的树求并时高度才增加，下面是按高度求并的一段代码：
```cpp
void UnionByHeight(DisjSet S, SetType Root1, Set)
{
  if(S[Root2] < S[Root1])
    S[Root1] = S[Root2];
  else{
    if(S[Root1] == S[Root2])
      S[Root1]--;
    S[Root2] = Root1;
  }
}
```
### 路径压缩(Path Compression)
- 在Find操作期间执行额外的工作来进行路径压缩，加速算法的进行
- 修改后的Find操作如下：
```cpp
SetType Find(ElementType S, DisjSet S)
{
	if(S[X] <= 0)
		return X;
	else
		return S[X] = Find(S[X], S);
}
```
- 该算法仅仅修改了最后一行，使S[X]等于由Find返回的值，从而在集合的根被递归的找到后，路径上的每一个节点都将直接指向根节点
- 路径压缩算法和按大小求并算法可以完美兼容；但是对于按高度求并算法，由于路径压缩可以改变树的高度，因此我们不再去计算树的确切高度，而是以估计的高度（秩）去计算