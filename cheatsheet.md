## 1 保留几位小数（无import）
```python
ans = 0
for i in range(n):
    if w >= a[i].weight:
        w -= a[i].weight
        ans += a[i].value
    else:
        ans += a[i].value * w / a[i].weight
        break
print(f"{ans:.1f}")
```

-------

## 2 持续输入
```python
while True:
  try:
    输入
  except EOFError:
    break
```

------

## 3 取根号（有import math,也可以1/2次方）
```python
import math
T = int(input())
for _ in range(T): print(int(math.sqrt(int(input()))))
```

------

## 4 字符取ASCII,及常见ascii值
```python
k = int(input())
s = input()
t = len(s)
k %= 26
for i in range(t):
    x = ord(s[i]) #字符转整数
    if x >= 97:
        x -= k
        if x <= 96:
            x = x + 122 - 96
    else:
        x -= k
        if x <= 64:
            x = x + 90 - 64
    print(chr(x), end = "") #整数转字符
""" "1"-49, A 65-90, 97-122"""
```

------

## 5 结构体+排序
```python
NR = 10000
n, w = map(int, input().split())
class Node:
    def __init__(self, value, weight, ratio):
        self.ratio = ratio
        self.value = value
        self.weight = weight
a = []
for i in range(n):
    x, y = map(int, input().split())
    a.append(Node(x, y, x / y))
a = sorted(a, key=lambda _: _.ratio, reverse = True)
```

------

## 6 线段树
```python
class Node:
    def __init__(self, mn, mx):
        self.mn = mn
        self.mx = mx
NR = int(1e5 + 10)
ls = lambda x: x << 1
rs = lambda x: x << 1 | 1
tree = [Node(float("inf"), float("-inf")) for i in range(NR * 4 + 1)]

def pushup(rt):
    tree[rt].mn = min(tree[ls(rt)].mn, tree[rs(rt)].mn)
    tree[rt].mx = max(tree[ls(rt)].mx, tree[rs(rt)].mx)

def upd(rt, l, r, x, MN, MX):
    if l == r:
        tree[rt].mn = MN
        tree[rt].mx = MX
        return
    mid = (l + r) >> 1
    if x <= mid: upd(ls(rt), l, mid, x, MN, MX)
    else: upd(rs(rt), mid + 1, r, x, MN, MX)
    pushup(rt)

def qmin(rt, l, r, qL, qR):
    if qL <= l and r <= qR: return tree[rt].mn
    mid = (l + r) >> 1
    ans = float('inf')
    if qL <= mid: ans = min(ans, qmin(ls(rt), l, mid, qL, qR))
    if mid < qR: ans = min(ans, qmin(rs(rt), mid + 1, r, qL, qR))
    return ans

def qmax(rt, l, r, qL, qR):
    if qL <= l and r <= qR: return tree[rt].mx
    mid = (l + r) >> 1
    ans = float('-inf')
    if qL <= mid: ans = max(ans, qmax(ls(rt), l, mid, qL, qR))
    if mid < qR: ans = max(ans, qmax(rs(rt), mid + 1, r, qL, qR))
    return ans

n, d = map(int, input().split())
#a = list(map(int, input().split()))
a = []
for i in range(n): a.append(int(input()))
b = [[] for i in range(n + 2)]
bmn = [float('inf') for i in range(n + 2)]
bmx = [float('-inf') for i in range(n + 2)]
upd(1, 1, n, 1, a[0], a[0])
now = 1
b[1].append(a[0])
bmn[1] = bmx[1] = a[0]
for i in range(1, n):
    l = 1; r = now; ans1 = now + 1
    while l <= r:
        mid = (l + r) >> 1
        mn = qmin(1, 1, n, mid, now)
        mx = qmax(1, 1, n, mid, now)
        if a[i] - mn > d or mx - a[i] > d: l = mid + 1
        else: ans1 = min(ans1, mid);r = mid - 1
    if ans1 == now + 1:
        now += 1
        b[now].append(a[i])
        bmn[now] = bmx[now] = a[i]
        upd(1, 1, n, now, a[i], a[i])
        continue
    l = ans1; r = now; ans2 = now + 1
    while l <= r:
        mid = (l + r) >> 1
        mx = qmax(1, 1, n, ans1, mid)
        if a[i] >= mx: l = mid + 1
        else: ans2 = min(ans2, mid);r = mid - 1
    if ans2 == now + 1:
        now += 1
        b[now].append(a[i])
        bmn[now] = bmx[now] = a[i]
        upd(1, 1, n, now, a[i], a[i])
        continue
    if a[i] < bmn[ans2]: upd(1, 1, n, ans2, a[i], bmx[ans2])
    b[ans2].append(a[i])
    bmn[ans2] = min(bmn[ans2], a[i])
    bmx[ans2] = max(bmx[ans2], a[i])
for i in range(1, now + 1):
    for j in sorted(b[i]): print(j)
```

```cpp
# include <bits/stdc++.h>
# define f(i,a,b) for (int i = a; i <= b; i++)
# define _f(i,a,b) for (int i = a; i >= b; i--)
# define ls(x) x << 1
# define rs(x) x << 1 | 1
using namespace std;
const int NR = 1e5 + 10;
struct Node {
    int mn, mx;
} tree[NR * 4 + 1];

void pushup(int rt) {
    tree[rt].mn = min(tree[ls(rt)].mn, tree[rs(rt)].mn);
    tree[rt].mx = max(tree[ls(rt)].mx, tree[rs(rt)].mx);
}

void upd(int rt, int l, int r, int x, int MN, int MX) {
    if (l == r) {
        tree[rt].mn = MN, tree[rt].mx = MX;
        return ;
    }
    int mid = (l + r) >> 1;
    if (x <= mid) upd(ls(rt), l, mid, x, MN, MX);
    else upd(rs(rt), mid + 1, r, x, MN, MX);
    pushup(rt);
}

int qmin(int rt, int l, int r, int qL, int qR) {
    if (qL <= l && r <= qR) return tree[rt].mn;
    int mid = (l + r) >> 1, ans = 2e9;
    if (qL <= mid) ans = min(ans, qmin(ls(rt), l, mid, qL,qR));
    if (mid < qR) ans = min(ans, qmin(rs(rt), mid + 1, r, qL, qR));
    return ans;
}

int qmax(int rt, int l, int r,int qL, int qR) {
    if (qL <= l && r <= qR) return tree[rt].mx;
    int mid = (l + r) >> 1, ans = -2e9;
    if (qL <= mid) ans = max(ans, qmax(ls(rt), l, mid, qL,qR));
    if (mid < qR) ans = max(ans, qmax(rs(rt), mid + 1, r, qL, qR));
    return ans;
}

int n, d;
int a[NR + 1];
int now = 0;
multiset <int> b[NR + 1];
int duan[NR + 1];

int main() {
    scanf("%d%d", &n, &d);
    f(i,1,n) scanf("%d", a + i);
    upd(1, 1, n, 1, a[1], a[1]);
    now = 1;
    b[1].insert(a[1]);
    duan[1] = 1;
    f(i,2,n) {
        int l = 1, r = now, mid, ans1 = now + 1, ans2 = now + 1, mn, mx;
        while (l <= r) {
            mid = (l + r) >> 1;
            mn = qmin(1, 1, n, mid, now);
            mx = qmax(1, 1, n, mid, now);
            if (a[i] - mn > d || mx - a[i] > d) l = mid + 1;
            else ans1 = min(ans1, mid), r = mid - 1;
        }
        if (ans1 == now + 1) {
            b[++now].insert(a[i]);
            duan[i] = now;
            upd(1, 1, n, now, a[i], a[i]);
            continue;
        }
        l = ans1, r = now;
        while (l <= r) {
            mid = (l + r) >> 1;
            mx = qmax(1, 1, n, ans1, mid);
            if (a[i] >= mx) l = mid + 1;
            else ans2 = min(ans2, mid), r = mid - 1;
        }
        if (ans2 == now + 1) {
            b[++now].insert(a[i]);
            duan[i] = now;
            upd(1, 1, n, now, a[i], a[i]);
            continue;
        }
        if (a[i] < *b[ans2].begin()) upd(1, 1, n, ans2, a[i], *b[ans2].rbegin());
        b[ans2].insert(a[i]);
    }
    f(i,1,now) {
        for(multiset<int>::iterator it = b[i].begin(); it != b[i].end(); it++) {
            printf("%d\n", *it);
        }
    }
    puts("");
    return 0;
}

```

------

# 6 字典

```python

```