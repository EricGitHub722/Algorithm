# Leetcode详解

## 每日一题

### 3.30-1606.找到处理最多请求的服务器

思路：只能使用优先队列+set加速搜索，每到一个新任务，先释放空闲的服务器，再从空闲服务器中 lower_bound快速分配服务器，代码注释较多，应该易懂。

```C++
class Solution {
public:
    vector<int> busiestServers(int k, vector<int>& arrival, vector<int>& load) {
        // O（nlog（n））才能通过
        vector<int> count(k, 0); //记录服务了多少个请求
        int now = 0; // 公共时间，理解为“现在”
        int size = arrival.size();
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>> > busy; // 释放时间,服务器
        set<int> free;

        for (int i = 0; i < k; ++i) free.insert(i);

        for (int i = 0; i < size; ++i){
            now = arrival[i]; //时间在变化

            while (!busy.empty() && busy.top().first <= now){ //释放完成任务服务器
                free.insert(busy.top().second);
                busy.pop();
            }
            
            if (free.empty()) continue; //没有空闲 跳过

            //开始分配
            //在指定区域内查找不小于目标值的第一个元素
            //temp为迭代器，返回为迭代器的位置
            auto temp = free.lower_bound(i % k);
            //找到了或者没找到，此时再次从头部开始寻找
            if (temp == free.end()) temp = free.lower_bound(0);

            busy.push(make_pair(now + load[i], *temp));
            count[*temp] ++;
            free.erase(temp);
        }

        int max_times = *max_element(count.begin(), count.end());
        vector<int> res;
        for (int i = 0; i < k; ++i){
            if (count[i] == max_times) res.emplace_back(i);
        }
        return res;
    }
};

```



### 3.17-720.词典最长的单词（E）

总结该题是为了熟悉自定义排序的写法，以及对set的使用

```C++
class Solution {
public:
    string longestWord(vector<string>& words) {
        sort(words.begin(), words.end(), [](const string & a, const string & b) {
            if (a.size() != b.size()) {
                //长度升序
                return a.size() < b.size();
            } else {
                //长度相同时按字典序降序
                return a > b;
            }
        });
        string longest = "";
        unordered_set<string> cnt;
        cnt.emplace("");
        for (auto & word : words) {
            if (cnt.count(word.substr(0, word.size() - 1))) {
                cnt.emplace(word);
                longest = word;
            }
        }
        return longest;
    }
};
```

### 3.16-432.全O(1)的数据结构（H）

双链表加两个哈希。一个哈希根据 key 找节点，另一个节点根据值找节点，应为值只能加一或减一，所以直接找相邻值的节点然后插进去。

**其中，哈希表存储 key->Node 的映射关系，双向链表按频率排序，对于 LRU 就是最近最少使用，对于今天这道题就是每个 key 的次数。**

```C++
/**
 * Your AllOne object will be instantiated and called as such:
 * AllOne* obj = new AllOne();
 * obj->inc(key);
 * obj->dec(key);
 * string param_3 = obj->getMaxKey();
 * string param_4 = obj->getMinKey();
 */
class AllOne {
private:
    //如何定义节点
    struct Node {
        int cnt;    //字符串出现的次数
        unordered_set<string> c;    //出现cnt次数的字符串集合
        Node* left;
        Node* right;
        Node() {}
        //构造函数创建，对于变量名字的合理命名
        Node(int _cnt, const string& s = "", Node* _l = nullptr, Node* _r = nullptr) {
            cnt = _cnt;
            c.insert(s);
            left = _l;
            right = _r;
        }
    };
    Node* head;
    Node* tail;
    // 两个哨兵、一个hash_map用来stirng到Node*的映射
    unordered_map<string, Node*> pro;
public:
    //为什么要使用双向链表？1：节点能够存储出现的次数，复杂度为O(1)；2：插入和删除也是O(1)复杂度。
    AllOne() {
        head = new Node(-1);
        tail = new Node(-1);
        head->right = tail;
        tail->left = head;
    }

    void clear(Node* node)
    {
        if (node->c.empty())    //判断是否可以删除
        {
            node->left->right = node->right;
            node->right->left = node->left;
        }
    }
    
    void inc(string key) {
        //如果存在
        if (pro.find(key) != pro.end())
        {
            Node* node = pro[key];
            int cnt = node->cnt;
            Node* next = nullptr;	//好习惯，先写nullptr
            if (node->right->cnt == cnt + 1)    //如果后面的节点出现了cnt+1次
            {
                next = node->right;
            }
            else {  //创建一个新的节点
                next = new Node(cnt + 1, key, node, node->right);
                node->right = next;
                next->right->left = next;
            }
            next->c.insert(key);
            pro[key] = next;
            node->c.erase(key);

            clear(node);    //默认清除一下
        }
        else {	//否则创建一个新的节点
            Node* node = nullptr;
            if (head->right->cnt == 1) {
                node = head->right;
            }
            else {
                node = new Node(1, key, head, head->right);
                head->right = node;
                node->right->left = node;
            }
            node->c.insert(key);    //表示key出现了1次
            pro[key] = node;    //将该<key, Node>连接起来
        }
    }
    
    void dec(string key) {
        Node* node = pro[key];
        int cnt = node->cnt;
        if (cnt == 1)
        {
            pro.erase(key);
        }
        else {
            Node* next = nullptr;
            if (node->left->cnt == cnt - 1) {
                next = node->left;
            }
            else {
                next = new Node(cnt - 1, key, node->left, node);
                node->left->right = next;
                node->left = next;
            }
            pro[key] = next;
            next->c.insert(key);
        }
        node->c.erase(key);
        clear(node);    //默认清除一下
    }
    
    string getMaxKey() {
        for (const auto& c : tail->left->c) return c;
        return "";
    }
    
    string getMinKey() {
        for (const auto& c : head->right->c)    return c;
        return "";
    }
};
```



### 3.12-393（M）

今天的题思路不难，首先就想到对于每个数使用位运算，注意细节然后遍历一遍就行。

```C++
class Solution {
public:
    bool validUtf8(vector<int>& data) {
        int n = data.size();
        for (int i = 0; i < n; i ++ )
        {
            int x = data[i];
            int m = check(x);	//检测前n位是1
            if (m > 1 && m <= 4)	//2-4字节的情况
            {
                for (int j = 1; j < m; j ++ )
                {
                    if (i + j < n)
                    {
                        int y = data[i + j];
                        //前两位是否是10
                        if (y >> 6 == 2) continue;
                        else    return false;
                    }
                    else    return false;
                }
                //i跟到下一个字符
                i += m - 1;
            }
            else if (m == 1 || m > 4)    return false;
            //1字节的第一位是0，大于4字节也返回false
        }
        return true;
    }

    int check(int x)
    {
        int res = 0;
        for (int i = 7; i >= 0; i --)
            if (x >> i & 1) res ++;
            else    return res;
        return res;
    }
};
```



### 3.11-2049（M）

做这道题我首先想的是每连接一个子节点，就沿着其根节点一直往上传，然后统计方法与下面相同，然而因为耗时太长，该方法不出所料TLE了，后来则想到了DFS。使用两个vector数组分别存左右子节点，两个map分别存当前节点左右子树的节点个数，tmp则保存该节点上面剩余的节点个数，也就是用n - R[i] - L[i] - 1得到。

代码如下：

```C++
class Solution {
public:
    unordered_map<int, long long> L;
    unordered_map<int, long long> R;
    int countHighestScoreNodes(vector<int>& parents) {
        int n = parents.size();
        vector<int> left(n, -1);
        vector<int> right(n, -1);
        int root = -1;
        for (int i = 0; i < n; i ++ )
        {
            if (parents[i] == -1)   root = i;   //找根
            else if (left[parents[i]] == -1)    left[parents[i]] = i;   //找左右子节点
            else if (right[parents[i]] == -1)   right[parents[i]] = i;
        }
        dfs(left, right, root);
        unordered_map<long long, int> ret;
        long long res = 0;
        for (int i = 0; i < n; i ++ )
        {
            long long Sum = 1;
            long long tmp = n - L[i] - R[i] - 1;
            L[i] = max(L[i], 1ll * 1);
            R[i] = max(R[i], 1ll * 1);
            tmp = max(tmp, 1ll * 1);
            Sum = tmp * L[i] * R[i];
            res = max(res, Sum);
            ret[Sum] ++;
        }
        return ret[res];
    }

    int dfs(vector<int>& left, vector<int>& right, int root)
    {
        int tleft = 0, tright = 0;
        if (left[root] == -1 && right[root] == -1)  return 1;
        if (left[root] != -1)   tleft = dfs(left, right, left[root]);
        if (right[root] != -1)   tright = dfs(left, right, right[root]);
        L[root] = tleft;
        R[root] = tright;
        return tleft + tright + 1;
    }
};
```



### 3.9-798（M+）

首先就能想到差分，其次开始列其出现的状态

每个数在从轮调0次到论调K次，都会经过以下的几种情况

```C++
nums[i] <= i;
nums[i] == i;
nums[i] >= i;
```

故思路应该是：先记录开始时候的得分，然后根据移动的次数分别进行差分计算，最后累计求和的过程中寻找最大值

```C++
class Solution {
public:
    int bestRotation(vector<int>& nums) {
        int n = nums.size();
        vector<int> ret(n + 1, 0);
        int mount = 0;
        int res = 0;
        for (int i = 0; i < n; i ++ )
        {
            if (nums[i] > i)
            {
                ret[i + 1] ++;  //此时只需将其直接移动到最右边，即可满足题意
                ret[n - nums[i] + i + 1] --;    //在移动到最右边后继续移动的过程中，可能会再次回到nums[i] > i的情况
            }
            else
            {
                mount ++;
                ret[i - nums[i] + 1] --;    //先要移动到num[i] <= i的情况
                ret[i + 1] ++;  //道理同上
            }
        }
        int sum = mount;
        for (int i = 0; i < n; i ++ )
        {
            sum += ret[i];
            cout << sum << endl;
            if (sum > mount)
            {
                mount = sum;
                res = i;
            }
        }
        return res;

    }
};
```

### 2.26-553（M-）

一开始误以为是DFS，知道后来才知道是有特定的最优解的，这也是这道题简单的原因。对于一个除法而言，保证其分子最大，分母最小即是最大解。分子最大只要保证不除即可，而分子最小同理（理解后发现只需按序除即可）

```C++
class Solution {
public:
    string optimalDivision(vector<int>& nums) {
        if (nums.size() == 1)   return to_string(nums[0]);
        if (nums.size() == 2)   return to_string(nums[0]) + "/" + to_string(nums[1]);
        string ret = to_string(nums[0]) + "/(";
        for (int i = 1; i < nums.size(); i ++ )
            ret += to_string(nums[i]) + (i == nums.size() - 1 ? "" : "/");
        return ret + ")";
    }
};
```



### 2.25-1706（M）

该题使用**模拟**，找到小球无法下降到最低端的3种情况：被左墙堵死、被右墙堵死以及中间墙V字型

```C++
class Solution {
public:
    vector<int> findBall(vector<vector<int>> &grid) {
        int n = grid[0].size(); //有n列
        vector<int> ans(n);
        for (int j = 0; j < n; ++j) {
            int col = j; // 球的初始列
            for (auto &row : grid) {
                int dir = row[col];
                col += dir; // 移动球
                if (col < 0 || col == n || row[col] != dir) { // 到达侧边或 V 形
                    col = -1;
                    break;
                }
            }
            ans[j] = col; // col >= 0 为成功到达底部
            //col即是从哪一列掉出
        }
        return ans;
    }
};
```

### 2.22-1994（H+）

看到数据范围想到了bit set，之后看到所有数都是小于等于30，故想到使用Map来存储所有的数，然后将30以内的所有质数都挑出来组成primes数组，使用一维DP解决该问题：遍历Map中的每个数（1除外），然后再遍历所有的状态，如果该数是该状态的一个子集，便可以进行状态转移。最终将1的情形相乘即可。该解法也几乎是该题的最优解。

```C++
class Solution {
public:
    vector<int> primes = {2,3,5,7,11,13,17,19,23,29};
    int M = 1e9 + 7;
    int numberOfGoodSubsets(vector<int>& nums) {
        unordered_map<int, int> Map;
        for (auto x : nums)
            Map[x] ++;
        int n = primes.size();
        vector<long> dp(1 << n);
        dp[0] = 1;	//一个数都没有，也就是空集数目是1个

        for (auto p : Map)
        {
            int num = p.first;
            int count = p.second;
            if (num == 1)   continue;
            int encode = encoding(num);
            if (encode == -1)   continue;	//若有重复质数则返回-1

            for (int state = (1 << n) - 1; state >= 1; state --)
            {	//从大到小遍历，原理同一维背包，若要从小到大遍历则需要设置中间量dp_old[]
                if (state - encode == (state ^ encode))
                {	//判断是否是子集的方法
                    dp[state] += dp[state - encode] * count;
                    dp[state] %= M;
                }
            }
        }
        //将所有可能的状态都相加
        long ret = 0;
        for (int state = 1; state < (1 << n); state ++ )
            ret = (ret + dp[state]) % M;
        
        //单独计算1
        long power2 = 1;	
        for (int i = 0; i < Map[1]; i ++ )
            power2 = power2 * 2 % M;

        return ret * power2 % M;
    }

    int encoding(int num)
    {
        int encode = 0;
        for (int i = 0; i < primes.size(); i ++ )
        {
            if (num % primes[i] == 0)
            {
                encode += (1 << i);
                num /= primes[i];
            }
            if (num % primes[i] == 0)	//检查是否还有重复质数
                return -1;
        }
        return encode;
    }
};
```

## 周赛题解

### 283周赛-2197（H）

思路：使用栈去处理nums，对于入栈只要栈当前有元素，就进行gcd计算是否互质，如果不互质则计算两者的最小公倍数

```C++
#define x first
#define y second
#define rep(i, a, b) for(int i = a; i < (b); i ++)
#define sz(n) (int)(n).size()
typedef pair<int, int> PII;
typedef vector<int> vi;
typedef long long ll;
typedef long double ld;

class Solution {
public:
    vector<int> replaceNonCoprimes(vector<int>& nums) {
        vector<int> ret;
        stack<ll> stk;
        int n = sz(nums);
        rep(i, 0, n)
        {
            int t = eval(stk, nums, nums[i]);
            while (t)   //若成功处理一组，则看有没有迭代过程
            {
                ll tmp = stk.top();
                stk.pop();
                t = eval(stk, nums, tmp);
            }
        }
        while (stk.size())
        {
            ret.push_back(stk.top());
            stk.pop();
        }
        reverse(ret.begin(), ret.end());
        return ret;
    }

    int eval(stack<ll>& stk, vector<int>& nums, ll u)
    {
        int flag = 0;
        if (stk.size() == 0)    stk.push(u);
        else
        {
            ll tnum = stk.top();
            ll res = gcb(tnum, u);
            if (res == 1)
            {
                stk.push((ll)u);
            }
            else
            {
                ll tmp = gcd(max(tnum, (ll)u), min(tnum, (ll)u));

                stk.pop();
                //n*m的最大乘积除以最大公约数即是最小公倍数
                stk.push((ll)tnum * u / tmp);
                flag = 1;   //成功处理一组
            }
        }
        return flag;
    }

    int gcd(int a, int b)
    {
        return b ? gcd(b, a % b) : a;
    }
};
```



### 282周赛-6010（M-）

思路：**二分**时间，积累该题的关键在于二分上下界的设置以及二分的写法上

```C++
class Solution {
public:
    long long minimumTime(vector<int>& time, int totalTrips) {
        long long min_time = INT_MAX;
        for (auto t : time)
            //注意：在min和max中，两相比较的数一定要类型一致
            min_time = min(min_time, 1LL * t);
        //在该题中，下界是min(time)，上界是min(time)*totalTrips
        long long l = min_time, r = min_time * totalTrips;
        int n = time.size();
        while (l < r)
        {
            long long tmp = 0;
            long long mid = (l + r) / 2;
            for (int i = 0; i < n; i ++ )
            {
                tmp += mid / (long long)time[i];
            }
            if (tmp >= (long long)totalTrips)   r = mid;
            else    l = mid + 1;
        }
        return r;
    }
};
```

### 282周赛-6011（H）

思路：**完全背包**，该背包关键在于预处理阶段，初始化pre[ ]数组，此处时间复杂度为1e8，将走 j 圈所一次性使用的轮胎代价记录到pre中，然后完全背包

```C++
class Solution {
public:
    int minimumFinishTime(vector<vector<int>>& tires, int changeTime, int numLaps) {
        vector<long long> dp(1010), pre(1010);
        for (int i = 0; i < 1010; i ++ )
            dp[i] = pre[i] = 0x3f3f3f3f;
        int n = tires.size();
        for (int i = 0; i < n; i ++ )
        {
            long long val = tires[i][0];
            long long fenzi = tires[i][1];
            long long fenmu = fenzi;
            long long tmp = 0;
            //假设该轮胎连续走j圈
            for (int j = 1; j <= numLaps; j ++ )
            {
                tmp += val * fenzi / fenmu;
                if (tmp > 0x3f3f3f3f)   break;
                pre[j] = min(pre[j], tmp);
                fenzi *= fenmu;
            }
        }
        for (int i = 1; i <= numLaps; i ++ )
        {
            dp[i] = pre[i];
            for (int k = 1; k < i; k ++ )
                dp[i] = min(dp[i], dp[i - k] + pre[k] + changeTime);
        }
        return dp[numLaps];
    }
};
```

### 281周赛-2183（H-）

思路：**分解质因数**，将nums中的所有数都于k进行一个分解质因数，若互质则会返回1，否则返回两者的最大公约数，然后将nums中的所有数都添加入Map当中，最后两次遍历Map，进行ret的累加

```C++
class Solution {
public:
    long long coutPairs(vector<int>& nums, int k) {
        long long ret = 0;
        unordered_map<int, int> Map;
        for (auto t : nums)
            Map[gcd(t, k)] += 1;
        for (auto [k1, v1] : Map)
            for (auto [k2, v2] : Map)
            {
                if (1ll * k1 * k2 % k == 0)
                {
                    if (k1 < k2)
                        ret += 1ll * v1 * v2;
                    else if (k1 == k2)
                        ret += 1ll * v1 * (v1 - 1) / 2;
                }
            }
        return ret;
    }

    int gcd(int a, int b)
    {
        return b ? gcd(b, a % b) : a;
    }
};
```

### 263周赛-2045（H-）

思路：先构建一个邻接表，之后用队列记录BFS过程，然后将每个点设为可以经过两次，则第二次经过n的时间就是第二短时间，该题同样需要使用visited数组和dist数组

```C++
//邻接表构建模板
vector<vector<int>> adj(n);
for (auto t : edges)
{
    adj[t[0]].push_back(t[1]);
    adj[t[1]].push_back(t[0]);
}
//or
vector<int> next[n + 1];
for (auto t : edges)
{
    next[t[0]].push_back(t[1]);
    next[t[1]].push_back(t[0]);
}
```

完整代码：

```C++
class Solution {
public:
    int secondMinimum(int n, vector<vector<int>>& edges, int time, int change) {
        /*
            先建立一个邻接表
            使用vector数组实现
        */
        vector<int> next[n + 1];
        for (auto t : edges)
        {
            next[t[0]].push_back(t[1]);
            next[t[1]].push_back(t[0]);
        }
        //需要使用一个时间数组dist和一个visited数组判断访问次数
        vector<int> dist(n + 1, -1);
        vector<int> visited(n + 1, 0);
        queue<pair<int, int>> q;
        q.push({1, 0});
        dist[1] = 0;

        while (!q.empty())
        {
            auto t = q.front();
            q.pop();
            int ttime = t.second;
            int pos = t.first;

            int tt;
            //检查已经经过了多久时间
            int turn = ttime / change;
            //检查已经过了多少轮
            if (turn % 2 == 0)
                tt = ttime + time;
            else
                tt = (turn + 1) * change + time;
            for (auto x : next[pos])
            {
                if (visited[x] < 2 && dist[x] < tt)
                {
                    q.push({x, tt});
                    visited[x] += 1;
                    dist[x] = tt;
                    if (x == n && visited[n] == 2)
                        return tt;
                }
                
            }
        }

        return -1;
    }
};
```





## 日常题解

### 2172.数组的最大与和（H+）

思路：首先发现其是二分图匹配类型，其次属于带权二分图匹配。不考虑KM算法以及最大网络流时，使用状态压缩+DP做法可以解决许多问题。

```C++
/*
DP思路1：
dp[i][j]:前i个篮子装填状态为j的物品
for (int i = 0; i < numSlots; i ++ )
	for (int state = 0; state < (2 ^ n); state ++ )
		for (auto pickedNums for i-th slot: available plans)
			dp[i][state] = max(dp[i][state], dp[i - 1][state - pickedNums] + (pickedNums & slot[i]));
时间复杂度分析：9*pow(2,18)*[C(18,2)+C(18,1),C(18,0)]约等于4e7
故会TLE

DP思路2：
dp[i][j]:前i个物品装入j个篮子
for (int i = 0; i < nums.size(); i ++ )
	for (int state = 0; state < (3 ^ m); state ++ )
		for (auto pickedSlot for i-th num: available plans)
			dp[i][state] = max(dp[i][state], dp[i - 1][state - pickedSlot] + (pickedSlot & nums[i]));
时间复杂度分析：18*pow(3,9)*9约等于1e6

代码：
*/
class Solution {
public:
    int maximumANDSum(vector<int>& nums, int numSlots) {
        int n = nums.size();
        nums.insert(nums.begin(), 0);
        int m = numSlots;
        int ret = 0;
        vector<vector<int>> dp(n + 1, vector<int>(pow(3, m), INT_MIN / 2));
        dp[0][0] = 0;
        for (int i = 1; i <= n; i ++ ) 
            for (int state = 0; state < pow(3, m); state ++ ) {
                for (int j = 0; j < m; j ++ )
                    if (find_bits(state, j) >= 1)
                        dp[i][state] = max(dp[i][state], dp[i - 1][state - pow(3, j)] + (nums[i] & (j + 1)));
                if (i == n) ret = max(ret, dp[i][state]);   
            }
        return ret;
    }

    int find_bits(int state, int j) {
        state /= pow(3, j);
        return state % 3;
    }
};
```



### 630.课程表|||（H）

做法：sort+PQ去得到比较贪心的结果，本题是带反悔的贪心

有DDL的属性一般都要先满足DDL

方法：先将其按照DDL从小到大排序，然后依次添加即可，如果添加当前课程不符合，即（现在的总时间>该课程的截止日期），则弹出最后一个课程再加入该课程（由于贪心，并且弹出的该课程之前的总时间一定小于等于之前课程的截止日期，所以并不会减少课程的数量）；否则直接加入。以上推导基于结论：**对于两门课，如果后者的关闭时间比较晚，那么我们先学习前者再学习后者，结果总是最优的。**

```C++
class Solution {
public:
    int scheduleCourse(vector<vector<int>>& courses) {
        sort(courses.begin(),courses.end(),[](vector<int> &a, vector<int> &b){
            return a[1] < b[1];
        });
        priority_queue<int> q;
        int now = 0;// 当前的总计上课时间
        for(auto &v : courses){
            now += v[0];// 直接上该门课
            q.push(v[0]);
            if(now > v[1]){//但门课无法在截止日期前上完  则去掉一个最费时的课程
                now -= q.top(); 
                q.pop();
            }
        }
        return q.size();
    }
};
```

