# Ds06-优先队列·堆(priority queue · heap)
## 二叉堆(binary heap)
1. 二叉堆普遍的用在实现优先队列上，当堆(heap)这个名词不加修饰地使用的时候，一般就是指二叉堆对优先队列的实现
2. 和二叉查找数一样，堆也有两个性质：结构性和堆序性
### 结构性质
1. 堆是一棵 **完全二叉树**(complete binary tree)——除了底层其他节点被完全填满的二叉树（底层元素从左到右填入）
2. 完全二叉树高度_h=logN_
3. 完全二叉树因为很有规律性，所以可以使用数组表示，对于数组任意位置_i_上的元素：
	1. Left-Child在位置_2i_上
	2. Right-Child在位置_2i+1_上
	3. Parent在位置[_i/2_]上
### 堆序性质
1. 对于每一个节点X，X的父亲中的关键字小于（或等于）X中的关键字，根结点除外
### Insert
1. 在下一个空闲位置创建一个空穴，将X填入，然后不断**上滤(percolate up)**，知道元素X移动到合适的位置
2. 为了使一个小值在插入后能顺利结束while循环，可以在位置0处存放一个很小的值作为哑信息(dummy piece of information)，这个值也可以称作是哨兵(sentinel)
```cpp
void Insert(ElementType X, PriorityQueue H)
{
	int i;

	if(isFull(H))
	{
		Error("Priority Queue is Full");
		return;
	}
	for(i=++H->Size; H->Elements[i/2]>X; i/=2)
		H->Elements[i]=H->Elements[i/2];
	H->Elements[i]=X;
}
```
3. 最坏情况（插入元素是新的最小元）下，时间复杂度为O(log N)，平均看来要好得多，执行一次插入，平均需要2.6次比较、元素平均上移1.6层
### DeleteMin
1. 在顶部删去最小元，形成一个空穴，然后不断**下滤(percolate down)**，将空穴的两个子节点中的较小者移入空穴，直到堆中的最后一个元素X可以放入空穴
```cpp
ElementType DeleteMin(PriorityQueue H)
{
	int i, Child;
	ElementType MinElement, LastElement;

	if(isEmpty(H)){
		Error("Priority queue is empty");
		return H->Elements[0];   //return the sentinel
	}
	MinElement = H->Elements[1];
	LastElement = H->Elements[h->Size--];
	
	for(i = 1; i*2<=H->Size; i=Child){
		Child = i*2;
		if(Child != H->Size 
			&& H->Elements[Child+1] < H->Elements[Child])
			H->Elements[i] = H->Elements[Child];
		else
			break;
	}
	H->Elements[i] = LastElement;
	return MinElement;
}
```
2. 算法的时间复杂度在最坏和平均情况下均为O(log N)
### 其他操作
1. **DecreaseKey(P, Δ, H)**：降低在位置P处的关键字的值，并通过上滤对堆进行调整
2. **IncreaseKey(P, Δ, H)**：增加在位置P处的关键字的值，并通过下滤对堆进行调整
3. **Delete(P, H)**：删除堆中位置P上的节点，一般先是DecreaseKey(P, ∞, H)，然后再DeleteMin(H)
4. **BulidHeap**：总的运行时间是O(N)而不是O(log N)（使用平均时间计算而不是最坏时间），一般的算法是将N个关键字以任意顺序放入树中，保持结构特性，然后不断下滤：
```cpp
for(i=N/2; i>0; i--)
	PercolateDown(i);
```
**定理**包含2^{h+1}-1个节点的高为h的理想二叉树(perfect binary tree)的节点的高度和为2^{h+1}-1-(h+1)
## d-堆
1. d-堆的每一个节点都有d个子节点
2. Insert的时间改进为O(log_d N)，但对比较大的d来讲，DeleteMin的时间增加到O(d log_d N)
3. 对于一个下标为a的节点，其亲节点的下标b=[(a+1)/d]