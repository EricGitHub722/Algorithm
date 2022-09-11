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
2. 


#### 滑动窗口
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





