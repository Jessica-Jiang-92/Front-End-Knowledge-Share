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

通过
```
// 旧首节点
  let oldStartIdx = 0
  // 新首节点
  let newStartIdx = 0
  // 旧尾节点
  let oldEndIdx = oldCh.length - 1
  // 新尾节点
  let newEndIdx = newCh.length - 1
```
这些变量分别指向旧节点的首尾、新节点的首尾。
根据这些指针，在一个 `while` 循环中不停的对新旧节点的两端的进行对比，然后把两端的指针向不断内部收缩，直到没有节点可以对比。在讲对比过程之前，要讲一个比较重要的函数：`sameVnode`：
```
function sameVnode (a, b) {
  return (
    a.key === b.key && (
      (
        a.tag === b.tag &&
        a.isComment === b.isComment &&
        isDef(a.data) === isDef(b.data) &&
        sameInputType(a, b)
      )
    )
  )
}
```
它是用来判断节点是否可用的关键函数，可以看到，判断是否是 `sameVnode`，传递给节点的 `key` 是关键。

然后我们接着进入`diff`过程，每一轮都是同样的对比，其中某一项命中了，就递归的进入 `patchVnode` 针对单个 `vnode` 进行的过程（如果这个 `vnode` 又有 `children`，那么还会来到这个 `diff children` 的过程 ）：

- 旧首节点和新首节点用 `sameNode` 对比
- 旧尾节点和新尾节点用 `sameNode` 对比
- 旧首节点和新尾节点用 `sameNode` 对比
- 旧尾节点和新首节点用 `sameNode` 对比
- 如果以上逻辑都匹配不到，再把所有旧子节点的 `key` 做一个映射到旧节点下标的 `key -> index` 表，然后用新 `vnode` 的 `key` 去找出在旧节点中可以复用的位置。

然后不停的把匹配到的指针向内部收缩，直到新旧节点有一端的指针相遇（说明这个端的节点都被patch过了）。在指针相遇以后，还有两种比较特殊的情况：

1. 有新节点需要加入。 如果更新完以后，`oldStartIdx > oldEndIdx`，说明旧节点都被 `patch` 完了，但是有可能还有新的节点没有被处理到。接着会去判断是否要新增子节点。
2. 有旧节点需要删除。 如果新节点先`patch`完了，那么此时会走 `newStartIdx > newEndIdx` 的逻辑，那么就会去删除多余的旧子节点。

## 为什么不要用index作为key

### 1. 节点reverse场景

假设我们有这样一段代码：
```
<div id="app">
      <ul>
        <item
          :key="index"
          v-for="(num, index) in nums"
          :num="num"
          :class="`item${num}`"
        ></item>
      </ul>
      <button @click="change">Change</button>
    </div>
    <script src="./vue.js"></script>
    <script>
      var vm = new Vue({
        name: "parent",
        el: "#app",
        data: {
          nums: [1, 2, 3]
        },
        methods: {
          change() {
            this.nums.reverse();
          }
        },
        components: {
          item: {
            props: ["num"],
            template: `
                    <div>
                       {{num}}
                    </div>
                `,
            name: "child"
          }
        }
      });
    </script>
```
组件很简单，是一个列表，渲染`1 2 3`三个数字。我们如果以index作为key，那么会有怎样的表现呢？

在首次渲染时，我们的虚拟节点列表`oldChildren`表示如下：
```
[
  {
    tag: "item",
    key: 0,
    props: {
      num: 1
    }
  },
  {
    tag: "item",
    key: 1,
    props: {
      num: 2
    }
  },
  {
    tag: "item",
    key: 2,
    props: {
      num: 3
    }
  }
];
```
点击了`Change`按钮之后，会对数组做`reverse`操作。此时生成的`newChildren`列表如下：
```
[
  {
    tag: "item",
    key: 0,
    props: {
+     num: 3
    }
  },
  {
    tag: "item",
    key: 1,
    props: {
+     num: 2
    }
  },
  {
    tag: "item",
    key: 2,
    props: {
+     num: 1
    }
  }
];
```
我们会发现：key的顺序没变，传入的值完全变了。这会导致一个什么问题？

本来按照最合理的逻辑来说，旧的第一个`vnode` 是应该直接完全复用 新的第三个`vnode`的，因为它们本来就应该是同一个`vnode`，自然所有的属性都是相同的。但是在进行子节点的 `diff` 过程中，会在 旧首节点和新首节点用`sameNode`对比。 这一步命中逻辑，因为现在新旧两次首部节点的 `key` 都是 `0`了，然后把旧的节点中的第一个 `vnode` 和 新的节点中的第一个 `vnode` 进行 `patchVnode` 操作。

这会发生什么呢？

首先，在进行 patchVnode 的时候，会去检查 props 有没有变更，如果有的话，会通过 `_props.num = 3` 这样的逻辑去更新这个响应式的值，触发 `dep.notify`，触发子组件视图的重新渲染等一套很重的逻辑。然后，还会额外的触发以下几个钩子，假设我们的组件上定义了一些dom的属性或者类名、样式、指令，那么都会被全量的更新。

- updateAttrs
- updateClass
- updateDOMListeners
- updateDOMProps
- updateStyle
- updateDirectives

而这些所有重量级的操作（虚拟dom发明的其中一个目的不就是为了减少真实dom的操作么？），都可以通过直接复用 第三个`vnode` 来避免，是因为我们偷懒写了 `index` 作为 `key`，而导致所有的优化失效了。

### 2. 节点删除场景








