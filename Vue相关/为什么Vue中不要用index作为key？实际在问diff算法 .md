## 参考文章
[1. 为什么Vue中不要用index作为key？](https://juejin.cn/post/6844904113587634184)
[2. ]()

## 前言
可以试想一下：在Vue开发中，我们的key是拿来做什么的？为什么不推荐用index作为key呢？接下来就将为你揭秘key的作用。

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










