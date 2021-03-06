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

另外，除了会导致性能损耗以外，在删除子节点的场景下还会造成更严重的错误，假设我们有下面的代码结构：
```
<body>
  <div id="app">
    <ul>
      <li v-for="(value, index) in arr" :key="index">
        <test />
      </li>
    </ul>
    <button @click="handleDelete">delete</button>
  </div>
  </div>
</body>
<script>
  new Vue({
    name: "App",
    el: '#app',
    data() {
      return {
        arr: [1, 2, 3]
      };
    },
    methods: {
      handleDelete() {
        this.arr.splice(0, 1);
      }
    },
    components: {
      test: {
        template: "<li>{{Math.random()}}</li>"
      }
    }
  })
</script>
```
那么一开始`vnode列表`是：
```
[
  {
    tag: "li",
    key: 0,
    // 这里其实子组件对应的是第一个 假设子组件的text是1
  },
  {
    tag: "li",
    key: 1,
    // 这里其实子组件对应的是第二个 假设子组件的text是2
  },
  {
    tag: "li",
    key: 2,
    // 这里其实子组件对应的是第三个 假设子组件的text是3
  }
];
```
有一个细节需要注意，Vue 对于组件的 `diff` 是不关心子组件内部实现的，它只会看你在模板上声明的传递给子组件的一些属性是否有更新。也就是和`v-for`平级的那部分，回顾一下判断 `sameNode` 的时候，只会判断`key`、 `tag`、`是否有data的存在（不关心内部具体的值）`、`是否是注释节点`、`是否是相同的input type`，来判断是否可以复用这个节点。
```
<li v-for="(value, index) in arr" :key="index"> // 这里声明的属性
  <test />
</li>
```
有了这些前置知识以后，我们来看看，点击删除子元素后，`vnode` 列表 变成什么样了。
```
[
  // 第一个被删了
  {
    tag: "li",
    key: 0,
    // 这里其实上一轮子组件对应的是第二个 假设子组件的text是2
  },
  {
    tag: "li",
    key: 1,
    // 这里其实子组件对应的是第三个 假设子组件的text是3
  },
];
```

### 3. 为什么不要用随机数作为key?

```
<item
  :key="Math.random()"
  v-for="(num, index) in nums"
  :num="num"
  :class="`item${num}`"
/>
```
其实我听过一种说法，既然官方要求一个 唯一的`key`，是不是可以用 `Math.random()` 作为 `key` 来偷懒？我们来看看会发生什么吧。

首先 oldVnode 是这样的：
```
[
  {
    tag: "item",
    key: 0.6330715699108844,
    props: {
      num: 1
    }
  },
  {
    tag: "item",
    key: 0.25104533240710514,
    props: {
      num: 2
    }
  },
  {
    tag: "item",
    key: 0.4114769152411637,
    props: {
      num: 3
    }
  }
];
```
更新以后是：
```
[
  {
    tag: "item",
+   key: 0.11046018699748683,
    props: {
+     num: 3
    }
  },
  {
    tag: "item",
+   key: 0.8549799545696619,
    props: {
+     num: 2
    }
  },
  {
    tag: "item",
+   key: 0.18674467938937478,
    props: {
+     num: 1
    }
  }
];
```
可以看到，key 变成了完全全新的 3 个随机数。

上面说到，`diff` 子节点的首尾对比如果都没有命中，就会进入 `key` 的详细对比过程，简单来说，就是利用旧节点的 `key -> index` 的关系建立一个 `map` 映射表，然后用新节点的 `key` 去匹配，如果没找到的话，就会调用 `createElm` 方法 重新建立一个新节点。具体代码如下：
```
// 建立旧节点的 key -> index 映射表
oldKeyToIdx = createKeyToOldIdx(oldCh, oldStartIdx, oldEndIdx);

// 去映射表里找可以复用的 index
idxInOld = findIdxInOld(newStartVnode, oldCh, oldStartIdx, oldEndIdx);
// 一定是找不到的，因为新节点的 key 是随机生成的。
if (isUndef(idxInOld)) {
  // 完全通过 vnode 新建一个真实的子节点
  createElm();
}
```
也就是说，咱们的这个更新过程可以这样描述： `123 -> 前面重新创建三个子组件 -> 321123 -> 删除、销毁后面三个子组件 -> 321`。这是毁灭性的灾难，创建新的组件和销毁组件的成本非常大，本来仅仅是对组件移动位置就可以完成的更新，被我们毁成这样了。

## 总结

- 用组件唯一的 `id`（一般由后端返回）作为它的 `key`，实在没有的情况下，可以在获取到列表的时候通过某种规则为它们创建一个 `key`，并保证这个 `key` 在组件整个生命周期中都保持稳定。
- 如果你的列表顺序会改变，别用 `index` 作为 `key`，和没写基本上没区别，因为不管你数组的顺序怎么颠倒，`index` 都是 `0, 1, 2` 这样排列，导致 `Vue` 会复用错误的旧子节点，做很多额外的工作。列表顺序不变也尽量别用，可能会误导新人。
- 千万别用随机数作为 `key`，不然旧节点会被全部删掉，新节点重新创建，你的`leader`会被你气死。







