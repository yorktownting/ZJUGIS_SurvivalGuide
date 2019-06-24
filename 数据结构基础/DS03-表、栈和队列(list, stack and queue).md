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
    - 读到一个操作符：将其和栈顶的操作符进行比较，如果栈顶操作符优先级不高于所读操作符，弹出栈顶操作符；如此循环直到栈顶操作符优先级高于所读操作符，然后（无论有无弹出）压入所读操作符
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



