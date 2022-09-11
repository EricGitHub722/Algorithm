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


### T3.登机牌条码

#### 先附上40分垃圾代码
```C++
#include <bits/stdc++.h>
using namespace std;

int w, s;
string str;
unordered_map<char, int> Big = {{'A',0},{'B',1},{'C',2},{'D',3},{'E',4},{'F',5},{'G',6},{'H',7},
	{'I',8},{'J',9},{'K',10},{'L',11},{'M',12},{'N',13},{'O',14},{'P',15},{'Q',16},{'R',17},
	{'S',18},{'T',19},{'U',20},{'V',21},{'W',22},{'X',23},{'Y',24},{'Z',25}};
	
unordered_map<char, int> Small = {{'a',0},{'b',1},{'c',2},{'d',3},{'e',4},{'f',5},{'g',6},{'h',7},
	{'i',8},{'j',9},{'k',10},{'l',11},{'m',12},{'n',13},{'o',14},{'p',15},{'q',16},{'r',17},
	{'s',18},{'t',19},{'u',20},{'v',21},{'w',22},{'x',23},{'y',24},{'z',25}};

unordered_map<char, int> Digit = {{'0',0},{'1',1},{'2',2},{'3',3},{'4',4},{'5',5},{'6',6},{'7',7},
	{'8',8},{'9',9}};
	
vector<int> a; 
vector<int> b;

int main() {
	cin >> w >> s;
	cin >> str;
	int len = str.size();
	int mode = 0;	//0:大写，1：小写，2：数字 
	for (int i = 0; i < len; i ++ ) {
		char temp = (char)str[i];
		if (temp >= 'A' && temp <= 'Z') {
			if (mode == 1) {
				a.push_back(28);
				a.push_back(28);
				mode = 0;
			} 
			else if (mode == 2) {
				a.push_back(28);
				mode = 0;
			}
			a.push_back(Big[temp]);
		}
		else if (temp >= 'a' && temp <= 'z') {
			if (mode == 0) {
				a.push_back(27);
				mode = 1;
			}
			else if (mode == 2) {
				a.push_back(27);
				mode = 1;
			}
			a.push_back(Small[temp]);
		}
		else {
			if (mode != 2) {
				a.push_back(28);
				mode = 2;
			}
			a.push_back(Digit[temp]);
		}
	}
	int length = a.size();
	if (length % 2 != 0) {
		a.push_back(29);
	}
	len = a.size() / 2;
	for (int i = 0; i < len; i ++ ) {
		int l = a[i * 2];
		int r = a[i * 2 + 1];
		b.push_back(30 * l + r);
	}
	reverse(b.begin(), b.end());
	b.push_back(b.size() + 1);
	reverse(b.begin(), b.end());
	int need = 0;
	if (b.size() % w != 0) {
		need = ((b.size() / w) + 1) * w - b.size();
	}
	for (int i = 0; i < need; i ++ ) {
		b.push_back(900);
	}
	b[0] = b.size();
	for (int i = 0; i < b.size(); i ++ ) {
		cout << b[i] << endl;
	}
	
	return 0;
}
```
