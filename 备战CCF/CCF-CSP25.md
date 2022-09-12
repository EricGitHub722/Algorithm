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

### 资源调度管理器

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
