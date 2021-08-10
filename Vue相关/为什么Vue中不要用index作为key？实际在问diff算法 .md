## 参考文章
[1. 为什么Vue中不要用index作为key？](https://juejin.cn/post/6844904113587634184)

## 前言
可以试想一下：在Vue开发中，我们的key是拿来做什么的？为什么不推荐用index作为key呢？

其实我们应该常常会遇到循环使用html的情况，一旦我们使用`:key="index"`进行循环，你将会在控制台中看到vue给出的`warning`信息，这个时候往往我们只需要做一个小小的改动就能避免大量的提示信息，使用`:key="index + 'tab'"`。

接下来就将为你揭秘key的作用。

## 示例
有这样一段HTML代码：
```
<ul>
  <li>1</li>
  <li>2</li>
</ul>
```
我们知道在Vue中，都是先将节点转换为虚拟DOM，等到真正渲染的时候通过diff算法进行比对之后，才会成功渲染成我们需要的页面，而这个虚拟DOM是什么呢？其实就是JavaScript的一个对象。

那么上面示例的vdom节点大概就是下面这种形式：
```
{
  tag: 'ul',
  children: [
    { tag: 'li', children: [ { vnode: { text: '1' } } ] },
    { tag: 'li', children: [ { vnode: { text: '2' } } ] },
],
}
```
假设更新后，我们把子节点的顺序调换一下：

```
{
  tag: 'ul',
  children: [
    { tag: 'li', children: [ { vnode: { text: '1' } } ] },
    { tag: 'li', children: [ { vnode: { text: '2' } } ] },
],
}
```








