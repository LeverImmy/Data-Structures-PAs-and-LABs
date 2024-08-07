## Risk 解题报告

### 算法原理

使用单调队列来维护当前区间确诊病例的最大值。

每次入队前，我们要检查队中所有数据是不是都在 $i - m_i$ 这一天及以后，否则需要将它们出队。在每次入队时，我们 **从队尾开始不断弹出比当前想要入队的元素小的元素**，这样就能保证这个队列是 **单调递减** 的。这些从队尾出队的元素不会对当前区间最大值有贡献，因为此时新入队的元素一定比它们大，所以区间最大值也只会是这个新入队的元素。由于这个队列是单调递减的，所以队首即为当前区间最大值。

得到每一次统计确诊病例数最大值数组 $k$ 之后，对 $k$ 进行排序，这样对于每次询问，就能使用二分查找来查询满足 $k_i$ 位于区间 $[0, p)$ 和 $[p, q)$ 中的 $i$ 的个数。

### 细节处理

#### 使用 `unsigned` 还是 `long long`

由于 $p, q, m_i$ 的可能范围超过了 `int` 能表示的 $2^{31} - 1$，所以我使用 `long long` 类型来存储它们。理论上使用 `unsigned` 就足够了，但是由于潜在的减法导致得到负数的风险，我还是使用了更加稳妥的 `long long`。

#### 二分区间怎么写

对于每次询问 $p, q$，我们需要使用二分查找实现这样的功能：在 $k$ 数组中，找到满足第一个 $k_i \ge v$ 的 $i$。第一次查询 $v \gets p$，第二次查询 $v \gets q$。

在我的程序中，合法的答案区间为 $[l, r)$，所以我在二分初始时设置 $l \gets 0, r \gets n$。

如果中点 $m = \dfrac{l + r}{2}$ 是非法的，那么答案区间应改为 $(m, r]$，即 $l \gets m$；如果是合法的，那么答案区间应改为 $(l, m]$，即 $r \gets m$。

当 $l = r - 1$ 时答案区间仅有 $r$ 一个数，即为答案。另外，如果不存在这样的 $i$，那么返回 $r + 1$ 作为标志。

### 时空复杂度分析

#### 时间复杂度

单调队列部分，由于每个元素最多只会进一次队、出一次队，一共耗时 $O(n)$，所以每次操作是均摊 $O(1)$ 的。

对 $k$ 数组的排序的时间复杂度为 $O(n\log n)$，单次询问使用二分查找的时间复杂度为 $O(\log n)$。

因此整体的时间复杂度为 $O((n + T)\log n)$。

#### 空间复杂度

由于整个程序只使用了三个长度为 $O(n)$ 的数组，因此空间复杂度为 $O(n)$。
