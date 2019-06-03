## 机试笔记

----------------------------------------------------------------------------------------------------------------

#### 一、日期类问题

转到固定目标点，比如都计算到0000年1月1日的距离，然后求绝对值。

- 闰年`((year % 4 == 0 && year % 100 != 0) || year % 400 == 0)`

-----------------------------------------------------------

#### 二、堆

需要`#include<queue>`

- 使用`priority<int> Q`声明的堆Q是大顶堆，即`Q.top()`返回的是堆中最大的元素

- 若要使用小顶堆，需要声明为：==`priority_queue<int, vector<int>, greater<int>> Q`==

##### 哈夫曼树

生成哈夫曼树可以使用小顶堆

###### 例题1：

计算$\sum$叶节点路径长$\times​$叶节点权值：

- 只用做加法！
- 只要堆中元素大于1，每次取前两个值，相加，把这个值加到最终结果里，再将这个值push回堆里
- 虽然是乘法，但是主要在答案中把权值加深度次就行了（上面那一步就保证了）

---------------------

#### 三、二叉树

##### 二叉树的遍历

- 前序遍历：根 -> 左子树 -> 右子树
- 中序遍历：左子树 -> 根 -> 右子树
- 后序遍历：左子树 -> 右子树 -> 根

###### 例题 1：

借助二叉树的前序遍历和中序遍历，返回其后序遍历。

- 前序遍历$\in [s1, e1]​$结果的第一个值$s1​$总是当前子树的根节点
- 借助根节点可以从中序遍历$\in [s2, e2]​$中得出中序遍历中左子树$\in [s2, rootidx - s1]​$和右子树$\in [rootidx + 1, e2]​$的范围
- 则前序遍历中左子树$\in [s1 + 1, s1 + (rootidx - s2)]​$，右子树$\in [s1 + (rootidx - s2) + 1, e1]​$。
- 对子树重复上述过程可以复原二叉树。
- 使用递归的方法输出后序遍历。

---------------------------------

#### 四、二叉排序树

- > 左子树上节点的值都小于等于根节点值，右子树上节点的值都大于等于根节点值。

- 当两个二叉排序树在**中序遍历**+**另一种遍历**下结果均相同时，此二叉排序树相同。

> ==插入时，可以使用递归方法。==

###### Tricks

- [ ] 读入一串数字序列（数字为0-9，长度小于10）的方法（别想太麻烦，就是一字符串）

  ```C
  char tmp[12] = {'\0'};
  scanf("%s", tmp);
  
  ```

--------------------------------------

## 数学Tricks部分

- [ ] 使用`%`时，为了保证结果在[0, b-1]，可以`r = ( a % b + b) % b`

##### 查找素因子（厄拉托塞斯筛法）

- [ ] 进行素因子分解时，可以使用两个数组进行初始化，一个标记所有非素数，另一个记录范围内的全部素数（厄拉托塞斯筛法）。

  例：

```c
	int total[1000] = {0}, p[1000] = {0};
	int primesize = 0;
	void init(void)
	{
        // 注意：从2开始即可（0，1无意义）
    	for(int i = 2; i < 1000; ++i)
    	{	
        	if(total[i])
            	continue;
        	// total[i] = 1;
        	p[primesize ++] = i;
            // j起始值为i^2
        	for(int j = i*i; j < 1000; j += i)
            	total[j] = 1;
    	}
	}
```

​	之后每次只需要在`for (int j = 0; j < primesize; ++j)`进行素数的测试。

##### 模重复平方法

- [ ] 模重复平方法

  计算$a ^ b (mod \ m)​$时，可以将b表示成为二进制，即$b = k_n \times 2^n + k_{n-1} \times 2^{n-1} + ... + k_1 \times 2 + k_0 \times 2^0​$ 。

  那么

  $$ a^b = a ^{ k_n \times 2^n + k_{n-1} \times 2^{n-1} + ... + k_1 \times 2 + k_0 \times 2^0} = (a^{2^n}) ^ {k_n} \times (a^{2^{n-1}}) ^ {k_{n-1}} \times ... \times (a^{2}) ^ {k_1} \times (a^1) ^ {k_0}$$

  计算最终结果的方法如下：

  ```c
  int ans = 1;
  while(b != 0)
  {
  	if(b % 2 == 1)
      {
          ans *= a;
          ans %= m;
      }
      b /= 2;
      a *= a;
      a %= m;
  }
  ```

##### 高精度整数

- [ ] 定义结构体bigInteger

```c++
struct bigInteger
{
  int digit[1000];  //每个digit[i]存储4位十进制值
  int size;  //digit的长度
  void init();
  void set(int x);
  // 重载 + * / %操作符
  bigInteger operator + (const bigInteger &a) const {};
  bigInteger operator * (const bigInteger &a) const {};
  int operator % (const int x) const {};
  int operator / (const int x) const {};
};
```

- [ ] 读入的数字为十进制时可以通过字符串进行读取，4个字符大小为一个窗口，从后往前扫描。

- [ ] 读入的数字进制不确定时，可以通过定义两个`bigInteger a, b`，其中a（初始值为0）保存转换成为10进制后的数值，b（初始值为1）保存的每一次的权重。具体如下所示：

  ```c++
  // m：输入数字的进制， n：目标进制
  int m = 0, n = 0;
  // char ans[1001] = {'\0'};
  // 保存输入字符串
  char input[1001] = {'\0'};
  while(scanf("%d %d", &m, &n) != EOF)
  {
      bigInteger a, b;
  	a.init();
  	b.init();
  	a.set(0);
  	b.set(1);
      scanf("%s", input);
      int length = strlen(input);
      for(int i = length - 1; i >= 0; --i)
      {
          int tmp = 0;
          if(input[i] >= '0' && input[i] <= '9')
              tmp = input[i] - '0';
          if(input[i] >= 'A' && input[i] <= 'Z')
              tmp = input[i] - 'A' + 10;
          a = a + tmp * b;
          b = b * m;
      }
      // a现在保存了输入字符对应的十进制值
  }
  ```

------------------------

#### 小插曲

#### POJ - 1077

###### 题目

输入$3 \times 3$矩阵，填充数字1-8和'x'。只能移动‘x’和上下左右的数字分别进行交换（对应操作为'u','d','l','r'），每一次只能进行一次交换，最终若能到达如下情况，则输出操作的方法；否则输出'unsolvable'。
$$
1\qquad 2\qquad 3 \\
4\qquad 5\qquad 6 \\
7\qquad 8\qquad x \\
$$

###### 思路

- 开始时想可能和动态规划有关（由此可见对动态规划掌握得也不太好）。后来发现更类似贪心？

- 同学说看到矩阵想**BFS/DFS**？但是似乎BFS会超时。
- **A Star算法**效果更好。

###### 解决问题

1. 如何判断可解还是不可解？

   - 矩阵可以看作是一个有序序列，每次交换相当于有序序列的交换。

   - 线性代数中关于序列**逆序数**有如下定理：
     $$
     有序序列中两个数字进行交换，不影响整个序列逆序数的奇偶性
     $$
     

   因此可以先对输入序列计算逆序数，与最终结果的逆序数进行**异或**，如果相异则一定不可解。

2. 如何标记每一种序列？

   **康托展开**可以将一个序列映射到一个整数，具体方法如下：

   若一个序列为$[k_1, k_2, ..., k_{n-1}, k_n], k_i \in [1, n] \ \forall i \in [1, n]​$，则其对应的康托展开为：
   $$
   x = \sum_{i = 1}^n F(k_i) \times (n - i)!​
   $$
   其中，$F$函数定义为：
   $$
   F(k_i) = \sum_{ i < j\le n, k_i > k_j}1
   $$
   即当前序列在$k_i​$之后小于$k_i​$的值的个数，类似求逆序数的过程，但是康托展开每一位对应不同的权重，因此这种映射是单射（其实也是**双射**，即映射值和序列是一一对应的）。

3. BFS实现方法

   ```c++
   struct node{
     int seq[9];
     int x;
     int y;
   };
   int visited[362880] = {0}; 
   ```

   

4. A Star实现方法

   ```c++
   struct node{
       int seq[9];
       // (x, y) is the position of 'x'
       int x;
       int y;
       int hash;
       node* pre;
       int g;
       int f;  // f = g + h,其中 h 为 当前序列到目标序列的Manhattan distance。
       bool operator > (const node& a) const{
           return f > a.f;
       }
   };
   int direct[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};
   char operations[5] = {'u', 'd', 'l', 'r'};
   node start, end;
   int size = 0;
   node t[362880];
   priority_queue<node> open;
   int visited [362880] = {0};
   void astar(void)
   {
       int flag = 0;
       node ans;
       while(!open.empty())
           open.pop();
       if(start.hash != end.hash)
       {
           t[size++] = start;
           open.push(start);
           while(!open.empty())
           {
               node cur = open.top();
               open.pop();
               if(visited[cur.hash])
                   continue;
               for(int i = 0; i < 4; ++i)
               {
                   node tmp;
                   int nextx = tmp.x + direct[i][0];
                   int nexty = tmp.y + direct[i][1];
                   change(tmp, cur, nextx, nexty);
                   if(tmp.hash == end.hash)
                   {
                       flag = 1;
                       ans = tmp;
                       break;
                   }
               }
               if(flag)
                   break;
               visited[cur.hash] = 1;
           }
       }
       
   }
   ```

   