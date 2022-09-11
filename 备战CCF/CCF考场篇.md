### 基础算法

1. 双指针
2. 二维前缀和
3. 差分
4. 二分

#### 双指针

```C++
for (int i = 0, j = 0; i < n; i ++ )
{
    tmp[s[i]] ++ ;  
    while (tmp[s[i]] > 1)   tmp[s[j ++ ]] -- ;   
    res = max(res, i - j + 1);
}
```

#### 二维前缀和
```C++
for (int i = 1; i <= n; i ++ )
    for (int j = 1; j <= m; j ++ )
        s[i][j] = s[i - 1][j] + s[i][j - 1] - s[i - 1][j - 1] + a[i][j];
while (q -- )
{
    int x1, y1, x2, y2;
    scanf("%d%d%d%d", &x1, &y1, &x2, &y2);
    printf("%d\n", s[x2][y2] - s[x1 - 1][y2] - s[x2][y1 - 1] + s[x1 - 1][y1 - 1]);
}
```

#### 差分
```C++
int l, r, val;
cin >> l >> r >> val;
b[l] += val;
b[r + 1] -= val;

// 数组复原
for (int i = 1; i <= n; i ++ )
    b[i] += b[i - 1];
```

#### 二分
```C++
void merge(int x)
{
    int l = 0, r = n - 1;
    int mid;
    while (l < r)
    {
        mid = (l + r) >> 1;
        if (s[mid] >= x)    r = mid;
        else    l = mid + 1;
    }
    if (s[r] != x)  cout << "-1 -1" << endl;
    else 
    {
        cout << r << " ";   //数字出现的最左端
        l = 0, r = n - 1;
        while (l < r)
        {
            mid = (l + r + 1) >> 1;
            if (s[mid] <= x)    l = mid;
            else    r = mid - 1;
        }
        cout << r << endl;  //数字出现的最右端
    }
}
```

### 基础数据结构

1. 滑动窗口
2. KMP
3. 并查集
4. 哈希表
5. 字典树


#### 滑动窗口(*)
```C++
#include <bits/stdc++.h>
using namespace std;

int main(){
    int n, k;   // 数组长度 & 窗口大小
    cin >> n >> k;
    vector<int> a(n);
    vector<int> res;
    int q[n], hh = 0, tt = -1;  // hh队头 tt队尾
    for (int i = 0; i < n; i ++)    cin >> a[i];
    
    for (int i = 0; i < n; i ++){
        //维持滑动窗口的大小
        //当队列不为空(hh <= tt) 且 当当前滑动窗口的大小(i - q[hh] + 1)>我们设定的
        //滑动窗口的大小(k),队列弹出队列头元素以维持滑动窗口的大小
        if (hh <= tt && q[hh] < i - k + 1)  hh ++;
        //构造单调递增队列
        //当队列不为空(hh <= tt) 且 当队列队尾元素>=当前元素(a[i])时,那么队尾元素
        //就一定不是当前窗口最小值,删去队尾元素,加入当前元素(q[ ++ tt] = i)
        while (hh <= tt && a[q[tt]] <= a[i])  tt --;
        q[++ tt] = i;
        if (i > k - 2) cout << a[q[hh]] << " ";
    }
    return 0;
}
```

#### KMP(*)
```C++
#include <iostream>
using namespace std;

const int N = 100010, M = 1000010;

int n, m;
char p[N], s[M];
int ne[N];

int main()
{
    cin >> n >> p + 1 >> m >> s + 1;
    //处理子串
    for (int i = 2, j = 0; i <= n; i ++ )
    {
        while (j && p[i] != p[j + 1]) j = ne[j];
        if (p[i] == p[j + 1])   j ++ ;
        ne[i] = j;
    }
    
    //s是长串;p是子串
    for (int i = 1, j = 0; i <= m; i ++ )
    {
        while (j && s[i] != p[j + 1])   j = ne[j];
        if (s[i] == p[j + 1])   j ++ ;
        if (j == n)
        {
            printf("%d ", i - n);
            j = ne[j];
        }
    }
    return 0;
}
```

#### 并查集(*)
```C++
#include <iostream>
using namespace std;

const int N = 100010;

int n, m, f[N];

int find(int x)
{
    //求祖先节点+路径压缩
    if (f[x] != x)  f[x] = find(f[x]);
    return f[x];
}

int main()
{
    cin >> n >> m;
    for (int i = 0; i < n; i ++ )   f[i] = i;
    while (m -- )
    {
        char s[2];
        int a, b;
        cin >> s >> a >> b;
        if (s[0] == 'M')    f[find(a)] = find(b);   //M:将a与b合并
        else // 询问a与b是否在同一集合当中
        {
            if (find(a) == find(b)) cout << "Yes" << endl;
            else cout << "No" << endl;
        }
    }
    return 0;
}
```


#### 哈希表(*)

```C++
#include <iostream>
using namespace std;
const int P = 131, N = 200003;
typedef unsigned long long ULL;

//p[N]代表p的幂, h[N]代表当前长度的子串的哈希值
ULL p[N], h[N];
int n, m;
char s[N];

//和前缀和一个道理, 只不过此时是进制问题, 样例参考1234取34, 先要把12 * 100然后再减
ULL func(int l, int r)
{
    return h[r] - h[l - 1] * p[r - l + 1];
}

int main()
{
    cin >> n >> m;
    cin >> s;
    p[0] = 1;
    for (int i = 1; i <= n; i ++ )
    {
        p[i] = p[i - 1] * P;
        h[i] = h[i - 1] * P + s[i - 1];
    }
    while (m -- )
    {
        int l1, r1, l2, r2;
        cin >> l1 >> r1 >> l2 >> r2;
        if(func(l1, r1) == func(l2, r2))    cout << "Yes" << endl;
        else cout << "No" << endl;
    }
    return 0;
}
```

#### 字典树

```C++
#include <iostream>
using namespace std;

const int N = 100010;

int son[N][26], cnt[N], idx;
char s[N];

void insert(char s[])
{
    int p = 0;
    for (int i = 0; s[i]; i ++ )
    {
        int tmp = s[i] - 'a';
        if (!son[p][tmp])   son[p][tmp] = ++ idx;   // 没有就构建
        p = son[p][tmp];    // 有就延伸
    }
    cnt[p] ++ ;
}

int query(char s[])
{
    int p = 0;
    for (int i = 0; s[i]; i ++ )
    {
        int tmp = s[i] - 'a';
        if (!son[p][tmp])   return 0;
        p = son[p][tmp];
    }
    return cnt[p];
}

int main()
{
    int n;
    cin >> n;
    while (n -- )
    {
        char x[2];
        scanf("%s%s", x, s);
        if (x[0] == 'I')   insert(s);   // 插入操作
        else printf("%d\n", query(s));  // 查询操作
    }
    return 0;
}
```
