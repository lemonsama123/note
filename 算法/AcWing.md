---
headingNumber: true
enableMacro: true
customVar: Hello
define:
    --APP_NAME--: Yank Note
    --APP_VERSION--: '[= $ctx.version =]'
---

[toc]{type: "ol", level: [2,3]}

# 算法基础课

## 基础算法

### 快速排序

[快速排序](https://www.acwing.com/problem/content/787/)

```cpp
#include <iostream>

using namespace std;

const int N = 10e6 + 10;
int n;
int q[N];

void quickSort(int q[], int l, int r) {
    if (l >= r) {
        return;
    }
    int i = l - 1;
    int j = r + 1;
    int x = q[(l + r) / 2];
    while (i < j) {
        while (q[++i] < x);
        while (q[--j] > x);
        if (i < j) {
            swap(q[i], q[j]);
        }
    }
    quickSort(q, l, j);
    quickSort(q, j + 1, r);
}

int main() {
    scanf("%d", &n);
    for (int i = 0; i < n; ++i) {
        scanf("%d", &q[i]);
    }
    quickSort(q, 0, n - 1);
    for (int i = 0; i < n; ++i) {
        printf("%d ", q[i]);
    }
    return 0;
}
```

[第 K 个数](https://www.acwing.com/problem/content/787/)

```cpp
#include <iostream>

using namespace std;

const int N = 10e5 + 10;

int n, k;
int q[N];

int quickSort(int l, int r, int k) {
    if (l == r) {
        return q[l];
    }
    int x = q[(l + r) >> 1];
    int i = l - 1;
    int j = r + 1;
    while (i < j) {
        while (q[++i] < x);
        while (q[--j] > x);
        if (i < j) {
            swap(q[i], q[j]);
        }
    }
    int sl = j - l + 1;
    if (k <= sl) {
        return quickSort(l, j, k);
    }
    return quickSort(j + 1, r, k - sl);
}

int main() {
    scanf("%d%d", &n, &k);
    for (int i = 0; i < n; ++i) {
        scanf("%d", q + i);
    }
    printf("%d", quickSort(0, n - 1, k));
    return 0;
}
```
