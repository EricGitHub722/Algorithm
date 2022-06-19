# CCF-CSP

## 第16次CCF

### T1：小中大

遍历 + 特殊处理即可

### T2：二十四点

模板题：**表达式求值问题**，使用两个栈即可

注意：该题的除法是向下取整，但C++中的整除问题与数学中是不一样的，C++中是向0取整

#### 示例代码

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 110;

stack<int> num;
stack<char> op;
int n;

void eval()
{
	int res;
	int b = num.top();	num.pop();
	int a = num.top();	num.pop();
	char c = op.top();	op.pop();
	if (c == '+')
		res = a + b;
	else if (c == '-')
		res = a - b;
	else if (c == 'x')
		res = a * b;
	else
	{
		if (a * b >= 0)
			res = a / b;
		else	//向下取整，而C++是向0取整 
		{
			if (a % b == 0)	res = a / b;
			else	res = a / b - 1;
		}
	}
	num.push(res);
	return;
}
 
int main()
{
	unordered_map<char, int> pr;
	pr['+'] = pr['-'] = 1;
	pr['x'] = pr['/'] = 2;
	scanf("%d", &n);
	while (n --)
	{
		string str;
		cin >> str;
		num = stack<int>();
		op = stack<char>(); 
		for (auto c : str)
			if (c >= '0' && c <= '9')	num.push(c - '0');
			else
			{
				while (op.size() && pr[op.top()] >= pr[c])	eval();
				op.push(c);
			}
		while (op.size())	eval();
		if (num.top() == 24)	puts("Yes");
		else	puts("No");
	}
	return 0; 
}
```



## 第17次CCF

### T1：小明种苹果

遍历记录即过

### T2：小明种苹果（续）

遍历记录即过，记录连续可以使用Two Pointers

### T3：字符画

#### 题意理解

输入格式：先输入m 和 n 表示图片的宽和高，然后每个里面又包含了一个p 和 q ，表示每一小块的宽和高。之后输入每一小块的像素，其中有一些小处理

滤清思路：先是准备一个三维的int数组，记录了每一块像素的RGB，其次通过输入以及处理输入，将RGB读入到该变量中，接着开始缩放，将每一小部分均缩放之后观察其和原来背景颜色有何差异（背景初始化都是0），此处看题解即可

```c++
/*
CSDN高手理解输出

输出压缩后的图片的背景色

像素块行处理：
若背景色与该行的像素块的前一块（第一块与默认值比较）颜色相同，则不处理；否则若与默认值相同则输出 ESC[0m 的格式化表示，不相同则输出 ESC[48;2;R;G;Bm 的格式化表示（此处RGB指代像素块的RGB）。
每一个像素块后必须紧跟一个格式化的空格： \x20
像素块行尾处理：
若该行的最后一个像素块颜色不是默认值则输出 ESC[0m 的格式化表示。
始终在像素块行尾追加一个格式化的回车: \x0A
*/
```

#### 示例代码

```C++
#include <iostream>
#include <cstring>
#include <algorithm>

using namespace std;

typedef unsigned char UC;
const int N = 1080, M = 1920;

int m, n, p, q;
//g[i][j]表示第i行第j列的像素表示
UC g[N][M][3];

//转成数字
inline int get(char c)
{
    if (c <= '9') return c - '0';
    return c - 'a' + 10;
}

//转成字符
inline char get(int x)
{
    if (x <= 9) return x + '0';
    return x - 10 + 'A';
}

//按照要求输出
inline void print(char* str)
{
    for (int i = 0; str[i]; i ++ )
        printf("\\x%c%c", get(str[i] / 16), get(str[i] % 16));
}

int main()
{
    scanf("%d%d%d%d", &m, &n, &p, &q);
    char str[100];
    for (int i = 0; i < n; i ++ )
        for (int j = 0; j < m; j ++ )
        {
            //处理输入RGB
            scanf("%s", str);
            int len = strlen(str);
            if (len == 2)
            {
                int t = get(str[1]);
                for (int k = 0; k < 3; k ++ )
                    g[i][j][k] = t * 16 + t;
            }
            else if (len == 4)
            {
                for (int k = 0; k < 3; k ++ )
                {
                    int t = get(str[1 + k]);
                    g[i][j][k] = t * 16 + t;
                }
            }
            else
            {
                for (int k = 0; k < 3; k ++ )
                    g[i][j][k] = get(str[1 + k * 2]) * 16 + get(str[2 + k * 2]);
            }
        }

    int bg[3] = {0};
    for (int i = 0; i < n / q; i ++ )
    {
        for (int j = 0; j < m / p; j ++ )
        {
            int cur[3] = {0};
            for (int x = 0; x < q; x ++ )
                for (int y = 0; y < p; y ++ )
                    for (int z = 0; z < 3; z ++ )
                        //缩放每一小块
                        cur[z] += g[i * q + x][j * p + y][z];
            for (int k = 0; k < 3; k ++ ) cur[k] /= p * q;
            
            if (cur[0] == bg[0] && cur[1] == bg[1] && cur[2] == bg[2]) ;  // pass
            else if (!cur[0] && !cur[1] && !cur[2]) print("\033[0m");
            else
            {
                //将后面的字符串输出到str中
                //只能使用char*,不能使用string
                sprintf(str, "\033[48;2;%d;%d;%dm", cur[0], cur[1], cur[2]);
                print(str);
            }
            for (int k = 0; k < 3; k ++ ) bg[k] = cur[k];
            //每处理完一个元素的空格
            print(" ");
        }
        if (bg[0] || bg[1] || bg[2])
        {
            print("\033[0m");
            for (int k = 0; k < 3; k ++ ) bg[k] = 0;
        }
        //每一行过后都添加换行符
        print("\n");
    }
    return 0;
}
```



### T4：推荐系统

#### 题意理解

商品的初始编号为0 ~ m - 1类商品，且每类商品初始有编号不同的n个商品，每个商品有id、score两个属性。支持3个操作：添加、删除、查询前K大值（对每一行都有限制条件）

（官网题目是错的，无法排序输出）

对于查询：优先选择行值（类编号）较小的，同行选择id较小的，按照分值排序从大到小输出。但其数据量很小（<=100），故可以暴力完成。

如何查询决定如何存储，如何查询？使用**多路归并**，复杂度100 * 100 * 50 * logN

如何存储每一行？需要支持从大到小且支持添加删除。故使用set，可以在logN的复杂度内维护一个有序序列（而堆是在logN时间内动态维护一个最大值，这是两者的不同之处）。我们使用pair去存储每个商品，（score，-id）的方式去存，这样就能保证相同score的编号从反向看就是从小到大。

删除时候只给id不给score，故还需要一个Hash表来存储每一行的id对应的score是多少

#### 示例代码

```C++
#include <bits/stdc++.h>
#define x first
#define y second
using namespace std;

typedef pair<int, int> PII;
const int N = 55;

int m, n;
set<PII> g[N];
unordered_map<int, int> f[N];
int sum[N];	//每一类元素最多选多少个
int cnt[N]; //当前...元素数量
//从前往后遍历序列，故需要一个迭代器，又由于要从大到小遍历，故需要一个反向迭代器
set<PII>::reverse_iterator it[N];
//答案数组
vector<int> ans[N];
int main()
{
    scanf("%d%d", &m, &n);
    for (int i = 1; i <= n; i ++ )
    {
		int id, score;
        scanf("%d%d", &id, &score);
        for (int j = 0; j < m; j ++ )
        {
            f[j][id] = score;
            g[j].insert({score, -id});
        }
    }
    int Q;
    scanf("%d", &Q);
    while (Q -- )
    {
        int t;
        scanf("%d", &t);
        if (t == 1)
        {
            int type, id, score;
            scanf("%d%d%d", &type, &id, &score);
            f[type][id] = score;
            g[type].insert({score, -id});
        }
        else if (t == 2)
        {
            int type, id;
            scanf("%d%d", &type, &id);
            g[type].erase({f[type][id], -id});
            f[type].erase(id);
        }
        else
        {
            int tot;
            scanf("%d", &tot);
            for (int i = 0;i < m; i ++ )
            {
                scanf("%d", &sum[i]);
                it[i] = g[i].rbegin();
                cnt[i] = 0;
                ans[i].clear();
            }
            //多路归并
            while (tot -- )
            {
				int k = -1;
                for (int i = 0; i < m; i ++ )
                    if (it[i] != g[i].rend() && cnt[i] < sum[i])
                        if (k == -1 || it[i]->x > it[k]->x)
                            k = i;
                if (k == -1)	break;
                ans[k].push_back(-it[k]->y);
                cnt[k] ++;
                it[k] ++;
            }
            for (int i = 0; i < m; i ++ )
                if (ans[i].empty())	puts("-1");
            	else
                {
                    for (auto x : ans[i])
                        printf("%d ", x);
                    puts("");
                }
        }
    }
    return 0;
}
```





## 第18次CCF

### T1：报数

暴力即过

### T2：回收站选址

暴力枚举即可满分

### T3：化学方程式

#### 题意理解

1. 先将一个字符串拆分成等号左边和等号右边的情况，通过分析左右两边返回的Hash表是否相等判断 Y or N
2. 其次将左右用加号分隔分别处理
3. 先处理小字符串最左边的系数
4. dfs剩下的字符串，如果遇到左括号则递归处理，遇到右括号break，剩下的处理字母+数字即可

满分代码

```C++
#include <bits/stdc++.h>
using namespace std;

typedef unordered_map<string, int> MPSI;

MPSI dfs(string& str, int& u)
{
    MPSI res;
    while (u < str.size())
    {
        if (str[u] == '(')
        {
            u ++;	//消除左括号
            auto t = dfs(str, u);
            u ++;	//消除右括号
            int cnt = 1, k = u;
            while (k < str.size() && isdigit(str[k]))   k ++;
            if (k > u)
            {
                cnt = stoi(str.substr(u, k - u));
                u = k;
            }
            for (auto c : t)
                res[c.first] += c.second * cnt;
        }
        else if (str[u] == ')') break;
        else
        {
            int k = u + 1;
            while (k < str.size() && str[k] >= 'a' && str[k] <= 'z')    k ++;	//大写字母 | 大写字母 + 小写字母
            auto key = str.substr(u, k - u);
            u = k;
            
            //统计后面的数字
            int cnt = 1;
            while (k < str.size() && isdigit(str[k]))   k ++;
            if (k > u)
            {
                cnt = stoi(str.substr(u, k - u));
                u = k;
            }
            res[key] += cnt;
        }
    }
    return res;
}

MPSI work(string str)
{
    MPSI res;
    int i = 0;
    while (i < str.size())
    {
        int j = i + 1;
        while (j < str.size() && str[j] != '+') j ++;
        auto t = str.substr(i, j - i);	//按照+号分隔
        i = j + 1;
        
        //统计数部分
        int cnt = 1, k = 0;
        while (k < t.size() && isdigit(t[k]))   k ++;
        if (k)  cnt = stoi(t.substr(0, k));
        
        auto tmp = dfs(t, k);	//处理字符串
        for (auto c : tmp)
            res[c.first] += c.second * cnt;
    }
    return res;
}

int main()
{
    int n;
    cin >> n;
    while (n -- )
    {
        string str;
        cin >> str;
        int k = str.find('=');
        auto left = work(str.substr(0, k));
        auto right = work(str.substr(k + 1));
        if (left == right)  puts("Y");
        else    puts("N");
    }
    return 0;
}
```

### T4：区块链

#### 题意理解

有 n 个节点通过 m 条边相连，节点编号由 1 - n 。初始化时有相同的创世块，链长为1 。每个节点都要维护一条主链

方法：**可持久化链表 + 堆**

#### 示例代码

```C++
#include <bits/stdc++.h>
using namespace std;

typedef vector<int> VI;
const int N = 510, M = 20010;	//无向图要加倍 

int n, m, w, Q;
int h[N], e[M], ne[M], idx;	//邻接表
vector<VI> g;
int node[N];

struct Op
{
	int t, id, pid, hid;	//时间，编号，从哪来，编号 
	//操作id和hid的内容 
	bool operator< (const Op& r) const
	{
		return t > r.t;
	}
};
priority_queue<Op> heap; 

void add(int a, int b)
{
	e[idx] = b, ne[idx] = h[a], h[a] = idx ++;
}

void eval()
{
	auto t = heap.top();
	heap.pop();
	auto &a = g[node[t.id]], &b = g[t.hid];
	if (b.size() > a.size() || b.size() == a.size() && b.back() < a.back())
	{
		node[t.id] = t.hid;
		for (int i = h[t.id]; ~i; i = ne[i])
			if (e[i] != t.pid && e[i] != t.id)
				heap.push({t.t + w, e[i], t.id, t.hid}); 
	}
}

int main()
{
	scanf("%d%d", &n, &m);
    //根据题意初始化时的相同的”创世块“链长为1，编号为0
	g.push_back({0});	
	memset(h, -1, sizeof h);
	while (m --)
	{
		int a, b;
		scanf("%d%d", &a, &b);
		add(a, b), add(b, a);
	}
	
	scanf("%d%d", &w, &Q);
	getchar();	//由于刚才输入了w和Q以及\n（回车），下面需要使用fgets()，故此处将\n读完
	char str[100];
	while (Q -- )
	{
		fgets(str, 100, stdin);	//why?
		stringstream ssin(str);
		int a[3], cnt = 0;
		while (ssin >> a[cnt])	cnt ++;
		if (cnt == 3)	
		{
			//清空在此之前的所有操作 
			while (heap.size() && heap.top().t <= a[1])	eval();
			//将node[a[0]]链表复制一份到最后 
			g.push_back(g[node[a[0]]]);	
			//新增节点 
			g.back().push_back(a[2]);
			//赋值直接赋编号即可 
			node[a[0]] = g.size() - 1;
			for (int i = h[a[0]]; ~i; i = ne[i])
				if (e[i] != a[0])
					//时间，目标节点，源节点，链表编号 
					heap.push({a[1] + w, e[i], a[0], node[a[0]]});
		} 
		else
		{
			while (heap.size() && heap.top().t <= a[1])	eval();
			printf("%d ", g[node[a[0]]].size());
			for (auto x : g[node[a[0]]])
				printf("%d ", x);
			puts("");
		}
	}
	return 0;
}
```

#### 代码解释

##### 重载运算符

​	运算符重载是通过创建运算符函数实现的，其定义了重载的运算符将要进行的操作，其与其它类型函数唯一的区别就是**运算符函数的函数名是由关键字operator和其后要重载的运算符符号构成的**。运算符函数定义的一般格式如下：

```C++
<返回类型说明符> operator<运算符符号> (<参数表>)
{
    <参数体>
}
```

重载时候要遵循的规则：

（1）除了类属关系运算符"."、成员指针运算符"*"、作用域运算符"::"、sizeof运算符和三目运算符"?:"以外，C++中所有运算符都可以重载。

（2）重载运算符时不能创建新的运算符

（3）运算符重载实质上是函数重载

（4）重载不能改变运算符的优先级以及语法结构

（5）运算符重载只能和用户自定义的对象一起使用，或者用于用户自定义类型的对象和内部类型的对象混合使用

```C++
//常见的一些重载运算符的比较函数
struct Op
{
	int t, id, pid, hid;	
    //括号中的const表示参数r对象不会被修改，最后的const表明调用函数对象不会被修改!
	bool operator< (const Op& r) const	//最后的const必写
	{
		return t > r.t;	//升堆，反之降序
	}
};

```

##### priority_queue的使用

优先队列的声明如下

```C++
priority_queue<type, container, function>;
//其中第一个参数不可省略，后两个参数可以省略
//type：数据类型
//container：实现优先队列的底层容器，要求必须是以数组形式实现的容器
//function：元素之间的比较方式

//一般形式如下
priority_queue<int> q;	//默认定义为大根堆
priority_queue<int, vector<int>, less<int>> q;	//等价于上面
priority_queue<int, vector<int>, greater<int>> q;	//小根堆
```

##### 字符串输入fgets()函数

函数原型

```C++
char fgets(char* str, int size, FILE* stream);
//*str：字符型指针，存储所得数据的地址
//size：要复制到str中的字符串的长度，包含终止NULL
//*stream：文件结构体指针，将要读取的文件流
//意义：从stream所指向的文件中读取size-1个字符送入字符串数组str中。
//功能：从文件中读取字符串，每次只读取一行。

//fgets()函数的第三个参数指明要读入的文件。如果读入从键盘输入的数据，则以stdin(标准输入)作为参数，该标识定义在stdio.h中。
```

注意：

1. fgets每次最多只能读取n-1个字符，第n个为NULL。
2. 当遇到换行符或者EOF时，即使当前位置在n-1之前也读出结束。
3. 若函数返回成功，则返回字符串数组str的首地址。

##### stringstream使用

C++中引入了ostringstream、istringstream、stringstream，着三个类位于sstream.h头文件

istringstream是由一个string对象构造而来，从一个string对象读取字符。 

ostringstream同样是有一个string对象构造而来，向一个string对象插入字符。

stringstream则是用于C++风格的字符串的输入输出的。

```C++
stringstream ssin(str);
int a[3], cnt = 0;
//按照空格写入字符串，写入类型由写入的数据结构决定，此处为int型
while (ssin >> a[cnt])	cnt ++;
```

stringstream可以用于数据转换

```C++
int main()
{
    stringstream sstream;
    string strResult;
    int nValue = 1000;
 
    // 将int类型的值放入输入流中
    sstream << nValue;
    // 从sstream中抽取前面插入的int类型的值，赋给string类型
    sstream >> strResult;
 
    cout << "[cout]strResult is: " << strResult << endl;
    printf("[printf]strResult is: %s\n", strResult.c_str());
 
    return 0;
}

//并且strignstream类型可以转换为string类型使用（使用str()方法）
stringstream sstream;
sstream << " first";
cout << sstream.str();
```

## 第21次CCF

### T1：期末预测之安全指数

遍历即可

### T2：期末预测之最佳阈值（x）



## 第22次CCF

### T1：灰度直方图

遍历即过

### T2：邻域均值（x）



### T3：DHCP服务器

**练习：1**

**从实现细节开始看即可**，每次要知道哪部分题意是最重要的。

该题只需按照题目一步一步做就好

维护n个ip地址，每个ip需要维护：**状态state**：未分配、待分配、占用、过期；**过期时间**：待分配与占用都大于0；**占用者**：除了未分配都有 ————故采用struct数组存储即可

先判断是否需要处理

#### 示例代码

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 10010;	//控制到O(n2)即可

int n, m, t_def, t_max, t_min;
string h;
struct IP
{
    int state;	//0未分配、1待分配、2占用、3过期
    int t;
    string owner;
}ip[N];

void update_ips_state(int tc)
{
    for (int i = 1; i <= n; i ++ )
        if (ip[i].t && ip[i].t <= tc)
        {
            if (ip[i].state == 1)
            {
                ip[i].state = 0;
                ip[i].owner = "";
                ip[i].t = 0;
            }
            else
            {
                ip[i].state = 3;
                ip[i].t = 0;
            }
        }
}

int get_ip_by_state(int state)
{
    for (int i = 1; i <= n; i ++ )
        if (ip[i].state == state)
            return i;
    return 0;
}

int get_ip_by_owner(string client)
{
    for (int i = 1; i <= n; i ++ )
        if (ip[i].owner == client)
            return i;
   	return 0;
}

int main()
{
    cin >> n >> t_def >> t_max >> t_min >> h;
    cin >> m;
    while (m -- )
    {
        int tc;
        string client, server, type;
        int id, te;
        cin >> tc >> client >> server >> type >> id >> te;
        if (server != h && server != "*")
            if (type != "REQ")
                continue;
        if (type != "DIS" && type != "REQ") continue;
        if ((server == "*" && type != "DIS") || (server == h && type == "DIS"))	continue;
        update_ips_state(tc);
        if (type == "DIS")
        {
            int k = get_ip_by_owner(client);
            if (!k)	k = get_ip_by_state(0);
            if (!k)	k = get_ip_by_state(3);
            if (!k)	continue;
            ip[k].state = 1;
            ip[k].owner = client;
            if (!te)	ip[k].t = tc + t_def;
            else
            {
                int t = te - tc;
                t = max(t_min, t), t = min(t, t_max);
                ip[k].t = tc + t;
            }
            cout << h << ' ' << client << ' ' << "OFR" << ' ' << k << " " << ip[k].t << endl;
        }
        else
        {
            if (server != h)
            {
				for (int i = 1; i <= n; i ++ )
                    if (ip[i].owner == client && ip[i].state == 1)
                    {
                        ip[i].state = 0;
                        ip[i].owner = "";
                        ip[i].t = 0;
                    }
                continue;    
            }
            if (!(id >= 1 && id <= n && ip[id].owner == client))
            {
                cout << h << ' ' << client << ' ' << "NAK" << ' ' << id << ' ' << 0 << endl;
                
            }
            else
            {
                ip[id].state = 2;
                if (!te)	ip[id].t = tc + t_def;
                else
                {
                    int t = te - tc;
                    t = max(t_min, t), t = min(t, t_max);
                    ip[id].t = tc + t;
                }
                cout << h << " " << client << " " << "ACK" << " " << id << " " << ip[id].t << endl;
            }
        }
    }
    return 0;
}
```



### T4：校门外的树（x）



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







