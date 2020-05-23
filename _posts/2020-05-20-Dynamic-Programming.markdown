---
layout: post
title: "算法之动态规划（Dynamic Programming）"
date: 2020-05-12 14:14:14 +0800
categories: Algorithm
tag: [algorithm, programming]
---

## 动态规划 Dynamic Programming

是通过把问题局部化最终获取整体解的方法。

定义：Dynamic Programming 动态规划是指通过将一个复杂问题分解成一组简单子问题并对每个子问题进行一次求解并存储其结果，理想状态是存储在基于内存的存储结构中，最终解决复杂问题本身的一种解决问题的方法。

<!--more-->

DP 跟递归（Recursion）算法的关系密切，可以说是对递归算法的优化解法，降低了通过递归算法带来的额外数据空间的开销以及时间开销。因此，如果要掌握 DP，就首先要对递归算法有清楚的了解和掌握。递归分为头递归与尾递归: 先递归，再运算称为头递归，反之为尾递归。

DP 分为自顶向下（Memo）和自底向上两种解决思路。

## DP 的两个关键点：

1. 子结构优化 Optimal Substructure --- 贪心算法
2. 重复子问题 Overlapping Subproblems ---

## 自顶向下

通过对递归模型的进一步优化，如对中间结果的记忆存储，得出新的优化算法即为：备忘录法（Memoization）

关于备忘录 Memoization，维基百科是这样描述的：

> memoization 最初是用来优化计算机程序使之计算的更快的技术，是通过存储调用函数的结果并且在同样参数传进来的时候返回结果。大部分应该是在递归函数中使用。memoization 这个词是在 1968 年被 Donald Michie 创造出来的，它源于拉丁语 memoradum，在英语中通常简写为 memo，因此就有了将一个函数的返回结果暂存入某个变量中的意思。

- Memoization = 递归调用 + 中间结果缓存 - 重复子问题计算

## DP 的适用范围

可以进行字结构优化以及有重复子问题的都可以用 DP 的方式来解决

1. 是否可以将问题分解成同类型的子问题？ 如果是则可以首先通过递归算法求出一般解
2. 子问题是否有重叠？如果是则可以通过 Cache 进行空间换时间，提升效率
3. 是否需要进行优化，最大化或最小化或者需要统计可能的各种情况？

## DP 的短板

为了解决主问题，需要计算全部子问题，因而会带来效率上的损失，甚至有的时候会造成性能低于递归算法。
如杨辉三角的解法：
1
1 1
1 2 1
1 3 3 1
1 4 6 4 1
1 5 10 10 5 1

## DP 解题的思路

1. 检查是否可用 DP 解决。即是否满足上面适用范围提到的 1 2 两点。
2. 定义递归结构。有同结构的子问题即意味着可以用递归来解决。
   1. 用子问题重新定义问题：自顶向下定义，此时不用考虑时间复杂度。
   2. 解决最基本的情况，其他交给递归。子问题通过递归来解决，剩下的就是基本情况（case）
   3. 添加终结条件。该步骤相对不太重要。
3. 试着引入 Memo(可选步骤)：如果某个子问题被调用了多次，那么就应该将其结果缓存，再次调用的时候就返回缓存。
4. 试着自底向上解决问题。这是我们试着消除递归并且重新定义并优化我们的最初解决方案的步骤，并且只存储后续步骤需要的中间结果。

## DP 实际应用

> 例题：鸡蛋和楼层的问题
> 假设你有 2 颗鸡蛋，现在有 100 层楼，希望测出在几层楼上将会摔碎，求最少的测试次数。
>
> 进一步：扩展到 M 颗鸡蛋和 N 层楼将会什么样的？

### 思路：

1.  如果只有 1 颗鸡蛋，那么只能从第一层开始扔起，直到破碎或 100 层
2.  如果是 2 颗鸡蛋，可以从第 x (1 < x < 100 ) 层扔起，
3.  如果破了则返回 1，如果没破，则从 x+1 到 N 层 继续测试。相当于对合计 N-x 层重新进行测试。

### 递归公式推导

根据上面的推导可以得出:
eggTest(M, N) = 1 + Max(eggTest(M-1, x-1),eggTest(M, N - x))

| 鸡蛋 | 1      | 2   | 3    | 4   | ... | M   |
| ---- | ------ | --- | ---- | --- | --- | --- |
| 楼层 |        |     | 次数 |
| 1    | 1      | 1   | 1    | 1   | ... | 1   | <见注 1 |
| 2    | 2      | 2   | ...  |
| 3    | 3      | 3   |
| 4    | 4      |
| ...  | ...    |
| N    | N      |
| 楼层 | 见注 2 |

注 1：如果只有 1 层楼，那么测试次数就是 1 次，无论鸡蛋有多少个
注 2：如果只有 1 颗鸡蛋，那么测试次数是楼层数，因为只能从第一层开始测起。

### 具体实现

首先根据计算条件和收敛原则给出递归算法，运算复杂度 O(3\*\*MN)：
下面是用 Typescript 实现的相关代码，

#### 递归方法实现

```typescript
function eggTestR(eggs: number, floors: number): number {
  //蛋和楼层应均为自然数
  if (eggs < 1 || floors < 1) {
    return 0;
  }
  //终结条件，见上注1和注2
  if (eggs < 2 || floors < 2) {
    return floors;
  }

  //假设对应M颗鸡蛋N层楼最少需要x次,那么 1<=x<=floors,
  //即只需要求出x的最小值即为测试所需最小次数
  let x = floors,
    tmp: number;
  for (let k = 1; k <= floors; k++) {
    tmp = 1 + Math.max(eggTestR(eggs - 1, k - 1), eggTestR(eggs, floors - k));
    if (x > tmp) {
      x = tmp;
    }
  }
  return x;
}
```

#### DP 方法实现

根据上述递归算法以及对应自底向上的表格推演得出 DP 算法：运算复杂度为 O（M\*N）

```typescript
function eggTest(eggs: number, floors: number): number {
  let matrix = new Array<Array<number>>();
  //matrix[i][j] : i:eggs, j:floors
  matrix[0] = new Array<number>();
  matrix[0][0] = 0;

  for (let i = 1; i <= eggs; i++) {
    matrix[i] = new Array<number>();
    matrix[i][0] = 0;
    matrix[i][1] = 1; // 见注1
  }
  for (let j = 1; j <= floors; j++) {
    matrix[0][j] = 0;
    matrix[1][j] = j; // 见注2
  }
  //首先对矩阵初始化，最大可能测试数即为楼层数
  for (let i = 2; i <= eggs; i++) {
    for (let j = 2; j <= floors; j++) {
      matrix[i][j] = j;
    }
  }
  //然后逐层求最少测试次数
  for (let i = 2; i <= eggs; i++) {
    for (let j = 1; j <= floors; j++) {
      for (let k = 1; k < j; k++) {
        matrix[i][j] = Math.min(
          matrix[i][j],
          1 + Math.max(matrix[i - 1][k - 1], matrix[i][j - k])
        );
      }
    }
  }
  //console.log(matrix);
  return matrix[eggs][floors];
}
```

#### 实测效率对比

经实际检测，用递归的方法运算速度比 DP 慢了太多, 下面是 2 颗鸡蛋 30 层楼的运算时间对比:
为啥不是用 100 层楼的？因为从开始运算到现在已经过去 20 分钟了，递归算法还在运行，没解出来...

```
..deno> deno run dp2.ts
Compile eggfloor.ts
recursive result: 8
recursive: 10344ms
dp result: 8
dp: 1ms
```

> PS：deno 1.0 正式版正式推出了，自带 typescript 解释器，运行 ts 程序太方便了。不再需要用 tsc 先翻译成 javascript 再用 node 来运行了。关于 deno 和 node 的“恩怨情仇”似乎可以再写一篇。
