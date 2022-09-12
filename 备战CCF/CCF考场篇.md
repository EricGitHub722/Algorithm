### 基础算法

1. 双指针
2. 二维前缀和
3. 差分
4. 二分
5. 位运算
6. 前缀和与差分
7. RMQ

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

### 数据结构

1. 滑动窗口
2. KMP
3. 并查集
4. 哈希表
5. 字典树
6. 树状数组
7. 线段树
8. AC自动机


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
const int N = 100010;
int son[N][26], cnt[N], idx;
char s[N];

void insert(char s[])   // 插入
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

int query(char s[])     // 查询
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
```

### 图论

1. DFS
2. BFS
3. 拓扑排序
4. Dijkstra
5. Bellman-Fold
6. SPFA
7. Floyd
8. Prim
9. Kruskal
10. 染色法判定二分图
11. 匈牙利算法
12. 最小生成树
13. 二分图

#### DFS

字典序输出所有顺序
```C++
bool str[N];
int len[N];

void dfs(int u)
{
    if (u == n)
    {
        for (int i = 0; i < n; i ++ )
            cout << len[i] << " ";
        cout << endl;
        return;
    }
    for (int i = 1; i <= n; i ++ )
    {
        if (!str[i])
        {
            len[u] = i;
            str[i] = true;
            dfs(u + 1);
            str[i] = false;
        }
    }
    return;
}

int main()
{
    int n;
    cin >> n;
    dfs(0);
    return 0;
}
```

#### BFS

走迷宫
```C++
#include <bits/stdc++.h>
using namespace std;
typedef pair<int, int> PII;

const int N = 100;
int n, m;
int g[N][N], d[N][N];
PII q[N * N];

int bfs()
{
    d[0][0] = 0;
    int i, j;
    i = j = 0;
    q[ j ++ ] = {0, 0};
    while (i <= j)
    {
        auto t = q[i ++ ];
        int dx[4] = {-1, 0, 1, 0}, dy[4] = {0, 1, 0, -1};
        for (int i = 0; i < 4; i ++ )
        {
            int x = t.first + dx[i];
            int y = t.second + dy[i];
            if (x >= 0 && x < n && y >= 0 && y < m && d[x][y] == -1 && g[x][y] == 0)
            {
                d[x][y] = d[t.first][t.second] + 1;
                q[ j ++ ] = {x, y};
            }
        }
    }
    return d[n - 1][m - 1]; // 左上-右下的最少移动次数
}

int main()
{
    cin >> n >> m;
    for (int i = 0; i < n; i ++ )
        for (int j = 0; j < m; j ++ )
            cin >> g[i][j];
    memset(d, -1, sizeof d);
    cout << bfs() << endl;
    return 0;
}
```

#### 拓扑排序

随机输出一条合法的有向序列
```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 100010;

int h[N], e[N], ne[N], idx;
int p[N];
int res[N], cnt = 0;

int n, m;

// 邻接表
void add(int a, int b)
{
    e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void topsort()
{
    queue<int> stk;
    // 1-n共n个节点
    // 将所有入度为0的节点压栈
    for (int i = 1; i <= n; i ++ )
        if (!p[i])
            stk.push(i);
    while (stk.size())
    {
        auto t = stk.front();
        res[cnt ++ ] = t;
        stk.pop();
        for (int i = h[t]; i != -1; i = ne[i])
        {
            int j = e[i];
            p[j] --;
            if (!p[j])
                stk.push(j);
        }
    }
    if (cnt == n)   // 路径可达
    {
        for (int i = 0; i < cnt ; i ++ )
            cout << res[i] << " ";
        cout << endl;
    }
    else
    {
        cout << "-1" << endl;
    }
    return;
}

int main()
{
    memset(h, -1, sizeof h);
    memset(p, 0, sizeof p);
    cin >> n >> m;
    while (m -- )
    {
        int a, b;
        cin >> a >> b;
        add(a, b);
        p[b] ++;    // 入度+1
    }
    
    topsort();
    return 0;
}
```

#### Dijkstra

```C++
#include <bits/stdc++.h>
using namespace std;

typedef pair<int, int> PII;

const int N = 150010;

//稠密图用邻接矩阵, 稀疏图用邻接表
int n, m, idx, h[N], e[N], ne[N], dis[N], w[N];
bool s[N];

void add(int a, int b, int c)
{
    e[idx] = b, w[idx] = c, ne[idx] = h[a], h[a] = idx ++ ;
}

int dijkstra()
{
    memset(dis, 0x3f, sizeof dis);
    dis[1] = 0;
    //priority_queue:1.使用的数据结构; 2.使用存储的结构; 3.构造小根堆使用greater<PII>
    priority_queue<PII, vector<PII>, greater<PII>> heap;
    heap.push({0, 1});
    while (heap.size())
    {
        auto t = heap.top();
        heap.pop();
        int x = t.first, y = t.second;
        //多条边连接两个点的话只需考虑s数组即可
        if (s[y])   continue;
        s[y] = true;
        for (int i = h[y]; i != -1; i = ne[i])
        {
            int j = e[i];  // i只是个下标，e中才存的是i这个下标对应的点
            if (dis[j] > x + w[i])
            {
                //w[i]是x到e[i]的路径, 若大于则将其加入堆中
                dis[j] = x + w[i];
                heap.push({dis[j], j});
            }
        }
    }
    if (dis[n] != 0x3f3f3f3f)   return dis[n];
    return -1;
}

int main()
{
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while (m -- )
    {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }
    
    int ans = dijkstra();
    cout << ans << endl;
    return 0;
}
```

#### Bellman-Fold(*)

```C++
#include <iostream>
#include <cstring>
using namespace std;

const int N = 100010;
//准备原距离和备份距离
int n, m, k, dis[N], cpy[N];
**//该算法效率不高, 使用结构体存储边**
struct Edge{
    int a, b, c;
}s[N];

int main()
{
    cin >> n >> m >> k;
    for (int i = 0; i < m; i ++ )
    {
        int a, b, c;
        cin >> a >> b >> c;
        s[i] = {a, b, c};
    }
    memset(dis, 0x3f3f3f3f, sizeof dis);
    dis[1] = 0;
    //循环k次
    for (int i = 0; i < k; i ++ )
    {
        //在每次更新前先将原来状况复制
        memcpy(cpy, dis, sizeof dis);
        for (int j = 0; j < m; j ++ )
        {
            int x = s[j].a, y = s[j].b, l = s[j].c;
            //确保更新时候无串联情况, 想象成BFS即可, 一层一层往外蔓延, 只不过是k次蔓延
            dis[y] = min(dis[y], cpy[x] + l);
        }
    }
    //可能该点在改变后数字不是准确的0x3f3f3f3f, 故只需保持一样的数量级即可
    if (dis[n] > 0x3f3f3f3f / 2)    puts("impossible");
    else    cout << dis[n] << endl;
    
    return 0;
}
```

#### SPFA(*)

可以判断带负权值的图是否可达
```C++
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 100010;

int n, m, e[N], ne[N], h[N], w[N], q[N], dis[N], st[N], idx;

void add(int a, int b, int c)
{
    e[idx] = b, ne[idx] = h[a], w[idx] = c, h[a] = idx ++ ;
}

int spfa()
{
    int hh = 0, tt = -1;
    memset(dis, 0x3f, sizeof dis);
    dis[1] = 0;
    st[1] = true;
    q[ ++ tt ] = 1;
    while (hh <= tt)
    {
        auto tmp = q[hh ++ ];
        st[tmp] = false;
        for (int i = h[tmp]; i != -1; i = ne[i])
        {
            int j = e[i];
            if (dis[j] > dis[tmp] + w[i])
            {
                dis[j] = dis[tmp] + w[i];
                if (!st[j])
                {
                    q[ ++ tt ] = j;
                    st[j] = true;
                }
            }
        }
    }
    return dis[n];
}

int main()
{
    cin >> n >> m;
    memset(h, -1, sizeof h);
    while (m -- )
    {
        int a, b, c;
        cin >> a >> b >> c;
        add(a, b, c);
    }
    auto ans = spfa();
    
    if (ans == 0x3f3f3f3f)   puts("impossible");
    else    cout << ans << endl;
    
    return 0;
}
```

#### Floyd(*)

```C++
#include <iostream>
using namespace std;

const int N = 200, INF = 1e9;

int n, m, k, s[N][N];

void floyd()
{
    for (int k = 1; k <= n; k ++ )
        for (int i = 1; i <= n; i ++ )
            for (int j = 1; j <= n; j ++ )
                s[i][j] = min(s[i][j], s[i][k] + s[k][j]);
}

int main()
{
    cin >> n >> m >> k;
    for (int i = 1; i <= n; i ++ )
        for (int j = 1; j <= n; j ++ )
        {
            if (i == j) s[i][j] = 0;
            else    s[i][j] = INF;
        }
    while (m -- )
    {
        int a, b, c;
        cin >> a >> b >> c;
        s[a][b] = min(s[a][b], c);
    }
    floyd();
    while (k -- )
    {
        int a, b;
        cin >> a >> b;
        if(s[a][b] > INF / 2)   puts("impossible");
        else    cout << s[a][b] << endl;
    }
    return 0;
}
```

#### Prim(*)

```C++
#include <iostream>
#include <cstring>
#include <algorithm>
using namespace std;

const int N = 510;

int n, m, s[N][N], dist[N];
bool st[N];

int main()
{
    cin >> n >> m;
    memset(s, 0x3f, sizeof s);
    while (m -- )
    {
        int a, b, val;
        cin >> a >> b >> val;
        s[a][b] = s[b][a] = min(s[a][b], val);
    }
    memset(dist, 0x3f, sizeof dist);
    int res = 0;
    for (int i = 0; i < n; i ++ )
    {
        int t = -1;
        for (int j = 1; j <= n; j ++ )
            if (!st[j] && (t == -1 || dist[t] > dist[j]))
                t = j;
        if (i && dist[t] == 0x3f3f3f3f)
        {
            puts("impossible");
            return 0;
        }
        if (i)  res += dist[t];
        st[t] = true;
        for (int j = 1; j <= n; j ++ )  dist[j] = min(dist[j], s[t][j]);
    }
    cout << res << endl;
    return 0;
}
```

#### Kruskal(*)

```C++
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 100010, M = 200010;
int p[N];

struct Edge
{
    int a, b, val;
    
    bool operator < (const Edge &W)const
    {
        return val < W.val;
    }
    
}edge[M];

int find(int x)
{
    if (p[x] != x)  p[x] = find(p[x]);
    return p[x];
}

int main()
{
    int n, m;
    cin >> n >> m;
    for (int i = 0; i < n; i ++ )   p[i] = i;
    for (int i = 0; i < m; i ++ )
    {
        int a, b ,val;
        cin >> a >> b >> val;
        edge[i] = {a, b, val};
    }
    sort(edge, edge + m);
    
    int res = 0, cnt = 0;
    for (int i = 0; i < m; i ++ )
    {
        int x = edge[i].a;
        int y = edge[i].b;
        int fx = find(x);
        int fy = find(y);
        if (fx != fy)
        {
            p[fx] = fy;
            cnt ++ ;
            res += edge[i].val;
        }
    }
    if (cnt < n - 1)    puts("impossible");
    else    cout << res << endl;
    return 0; 
}

```






