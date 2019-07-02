# DS02-算法分析(Algorithm Analysis)

## 算法

- 什么是算法：
    - 一个有限指令集
    - 接受一些输入（或在有些情况下不需要输入）
    - 产生输出
    - 一定在有限步骤之后终止
    - 每一条指令必须：
        - 有充分明确的目标，不可有歧义
        - 在计算机能处理的范围之内
        - 描述应当不依赖于任何一种计算机语言以及具体的实现手段
    
- 算法可以用自然语言、伪代码或者是流程图来表示

- 衡量算法好坏的两个指标：

    - 空间复杂度$S(N)$：根据算法写成的程序在执行中占用存储单元的长度
    - 时间复杂度$T(N)$：根据算法写成的程序在执行中耗费时间的长度
    - 分析复杂度时，常常关注其最坏情况下的复杂度和平均复杂度（分析较为困难）

- 复杂度的定义：

    - $T(N)=O(f(N))$：表示$f(N)$为复杂度的上界
    - $T(N)=Ω(f(N))$：表示$f(N)$为复杂度的下界
    - $T(N)=Θ(f(N))$：表示$f(N)$既是复杂度的上界，又是复杂度的下界
    - $T(N)= o (f(N))$：表示$f(N)$是复杂度的上界，但不是复杂度的下界

- 算法分析的法则：

    - $T_1(N)+T_2(N)=max(O_1(N),  O_2(N))$
    - $T_1(N)×T_2(N)=O_1(N) × O_2(N)$
    - 如果$T(N)$是一个k次多项式，则$T(N)=Θ(N^k)$
    - 对任意常数k，$log^kN=O(N)$.

- 复杂度的增长率：

    $c<logN<log^2N<N<NlogN<N^2<N^3<2^N<N!$



## 递归算法的时间与空间复杂度[CSDN](https://blog.csdn.net/superwangxinrui/article/details/79626870)

- 若以树的形式来表现递归之间的关系，则：

    - 时间复杂度为二叉树的节点个数，以求斐波那契数列为例，$T(N)=O(2^N)$

    - 空间复杂度为二叉树的深度，以求斐波那契数列为例，$S(N)=O(N)$

        > 以循环算法求斐波那契数列时，时间复杂度为$O(N)$， 空间复杂度为$O(1)$



[练习1](https://www.cnblogs.com/nonlinearthink/p/11037523.html)

---

# DS03-表、栈和队列(list, stack and queue)

## 抽象数据类型(ADT)

- 模块化程序的优点：
    1. 调试容易
    2. 多人同时编程
    3. 把依赖关系局限在一个例程中
- 抽象数据类型：是一些操作的集合；是数学的抽象；在定义中没有涉及如何实现操作；是模块化设计的扩充

## 表(List)ADT

- 空表：大小（表中所含元素）为0的表
- 常用的操作：PrintList, MakeEmpty, Find, Insert, Delete, FIndKth

### 链表(Linked List)

- 使用Next指针实现对元素的不连续储存

- **表头(header)**：或称**哑结点(dummy node)**，处在位置0处，用以表明链表的起始端（本身不存放数据）

- 插入和删除：

    ```cpp
    //注意malloc和free的使用：
    void Insert(ElementType X, Position P)
    {
        Position TempCell;
        TempCell=malloc(sizeof(struct Node));   
        	//注意，sizeof(PtrToNode)的方式不能给结构体分配足够的空间
        if(TempCell == NULL)
            FatalError("Out of space!");
        TempCell->Element = X;
        TempCell->Next = P->Next;
        P->Next = TempCell;
    }
    void Delete(ElementType X, List L)
    {
        Position P, TempCell;
        P = FindPrevious(X, L);
        if(!IsLast(P, L))
        {
            TempCell = P->Next;
            P->Next = TempCell->Next;
            free(TempCell);   //释放被删除节点的内存
        }
    }
    ```

- 另外，还有双链表和循环链表以满足不同的需求

- **多重表**：可以用于替代二维数组（e.g. 学生选课情况）

- 在无法使用指针的情况下（如BASIC语言），可以使用“全局数组+游标”来实现链表

## 栈(Stack)ADT

- 栈，有时又叫做LIFO（后进先出）表，只能在表的尾端进行插入和删除操作：

    - **顶(top)**：表的尾端
    - **进栈(Push)**：插入新元素
    - **出栈(Pop)**：删除最后插入的元素

- 栈可以通过链表实现，其栈顶是链表的前端——但是对于malloc和free的调用的开销非常昂贵

- 栈也可以通过数组实现（这种方法更流行）唯一潜在的危害是需要实现声明数组的大小——不过在典型的应用程序中，即使有相当多的栈操作，在任一时刻栈元素的实际个数不会太大，空间占用是可以接受的

    ```cpp
    struct StackRecord
    {
        int Capacity;
        int TopOfStack;
        ElementType *Array;
    }
    ```

### 表达式的后缀(postfix)记法

- 使用栈可以方便的计算使用后缀记法的表达式：
    - 读取到一个数，将其压入栈
    - 遇到一个运算符，将其作用在栈弹出的两个数上，再将所得结果压入栈
    - 该算法的时间复杂度是线性的
- 使用栈还可以实现中缀表达式到后缀的转换：
    - 读到一个数，将其放到输出中
    - 读到一个操作符：将其和栈顶的操作符进行比较，如果栈顶操作符优先级**不高于**所读操作符，弹出栈顶操作符；如此循环直到栈顶操作符优先级高于所读操作符，然后（无论有无弹出）压入所读操作符
    - 左括号有最高的优先级，除非遇到右括号，它不会被弹出
    - 读到右括号时，栈元素将一直弹出直到左括号也弹出（但是左右括号不进入输出）
    - 该算法的时间复杂度是线性的

### 尾递归

    ```cpp
    void PrintList(List L)
    {
        if(L != NULL){
            Print(L->Element);
            PrintList(L->Next);
        }
    }
    ```

- 形如以上例程的递归被称为尾递归：在函数的最后一行进行了递归操作，这种递归实际上没有需要存储的数据，因此可以改成goto语句并重新赋值参数实现
- 尾递归总是能够被彻底除去的，一般使用栈来实现。 

## 队列(queue) ADT

- 和栈一样，队列也是表，使用队列时插入在一端进行而删除在另一端进行

    - **队尾(rear)**：表的末端，即插入端
    - **队头(front)**：表的顶端，即删除端
    - **入队(Enqueue)**：在队尾插入一个元素
    - **出队(Dequeue)**：在队头删除一个元素

- 队列可以通过循环数组来实现：

    ```cpp
    typedef struct QueueRecord *Queue;
    struct QueueRecord
    {
        int Capacity;
        int Front;
        int Rear;
        int Size;    //最好还是使用一个成员来表示队列此时的大小，这样可以避免很多麻烦
        ElementType *Array;
    }    
    void MakeEmpty(Queue Q)
    {
        Q->Size = 0;
        Q->Front = 1;
        Q->Rear = 0;
    }
    int isFull(Queue Q)
    {
        return Q->Size == Q->Capacity;
    }
    int isEmpty(Queue Q)
    {
        return Q->Size == 0;
    }
    void Enqueue(ElementType X, Queue Q)
    {
        if(isFull(Q))
            Error("Full queue");
        else
        {
            Q->Rear++;
            if(Q->Rear == Q->Capacity)
                Q->Rear=0;
            Q->Size++;
            Q->Array[Q->Rear]=X;
        }
    }
    ```

[练习3-1-链表](https://www.cnblogs.com/nonlinearthink/p/11039494.html)
[练习3-2-栈](https://www.cnblogs.com/nonlinearthink/p/11040795.html)
[练习3-3-队列](https://www.cnblogs.com/nonlinearthink/p/11041120.html)

---

# DS04-树(tree)

## 预备知识

- 一棵树是一些节点的集合，这个集合可以是空集

- 一个非空的树由1个称作**根(root)**的节点$r$以及0个或多个非空的（子）树$T$~1~, $T$~2~, ... , $T$~k~组成，这些子树中每一棵的根都被来自根$r$的一条有向的边(edge)所连接

- 每一棵子树的根叫做根$r$的**child**，而$r$是每一棵子树的**parent**

- 一棵非空树是N个节点和N-1条边的集合

- 没有child的节点称为**树叶(leaf)**

- 具有相同parent的节点称为**sibling**

- 从节点$n_1$到$n_k$的**路径(path)**定义为节点$n_1, n_2, ... , n_k$的一个序列，使得对于1≤$i$<$k$，节点$n_i$是$n_i+1$的parent。路径的**长(length)**为路径上的边的条数，即$k-1$，节点到节点本身的路径长度为0，在一棵树中从根到每个节点恰好存在一条路径

- 对任意节点，其==**深度(depth)**为根到这个节点的唯一路径的长==，根的深度为0

- 对任意节点，其==**高(height)**是从这个节点到一个叶节点的最长路径的长==，每个叶节点的高都为0

- 对任意节点，其==**度(degree)**是这个节点的子树的数目==，根节点的度为0

- 一棵树的深度等于他最深的树叶的深度，高等于它的根的高，度等于它所有节点的度的最大值

- 若存在从$n$~1~到$n$~2~的一条路径，那么$n_1$是$n_2$的一位**祖先(ancestor)**，而$n_2$是$n_1$的一个**后裔(descendant)**，如果两个节点不同，那么称为真祖先和真后裔

- 一种简单的树的节点的结构：

    ```cpp
    typedef struct TreeNode *PtrToNode;
    struct TreeNode
    {
        ElementType Element;
        PtrToNode FirstChild;
        PtrToNode NextSibling;
    }
    ```

- 树的遍历：先序(preorder traversal)、中序(inorder)、后序(postorder)、层序(levelorder)

- 层序遍历需要借助队列实现，难点在于判断哪些是“一层的”

### 表达式树

- 操作数（operand）存放在表达式树的leaves中，操作符(operator)放在其他节点
- 中缀表达式可以通过中序遍历产生
- 后缀表达式可以通过后续遍历产生
- 前缀表达式可以通过先序遍历产生
- 通过栈可以将后缀表达式方便地转化为表达式树（遇到操作符就出栈）：
    1. 读取到输入为操作数，就创建两棵单节点树并将它们的指针压入栈中
    2. 如果读到操作符，就将栈最顶端的两个树的指针弹出，作为该操作符的两个子树，形成新树并压入栈中
    3. 当最后一个操作符读入吼，只留下一棵树在栈中，这就是最后要得到的表达式树

## 二叉查找树

- 性质：对于树中每个节点$X$，他的左子树中所有的关键字的值都小于$X$的关键字值，而右子树中的所有关键字的值都大于$X$的关键字值。（假定所有的关键字都是互异的）

### Find (FindMin&FindMax)

- 使用尾递归来完成（算法的栈空间不过是$O(logN)$的量）
- 特别要小心空树这种退化情况，避免死循环递归

    ```cpp
    //以FindMin为例:
    Position FindMin(SearchTree T)
    {
        if(T == NULL)
            return NULL;    //一定要先考虑是不是是空树，否则就会一直递归
        else
            if(T->Left ==NULL)
                return T;
            else
                return FindMin(T->Left);
    }
    ```

### Insert

```cpp
SearchTree Insert(ElementType X, SearchTree T)
{
    if(T == NULL)
    {
        //create and return a one-node tree
        T = malloc(sizeof(struct TreeNode));
        if(T == NULL)
            FatalError("Out of Space!");
        else{
            T->Element = X;
            T->Left = T->Right = NULL;
        }
    }
    else if(X < T->Element)
        T->Left = Insert(X, T->Left);
    else if(X > T->Element)
        T->Right = Insert(X, T->Left);
    //else: X==T->Element: X is already in the tree, do nothing
    return T;
}
```

### Delete

- 删除一个节点主要用下面几种可能的情况：
    1. 节点是leaf：可以被立刻删除
    2. 节点只有一个child：节点可以在父节点调整指针绕过该节点后被删除
    3. 节点有两个child：用该节点右子树的最小数据代替该节点的数据，然后递归的删除那个节点（此时需要再次查找）——这很简单，因为右子树的最小节点不可能有Left-Child，所以此时回到第二种情况
        **NOTE**：但是这种操作方法会导致多次删除后树会明显地不平衡
- 如果删除的次数不多，可以采用**懒惰删除(lazy deletion)**的策略：只做一个删除记号

### 平均情形分析

- 树的所有节点的平均（期望）深度是$O(logN)$
- **内部路径长(internal path length)**：一棵树所有节点的深度的和。二叉查找树的内部路径长平均为$O(N*logN)$
- 对于一一棵平衡的二叉树，其Find, FindMin/Max, Insert, Delete的操作的平均时间都是$O(logN)$。

### 线索二叉树(Threaded Binary Tree)

- 一个二叉树若有$n$个节点，那么就有$2n$个指针，但是树的边只有$n-1$个，剩余的$n+1$个指针可以作为**线索(thread)**指向其他节点：

    - 如果`ptr->leftChild`为空，则存放指向中序遍历序列中该结点的前驱结点。这个结点称为ptr的中序前驱；

    - 如果`ptr->rightChild`为空，则存放指向中序遍历序列中该结点的后继结点。这个结点称为ptr的中序后继；

    - 如果前驱/后继节点为空（最左侧/右侧的叶结点），那么相应的指针指向NULL

    - 当然指针也可以以前序遍历序列、后序遍历序列的方式来安排

    - 实现方法（以中序为例）：

        > 在遍历递归中实现，函数的参数为当前结点current,和上次访问的结点previous. 
        > (1)若previous的右指针为空,则把previous的右指针指向current,并把previous的RTag设置为true; 
        > (2)若current的左指针为空,则把current的左指针指向previous,并把current的LTag设置为true;

[练习4-1-树](https://www.cnblogs.com/nonlinearthink/p/11042402.html)
[练习4-1-二叉搜索树](https://www.cnblogs.com/nonlinearthink/p/11042714.html)

---

# DS05-散列（Hashing）

## Basic of hash

1. 表的大小最好是素数
2. 装填因子(load factor)：$λ=n/TableSize$
3. 在散列函数中，可以用移位运算来代替乘法（乘数需要用2的次幂来代替），用按位取或来代替加法，从而加速计算

## 分离链接法(separate chaining)

1. 将散列到同一个值的所有元素保留到一个表中，并使用链表连接

## 开放定址法(open addressing hashing)

### 线性探测法(linear probing)

1. 冲突数据被直接放入下一个空闲地址
2. 占据的单元会形成一些区块，形成**一次聚集**(primary clustering)
3. 装填因子大于0.5时，线性探测的效率会大大降低

### 平方探测法(quadratic probing)

1. **定理**：==如果使用平方探测，且表的大小是素数，那么当表至少有一半是空的时候，总能够插入一个新的元素==
2. 下面是平方探测法的Find例程：

    ```cpp
    Position Find(ElementType Key, HashTable H)
    {
        Position CurrentPos;
        int CollisionNum;

        CollisonNum=0;
        CurrentPos=Hash(Key,H->TableSize);
        while(H->TheCells[CurrentPos].Info!=Empty &&
            H->TheCells[CurrentPos].Element!=Key)
        {
            CurrentPos+=2*(++CollisionNUm)-1;
            if(CurrentPos>=H->TableSize)
                CurrentPos-=H->TableSize;  
                //此处减的效率高于取余，因为CollisionNum不会大于2*TableSize
        }。
        return CurrentPos;
    }
    ```

3. 下面是平方探测法的Insert程：

    ```cpp
    void Insert(ElementType Key,HashTable H)
    {
        Position Pos;

        Pos=Find(Key,H);
        if(H->TheCells[Pos].Info!=Legitimate)
        {
            H->TheCells[Pos].Info=Legitimate;
            H->TheCells[Pos].Element=Key;
        }
    }
    ```

4. 虽然平方探测避免了一次聚集的情况，但是散列到同一位置上的那些元素将探测相同的备选单元，形成二次聚集(secondary clustering)

### 双散列

1. $F(i) = i*hash_2(X)$，即用第二个散列函数算出x的散列值，然后在距离$hash_2( X )$，$2hash_2(X)$的地方探测。
2. 一种良好的$hash_2$：$hash_2(X) = R-(X mod R)$，其中R为小于TableSize的素数

## 再散列

1. 对于使用平方探测的开放定址散列法，当散列表太满时，为了避免效率严重下降，可以：
    1. 另外建立一个大约两倍大的表（及一个新的散列函数）
    2. 扫描整个原始散列表
    3. 计算每个未删除的元素的新散列值并将其插入到新表中

## 散列的复杂度

- 从一个散列中进行查找操作的复杂度并不确定，需要看是否发生冲突、冲突有多严重
- 如果有N个元素发生了冲突，要找到这N个元素所需要进行的探测次数为$N(N+1)/2$

[练习5](https://www.cnblogs.com/nonlinearthink/p/11044752.html)

---

# Ds06-优先队列·堆(priority queue · heap)

## 二叉堆(binary heap)

1. 二叉堆普遍的用在实现优先队列上，当堆(heap)这个名词不加修饰地使用的时候，一般就是指二叉堆对优先队列的实现
2. 和二叉查找数一样，堆也有两个性质：结构性和堆序性

### 结构性质

1. ==堆是一棵 **完全二叉树**(complete binary tree)==——除了底层其他节点被完全填满的二叉树（底层元素从左到右填入）
2. 完全二叉树高度_h=logN_
3. 完全二叉树因为很有规律性，所以可以使用数组表示，对于数组任意位置_i_上的元素：
    1. Left-Child在位置_2i_上
    2. Right-Child在位置_2i+1_上
    3. Parent在位置[_i/2_]上

### 堆序性质

1. 对于每一个节点X，X的父亲中的关键字小于（或等于）X中的关键字，根结点除外

### Insert

1. 在下一个空闲位置创建一个空穴，将X填入，然后不断**上滤(percolate up)**，知道元素X移动到合适的位置
2. 为了使一个小值在插入后能顺利结束while循环，可以在位置0处存放一个很小的值作为哑信息(dummy piece of information)，这个值也可以称作是**哨兵(sentinel)**

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

3. 最坏情况（插入元素是新的最小元）下，时间复杂度为$O(log N)$，平均情况要好得多，执行一次插入，平均需要2.6次比较、元素平均上移1.6层

### DeleteMin

1. 在顶部删去最小元，形成一个空穴，然后不断**下滤(percolate down)**，将空穴的两个子节点中的较小者移入空穴，直到==堆中的最后一个元素X可以放入空穴==（即此时空穴的两个子节点均大于X）

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

2. 算法的时间复杂度在最坏和平均情况下均为$O(log N)$

### 其他操作

1. **DecreaseKey(P, Δ, H)**：降低在位置P处的关键字的值，并通过上滤对堆进行调整
2. **IncreaseKey(P, Δ, H)**：增加在位置P处的关键字的值，并通过下滤对堆进行调整
3. **Delete(P, H)**：删除堆中位置P上的节点，一般先是`DecreaseKey(P, ∞, H)`，然后再`DeleteMin(H)`
4. **BulidHeap**：总的运行时间是$O(N)$而不是$O(N log N)$（使用平均时间计算而不是最坏时间），一般的算法是==将N个关键字以任意顺序放入树中，保持结构特性，然后不断下滤==：

    ```cpp
    for(i=N/2; i>0; i--)
        PercolateDown(i);
    ```
    
5. 注意：随机访问堆中任意键值的操作并不能在$O(log N)$的时间内完成——不是二叉搜索树

- **定理**：包含$2^{h+1}-1$个节点的高为h的理想二叉树(perfect binary tree)的节点的高度和为$2^{h+1}-1-(h+1)$
- ==对于任何的一个无序数组，可以在$O(N)$的时间内，将其转化为最大/最小堆：从倒数第二层开始，将每个节点与大于/小于其值（都大于/小于则选择更大/更小）的方向下滤，直到来到root层==

## d-堆

1. d-堆的每一个节点都有d个子节点
2. Insert的时间改进为$O(log_d N)$，但对比较大的d来讲，DeleteMin的时间增加到$O(d log_d N)$
3. 对于一个下标为 $i$ 的节点:
    - 其==亲节点的下标 $p=[(i+d-2)/d]$==；
    - 其==第一个子节点的下标 $c_1=(i-1)d+2$==；
    - 其==最后一个子节点的下标 $c_n=id+1$==.

[练习6](https://www.cnblogs.com/nonlinearthink/p/11043033.html)

---

# DS07-排序

## 插入排序(Insertion Sort)

- 插入排序由N-1趟(pass)排序组成，每次保证前P个元素为已排序状态

    ```cpp
    void InsertionSort(ElementType A[], int N)
    {
      int j, P;
      ElementType Tmp;
      for(P=1; P<N; P++){
        Tmp = A[P];
        for(j=P; j>0 && A[j-1]>Tmp; j--)
          A[j]=A[j-1];
        A[j]=Tmp;
      }
    }
    ```

- 插入排序的最坏和平均时间是Θ(N^2^)
- 最好情况是$O(N)$（逆序）
- 插入排序的一个特点是：不到最后一轮结束，所有的元素可能都不在其最终排序的位置上

## 一些简单排序算法的下界

- **逆序(inversion)**是指数组中具有性质_i<j_但_A[i]>A[j]_的序偶(A[i], A[j])
- 定理：N个互异数的数组的平均逆序数是N(N-1)/4
- 定理：通过交换相邻元素进行排序的任何算法平均需要Ω(N^2^)的时间

## 希尔排序(Shellsort)

- 有时也称为缩小增量排序(diminishing increment sort)
- 比较相距一定间隔的元素，各趟比较所用的距离随算法的进行而减小，知道只比较相邻元素的最后一趟排序为止
- **增量(increments)**指的是参与比较的相邻元数之间的下标差：
    如：增量为3时，元素数组 $a_0, a_1, a_2, ... , a_{10}$中，$ \{ a_0,a_3,a_6,a_9 \} $, $ \{ a_1,a_4,a_7,a_10 \} $, $ \{ a_2,a_5,a_8 \} $分别为三组内部排序的子序列
- 希尔排序使用一个**增量序列(increment sequence): $h_1, h_2, ... , h_t$，只要$h_1=1$，任何增量序列都是可行的：
    - 一个流行但不高效的序列是希尔增量$h_t =[N/2]$（或者$h_k=[h_{k+1}/2]$）
    - **Hibbard增量**：$1, 3, 7, ... , 2^{k-1}$，相邻增量没有公因子

    ```cpp
    //使用希尔增量的希尔排序
    void Shellsort(ElementType A[], int N)
    {
      int i, j, Increment;
      ElementType Temp;

      for(Increment=N/2; Increment>0; Increment/=2){
        for(i=Increment; i<N; i++){
          Temp = A[i];
          for(j=i; j>=Increment; j-=Increment){
            if(Temp < A[j-Increment])
              A[j]=A[j-Increment];
            else
              break;
          }
          A[j]=Temp;
        }
      }
    }
    ```

- 使用希尔增量时，希尔排序的最坏情况是$Θ(N^2)$——主要问题在于这些增量对未必是互质的。
- 使用Hibbard增量时，希尔排序的最坏情况是$Θ(N^{3/2})$，猜测其平均情况是$Θ(N^{5/4})$
- 还有一种$\{1,\ 5,\ 19,\ 41,\ 109,\ ...\}$的Sedgewick增量，在实践中比Hibbard增量要好得多，最坏运行时间为$Θ(N^{5/4})$，平均为$Θ(N^{7/6})$

## 堆排序(Heapsort)

- 总的运行时间为$O(N logN)$，但是实践中慢于使用Sedgewick增量的希尔排序

- 在每次DeleteMax后，堆缩小1，因此刚好将堆最后空出的单元用来存放刚刚删去的元素（注意存放完这个元素后，这个单元要从堆中“分离”出去）

- 核心步骤：

    1. 建堆
    2. 交换堆顶和最后一个元素（DeleteMin/DeleteMax)
    3. 分离出最后一个单元，对剩下的堆数组从新排序生成堆

    ```cpp
    #define LeftChlid(i) (2*(i)+1)
    
    void PercDown(ElementType A[], int i, int N)
    {
        int Child;
        ElementType Temp;
        
        for(Temp=A[i]; LeftChild(i)<N; i=Child)
        {
            Child = LeftChild;
            if(Child!=N-1 && A[Child+1]>A[Child])
                Child++;
            if(Temp<A[Child])
                A[i]=A[Child];
            else
                break;
        }
        A[i]=Temp;
    }
    
    void Heapsort(ElementType A[], int N)
    {
        int i;
        
        for(i=N/2; i>=0; i--)
        	PercDown(A, i, N);
        for(i=N-1; i>0; i--)
        {
            Swap(&A[0], &A[i]);
            PercDown(A, 0, i);
        }
    }
    ```

- 程序结束后，数组所有的单元都从堆中“分离”，整个数组就是我们需要的排序序列，因此若要进行升序排序，则需要建立大根堆（每次的“最大“都在数组”最后“）

- 对N个互异项的随机排列进行堆排序，所用的比较平均次数为$2N*logN - O(N*loglogN)$；堆排序总是使用至少$N*logN-O(N)$次比较

- 由于初始构建堆所需要的比较次数较多，==不适合排序序列个数较少的情况==——元素较少时甚至性能不如直接插入排序

## 归并排序(Mergesort)

- 归并排序是一种递归算法，以$O(N*logN)$的最坏情形运行时间运行，而所用的比较次数几乎是最优的
- 假设初始序列含有n个记录，则可以看成是n个有序的子序列，每个子序列的长度为1，然后两两归并，得到$[n/2]$ _($[x]$表示不小于 $x$ 的最小整数)_ 个长度为2或1的有序子序列；再两两归并，…，如此重复，直至得到一个长度为n的有序序列为止，这种排序方法称为2路归并排序
- 归并排序的核心思路是将两个已排序的分表合并到一个表中：通过比较两个指针所在位置的元素的大小，将小的（假设分表是从小到大排列好的）那个元素填到新表中，并将指针后移
  
    - _每次比较都只把一个元素加入到新表中，但最后一次比较除外_
- 下面是一段典型的归并排序算法

    ```cpp
    //for function Merge(), the Lpos and Rpos represent for the strat of left/right half
    void Merge(ElementType A[], ElementType TempArray[], int Lpos, int Rpos, int RightEnd)
    {
        int i, LeftEnd, NumElements, TempPos;
        LeftEnd = Rpos-1;
        TempPos = Lpos;
        NumElements = RightEnd-Lpos+1;

        while(Lpos<=LeftEnd && Rpos<=RightEnd){
            if(A[Lpos]<=A[Rpos])
                TempArray[TempPos++]=A[Lpos++];
            else
                TempArray[TempPos++]=A[Rpos++];
        }
        //the next 2 while copy the rest of half lists
        while(Lpos<=LeftEnd)
            TempArray[TempPos++]=A[Lpos++];
        while(Rpos<=RightEnd)
            TempArray[TempPos++]=A[Rpos++];

        for(i=0; i<NumElements; i++,RightEnd--)
            A[RightEnd]=TempArray[RightEnd]  //利用RightEnd从右至左拷贝TempArray回A
    }

    void MSort(ElementType A[], ElementType TempArray[], int Left, int Right)
    {
        int Center;
        if(Left<Right)
        {
            Center=(Left+Right)/2;
            Msort(A,TempArray,Left,Center);
            MSort(A,TempArray,Center+1,Right);
            Merge(A,TempArray,Left,Center+1,Right);  //Merge后将TempArray中排列好的数据复制回A
        }
    }

    void Mergesort(ElementType A[], int N)
    {
        ElementType* TempArray;
        TempArray = malloc(N*sizeof(ElemenType));
        if(TempArray!=NULL)
        {
            MSort(A,TempArray,0,N-1);
            free(TempArray);
        }
        else
            FatalError("No space for temp array!");
    }
    ```

- 如上的Merge方法是比较巧妙的，避免了每次调用都局部声明一个临时数组，从而在任意时刻都可能有logN个临时数组处在活动期，并减少了malloc占用的时间
- 归并排序虽然有着很好的运行时间，但是很难用于主存排序
- 使用递归方法实现归并排序时，空间复杂度为$O(log2^N)$；使用非递归方法时，空间复杂度为$O(N)$.

## 快速排序(Quicksort)

- 快速排序是在实践中最快的已知排序算法，平均运行时间为$O(N*logN)$，对数组$S$进行排序的过程主要分为以下四步：

    1. 如果$S$中元素个数是0或者是1，则返回
    2. 取$S$中任一元素$v$，称为**枢纽元(pivot)**
    3. 将$S-${$v$}（即剩下的元素）以$x≤v$和$x≥v$分成两个不相交的集合$S$~1~和$S$~2~
    4. 返回{quicksort($S$~1~), $v$, quicksort($S$~2~)}

    - 值得注意的是，第三步的划分方法不是唯一的，一般只要尽量把关键字对半分到两个集合中即可

    ```cpp
    private static void qsort(int[] arr, int left, int right){
        if (left < right){
            int pivot=partition(arr, left, right);        //将数组分为两部分
            qsort(arr, low, pivot-1);                   //递归排序左子数组
            qsort(arr, pivot+1, high);                  //递归排序右子数组
        }
    }
    ```

    

### 选取枢纽元

- 如果直接选用第一个元素作为枢纽元，那么在输入经过与排序或者反序时，分割会变得劣质，将花费二次的时间——同理，选取前两个互异关键字中的较大者作为枢纽元也是不可取的
- 随机选取枢纽元相对来讲比较安全，但是随机数生成的耗费较大
- 三数中值分割法(Median-of-Three Partioning)：使用数组左端、右端和中心位置上的三个元素的中值作为枢纽元

### 分割策略

1. 将枢纽元与最后一个元数交换
2. 添加指针i, j：i指向第一个元素，j指向倒数第二个元素
3. 当i在j的左边时，右移i直到指向的元素大于枢纽元；同样地，左移j直到指向的元素小于枢纽元
4. 互换两个元素
5. 如此反复直到i和j交错，将枢纽元和i指向的元素交换

- 对于题目中给一串数组进行一轮快排：

    > 不做特别说明，第一个元素就是pivot，把它移走（产生一个空位），从数组末端开始找小数（小于pivot）放入前方空位，此时小数的位置成了空位，再从数组前端开始找大数放入后方空位；如此交替，直到
    >
    > 1. 没有合适的小数/大数而使pivot被放入空位
    > 2. 或再空位后方找不到小数/前方找不到大数
    >
    > 此趟排序结束
    >
    > {15，9，7，8，20，-1，7，4}
    >
    > ​                    ↓
    >
    > {4，9，7，8，7，-1，15，20}

#### 等于枢纽元的元素

- 一个重要的问题是如何处理等于枢纽元的元素：
    - ==无论是i还是j遇到等于枢纽元的元素，都让i和j一起停止，进行一次交换==
    - 只有这样，在所有元素值都相等时，才能保证$O(logN)$的时间复杂度
    - 如果只停一个或者都不停，那么在所有元素值都相等时，时间复杂度为$O(N^2)$.

## 排序的一般下界

- 任何只用到比较的排序算法在最坏情况下也需要$Ω(N*logN)$次比较

    > The best "worst-case time complexity" for any algorithm that sorts by comparisons only must be $O(NlogN)$

## 桶式排序(Bucket Sort)

1. 输入数据A~1~, A~2~, ... , A~N~都是小于M的正整数
2. 使用一个大小为M的称为Count的数组，即这个数组有M个单元（桶），这些桶被初始化为空
3. 当读A~i~时，Count[A~i~]增加1
4. 所有数据读入后，扫描数组Count，打印出排序后的表

- 该算法的时间复杂度为O(M+N)

## 基数排序(Radix Sort)

- 和桶排序的思路类似，将整数按位数切割成不同的数字，然后按每个位数分别比较

- 步骤:

    1. 将整数按位数切割成不同的数字，然后按每个位数分别比较。从低位开始将待排序的数按照这一位的值放到相应的编号为0~9的桶中。
    2. 等到低位排完得到一个子序列，再将这个序列按照次低位的大小进入相应的桶中，一直排到最高位为止，数组排序完成

- 相比桶排序，节省了内存空间

- 基数排序实际上是一种**多关键字排序**，多关键字排序有两种思路：

    1. **高优先级排序(MSD)**：先使用高优先级的关键字进行分组（e.g. 在排序扑克牌时，可以规定花色比点数优先，即可先根据花色分为四类：黑桃大于红心，红心大于梅花，梅花大于方块）
    2. **低优先级排序(LSD)**：先使用低优先级的关键字进行分组（e.g. 在排序扑克牌时，根据点数分为13类）（e.g. 在排序数字时，先看个位数）

    - 多关键字排序时，关键字的空间不能太大、分组不能过多——会导致效率很低。多关键字排序适合于有多种关键字，但每个关键字又不是很大的情况

## 总结

|   排序方法   |      平均情况       |   最好情况   |  最坏情况  |     辅助空间     | 稳定性 |
| :----------: | :-----------------: | :----------: | :--------: | :--------------: | :----: |
|   冒泡排序   |      $O(N^2)$       |    $O(N)$    | $O(N^2 )$  |      $O(1)$      |  稳定  |
| 简单选择排序 |      $O(N^2)$       |   $O(N^2)$   |  $O(N^2)$  |      $O(1)$      | 不稳定 |
| 直接插入排序 |      $O(N^2)$       |    $O(N)$    |  $O(N^2)$  |      $O(1)$      |  稳定  |
|   希尔排序   | $O(NlogN)$~$O(N^2)$ | $O(N^{3/2})$ |  $O(N^2)$  |      $O(1)$      | 不稳定 |
|    堆排序    |     $O(NlogN)$      |  $O(NlogN)$  | $O(NlogN)$ |      $O(1)$      | 不稳定 |
|   归并排序   |     $O(NlogN)$      |  $O(NlogN)$  | $O(NlogN)$ |  $O(N)$(非递归)  |  稳定  |
|   快速排序   |     $O(NlogN)$      |  $O(NlogN)$  |  $O(N^2)$  | $O(logn)$~$O(N)$ | 不稳定 |

- ==不稳定的排序：选择排序、希尔排序、堆排序、快速排序（快希选堆）==

> 参考：[七大常见排序算法总结](https://blog.csdn.net/lutianfeiml/article/details/51958962)

[练习7](https://www.cnblogs.com/nonlinearthink/p/11082433.html)
> NOTE: 有几道快排的答案好像有点问题

---

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
- 如果一个类有$m$个成员，$n$个等价关系，那么这个类至少有$m-n$个等价类

### 不相交集的Union/Find算法：

- 若要添加关系a～b，首先需要对a、b执行**Find**来检验是否处在同一个等价类中；如果不是，进一步执行**Find**操作
- 上条目的算法是一个**动态(dynamic)**算法，同时也是**联机(on-line)**操作

## 基本数据结构

- 用一棵棵树来表示每一个等价类集合，这些树被非显示地存储在一个数组中：数组的每个成员$P[i]$表示元素$i$的父亲，如果$i$是根，那么$P[i]=0$（也有用 $P[i] = -x$ 来表示该根节点表示的集合中的元素个数）
- 数组最开始全部为0（表示元素个数时全为-1）

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

- 为了尽量降低树的深度，提高Find()的效率，在Union时应当采用**按大小求并(union-by-size)**的策略，使得总让较小的树成为较大的树的子树
- 使用按大小求并的策略后，所有树的深度最多是$O(log N)$，也就是说，Find的运行时间是$O(log N)$
- 另外一种策略是**按高度求并(union-by-height)**，同样保证了所有树的深度最多是$O(log N)$。这种算法还是一种平缓的算法，只有当两个相等深度的树求并时高度才增加，下面是按高度求并的一段代码：

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

---
# DS09-图（Graph）

## 若干定义

1. 简单路径：路径中间没有重复经过的顶点
2. 环(loop)：图含有一条从一个顶点到它自身的边
3. 圈(cycle)：路径的起点和终点是同一个顶点
4. 无圈的(acyclic)
5. 有向无圈图(DAG)
6. 连通的(connected)：图的每一个顶点到其他每个顶点都存在一条路径，对于有向图，还可以分为强连通和弱连通（去掉方向形成的无向图（称为基础图(underlying graph)）连通）
7. 完全的(complete)：图的每对顶点之间都存在一条边
8. 度(degree)：该顶点上连接的边的数目，在有向图中还可以分为入度(in-degree)和出度(out-degree)。
    **NOTE**：
    1. 在连通图中，所有顶点的度之和必定为偶数
    2. 在有向图中，所有顶点的出度、入度之和是边数目的两倍

## 邻接矩阵与邻接表

1. 邻接矩阵(adjacency matrix)：适用于稠密图，空间需求$V^2$
2. 邻接表(adjacency list)：适用于稀疏图，空间需求$V+E$
3. 使用邻接表时，无向图每条边会出现在两个表中，因此会使用双倍的空间：$V+2E$。不过，在存储有向图时，也往往会多写一份逆邻接表来记录顶点的"进"的情况

## 拓扑排序

1. 拓扑排序==仅适用于有向无环图==
2. ==排序结果唯一的条件：每次循环中入度为0的节点有且仅有一个==
3. 以下是一种时间复杂度为$O(|E|+|V|)$的拓扑排序算法

    ```c
    //Kahn算法：
    //1.找到入度为0的顶点入队
    //2.除去与队首（出队）相邻所有边并减少其度数，再次加入入度数为0的点循环往复。
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
    }
    ```

## 最短路径算法

### 单源最短路径

1. 如果存在负值圈(negative-cost cycle)，最短路径问题就是不确定的

### 无权最短路径

1. 使用广度优先搜索(breadth-first search, BFS)，按层处理顶点。下面是一种时间复杂度为$O(|E|+|V|)$的BFS算法（使用邻接表时）
   
    ```cpp
    void BFS_Unweighted(Table T)
    {
        Queue Q;
        Vertex V,W;
        
        Q=CreateQueue(NumVertex);
        MakeEmpty(Q);
        
        Enqueue(S,Q);
        //T[S].Dist=0;   //S是源点
        
        while(!IsEmpty(Q)){
            V=Dequeue(Q);
            //T[V].Known=True;
            
            for each W adjacent to V
                if(T[W].Dist == Infinity){
                    T[W].Dist = T[V].Dist+1;		// Dist初始值均为无穷，记录到源点的距离
                    T[W].Path = V;				   // Path记录了上一个节点的编号
                    Enqueue(W, Q);
                }
        }
        DisposeQueue(Q);
    }
    ```
    

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

3. ==于这种贪婪算法，只要没有负值边，该算法总能顺利完成==
4. 算法的时间复杂度取决于图是稠密的还是稀疏的：
    - ==稠密图：使用邻接矩阵——$T=O(|V|^2+E)$==
    - ==稀疏图：使用邻接表+优先队列（最小堆）——$T=O(|E|*log|V|)$==

## 最小生成树(minimum spanning tree)

- 一个无向图的最小生成树是由该图的那些连接G的所有顶点的总花费最低的边构成的树(A spanning tree of a graph G is a tree which consists of V( G ) and a subset of E( G ))
- 在最小生成树中，边的条数为$|V|-1$

### Prim算法

- 选定一个节点作为根，并在其上添加边，每一步均是如此，最终使生成树一步步长成
- 算法在每一阶段都可以选择边$(u,v)$，其中$u$在生成树上而$v$不在
- Prim算法基本上和求最短路径的Dijkstra算法一样，只不过Prim算法在无向图上进行，因此每一条边都要放在两个邻接表中
- 时间复杂度：
    - ==不用堆时（适合稠密图）：$O(|V|^2)$==
    - ==用二叉堆（适合稀疏图）：$O(|E| log|V|)$==

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

- 最坏运行时间是$O(|E| log|E|)$——由于$|E|=O(|V|^2)$，因此==实际时间是$O(|E| log|V|)$==

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

- 该算法保证了每条边只访问一次，所以==只要是使用了链接表，执行遍历的总时间就是$O(|E|+|V|)$==
- DFS的非递归算法（使用栈）：
    1. 将源节点压入栈
    2. 选取一个与栈顶节点相邻且未被遍历过的点，进行标记，然后压入栈，重复，直到满足第3步的条件
    3. 如果栈顶节点没有未经遍历的临邻接点，将此节点弹出栈，重复第2步
    4. 栈全部弹空后，DFS完成

### 无向图 · 深度优先生成树（森林）

- 在对无向图进行深度优先搜索时，如果对于一条边，处理(v, w)时发现w已被标记，处理(w, v)时发现v也已被标记，那么用一条虚线的**背向边(back edge)**来表示

### 双连通性

- 如果一个连通的无向图中的任一顶点删除后，剩下的图仍然连通，那么这样的无向连通图就是**双连通的(bioconnected)**
- 如果一个图不是双连通的，那么，将其删除后图将不再连通的那些顶点叫做**割点(articulation point)**

- 执行深度优先搜索时，对于每个顶点称其先序编号为$Num(v)$

- 对于每个顶点$v$，其通过树的零条或多条边（可能还有一条背向边）达到的编号最低的点为$Low(v)$，即$Low(v)$是：
    - $Num(v)$
    - 所有背向边$(v,w)$中的最低$Num(w)$
    - 树的所有边$(v,w)$中的最低$Low(w)$

    中的最小者

- 割点的判定：

    - 根：有多于一个的子结点
    - 其他顶点 $v$：有某个子结点 $w$，使得 $Low(w)≥Num(v)$. （这个条件在根上是必定满足的）

### 欧拉回路

- ==所有顶点的度都为偶数的任何连通图必然有欧拉回路==
- 算法所需的时间为O(|E|+|V|)

## 图的边的问题：

- 对于有$N$个顶点的强连通图，至少有$N$条边，至多有$N(N-1)$条边
- 对于有$N$个顶点的无向图，至少有$N-1$条边（最小生成树），至多有$N(N-1)/2$条边

## BFS和DFS：

- ==深度优先是堆栈，广度优先是队列==
- 深度优先类似于先序遍历；广度优先类似于层次遍历



[练习9-1-图基础](https://www.cnblogs.com/nonlinearthink/p/11045582.html)
[练习9-2-图遍历](https://www.cnblogs.com/nonlinearthink/p/11078896.html)