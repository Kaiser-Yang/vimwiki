注意：代码将会在后续开放提交后提供。

# [Two Fridges](https://csacademy.com/ieeextreme-practice/task/two-fridges)
由于题目中的温度范围非常小，我们只需要从小到大枚举温度，对于每个枚举，检查是否所有区间都被覆盖。
第一个覆盖所有区间的温度对即是答案。如果找不到输出 $$ -1 $$ 即可。

代码：[two_fridges.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/two_fridges.cpp)

# [Star Road]()
Not finished yet.

代码：

# [Increasing table](https://csacademy.com/ieeextreme-practice/task/increasing-table)
考虑到当第一行的元素确定后，第二行的元素也就确定了，因此我们只需要计算第一行的方案数即可。
对于输入，我们可以维护一个序列，序列中的元素表示第一行可以填入的数，以及这个数是否一定要填入到第一行，
序列按照可以填入的数从小到大排序。
现在我们需要计算从这个序列中选出 $$ N $$ 个数的方案数。
如果我们不考虑每一列是否满足递增关系，那么我们可以很轻松的用动态规划解决这个问题。
具体的，我们用 $$ dp[i][j][k] $$ 表示前 $$ i $$ 个数中选出 $$ j $$ 个数的方案数，
其中 $$ k $$ 表示第 $$ i $$ 个是否被选择，那么转移方程为：

$$
\begin{aligned}
dp[i][j][0] & = \begin{cases}
0，\text{当前必选} \\
dp[i - 1][j][0] + dp[i - 1][j][1]，\text{其他}
\end{cases} \\
dp[i][j][1] & = dp[i - 1][j - 1][0] + dp[i - 1][j - 1][1]
\end{aligned}
$$

我们现在考虑必须要让列也递增的情况，我们来依次来考虑第一行的每个数最大可能的取值：
* 对于第一个数，其只能是 $$ 1 $$。
* 对于第二个数，其最大是 $$ 3 $$，因为当第二个数是 $$ 4 $$ 的时候，第二行一定得出现 $$ 2，3 $$ 此时
第二列一定不可能递增。
* 对于第三个数，其最大是 $$ 5 $$，因为当第三个数是 $$ 6 $$ 的时候，第二行一定得出现 $$3，4，5 $$ (或者更小的数字)
此时第三列一定不可能递增。
* ……
* 对于第 $$ i $$ 个数，其最大是 $$ 2i - 1 $$。

不难发现，对于一个方案中的第 $$ i $$ 个数，若其均小于等于 $$ 2i - 1 $$，那么方案一定满足列是递增的。

因此，我们只需要对上面的转移方程增加一个限制条件即可：

$$
\begin{aligned}
dp[i][j][0] & = \begin{cases}
0，\text{当前必选} \\
dp[i - 1][j][0] + dp[i - 1][j][1]，\text{其他}
\end{cases} \\
dp[i][j][1] & = \begin{cases}
dp[i - 1][j - 1][0] + dp[i - 1][j - 1][1]，\text{当前小于等于} 2i - 1 \\
0 & \text{其他}
\end{cases}
\end{aligned}
$$

代码：[increasing_table.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/increasing_table.cpp)

# [Bounded tuple]()
Not finished yet.

代码：

# [Laser Defense](https://csacademy.com/ieeextreme-practice/task/laser-defense)
我们将激光分成四种：
* $$ a_u $$：$$ a_u[i] $$ 表示 $$ A $$ 点发出的第 $$ i $$ 条与上边界的交点座标。
* $$ a_r $$：$$ a_r[i] $$ 表示 $$ A $$ 点发出的第 $$ i $$ 条与右边界的交点座标。
* $$ b_u $$：$$ b_u[i] $$ 表示 $$ B $$ 点发出的第 $$ i $$ 条与上边界的交点座标。
* $$ b_l $$：$$ b_l[i] $$ 表示 $$ B $$ 点发出的第 $$ i $$ 条与左边界的交点座标。

我们分别对上面四种激光进行排序。

我们首先考虑只有 $$ A $$ 点激光的情况，此时被分成了 $$ len(a_u) + len(a_r) + 1 $$ 个区域。

接着对于某个 $$ B $$ 点发出的激光，如果其到达左边界，
那么此时区域个数会增加 $$ len(a_u) + len(a_r) + 1 $$；如果其到达上边界，
此时我们可以用二分查找在 $$ a_u $$ 中找到有多少个激光出现在其左边，不妨设为 $$ x $$，
那么此时区域个数会增加 $$ len(a_u) + len(a_r) + 1 - x $$。

代码：[laser_defense.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/laser_defense.cpp)

# [Another Sliding Window Problem](https://csacademy.com/ieeextreme-practice/task/another-sliding-window-problem)
首先我们不难发现要获得一个序列的 `optimal cost`，如果序列有偶数个元素，那么最大的要和最小的配对，
第二大的要和第二小的配对，以此类推；如果序列有奇数个元素，那么最大的元素要单独拎出来，
而其余元素按照序列有偶数个元素的情况处理。

有了上面的结论，我们可以有以下推论：
> 如果一个序列的 `optimal cost` 小于等于 $$ x $$，
那么删除最大的元素后的序列的 `optimal cost` 一定小于等于 $$ x $$。

证明如下：
> 如果一开始序列有奇数个元素，那么删除最大的元素后，序列有偶数个元素，
那么 `optimal cost` 的取值集合少了最后一个元素，因此 `optimal cost` 不会增加。
> 如果一开始序列有偶数个元素，那么删除最大的元素后，序列有奇数个元素，
那么 `optimal cost` 的取值集合中某一个元素变小了，因此 `optimal cost` 不会增加。

利用上面的推论，当我们计算出一个满足 `optimal cost` 小于等于 $$ x $$ 的区间 $$ [l, r] $$ 后，
那么 $$ [l, r - 1], [l, r - 2], \cdots, [l, l] $$ 一定也满足 `optimal cost` 小于等于 $$ x $$，
如果我们用 $$ s[i] $$ 表示 $$ [1, i] $$ 的前缀和，
那么这些区间的对答案的贡献为：$$ s[r] - s[l - 1] - (r - l + 1) \times a[l] $$。

接下来我们考虑对于一个给定的 $$ l $$，
如何快速求出最大的 $$ r $$ 使得 $$ [l, r] $$ 满足 `optimal cost` 小于等于 $$ x $$。

我们还是需要利用 `optimal cost` 的配对性质，首先我们先找到最后一个小于等于 $$ x $$ 的元素的位置，
不妨设为 $$ r $$，初始 $$ l = r $$，那么对于当前的区间 $$ [l, r] $$ 中的 $$ r $$ 一定是满足
`optimal cost` 小于等于 $$ x $$ 最大的。接下来我们考虑如何求解 $$ l - 1 $$ 对应的最大的 $$ r' $$。
实际上 $$ r' $$ 只有可能是 $$ \{r - 1, r, r + 1\} $$ 中的一个。这是因为：
* 如果 $$ [l, r] $$ 的长度是偶数，那么 $$ [l - 1, r] $$ 的长度是奇数，此时 $$ [l - 1, r] $$ 一定是满足
`optimal cost` 小于等于 $$ x $$ 的，因为此时可以让 $$ a[l - 1] $$ 单独一组；同时按照匹配规则，
如果 $$ a[l - 1] + a[r + 1] \le x $$ 那么 $$ [l - 1, r + 1] $$ 也一定是满足
`optimal cost` 小于等于 $$ x $$ 的。而对于 $$ [l - 1, r + 2] $$ 一定是不满足的，因为如果该区间满足，
那么 $$ [l - 1, r + 1] $$ 也一定满足，而 $$ [l - 1, r + 1] $$ 长度是偶数，此时我们把 $$ a[l - 1] $$
删除掉，那么 $$ [l, r + 1] $$ 一定满足，这与 $$ r $$ 是最大的矛盾。
所以当前情况下 $$ r' $$ 只有可能是 $$ \{r, r + 1\} $$ 中的一个。
* 如果 $$ [l, r] $$ 的长度是奇数，那么 $$ [l - 1, r] $$ 的长度是偶数，
此时如果 $$ a[l - 1] + a[r] \le x $$，那么 $$ [l - 1, r] $$ 一定是满足
`optimal cost` 小于等于 $$ x $$ 的，进一步的有如果 $$ a[r + 1] \le x $$ 成立，
那么 $$ [l - 1, r + 1] $$ 也一定是满足 `optimal cost` 小于等于 $$ x $$ 的。
而对于 $$ [l - 1, r + 2] $$ 一定是不满足的，因为如果该区间满足，而 $$ [l - 1, r + 2] $$ 长度是偶数，
此时我们把 $$ a[l - 1], a[r + 2] $$ 同时删除掉，那么 $$ [l, r + 1] $$ 一定满足，
这与 $$ r $$ 是最大的矛盾。而如果一开始就不满足 $$ a[l - 1] + a[r] \le x $$，那么由于 $$ [l, r] $$
区间长度是奇数，我们可以删除 $$ a[r] $$，而增加 $$ a[l - 1] $$，由于 $$ [l, r] $$ 满足，
我们用一个更小的数字取替换了一个最大的数字形成的 $$ [l - 1, r - 1] $$ 一定满足。
所以当前情况下 $$ r' $$ 只有可能是 $$ \{r - 1, r, r + 1\} $$ 中的一个。

因此我们只需要按照上面的过程，每次减少 $$ l $$ 后，计算出新的 $$ r $$ 对答案进行统计即可。

代码：[another_sliding_window_problem.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/another_sliding_window_problem.cpp)

# [IEEE754 Emulator]()
Not finished yet.

代码：

# [Triumvirates]()
Not finished yet.

代码：

# [Stick](https://csacademy.com/ieeextreme-practice/task/stick)
除去第一个正方形外，每增加一个正方形，所增加的面积是一个定值，
其增加值为单个正方形的面积减去两个正方形的公共部分的面积。因此最终答案为 $$ 4NL^2 - (N-1)S $$。
其中 $$ S $$ 为两个连续正方形相交部分的面积。

如果直接使用上面的公式，那么乘法的时候可能会溢出，因此我们可以将上面的公式进行变形成 $$ N(4L^2 - S) + S $$。并且最后得使用 `unsigned long long` 才行。当然 `Life is short; you need Python`。

代码：[stick.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/stick.cpp)

# [Increasing-decreasing permutations]()
Not finished yet.

代码：

# [Cheap Construction]()

代码：

# [Disparate Date Sets]()
Not finished yet.

代码：

# [Queries]()
Not finished yet.

代码：

# [Doubled Sequence]()
Not finished yet.

代码：

# [Icarus](https://csacademy.com/ieeextreme-practice/task/icarus)
我们记 $$ l_c $$ 表示 $$ S $$ 中 `L` 的出现次数，$$ r_c $$ 表示 $$ S $$ 中 `R` 的出现次数，$$ u_c $$
表示 $$ S $$ 中 `U` 的出现次数。

我们总可以通过构建一条链使其满足题目条件。这里我们以 $$ l_c \le u_c $$ 为例，
其余的情况可以进行类似的讨论。

当 $$ l_c \le u_c $$ 时，我们可以构建一条有 $$ u_c + 2 $$ 个结点的链，
链上的结点均以左结点的方式进行连接。这样构造后，输入中的 `R` 将可以被忽略，
同时我们可以在这个链上找到一个结点，
其满足从当前结点触发的第一轮操作中所有的 `L` 和 `U` 都是合法移动且最深的结点不会被访问到。

如何找到这样的点呢？我们可以先假设我们在一个两端无限长的链上的某个结点，初始化 $$ depth $$ 为 $$ 0 $$，
我们遍历输入中的移动操作，对于 `L` 我们将 $$ depth $$ 减一，对于 `U` 我们将 $$ depth $$ 加一，记录这个
过程中最小的 $$ depth $$，不妨记为 $$ high $$，如果我们将链上的结点按照深度从低到高进行编号，
那么我们从编号为 $$ -high + 1 $$ 的结点出发，后续的所有操作一定不能访问到编号为 $$ u_c + 2 $$ 的结点。

这是因为我们从编号为 $$ -high + 1 $$ 的点出发的话，第一轮操作肯定都是合法的，
且不会到达编号为 $$ u_c + 2 $$ 的结点；
对于第二轮操作，我们的起点一定是编号 $$ -high + 1 $$ 的结点或者其祖先。

如果第二轮起点是编号为 $$ -high + 1 $$ 的结点，那么此后续操作便开始循环，不会到达 $$ u_c + 2 $$；

如果第二轮起点是编号为 $$ -high + 1 $$ 的结点的祖先，那么第二轮可能会有一些 `U` 操作失效，
实际上第二轮的起点与第一轮的起点的深度差的绝对值 (不妨记为 $$ x $$) 就是 `U` 操作最多失效的次数，
下面来解释为什么第二轮的 `U` 操作最多失效 $$ x $$ 次。如果有多于 $$ x $$ 次的 `U` 操作失效，
不妨记为 $$ x' $$，此时我们假设链变成无限长，
那么这 $$ x' $$ 次失效的 `U` 操作将会使到达的最低深度减少 $$ x' $$，
这意味着我们的起点深度较上一轮应该减少 $$ x' $$，这就出现了矛盾。

这个结论意味着，当 `U` 的失效次数达到最大值时，我们第二轮的终点也不过是回到了第一轮的起点，
且这个过程中到达的最大深度也不过是第一轮到达的最大深度；而其余情况下，
我们的终点深度一定小于第一轮的起点深度，到达的最大深度也一定小于第一轮到达的最大深度。
继续这样推下去，我们便可以发现之后每一轮都不会到达 $$ u_c + 2 $$。

这里还有一点需要注意的是，如果 $$ l_c = 0 $$，而 $$ u_c = 1 $$，
此时按照上面的方法会构造出 $$ 3 $$ 个结点的树，这一点不满足题目的限制条件。
因此我们需要对这一部分进行特判。这里我们只需要构造一个有 $$ 2 $$ 个结点的树，$$ 2 $$ 为 $$ 1 $$ 的左结点，
那么从 $$ 1 $$ 号结点出发永远不会到达 $$ 2 $$ 号结点。
当然我们可以在上面的思路中选择构造结点个数为 $$ 2 (l_c + u_c + r_c) $$ 的链，这样也可以解决这一部分。

其余的情况可以进行类似的讨论。

代码：[icarus.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/icarus.cpp)

# [Power of three](https://csacademy.com/ieeextreme-practice/task/power-of-three/statement/)
设 $$ M $$ 表示输入 $$ N $$ 的位数，我们计算出 $$ x_{min} = (M - 1) \lfloor \log_3 10 \rfloor $$，
不难发现如果有解 $$ x $$，那么 $$ x \ge x_{min} $$ 时，且在理论上 $$ x - x_{min} \le 3 $$，
这是因为 $$ 3^3 = 27 $$，也就是乘以 $$ 3 $$ 个 $$ 3 $$ 之后，位数会增加 $$ 1 $$。

同时对于解 $$ x $$ 我们有 $$ 3^x \equiv N \pmod{P} $$，其中 $$ P $$ 是任意的数，
因此我们可以提前确定多个质数，从 $$ x = x_{min} $$ 开始，
然后检查是否对于这些质数都有 $$ 3^x \equiv N \pmod{P} $$，如果成立，
我们可以大概率认为这个 $$ x $$ 是答案。如果 $$ x $$ 在 $$ 3 $$ 次递增的过程中没有找到答案，
那么我们输出 $$ -1 $$ 即可。

代码：[power_of_three.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/power_of_three.cpp)

# [Halving]()
Not finished yet.

代码：

# [King's Order](https://csacademy.com/ieeextreme-practice/task/kings-order)
直接使用拓扑排序即可，只是在拓扑排序中需要将普通队列替换成优先队列。
优先队列的比较器设置为题目要求即可。

代码：[kings_order.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/kings_order.cpp)

# [Balls]()
Not finished yet.

代码：

# [Corporation]()
Not finished yet.

代码：

# [This is not an optimization problem]()
Not finished yet.

代码：

# [Digits swap]()
Not finished yet.

代码：

# [Brick stacks]()

代码：

# [Stones]()
Not finished yet.

代码：

# [Rectangles and arrays](https://csacademy.com/ieeextreme-practice/task/rectangles-and-arrays-ieeextreme-18)
我们首先考虑不进行修改的情况，那么我们可以使用单调栈来解决这个问题，具体的，我们需要计算出：
* $$ l1[i] $$：表示第 $$ i $$ 个元素左侧第一个比它小的元素的位置，如果不存在则设置为最小下标减一。
* $$ r1[i] $$：表示第 $$ i $$ 个元素右侧第一个比它小的元素的位置，如果不存在则设置为最大下标加一。

那么统计不进行修改的部分就是 $$ \max\limits_{1 \le i \le N}(r1[i] - l1[i] - 1)A_i $$，
这里公式表示以 $$ A_i $$ 为最小值所能形成的最大的正方形。

接下来我们考虑需要修改的情况，首先如果要修改某个元素，
我们直接将其修改为 $$ X $$ 一定会比修改成一个比 $$ X $$ 小的数更优。
这意味着对于 $$ A_i \ge X $$ 的元素，我们没有必要修改，
所有后面的修改将会针对满足 $$ A_i \lt X $$ 的元素。

接下来我们考虑如果将 $$ A_i $$ 修改成 $$ X $$，会对哪些部分产生影响。
显然对于 $$ l1[j] = i, j \gt i $$ 和 $$ r1[j] = i, j \lt i$$ 的位置 $$ j $$，
其 $$ l1[j], r1[j] $$ 可能会发生变化。
对于 $$ i $$ 位置，其 $$ l1[i], r1[i] $$ 也可能会发生变化。

因此，
我们只需要计算出修改 $$ A_i $$ 为 $$ X $$ 后的新的 $$ l1_i, r1_i $$ 即可按照之前的公式进行更新。

实际上对于非 $$ i $$ 位置的变化，显然 $$ l1, l2 $$ 会变成左右两侧第二个比它小的元素的位置。
为了避免混淆，我们增加以下定义：
* $$ l2_i $$：表示第 $$ i $$ 个元素左侧第二个比它小的元素的位置，如果不存在则设置为最小下标减一。
* $$ r2_i $$：表示第 $$ i $$ 个元素右侧第二个比它小的元素的位置，如果不存在则设置为最大下标加一。
* $$ l3_i $$: 表示将第 $$ i $$ 个元素修改为 $$ X $$ 后左侧第一个比它小的元素的位置，如果不存在则设置为最小下标减一。
* $$ r3_i $$: 表示将第 $$ i $$ 个元素修改为 $$ X $$ 后右侧第一个比它小的元素的位置，如果不存在则设置为最大下标加一。

而修改 $$ i $$ 位置对 $$ j $$ 造成影响实际上就是在修改 $$ j $$ 位置左侧或者右侧第一个比它小的元素。

因此这一部分的贡献就是 $$ \max\limits_{1 \le i \le N}\{(r2[i] - l1[i] - 1)A_i, (r1[i] - l2[i] - 1)A_i, (r3[i] - l3[i] - 1)X\} $$。

上面使用 $$ r2[i] - l1[i] - 1 $$ 和 $$ r1[i] - l2[i] - 1$$ 而不直接使用 $$ r2[i] - l2[i] - 1 $$ 是因为我们只能修改一个元素，
而不能同时将左右两个第一个比当前小的元素修改为 $$ X $$。

下面我们来介绍如何计算 $$ l2, r2, l3, r3 $$。

这里以计算 $$ l2 $$ 为例介绍如何计算 $$ l2, r2 $$。计算这一部分，我们需要用到三个栈，
第一个栈与在计算 $$ l1 $$ 时作用一样，而第二个栈用于保存从第一个栈中弹出的元素，由于栈是先进后出的，
第三个栈用于将从第一个栈中弹出的元素逆序放入到第二个栈中。
当然也可以将第二个栈换成队列，每次从队首取元素即可。这样操作后当从第二个栈弹出元素的时候，
也就找到了左侧第二个比栈顶元素小的元素。

```c++
for (int i = n; i >= 1; i--) {
    while (!s2.empty() && a[s2.top()] > a[i]) {
        l2[s2.top()] = i;
        s2.pop();
    }
    while (!s1.empty() && a[s1.top()] >= a[i]) {
        s3.push(s1.top());
        s1.pop();
    }
    s1.push(i);
    while (!s3.empty()) {
        s2.push(s3.top());
        s3.pop();
    }
}
while (!s1.empty()) {
    l2[s1.top()] = 0;
    s1.pop();
}
while (!s2.empty()) {
    l2[s2.top()] = 0;
    s2.pop();
}
```

计算完成后不要忘记排除 $$ X < A_i $$ 的情况：

```c++
for (int i = 1; i <= n; i++) {
    if (x < a[i]) {
        l2[i] = l1[i];
    }
}
```

这里以计算 $$ l3 $$ 为例介绍如何计算 $$ l3, r3 $$。计算 $$ l3 $$ 与计算 $$ l1 $$ 类似。
只是我们需要比较的值从 $$ A_i $$ 变成了 $$ X $$，但是这样会导致后续计算的时候有一些元素被提前弹出了栈，
实际上，我们可以先将弹出的元素放到一个队列里面，等到计算完 $$ l3[i] $$ 后再将这些元素放回栈中：

```c++
for (int i = 1; i <= n; i++) {
    queue<int> now_pop;
    while (!s.empty() && a[s.top()] >= x) {
        now_pop.push(s.top());
        s.pop();
    }
    l3[i] = (s.empty() ? 0 : s.top());
    while (!now_pop.empty()) {
        s.push(now_pop.front());
        now_pop.pop();
    }
    while (!s.empty() && a[s.top()] >= a[i]) { s.pop(); }
    s.push(i);
}
```

代码：[rectangles_and_arrays.cpp](https://github.com/Kaiser-Yang/OJProblems/blob/main/IEEExtreme/18/rectangles_and_arrays.cpp)

# [Invertible Pairs]()

代码：

# [Sierpinski]()
Not finished yet.

代码：
