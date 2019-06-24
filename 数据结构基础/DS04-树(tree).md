# DS04-树(tree)

## 预备知识

- 一棵树是一些节点的集合，这个集合可以是空集

- 一个非空的树由1个称作**根(root)**的节点$r$以及0个或多个非空的（子）树$T$~1~, $T$~2~, ... , $T$~k~组成，这些子树中每一棵的根都被来自根$r$的一条有向的边(edge)所连接

- 每一棵子树的根叫做根$r$的**child**，而$r$是每一棵子树的**parent**

- 一棵非空树是N个节点和N-1条边的集合

- 没有child的节点称为**树叶(leaf)**

- 具有相同parent的节点称为**sibling**

- 从节点$n$~1~到$n$~k~的**路径(path)**定义为节点$n$~1~, $n$~2~, ... ,$n$~k~的一个序列，使得对于1≤$i$<$k$，节点$n$~i~是$n$~i+1~的parent。路径的**长(length)**为路径上的边的条数，即$k-1$，节点到节点本身的路径长度为0，在一棵树中从根到每个节点恰好存在一条路径

- 对任意节点，其**深度(depth)**为根到这个节点的唯一路径的长，根的深度为0

- 对任意节点，其**高(height)**是从这个节点到一个叶节点的最长路径的长，每个叶节点的高都为0

- 一棵树的深度等于他最深的树叶的深度，高等于它的根的高

- 若存在从$n$~1~到$n$~2~的一条路径，那么$n$~1~是$n$~2~的一位**祖先(ancestor)**，而$n$~2~是$n$~1~的一个**后裔(descendant)**，如果两个节点不同，那么称为真祖先和真后裔

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

- 树的遍历：先序(preorder traversal)、中序(inorder)、后序(postorder)

### 表达式树

- 操作数（operand）存放在表达式树的leaves中，操作符(operator)放在其他节点
- 中缀表达式可以通过中序遍历产生
- 后缀表达式可以通过后续遍历产生
- 前缀表达式可以通过先序遍历产生
- 通过栈可以将后缀表达式方便地转化为表达式树（遇到操作符就出栈）

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
    3. 节点由两个child：用该节点右子树的最小数据代替该节点的数据，然后递归的删除那个节点（此时需要再次查找）——这很简单，因为右子树的最小节点不可能有Left-Child，所以此时回到第二种情况
- 如果删除的次数不多，可以采用**懒惰删除(lazy deletion)**的策略：只做一个删除记号

### 平均情形分析

- 树的所有节点的平均深度是$O(logN)$
- **内部路径长(internal path length)**：一棵树所有节点的深度的和。二叉查找树的内部路径长平均为$O(N*logN)$
- 