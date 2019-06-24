# DS05-散列（Hashing）
## Basic of hash
1. 表的大小最好是素数
2. 装填因子(load factor)：λ=n/TableSize
3. 在散列函数中，可以用移位运算来代替乘法（乘数需要用2的次幂来代替），用按位取或来代替加法，从而加速计算
## 分离链接法(separate chaining)
1. 将散列到同一个值的所有元素保留到一个表中，并使用链表连接
## 开放定址法(open addressing hashing)
### 线性探测法(linear probing)
1. 冲突数据被直接放入下一个空闲地址
2. 占据的单元会形成一些区块，形成**一次聚集**(primary clustering)
3. 装填因子大于0.5时，线性探测的效率会大大降低
### 平方探测法(quadratic probing)
1. **定理**如果使用平方探测，且表的大小是素数，那么当表至少有一半是空的时候，总能够插入一个新的元素
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
	}
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
1. F(i) = i*hash_2(X)
2. 一种良好的hash_2：hash_2(X) = R-(X mod R)，其中R为小于TableSize的素数
## 再散列
1. 对于使用平方探测的开放定址散列法，当散列表太满时，为了避免效率严重下降，可以：
	1. 另外建立一个大约两倍大的表（及一个新的散列函数）
	2. 扫描整个原始散列表
	3. 计算每个未删除的元素的新散列值并将其插入到新表中