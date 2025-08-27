# ACwing基础算法模板

## 1. 排序模板

- 快排模板

```cpp
quickSort(q, 0, n - 1);

void quickSort(int q[], int l, int r)
{
    if (l >= r)
        return;
        
    int x = q[l + r >> 1], i = l - 1, j = r + 1;
    while (i < j)
    {
        do i++; while (q[i] < x);
        do j--; while (q[j] > x);
        
        if (i < j)
            swap(q[i], q[j]);
    }
    
    //分治
    quickSort(q, l, j);
    quickSort(q, j + 1, r);
    
}
```

- 归并排序模板:

```cpp
mergeSort(q, 0, n - 1);

void mergeSort(int q[], int l, int r)
{
    //
    if (l >= r)
        return;
    
    
    //1. 分界点
    int mid = l + r >> 1;
    
    //2. 递归
    mergeSort(q, l, mid), mergeSort(q, mid + 1, r);
    
    //3. 合并
    int i  = l, j = mid + 1, k = 0;
    while (i <= mid && j <= r)
    {
        if (q[i] <= q[j])
            tmp[k++] = q[i++];
        else
            tmp[k++] = q[j++];
    }
    
    while (i <= mid)
        tmp[k++] = q[i++];
    while (j <= r)
        tmp[k++] = q[j++];
        
    
    //从tmp中导入回去
    for (i = l, j = 0; i <= r; i++,j++)
        q[i] = tmp[j];
    
}
```

## 2. 二分模板

- 整数二分

```cpp
bool check(int x) {/* ... */} // 检查x是否满足某种性质

// 区间[l, r]被划分成[l, mid]和[mid + 1, r]时使用：
int bsearch_1(int l, int r)
{
    while (l < r)
    {
        int mid = l + r >> 1;
        if (check(mid)) r = mid;    // check()判断mid是否满足性质
        else l = mid + 1;
    }
    return l;
}
// 区间[l, r]被划分成[l, mid - 1]和[mid, r]时使用：
int bsearch_2(int l, int r)
{
    while (l < r)
    {
        int mid = l + r + 1 >> 1;
        if (check(mid)) l = mid;
        else r = mid - 1;
    }
    return l;
}
```

- 浮点数二分

```cpp
bool check(double x) {/* ... */} // 检查x是否满足某种性质

double bsearch_3(double l, double r)
{
    const double eps = 1e-6;   // eps 表示精度，取决于题目对精度的要求
    while (r - l > eps)	//就说名l 和 r越来越接近。
    {
        double mid = (l + r) / 2;
        if (check(mid)) r = mid;
        else l = mid;
    }
    return l;
}
```

## 3. 高精度模板

```cpp
// C = A + B, A >= 0, B >= 0
vector<int> add(vector<int> &A, vector<int> &B)
{
    if (A.size() < B.size()) return add(B, A);

    vector<int> C;
    int t = 0;
    for (int i = 0; i < A.size(); i ++ )
    {
        t += A[i];
        if (i < B.size()) t += B[i];
        C.push_back(t % 10);
        t /= 10;
    }

    if (t) C.push_back(t);
    return C;
}
// C = A - B, 满足A >= B, A >= 0, B >= 0
vector<int> sub(vector<int> &A, vector<int> &B)
{
    vector<int> C;
    for (int i = 0, t = 0; i < A.size(); i ++ )
    {
        t = A[i] - t;
        if (i < B.size()) t -= B[i];
        C.push_back((t + 10) % 10);
        if (t < 0) t = 1;
        else t = 0;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}
// C = A * b, A >= 0, b >= 0
vector<int> mul(vector<int> &A, int b)
{
    vector<int> C;

    int t = 0;
    for (int i = 0; i < A.size() || t; i ++ )
    {
        if (i < A.size()) t += A[i] * b;
        C.push_back(t % 10);
        t /= 10;
    }

    while (C.size() > 1 && C.back() == 0) C.pop_back();

    return C;
}
// A / b = C ... r, A >= 0, b > 0
vector<int> div(vector<int> &A, int b, int &r)
{
    vector<int> C;
    r = 0;
    for (int i = A.size() - 1; i >= 0; i -- )
    {
        r = r * 10 + A[i];
        C.push_back(r / b);
        r %= b;
    }
    reverse(C.begin(), C.end());	//为了和上面三个模板的外部统一输出
    while (C.size() > 1 && C.back() == 0) C.pop_back();
    return C;
}

```

## 4. 前缀和模板

``` cpp
//a代表数据 s代表前缀和数组， 下标全从1开始
//一维前缀和 
s[i] = s[i - 1] + a[i];	//计算前缀和
s[l~r] = s[r] - s[l - 1] //计算区间l ~ r的和
//二维前缀和
s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j];	//计算前缀和
//(x1, y1) 到 (x2, y2) 这个矩形的和
x[(x1,y1)~(x2,y2)] = s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][x2 - 1];
```

## 5. 差分模板

```cpp
//一维差分
//在l~r区间的数加上C
void insert(int l, int r, int c)
{
    d[l] += c;
    d[r + 1] -= c;
}
//二维差分
void insert(int x1, int y1, int x2, int y2, int c)
{
    d[x1][y1] += c;
    d[x1][y2 + 1] -= c;
    d[x2 + 1][y1] -= c;
    d[x2 + 1][y2 + 1] += c;
}
```

## 6. 双指针模板

```cpp
for (int i = 0, j = 0; i < n; i ++ )
{
    while (j < i && check(i, j)) j ++ ;

    // 具体问题的逻辑
}
常见问题分类：
    (1) 对于一个序列，用两个指针维护一段区间
    (2) 对于两个序列，维护某种次序，比如归并排序中合并两个有序序列的操作
```

# ACwing数据结构模板

## 1. 单链表

```cpp
//初始化
void init()
{
    head = -1, idx = 0;
}
//头插
void add_to_head(int x)
{
    e[idx] = x, ne[idx] = head, head = idx++;
}

//在第k个位置之后插入节点
void insert(int k, int x)
{
    e[idx] = x, ne[idx] = ne[k], ne[k] = idx++;
}

// 在第k个位置之后删除节点
void remove(int k)
{
    ne[k] = ne[ne[k]];
}

```

## 2. 双链表

![image-20250819115441494](https://cdn.jsdelivr.net/gh/Kxq-xl/pic-bed/img/image-20250819115441494.png)

```cpp
//头节点左边是尾节点
//尾节点右边是头节点
void init()
{
    // 0处为头节点 1处为尾节点
    l[0] = r[0] = 1;
   	l[1] = r[1] = 0;
    idx = 2;
}

//在k位置后(右)插入节点
void insert(int k, int x)
{
    e[idx] = x;
    
    l[idx] = k;
    r[idx] = r[k];
    l[r[k]] = idx;
    r[k] = idx;
    
    idx++;
}
//删除k位置的节点
void remove(int k)
{
    r[l[k]] = r[k];
    l[r[k]] = l[k];
}
```

## 3. 栈和队列

```cpp
//***************************栈
int stk[N], tt = 0;
//入栈
stk[++tt] = x;
//出栈
tt--;
//栈顶值
stk[tt];
//判空
(tt == 0 ? "YES" : "NO");	//yes为空 NO 不为空
//***************************

//***************************队列
int que[N], hh = 0, tt = 0;	//hh对头指针， tt队尾指针。
//入队
que[tt++] = x;
//出队
hh++;
//对头元素和队尾元素
que[hh], que[tt - 1];
//判空
(hh == tt ? "YES" : "NO");	//yes为空， no不为空
```

## 4. 单调栈和单调队列

```cpp
常见模型：找出每个数左边离它最近的比它大/小的数
int tt = 0;
for (int i = 1; i <= n; i ++ )
{
    while (tt && check(stk[tt], i)) tt -- ;
    stk[ ++ tt] = i;
}
常见模型：找出滑动窗口中的最大值/最小值
int hh = 0, tt = 0;
for (int i = 0; i < n; i ++ )
{
    while (hh < tt && check_out(q[hh])) hh ++ ;  // 判断队头是否滑出窗口
    while (hh < tt && check(q[tt - 1], i)) tt -- ;	//q[tt - 1]才是队尾
    q[tt++] = i;
}
```

## 5. Tire

用来高效地存储和查找字符串集合的数据结构。

```cpp
(0) root
 ├─a→ (1)
 │   └─b→ (2)   cnt[2] = 1 ("ab")
 │       ├─c→ (3)   cnt[3] = 1 ("abc")
 │       └─d→ (4)   cnt[4] = 1 ("abd")
 └─b→ (5)
     └─c→ (6)
         └─a→ (7)   cnt[7] = 1 ("bca")
    
int son[N][26], cnt[N], idx;
void insert(char str[])
{
    int p = 0;
    for (int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a';
        if (!son[p][u]) son[p][u] = ++idx;
        p = son[p][u];
    }
    
    cnt[p]++;
}
int query(char str[])
{
    int p = 0;
    for (int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a';
        if (!son[p][u]) return 0;
        p = son[p][u];
    }
    
    return cnt[p];
}

```

## 6. 并查集

- 将两个集合合并
- 询问两个元素是否在一个集合当中

``` cpp
(1)朴素并查集：

    int p[N]; //存储每个点的祖宗节点

    // 返回x的祖宗节点
    int find(int x)
    {
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    // 初始化，假定节点编号是1~n
    for (int i = 1; i <= n; i ++ ) p[i] = i;

    // 合并a和b所在的两个集合：
    p[find(a)] = find(b);

(2)维护size的并查集：

    int p[N], size[N];
    //p[]存储每个点的祖宗节点, size[]只有祖宗节点的有意义，表示祖宗节点所在集合中的点的数量

    // 返回x的祖宗节点
    int find(int x)
    {
        if (p[x] != x) p[x] = find(p[x]);
        return p[x];
    }

    // 初始化，假定节点编号是1~n
    for (int i = 1; i <= n; i ++ )
    {
        p[i] = i;
        size[i] = 1;
    }

    // 合并a和b所在的两个集合：只有a b不在一个区间内才可以合并
   if(find(a) != find(b))
   {
        size[find(b)] += size[find(a)];
   		p[find(a)] = find(b);
   }

```



## 7. 堆

![Snipaste_2025-08-21_12-18-21](https://cdn.jsdelivr.net/gh/Kxq-xl/pic-bed/img/Snipaste_2025-08-21_12-18-21.png)

```cpp
// h[N]存储堆中的值, h[1]是堆顶，x的左儿子是2x, 右儿子是2x + 1
int h[N] size;

//将大的数往下
void down(int u)
{
    int t = u;
    if (u * 2 <= size && h[u * 2] < h[t]) t = u * 2;
    if (u * 2 + 1 <= size && h[u * 2 + 1] < h[t]) t = u * 2 + 1;
    if (u != t)
    {
        swap(u, t);
        down(t);
    }
}
//将小的数往上
void up(int u)
{
    while (u / 2 && h[u] < h[u / 2])
    {
        swap(u, u / 2);
        u >>= 1;
    }
}

// O(n)建堆
for (int i = n / 2; i; i -- ) down(i);
```

## 8. 哈希表

- 拉链法处理冲突

```cpp
const int N = 100003; //模一个很大的质数

int h[N], e[N], ne[N], idx;

memset(h, -1, sizeof h);	//记得初始化h数组头节点为-1

//拉链法处理冲突。
void insert(int x)
{
    int k = (x % N + N) % N;	//哈希值
    e[idx] = x;
    ne[idx] = h[k];
    h[k] = idx++;
    
}

bool find(int x)
{
    int k = (x % N + N) % N;
    for (int i = h[k]; i != -1; i = ne[i])
        if (e[i] == x)
            return true;
    
    
    return false;
}

```

- 开放寻址法处理冲突

```cpp
//开放寻址法的N必须比原本数据 多2~3倍， null是一个超级大的数字
const int N = 200003, null = 0x3f3f3f3f;

int h[N];
memset(h, 0x3f, sizeof h);	//记得初始化。
int find(int x)
{
    int k = (x % N + N) % N;
    
    while (h[k] != null && h[k] != x)
    {
        k++;
        if (k == N)
            k = 0;
    }
    
    return k;
}

```

