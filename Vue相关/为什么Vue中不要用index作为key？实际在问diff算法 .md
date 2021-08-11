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
    { tag: 'li', children: [ { vnode: { text: '1' } } ] }, // 1
    { tag: 'li', children: [ { vnode: { text: '2' } } ] }, // 2
],
}
```
假设更新后，我们把子节点的顺序调换一下：

```
{
  tag: 'ul',
  children: [
    { tag: 'li', children: [ { vnode: { text: '2' } } ] }, // 2
    { tag: 'li', children: [ { vnode: { text: '1' } } ] }, // 1
  ],
}
```
那么在对象变化后，我们的算法是如何识别的呢？首先响应式数据更新后，触发了渲染`Watcher`的回调函数`vm._update(vm._render())`去驱动视图更新，`vm._render()`其实生成的就是`vnode`， 而`vm._update`就会带着新的`vnode`去走触发`__patch__`的过程。

现在我们来对比`ul`这个`vnode`的`patch`过程。对比一下新旧节点是否是相同类型的节点。

### （1）不是相同节点

`isSameNode`为false的话，直接销毁旧的`vnode`，渲染新的`vnode`。这也就是为什么`diff`算法是同层对比的原因。

### （2）是相同节点

这种情况下，节点相同，那就要尽可能的做节点的复用，会调用`src/core/vdom/patch.js`下的`patchVNode`方法。

- 如果新 vnode 是文字 vnode

  就直接调用浏览器的`dom api`把节点的直接替换掉文字内容就好。

- 如果新 vnode 不是文字 vnode
  
  那么就要开始对子节点`children`进行对比了。（可以类比 ul 中的 li 子元素）。

- 如果有新 children 而没有旧 children

  说明是新增`children`，直接 `addVnodes` 添加新子节点。
  
- 如果有旧 children 而没有新 children

  说明是删除 `children`，直接 `removeVnodes` 删除旧子节点

- 如果新旧 children 都存在

  那么就是我们 diff算法 想要考察的最核心的点了，也就是新旧节点的 diff 过程。可以大致看下这个函数。





