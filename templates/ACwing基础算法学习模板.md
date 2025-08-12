# ACwing基础算法学习模板

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

