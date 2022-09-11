## 第23次CCF

### T1：数组推导

暴力即过

### T2：非零段划分

若使用Map + Two Pointers则会TLE————70分

#### 解法1：岛屿问题

时间复杂度O（n），水平面从上向下逐渐下降，每当凸峰出现时，岛屿数便会增加一个；每当凹谷出现，就会出现两个岛屿连接的情况，使得岛屿数减少一个。

做法：**使用数组cnt[ ]，cnt[ i ]表示海平面下降到 i 时岛屿数量的变化**

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 500005;
const int M = 10000;
int a[N], cnt[M];

int main()
{
	int n, m;
    scanf("%d", &n);
    
    for (int i = 1; i <= n; i ++ )
        scanf("%d", &a[i]);
    a[0] = a[n + 1] = 0;
    n = unique(a, a + n + 2) - a;
    
    for (int i = 1; i < n - 1; i ++ )
        if (a[i - 1] < a[i] && a[i] > a[i + 1])
            cnt[a[i]] ++;	//山峰情况
    	else if (a[i - 1] > a[i] && a[i] < a[i + 1])
            cnt[a[i]] --;	//山谷情况
    	//其余上坡下坡情况不做考虑
    
    int ans = 0, sum = 0;
    for (int i = 10000; i >= 0; i -- )
    {
        sum += cnt[i];
        ans = max(ans, sum);
    }
    printf("%d", ans);
    return 0;
}
```

补充：unique的用法

```C++
//unique是去除容器中的相邻元素的重复元素（不一定要求数组有序），其会将重复的元素添加到容器末尾（故数组大小并没有改变），返回值是去重后的尾地址的值
n = unique(a, a + n + 2) - a;
//由于返回的是容器末尾的地址，则如果想要得到去重后的size，需要减去其初始地址
```

#### 解法2：差分 + 前缀和

时间复杂度O（n），若a[ i ] > a[ i - 1 ]，则意味着当 p 取到a[ i - 1 ] + 1到a[ i ]之间的值时，非零段 + 1。数组cnt[ ]，cnt[ i ]表示 p 从 i - 1 上升到 i 时，非零段数量的变化。

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 500005;
const int M = 10000;
int a[N], cnt[M];

int main()
{
    int n, phrase = 0;	//整数个数，段数
    int i;
    
    scanf("%d", &n);
    for (int i = 1; i <= n; i ++ )
    {
		scanf("%d", &a[i]);
        if (a[i] > a[i - 1])
        {
            cnt[a[i - 1] + 1] ++;	//是指a[i - 1]后面的大山峰增加。
            cnt[a[i] + 1] --;	//同样要减少一个
            //通俗理解可以理解为水位逐渐上涨的过程
        }
    }
    
    int sum = 0;
    for (int i = 1; i < M; i ++ )
    {
        sum += cnt[i];
        phrase = max(phrase, sum);
    }
    printf("%d", phrase);
    return 0;
}
```

### T3：脉冲神经网络



#### 题意理解

**有许多Points（实则就是神经元和脉冲源），以及许多Lines（突触），去连接这些神经元或者脉冲源（神经元-神经元、脉冲源-神经元）。并且神经源中 v 和 u （神经元中的）的值会在 t （时间间隔是整数倍）发生变化，当 v >= 30时就会发送脉冲；脉冲源会根据myrand()函数随机发送脉冲。并且题目规定了结点的编号（因为题目给的突触会规定特定编号之间的连接关系！），[ 0 , N - 1 ]为神经元的编号，[ N , N + P - 1 ]为脉冲源的编号；并且题目提出在代码中使用double类型。**

**输入格式**

输入的第一行包括四个以空格分隔的正整数 N S P T，表示一共有 N 个神经元，S 个突触和 P 个脉冲源，输出时间刻 T 时神经元的 v 值。

【只需4个int or double型变量即可，但需要区分清楚神经元、突触和脉冲源这几个关键名词】

输入的第二行是一个正实数 Δt，表示时间间隔。

【Δt在该题中只用于 v 和 u 的计算】

输入接下来的若干行，每行有以空格分隔的一个正整数 RN 和六个实数 v u a b c d，按顺序每一行对应 RN 个具有相同初始状态和常量的神经元：其中 v u 表示神经元在时刻 0 时的变量取值；a b c d 为该神经元微分方程里的四个常量。保证所有的 RN 加起来等于 N。它们从前向后按编号顺序描述神经元，每行对应一段连续编号的神经元的信息。

【理解：每行有一种神经元，一种神经元有RN个，加起来一共有N个；v 和 u 都是神经元的变量，看做一个即可，a、b、c、d四个变量也可以看作一个变量，该段若是理解了题意则看似困难实则非常简单】

输入接下来的 P 行，每行是一个正整数 r，按顺序每一行对应一个脉冲源的 r 参数。

【每个脉冲源的参数】

输入接下来的 S 行，每行有以空格分隔的两个整数 s(0≤s<N+P)、t(0≤t<N) 、一个实数 w(w≥0) 和一个正整数 D，其中 s 和 t 分别是入结点和出结点的编号；w 和 D 分别表示脉冲强度和传播延迟。

【描述了脉冲源的出入口、强度、延迟】

**输出格式**

输出共有两行，第一行由两个近似保留 3 位小数的实数组成，分别是所有神经元在时刻 T 时变量 v 的取值的最小值和最大值。

【给予提示：要建立一个记录时间的数组（×），因为记录 v 的数组的值更新到最后即是时刻 T 的值】

第二行由两个整数组成，分别是所有神经元在整个模拟过程中发放脉冲次数的最小值和最大值。

【给予提示：建立一个记录发放脉冲次数的数组】

只要按照题目要求正确实现就能通过，不会因为计算精度的问题而得到错误答案。

**数据范围**

数据共 33 种类型：

- 类型 1 满足，1≤T,N,S,P,D≤100。
- 类型 2 满足，1≤T,N,S,P,D≤1000。
- 类型 3 满足，1≤T≤1e5，1≤N,S,P≤1000，1≤D≤10。

【T（时间）规定了1e5，而N（神经元）、S（突触）、P（脉冲源）、D（传播延迟）都是最大1e3，】

#### 自做+满分代码

先来个自做66分的，无滚动数组优化，最后运行超时

```C++
#include <bits/stdc++.h>
using namespace std;

const int M = 1010 * 2;
const int INF = 1e8;

int h[M], e[M], ne[M], TT[M], idx;
double power[M];
double I[M][M];	
//此处导致了该代码会爆内存
//如果要完全正确需要写I[M][100010]
//但这样会爆内存，直接运行错误0分，故只过部分样例即可
int cnt[M];
int N, S, P, T;
double dt;
double vv[M], uu[M], aa[M], bb[M], cc[M], dd[M];
int pp[M];

static unsigned long _next = 1;

/* RAND_MAX assumed to be 32767 */
int myrand(void) {
    _next = _next * 1103515245 + 12345;
    return((unsigned)(_next/65536) % 32768);
}

void add(int a, int b, double c, int d)
{
    e[idx] = b, power[idx] = c, TT[idx] = d, ne[idx] = h[a], h[a] = idx ++;
}

int main()
{
    memset(h, -1, sizeof h);
    scanf("%d%d%d%d", &N, &S, &P, &T);
    scanf("%lf", &dt);
    for (int i = 0; i < N; )
    {
        int rn;
        scanf("%d", &rn);
        double v, u, a, b, c, d;
        scanf("%lf%lf%lf%lf%lf%lf", &v, &u, &a, &b, &c, &d);
        for (int j = 0; j < rn; i ++ , j ++)
        {
            vv[i] = v, uu[i] = u, aa[i] = a, bb[i] = b, cc[i] = c, dd[i] = d;
        }
    }
    for (int i = 0; i < P; i ++ )
    {
        int r;
        scanf("%d", &r);
        pp[N + i] = r;
    }
    for (int i = 0; i < S; i ++ )
    {
        int s, t, D;
        double w;
        scanf("%d%d%lf%d", &s, &t, &w, &D);
        add(s, t, w, D);
    }
    for (int i = 1; i <= T; i ++ )
    {
        for (int j = N; j < N + P; j ++ )
        {
            int tmp = myrand();
            if (tmp < pp[j])
            {
                for (int k = h[j]; k != -1; k = ne[k])
                {
                    int x = e[k];
                    I[x][i + TT[k]] += power[k];
                }
            }
        }
        for (int j = 0; j < N; j ++ )
        {
            double v = vv[j];
            double u = uu[j];
            vv[j] = v + dt * (0.04 * v * v + 5 * v + 140 - u) + I[j][i];
            uu[j] = u + dt * aa[j] * (bb[j] * v - u);
            if (vv[j] >= 30)
            {
                for (int k = h[j]; k != -1; k = ne[k])
                {
                    int x = e[k];
                    I[x][i + TT[k]] += power[k];
                }
                vv[j] = cc[j];
                uu[j] = uu[j] + dd[j];
                cnt[j] ++;
            }
        }
    }
    double max_value = -INF;
    double min_value = INF;
    for (int i = 0; i < N; i ++ )
    {
        max_value = max(max_value, vv[i]);
        min_value = min(min_value, vv[i]);
    }
    int max_send = -INF;
    int min_send = INF;
    for (int i = 0; i < N; i ++ )
    {
        max_send = max(max_send, cnt[i]);
        min_send = min(min_send, cnt[i]);
    }
    printf("%.3lf %.3lf\n", min_value, max_value);
    printf("%d %d", min_send, max_send);
    return 0;
}
```

该代码附加了滚动数组优化内存

```C++
#include <bits/stdc++.h>
using namespace std;

const int INF = 1e8;
const int N = 2020;
//const int M = 1024;
int n, S, P, T;
//由于神经元较多，故使用邻接表
double dt;

//这种要用数组存的变量，就应该把命名赋值给数组
double v[N], u[N], a[N], b[N], c[N], d[N];

//一样
int r[N];

int h[N], e[N], ne[N], D[N], idx;
double W[N];

int cnt[N];

double I[1024][N / 2];
//此处进行了优化，大大压缩了空间
//此处优化到1e3是根据脉冲延迟压缩的

void add(int a, int b, double c, int d)
{
    e[idx] = b, W[idx] = c, D[idx] = d, ne[idx] = h[a], h[a] = idx ++;
}

static unsigned long _next = 1;

/* RAND_MAX assumed to be 32767 */
int myrand(void) {
    _next = _next * 1103515245 + 12345;
    return((unsigned)(_next/65536) % 32768);
}

int main()
{
    memset(h, -1, sizeof h);
    scanf("%d%d%d%d", &n, &S, &P, &T);
    scanf("%lf", &dt);
    for (int i = 0; i < n; )
    {
        int Rn;
        scanf("%d", &Rn);
        double vv, uu, aa, bb, cc, dd;
        scanf("%lf%lf%lf%lf%lf%lf", &vv, &uu, &aa, &bb, &cc, &dd);
        for (int j = 0; j < Rn; i ++, j ++ )
        {
            v[i] = vv, u[i] = uu, a[i] = aa, b[i] = bb, c[i] = cc, d[i] = dd;
        }
    }
    for (int i = n; i < n + P; i ++)
        scanf("%d", &r[i]);
        
    int mod = 0;
    
    for (int i = 0; i < S; i ++ )
    {
        int s, t, d;
        double w;
        scanf("%d%d%lf%d", &s, &t, &w, &d);
        add(s, t, w, d);
        mod = max(mod, d + 1);
        //此处找到脉冲延时的最大值
    }
    
    for (int i = 0; i < T; i ++ )
    {
        int t = i % mod;
        for (int j = n; j < n + P; j ++)
            if (r[j] > myrand())
            {
                for (int k = h[j]; k != -1; k = ne[k])
                {
                    int x = e[k];
                    I[(t + D[k]) % mod][x] += W[k];
                }
            }
        for (int j = 0; j < n; j ++ )
        {
            double vv = v[j], uu = u[j];
            //I[t][j]是指t时刻发给j神经元的脉冲总和 
            v[j] = vv + dt * (0.04 * vv * vv + 5 * vv + 140 - uu) + I[t][j];
            u[j] = uu + dt * a[j] * (b[j] * vv - uu);
            
            if (v[j] >= 30)
            {
                for (int k = h[j]; k != -1; k = ne[k])
                {
                    int x = e[k];
                    I[(t + D[k]) % mod][x] += W[k];
                }
                cnt[j] ++;
                v[j] = c[j], u[j] += d[j];
            }
        }
        //每次需要把该次循环当中使用过的部分都清0
        memset(I[t], 0, sizeof I[t]);
    }
    
    double minv = INF, maxv = -INF;
    int minc = INF, maxc = -INF;
    for (int i = 0; i < n; i ++ )
    {
        minv = min(minv, v[i]);
        maxv = max(maxv, v[i]);
        minc = min(minc, cnt[i]);
        maxc = max(maxc, cnt[i]);
    }
    printf("%.3lf %.3lf\n", minv, maxv);
    printf("%d %d", minc, maxc);
    return 0;
}
```

### T4：收集卡牌

练习：1

该题是裸题：裸求数学期望的一道题

#### 题意理解

首先看到变量范围就说明是一道状态压缩DP问题，卡牌最多共有1 << 16种状态，手中硬币最多有16 * 5枚

方法：**状态压缩DP**

#### 满分代码

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 16, M = 1 << N;

double dp[M][N * 5 + 1];
int n, k;
double a[N];

double func(int state, int coins, int needs)
{
    auto& v = dp[state][coins];
    if (v >= 0) return v;
    if (coins >= needs * k) return 0;
    v = 0;
    for (int i = 0; i < n; i ++ )
        if (state >> i & 1)
            v += a[i] * (func(state, coins + 1, needs) + 1);
        else
            v += a[i] * (func(state | (1 << i), coins, needs - 1) + 1);
    return v;
}

int main()
{
    scanf("%d%d", &n, &k);
    for (int i = 0; i < n; i ++ )
        scanf("%lf", &a[i]);
    memset(dp, -1, sizeof dp);
    //其实这里并不是赋值-1，而是not a number
    double ret = func(0, 0, n);
    printf("%.10lf", ret);
    return 0;
}
```
