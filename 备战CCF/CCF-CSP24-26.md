## 第26次CCF

### T1.归一化处理

注意事项，该int时int，该double时double，注意细节

#### 示例代码

```C++
#include <bits/stdc++.h>
using namespace std;

const int N = 1010;

double a[N];

int main()
{
	int n;
	double sum = 0;
	cin >> n;
	for (int i = 0; i < n; i ++ ) {
		cin >> a[i];
		sum += a[i];
	}
	double cover_a;
	cover_a = (double)sum / n;	// 平均值 
	double cover_sum = 0;
	for (int i = 0; i < n; i ++ ) {
		cover_sum += (a[i] - cover_a) * (a[i] - cover_a);
	}
	double cover_D;
	cover_D = cover_sum / n;
	cover_D = sqrt(cover_D);
	for (int i = 0; i < n; i ++ ) {
		double temp = a[i] - cover_a;
		double res = temp / cover_D;
		printf("%f\n", res);
	}
	return 0;
}
```
