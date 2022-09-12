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
5. SPFA
6. Floyd
7. Prim
8. Kruskal

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
    else    // 路径不可达
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

属于贪心算法的一种

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
    if (dis[n] != 0x3f3f3f3f)   return dis[n];  // 路径可达
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

#### SPFA

改良版的Bellman-Fold算法

与Dijkstra相比的优点：可以判断带负权值的图是否可达

“这一算法被认为在随机的稀疏图上表现出色，并且极其适合带有负边权的图。然而SPFA在最坏情况的时间复杂度与Bellman-Ford算法相同，因此在非负边权的图中仍然最好使用Dijkstra。”

```C++
#include <bits/stdc++.h>
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

#### Floyd

用途：处理多源最短路径

与Dijkstra算法的区别：

1. Dijkstra不能处理负权图，Flyod能处理负权图

2. Dijkstra处理单源最短路径，Flyod是处理多源最短路径

3. Dijkstra时间复杂度为O（n^2）；Flyod时间复杂度为O(n^3) 空间复杂度为O(n ^ 2)

所以题目中如果是单源点正权图，就用Dijkstra

如果是任意两个点之间的最短路径或者是负权图，就用Floyd；

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
    // 点,边,查询
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

#### Prim

prim 算法干的事情是：给定一个无向图，在图中选择若干条边把图的所有节点连起来。要求边长之和最小。在图论中，叫做求**最小生成树**。

prim 算法采用的是一种贪心的策略。

prim适合用于求**稠密图**的最小生成树

复杂度为O(n2)

```C++
//2022.6.1 更新

#include <bits/stdc++.h>
using namespace std;

const int N = 510;
int g[N][N];//存储图
int dt[N];//存储各个节点到生成树的距离
int st[N];//节点是否被加入到生成树中
int pre[N];//节点的前去节点
int n, m;//n 个节点，m 条边

void prim()
{
    memset(dt,0x3f, sizeof(dt));    //初始化距离数组为一个很大的数（10亿左右）
    int res= 0; // 所求的生成最小生成树的权重
    dt[1] = 0;  //从 1 号节点开始生成 
    for(int i = 0; i < n; i++)  //每次循环选出一个点加入到生成树
    {
        int t = -1;
        for(int j = 1; j <= n; j++)//每个节点一次判断
        {
            if(!st[j] && (t == -1 || dt[j] < dt[t]))//如果没有在树中，且到树的距离最短，则选择该点
                t = j;
        }
        if(dt[t] == 0x3f3f3f3f) {
            cout << "impossible";
            return;
        }

        st[t] = 1;// 选择该点
        res += dt[t];
        for(int i = 1; i <= n; i++)//更新生成树外的点到生成树的距离
        {
            if(dt[i] > g[t][i] && !st[i])//从 t 到节点 i 的距离小于原来距离，则更新。
            {
                dt[i] = g[t][i];//更新距离
                pre[i] = t;//从 t 到 i 的距离更短，i 的前驱变为 t.
            }
        }
    }

    cout << res;

}

void getPath()//输出各个边
{
    for(int i = n; i > 1; i--)//n 个节点，所以有 n-1 条边。

    {
        cout << i <<" " << pre[i] << " "<< endl;// i 是节点编号，pre[i] 是 i 节点的前驱节点。他们构成一条边。
    }
}

int main()
{
    memset(g, 0x3f, sizeof(g));//各个点之间的距离初始化成很大的数
    cin >> n >> m;//输入节点数和边数
    while(m --)
    {
        int a, b, w;
        cin >> a >> b >> w;//输出边的两个顶点和权重
        g[a][b] = g[b][a] = min(g[a][b],w);//存储权重
    }

    prim();//求最下生成树
    //getPath();//输出路径
    return 0;
}
```

#### Kruskal

Kruskal 算法干的事情是：给定一个无向图，在图中选择若干条边把图的所有节点连起来。要求边长之和最小。在图论中，叫做求**最小生成树**。。

Kruskal适合用于求**稀疏图**的最小生成树

与Prim的区别：

1. Prim算法是直接查找，多次寻找邻边的权重最小值，而Kruskal是需要先对权重排序后查找的

2. Kruskal在算法效率上是比Prim快的，因为Kruskal只需一次对权重的排序就能找到最小生成树，而Prim算法需要多次对邻边排序才能找到

Kruskal实现过程：Kruskal算法在找最小生成树结点之前，需要对权重从小到大进行排序。将排序好的权重边依次加入到最小生成树中（如果加入时产生回路就跳过这条边，加入下一条边），当所有的结点都加入到最小生成树中后，就找到了这个连通图的最小生成树


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
    // 点和边数
    int n, m;
    cin >> n >> m;
    for (int i = 0; i < n; i ++ )   p[i] = i;
    for (int i = 0; i < m; i ++ )
    {
        int a, b ,val;
        cin >> a >> b >> val;
        edge[i] = {a, b, val};
    }
    // 排序，以便选出最短边
    sort(edge, edge + m);
    
    int res = 0, cnt = 0;
    for (int i = 0; i < m; i ++ )
    {
        int x = edge[i].a;
        int y = edge[i].b;
        int fx = find(x);
        int fy = find(y);
        if (fx != fy)   // 不能构成环
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

### 动态规划-背包篇

1. 01背包问题
2. 完全背包问题
3. 多重背包问题
4. 多重背包问题||
5. 多重背包问题|||
6. 分组背包问题
7. 有依赖的背包问题
8. 背包问题求方案数
9. 背包问题求具体方案


### 动态规划-其它篇

1. 线性DP
2. 区间DP
3. 状态机模型
4. 状态压缩DP
5. 树形DP
6. 数位DP













