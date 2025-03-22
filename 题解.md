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

题意是个求**连通块数量-1**的题目。每次merge就代表联通块数量-1。

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
