# 完全背包问题

## 1. 问题描述

有n件物品和1个容量为W的背包。每种物品没有上限，第i件物品的重量为wi，价值为为Vi，求解将哪些物品装入背包可以使得价值总和最大？

## 2. 问题分析

最简单的思路就是把完全背包问题拆分成0-1背包问题，就是把0-1背包问题中状态转移方程进行扩展，也就是说0-1背包只考虑放与不放进去两种情况，
而完全背包问题要考虑放0、放1、放2...的情况。

![1620801161](https://user-images.githubusercontent.com/82437559/117929060-f24e0900-b32e-11eb-8d30-100ddffb31e4.png)

这个k当然不是无限制的，它受背包的容量与单件物品的重量限制，即`j/w[i]`。假设我们只有1种商品，它的重量是20，背包的容量为60，那么他就应
该放3个，在遍历时，就0、1、2、3地一次尝试。

程序需要求解`n*W`个状态，每一个状态需要的时间为`O(W/wi)`，总的时间复杂度为：

![1620801200](https://user-images.githubusercontent.com/82437559/117929174-0f82d780-b32f-11eb-9cb1-664e0d5db54e.png)

我们再次回顾一下0-1背包问题经典解法的核心code:
```
for(var i = 0 ; i < n ; i++){ 
   for(var j=0; j<=W; j++){ 
       f[i][j] = Math.max(f[i-1][j], f[i-1][j-weights[i]]+values[i]))
      }
   }
}
```
现在多了一个k，就意味着多了一重循环。
```
for(var i = 0 ; i < n ; i++){ 
   for(var j=0; j<=W; j++){ 
       for(var k = 0; k < j / weights[i]; k++){
          f[i][j] = Math.max(f[i-1][j], f[i-1][j-k*weights[i]]+k*values[i]))
        }
      }
   }
}
```
所以完整的实现是：
```
completeKnapsack(weights, values, W) {
    const f = [];
    const n = weights.length;
    f[-1] = [];
    for (let i = 0; i <= W; i++) {
      f[-1][i] = 0;
    }
    for (let i = 0; i < n; i++) {
      f[i] = new Array(W + 1);
      for (let j = 0; j <= W; j++) {
        f[i][j] = 0;
        const bound = j / weights[i];
        for (let k = 0; k <= bound; k++) {
          f[i][j] = Math.max(f[i][j], f[i - 1][j - k * weights[i]] + k * values[i]);
        }
      }
    }
    return f[n - 1][W];
  }

调用：
var a = completeKnapsack([3,2,2],[5,10,20], 5)
console.log(a) //40
```

## 3. 如何优化？

我们转变一下思路，让`f(i,j)`表示出生在前i种物品中选取若干件物品放入容量为j的背包所得到的最大值。
所以，对于第i件物品有放或者不放两种情况，放的情况又分为放1件、2件、....j/wi件;
- 不放：此时`f(i, j) = f(i-1, j)`
- 放：那么当前背包中应该至少出现一件第i种物品，所以`f(i, j) = f(i, j-wi)+vi`，为什么会是这种结果呢？
因为我们要把当前物品放入包内，因为物品i可以无限使用，所以要用`f(i, j-wi)`，如果我们用的是：`f(i-1, j-wi)`，它表示的是，我们只有
一件当前物品i，所以我们在放入物品的时候需要考虑到第i-1个物品的价值`f(i-1, j-wi)`。但是现在，我们有无限件当前物品i，我们不用再考虑
第i-1个物品了，我们所要考虑的是再当前容量下是否再装入一个物品i，而`j-wi`的意思是指要确保`f(i, j)`至少有一件第i件物品，所以要预留`c(i)`
的空间来存放一件第i种物品。总而言之，如果放当前物品i的话，它的状态就是它自己`i`，而不是上一个物品`i-1`。

![1620800250](https://user-images.githubusercontent.com/82437559/117927491-db0e1c00-b32c-11eb-8f4b-0f9e3aafc886.png)

与0-1背包问题比较，只是一点点不同，我们也不需要三重循环了。

![1620800421](https://user-images.githubusercontent.com/82437559/117927744-37713b80-b32d-11eb-81b7-1ce9c02166d7.png)

完整的实现是：
```
completeKnapsack(weights, values, W) {
    var f = [],
        n = weights.length;
    f[-1] = []; //初始化边界
    for (let i = 0; i <= W; i++) {
        f[-1][i] = 0;
    }
    for (let i = 0; i < n; i++) {
        f[i] = [];
        for (let j = 0; j <= W; j++) {
            if (j < weights[i]) {
                f[i][j] = f[i - 1][j];
            } else {
                f[i][j] = Math.max(f[i - 1][j], f[i][j - weights[i]] + values[i]);
            }
        }
        console.log(f[i].concat());//调试
    }
    return f[n - 1][W];
}

调用：
const a = unboundedKnapsack([3, 2, 2], [5, 10, 20], 5); //输出40
console.log(a);
```


