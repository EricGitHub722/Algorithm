### 基础算法

1. 双指针
2. 二维前缀和
3. 差分

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










