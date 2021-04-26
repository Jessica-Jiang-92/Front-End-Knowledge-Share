解法一：
```
function fn(n) {
  let num1 = 1,
    num2 = 1,
    num3 = 0;
  for (let i = 0; i < n - 2; i++) {
    num3 = num1 + num2;
    num1 = num2;
    num2 = num3;
  }
  return num3;
}

// fn(7);   // 13
```

解法二：
递归：这种计算方式简洁并且直观，但是由于存在大量的重复计算，实际运行效率很低，并且会占用较多的内存。
```
function fn(n) {
  if (n <= 2) {
    return 1;
  }
  return fn(n - 1) + fn(n - 2);
}

// fn(7);   // 13
```
解法三：
memoization方案在《JavaScript模式》和《JavaScript设计模式》中都有提到过。memoization是一种将函数执行结果用变量缓存起来的方法。当函数进行计算之前，先看缓存对象是否有次计算结果，如果有，就直接从缓存对象中获取结果；如果没有，就进行计算，并将结果保存到缓存对象中。(闭包方式实现)
```
let fibonacci = (function() {
  let memory = []
  return function(n) {
      if(memory[n] !== undefined) {
        return memory[n]
    }
    return memory[n] = (n === 0 || n === 1) ? n : fibonacci(n-1) + fibonacci(n-2)
  }
})()

// fn(7);   // 13
```
这个时候有一个问题就是，我们使用的数组来存储，如果把数组换成对象呢？答案是速度会快很多。是因为比如说我们调用fibonacci(100)的时候，<code>fibonacci</code>函数在第一次计算的时候会设置<code>memory[100]=xxx</code>，此时数组的长度为101，而前面100项都会初始化为undefined。所以memory类型为数组时比类型为对象时要慢。

