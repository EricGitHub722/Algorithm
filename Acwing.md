# Acwing

## 2022春季每日一题

### 3370. 牛年

思路：两个Hash表实现即可，Map用来存每个牛的岁数；p用来存每个牛的属相

```C++
#include <bits/stdc++.h>
using namespace std;

unordered_map<string, int> year = {{"Ox", 0}, {"Tiger", 1}, {"Rabbit", 2}, {"Dragon", 3}, {"Snake", 4}, {"Horse", 5}, {"Goat", 6}, {"Monkey", 7}, {"Rooster", 8}, {"Dog", 9}, {"Pig", 10}, {"Rat", 11}};
unordered_map<string, int> Map;
unordered_map<string, string> p;

void add(string name1, string name2, string preorafter, string y)
{
    string name2_y = p[name2];
    int r = year[name2_y];
    int l = year[y];
    int cnt = 0;
    if (preorafter == "previous")
    {
        if (r == l) cnt = 12;
        else {
            while (r != l)
            {
                r --;
                cnt ++;
                if (r == -1)    r = 11;
            }
        }
        Map[name1] = Map[name2] + cnt;
    }
    else {
        if (r == l) cnt = 12;
        else {
            while (r != l)
            {
                r ++;
                cnt ++;
                if (r == 12)    r = 0;
            }
        }
        Map[name1] = Map[name2] - cnt;
    }
    
    p[name1] = y;
}

int main()
{
    int n;
    
    Map["Bessie"] = 0;
    p["Bessie"] = "Ox";
    vector<string> tmp(8);
    cin >> n;
    while (n -- )
    {
        for (int i = 0; i < 8; i ++ )
            cin >> tmp[i];
        string name1, name2, preorafter, y;
        name1 = tmp[0], name2 = tmp[7], preorafter = tmp[3], y = tmp[4];
        add(name1, name2, preorafter, y);
    }
    cout << abs(Map["Elsie"] - Map["Bessie"]);
    return 0;
}
```



### 3358. 放养但没有完全放养

思路：每遍历一次字母表过一些字母，看需要遍历几次即可

```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    vector<char> s(26);
    for (int i = 0; i < 26; i ++ )  cin >> s[i];
    string res;
    int cnt = 0;
    cin >> res;
    int j = 0;
    while (j < res.size())
    {
        cnt ++;
        for (int i = 0; i < 26; i ++ )
            if (j < res.size() && s[i] == res[j])
                j ++;
    }
    cout << cnt << endl;
}
```

### 3346.你知道你的ABC吗

思路：可以想到最小的两个分别是A和B，C则可以用最后一个减去得到

```C++
#include <bits/stdc++.h>
using namespace std;

int main()
{
    vector<int> a;
    int tmp;
    for (int i = 0; i < 7; i ++ )
    {
        cin >> tmp;
        a.push_back(tmp);
    }
    sort(a.begin(), a.end());
    cout << a[0] << " " << a[1] << " " << a[6] - a[0] - a[1] << endl;
    return 0;
}
```

### 3745.牛的学术圈

思路：采用贪心策略，将数组按照降序进行排序，然后当a[i]>=i 且i最大的时候，i就是我们当前数组的指数(这个比较简单不做证明，有问题评论区提问即可)。

首先我们要判断出，每个数最多只能加1，所以h指数最大只能是h+1,
(证明：当前指数是h，a[h]>=h,a[h+1]<h+1,由于a[h+1]<h+1,所以a[h+1]最多只有变成h+1,不会变成h+2,更不会变更大，
在数组后面的数中，由于是逆序排列的，所以也小于等于h+1,更不可能变成h+2，由此可知，指数最大只能是h+1)

所以我们现在要做的是选择部分数，然后让他们加1后使得a[h+1] == h+1,首先就是要选择a[h+1]这个点，在这里进行判断，如果a[h+1]<h的话，就不可能变成h+1了(因为一个数最多加1)，所以最大指数就是h,如果a[h+1] == h的话，要找逆序排列中在1~h+1，这个范围中有多少个点等于h(cnt个)，这些点都需要加1，因为这些点都需要大于等于h+1,所以，至少需要cnt个点，当cnt<=L的时候满足条件，指数是h+1,否则是h

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 100010;
int a[N], n, l;

bool cmp(int x, int y)
{
    return x > y;    
}

int main()
{
    cin >> n >> l;
    //由于后面要使用，故下标从1开始
    for (int i = 1; i <= n; i ++ )   cin >> a[i];
    sort(a + 1, a + 1 + n, cmp);
    int h = 0;
    for (int i = 1; i <= n; i ++ ) {
        if (a[i] >= i)  h = i;
        else    break;
    }
    if (a[h + 1] < h) {
        cout << h << endl;
        return 0;
    }
    int cnt = 0;
    for (int i = 1; i <= h + 1; i ++ )
        if (a[i] == h)
            cnt ++;
    if (l >= cnt)   cout << h + 1 << endl;
    else    cout << h << endl;
    return 0;
}
```

