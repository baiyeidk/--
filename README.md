# 给自己看的题解，记录一下做题解法和思路等

## [选择配送](./hdu25春/1010%20选择配送.html)

计算曼哈顿距离最大值的最小值，转化成切比雪夫距离，切比雪夫距离定义如下：

设 $A(x_1,y_1),B(x_2,y_2)$

$d(A,B)=\max\{|x_1-x_2|,|y_1-y_2|\}$

曼哈顿距离与切比雪夫距离可以相互转化：

$\begin{aligned}D_{man}(A,B)&=|x_1-x_2|+|y_1-y_2|\\&=\max\{x_1-x_2+y_1-y_2,x_1-x_2+y_2-y_1,x_2-x_1+y_1-y_2,x_2-x_1+y_2-y_1\}\\&=\max\{|(x_1+y_1)-(x_2+y_2)|,|(x_1-y_1)-(x_2-y_2)|\}\\&=d((x_1+y_1,x_1-y_1),(x_2+y_2,x_2-y_2))\end{aligned}$

几何表示为将 $x+y$ 和 $x-y$ 作为新的坐标系，把曼哈顿距离转化为直线距离。
![ ](./hdu25春/QQ20250322-133111.png)

思路：
计算出所有客户哈密顿坐标，求最大/小的切比雪夫 $x,y$ 值。

读入快递站的坐标，转化成切比雪夫，与前面算好的 4 个值求最小 dist  

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 100000 + 10;
const int INF = 2147483647;
typedef long long ll;
const ll inf = 1e18;
int main()
{
    ios::sync_with_stdio(0), cin.tie(0);
    int t;
    cin >> t;

    while (t--)
    {
        ll maxS = -inf, minS = inf;
        ll maxD = -inf, minD = inf;
        ll n, m;
        cin >> n >> m;
        for (int i = 1; i <= n; i++)
        {
            int x, y;
            cin >> x >> y;
            ll s = x + y;
            ll d = x - y;
            maxS = max(maxS, s);
            minS = min(minS, s);
            maxD = max(maxD, d);
            minD = min(minD, d); 
        }
        ll ans = inf;
        for (int j = 1; j <= m; j++)
        {
            ll a, b;
            cin >> a >> b;
            ll s = a + b, d = a - b;
            ll dist = max({abs(maxS - s), abs(s - minS), abs(maxD - d), abs(d - minD)});
            ans = min(ans, dist);
        }
        cout << ans << "\n";
    }
}
```

> 自己写的时候把maxmin的初始化写到while外面去了。。。。调了一晚上，下次改成while(t--) solve();的形式避免这个问题

## 修复公路

题意是个求**连通块数量 -1** 的题目。每次merge就代表联通块数量 -1 。

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N = 300000 + 10;
int num[N];
int pre[N];
int rnk[N];
int find(int x)
{
    if (pre[x] == x)
        return x;
    return pre[x] = find(pre[x]);
}
void merge(int x, int y, int &components)
{
    x = find(x);
    y = find(y);
    if (x == y)
        return;
    if (rnk[x] < rnk[y])
    {
        swap(x, y);
    }
    pre[y] = x;
    if (rnk[x] == rnk[y])
        rnk[x]++;
    components--;
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    int t;
    cin >> t;
    while (t--)
    {
        int n;
        cin >> n;
        for (int i = 1; i <= n; i++)
        {
            cin >> num[i];
        }
        for (int i = 1; i <= n; i++)
        {
            pre[i] = i;
            rnk[i] = 0;
        }
        int components = n;
        for (int i = 1; i <= n; i++)
        {
            if (i + num[i] <= n)
                merge(i, i + num[i], components);
            if (i - num[i] >= 1)
                merge(i, i - num[i], components);
        }
        cout << components - 1 << "\n";
    }
}
```

## [P3131 USACO16JAN Subsequences Summing to Sevens S](https://www.luogu.com.cn/problem/P3131)

是一道前缀和的题，找最长和是 7 的倍数的子序列，可以求前缀和然后再计算。

```python
n = int(input())
cows = [int(input()) for _ in range(n)]

prefix = [0] * (n + 1)
for i in range(n):
    prefix[i + 1] = prefix[i] + cows[i]

ans = 0
flag = 1
while flag:
    for i in range(n, 0, -1):  
        for j in range(n - i + 1): 
            if (prefix[j + i] - prefix[j]) % 7 == 0:
                flag = 0
                ans = i
                break
        if not flag:
            break
print(ans)
```

TLE 了，因为 $n^2$ 复杂度还是不能接受，尝试优化如下：

因为两数 (prefix) 相减的差 %7 = 0 ，可以等价为两数 (prefix) %7 的余数相同。所以只需要统计前缀和 %7 的余数，然后 $7n$ 的复杂度遍历前缀和序列，找到最长的相等的两个值就行。

```python
n = int(input())
prefix =[0]*(n+1)
for i  in range(1,n+1):
    prefix[i]=(prefix[i-1]+int(input()))%7
ans = 0
for i in range(7):
    first=-1
    last=-1
    for j in range(n+1):
        if prefix[j]==i:
            if first==-1:
                first=j
            else:
                last=j
    ans= max(ans,last-first)
print(ans)
```

>本来想到 %7 了，但是没有估算复杂度，下次先估算复杂度，看到不太行的就别交了，对于 acm 赛制还是挺重要的
