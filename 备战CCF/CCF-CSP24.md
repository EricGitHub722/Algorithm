## CCF-CFP24

### T1.查询序列

按题意爆搜即可

#### 样例代码：
```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    int n, N;
    cin >> n >> N;
    vector<int> A(N + 1, 0);
    for (int i = 0; i < n; i ++ )
    {
        int temp;
        cin >> temp;
        A[temp] += 1;
    }
    for (int i = 1; i < N; i ++ ) {
        A[i] += A[i - 1];
    }
    for (int i = 1; i <= N; i ++ ) {
        A[i] += A[i - 1];
    }
    cout << A[N] << endl;
    return 0;
}
```

### T2.查询序列新解

考虑一种样例即可

```C++
a a a a b b c c c c c c c c c c c d d
e e e f f f g g g h h h i i i j j j h
```

分为根据首末估计函数的不同分为三种情况

#### 样例代码如下：
```C++
#include <bits/stdc++.h>
using namespace std;

const int M = 100010;
long long a[M];

int main() {
    int n, N;
    cin >> n >> N;
    for (int i = 1; i <= n; i ++ ) {
        cin >> a[i];    
    }
    a[n + 1] = N;
    long long r = N / (n + 1);

    long long sum = 0;
    for (int i = 1; i <= n + 1; i ++ ) {
        long long L = a[i - 1], R = a[i] - 1;
        long long x = i - 1;
        long long g_m = L / r;
        long long g_n = R / r;

        if (g_m == g_n) {
            sum += (R - L + 1) * labs(g_n - x);
        }
        else {
            sum += ((L / r + 1) * r - L) * labs(g_m - x);
            sum += ((R + 1) - (R / r) * r) * labs(g_n - x);
        }
        for (int j = g_m + 1; j < g_n; j ++ )
            sum += r * labs(j - x);
    }
    cout << sum << endl;

    return 0;
}
```
