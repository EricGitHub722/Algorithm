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






