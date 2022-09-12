## 第25次CCF-CSP

### T2.出行计划

主要方法是推出在t时间段做核酸之后，可以进的一个区间段

若在t时间做了核酸，可以在t + k ---t + k + c - 1时间段进入某个场所

若在ti时刻进入场所，则有ti >= t + k; ti <= t + k + c - 1

即ti - k - c + 1 <= t <= ti - k是区间

方法：差分 + 前缀和

#### 示例代码

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 200010;

int n, m, k;
int d[N];

int main() {
	
	cin >> n >> m >> k;
	for (int i = 1; i <= n; i ++ ) {
		int t, c;
		cin >> t >> c;
		d[max(1, t - k - c + 1)] ++;
		d[max(1, t - k + 1)] --;
	}
	for (int i = 1; i <= N; i ++ ) {
		d[i] += d[i - 1];
	}
	for (int i = 1; i <= m; i ++ ) {
		int q;
		cin >> q;
		cout << d[q] << endl;
	}
	return 0;
}

```

### 计算资源调度器

#### 20分代码（寄）
```C++


#include <iostream>
#include <vector>
 
using namespace std;
 
int main()
{
    int n, m;  // 计算节点的数目和可用区的数目
    cin>>n>>m;
 
    for (int i = 1; i <= n; ++i) {
        int li;
        cin>>li;
    }
 
    int g;  // 任务的组数
    cin>>g;
 
    int index = 0;
    for (int i = 0; i < g; ++i) {
        int f, a, na, pa, paa, paar;
        cin>>f>>a>>na>>pa>>paa>>paar;
 
        if (na == 0 && pa == 0 && paa == 0) {  // 不需要满足任何要求时
            for (int j = 0; j < f; ++j) {
                index = (index % n) + 1;
                cout<<index<<" ";
            }cout<<endl;
        }
        else {
 
        }
    }
 
    return 0;
}
```

#### 参考的AC代码如下
```C++
#include <bits/stdc++.h>
using namespace std;

typedef struct node{
	int id;
	int block;
	int nums;
	unordered_set<int> task;
	
	bool operator < (const node &a)const {
		if (this->nums != a.nums) {
			return this->nums < a.nums;
		}
		else {
			return this->id < a.id;
		}
	}
}node; 

unordered_map<int, node> Node;

void func1(int n, vector<int> &temp) {
	for (auto it = temp.begin(); it != temp.end();) {
		if (Node[*it].block != n) {
			temp.erase(it);
		}
		else it ++;
	}
	return;
}

void func2(int n, vector<int> &temp) {
	set<int> qu;
	for (auto it = Node.begin(); it != Node.end(); it ++ ) {
		if (it->second.task.count(n) == 1) {
			qu.insert(it->second.block);
		}
	}
	for (auto it = temp.begin(); it != temp.end(); ) {
		if (qu.count(Node[*it].block) == 0) {
			temp.erase(it); 
		} 
		else it ++;
	}
	return;
}

vector<int> func3(int n, vector<int> temp) {
	for (auto it = temp.begin(); it != temp.end(); ) {
		if (Node[*it].task.count(n) == 1) {
			temp.erase(it);
		}
		else it ++;
	}
	return temp;
}

int main() {
	int n, m;
	cin >> n >> m;
	vector<int> idsum;
	for (int i = 1; i <= n; i ++ ) {
		int temp;
		cin >> temp;
		Node[i] = node{i, temp, 0, unordered_set<int>()};
		idsum.push_back(i);
	}
	int T;
	cin >> T;
	vector<int> temp, temp2;
	while (T -- ) {
		int f, a, na, pa, ppa, paar;
		cin >> f >> a >> na >> pa >> ppa >> paar;
		while (f -- ) {
			temp = idsum;
			temp2.clear();
			if (na) {
				func1(na, temp);
			}
			if (pa) {
				func2(pa, temp);
			}
			if (ppa) {
				temp2 = func3(ppa, temp);
			}
			if ((temp2.empty() && paar == 1) || temp.empty()) {
				cout << "0 ";
				continue;
			}
			else if (temp2.empty() && paar == 0) {
				temp2 = temp;
			}
			sort(temp2.begin(),temp2.end(),[](const int &a1, const auto &b1)->bool{
				return Node[a1] < Node[b1];
			});
			Node[temp2[0]].nums ++;
			Node[temp2[0]].task.insert(a);
			cout << Node[temp2[0]].id << " ";
		}
		cout << endl;
		
	}
	return 0;
}
```
