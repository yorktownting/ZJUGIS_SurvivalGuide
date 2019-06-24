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
## 一些简单排序算法的下界
- **逆序(inversion)**是指数组中具有性质_i<j_但_A[i]>A[j]_的序偶(A[i], A[j])
- 定理：N个互异数的数组的平均逆序数是N(N-1)/4
- 定理：通过交换相邻元素进行排序的任何算法平均需要Ω(N^2^)的时间
## 希尔排序(Shellsort)
- 有时也称为缩小增量排序(diminishing increment sort)
- 比较相距一定间隔的元素，各趟比较所用的距离随算法的进行而减小，知道只比较相邻元素的最后一趟排序为止
- 希尔排序使用一个**增量序列(increment sequence)**_h1, h2, ... , ht_，只要h1=1，任何增量序列都是可行的：
	- 一个流行但不高效的序列是希尔增量ht =[N/2]（或者h(k)=[h(k+1)/2]）
	- **Hibbard增量**：1, 3, 7, ... , 2^k-1^，相邻增量没有公因子
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
- 使用希尔增量时，希尔排序的最坏情况是Θ(N^2^)——主要问题在于这些增量对未必是互质的。
- 使用Hibbard增量时，希尔排序的最坏情况是Θ(N^(3/2)^)，猜测其平均情况是Θ(N^(5/4)^)
- 还有一种{1, 5, 19, 41, 109, ...}的Sedgewick增量，在实践中比Hibbard增量要好得多，最坏运行时间为Θ(N^(5/4)^)，平均为Θ(N^(7/6)^)
## 堆排序
- 总的运行时间为O(N logN)，但是实践中慢于使用Sedgewick增量的希尔排序

- 在每次DeleteMin后，堆缩小1，因此刚好将堆最后空出的单元用来存放刚刚删去的元素

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

- 对N个互异项的随机排列进行堆排序，所用的比较平均次数为$2N*logN - O(N*loglogN)$；堆排序总是使用至少$N*logN-O(N)$次比较

## 归并排序(mergesort)

- 归并排序是一种递归算法，以$O(N*logN)$的最坏情形运行时间运行，而所用的比较次数几乎是最优的
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

## 快速排序

- 快速排序是在实践中最快的已知排序算法，平均运行时间为$O(N*logN)$，对数组$S$进行排序的过程主要分为以下四步：

    1. 如果$S$中元素个数是0或者是1，则返回
    2. 取$S$中任一元素$v$，称为**枢纽元(pivot)**
    3. 将$S-${$v$}（即剩下的元素）以$x≤v$和$x≥v$分成两个不相交的集合$S$~1~和$S$~2~
    4. 返回{quicksort($S$~1~), $v$, quicksort($S$~2~)}

    - 值得注意的是，第三步的划分方法不是唯一的，一半只要尽量把关键字对半分到两个集合中即可

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
6. 一个重要的问题时如何处理等于枢纽元的元素：无论是i还是j遇到等于枢纽元的元素，都让i和j一起停止，进行一次交换

```cpp
void quicksort()
```

## 排序的一般下界

- 任何只用到比较的排序算法在最坏情况下需要$Ω(N*logN)$次比较

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
    2. **低优先级排序(LSD)**：先使用低优先级的关键字进行分组（e.g. 在排序扑克牌时，根据点数分为13类）

    - 多关键字排序时，关键字的空间不能太大、分组不能过多——会导致效率很低。多关键字排序适合于有多种关键字，但每个关键字又不是很大的情况


