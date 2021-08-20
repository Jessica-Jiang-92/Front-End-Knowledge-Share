# 背景

Vue3正式发布到现在为止已经快一年了，很多项目也都开始往Vue3进行切换。同时，Vue3.2也已经正式发布了，对于使用上而言没有多大的变化，这个小版本的发布主要体现在了响应式的性能上面：

- More efficient ref implementation (~260% faster read / ~50% faster write)
- ~40% faster dependency tracking
- ~17% less memory usage

也就是说 `ref API` 的读效率提升约为 `260%`，写效率提升约为 `50%` ，依赖收集的效率提升约为 `40%`，同时还减少了约 `17%` 的内存使用。这个优化也就意味着对所有使用 Vue.js 开发的 App 的性能优化。而且这个优化并不是 `Vue` 官方人员实现的，而是社区一位大佬 `@basvanmeurs` 提出的，由于对内部的实现改动较大，官方一直等到了 `Vue 3.2` 发布，才把代码合入。

我们知道，相比于 `Vue2`，`Vue3` 做了多方面的优化，其中一部分是数据响应式的实现由 `Object.defineProperty API` 改成了 `Proxy API`。当初 `Vue3` 在宣传的时候，官方宣称在响应式的实现性能上做了优化，那么优化体现在哪些方面呢？有部分小伙伴认为是 `Proxy API` 的性能要优于 `Object.defineProperty` 的，其实不然，实际上 `Proxy` 在性能上是要比 `Object.defineProperty` 差的。可以参考这里[Thoughts on ES6 Proxies Performance](https://thecodebarbarian.com/thoughts-on-es6-proxies-performance)

既然 `Proxy` 慢，为啥 `Vue3` 还是选择了它来实现数据响应式呢？因为 `Proxy` 本质上是对某个对象的劫持，这样它不仅仅可以监听对象某个属性值的变化，还可以监听对象属性的新增和删除；而 `Object.defineProperty` 是给对象的某个已存在的属性添加对应的 `getter` 和 `setter`，所以它只能监听这个属性值的变化，而不能去监听对象属性的新增和删除。

而响应式在性能方面的优化主要体现在把嵌套层级较深的对象变成响应式的场景。
- Vue2: 在组件初始化阶段把数据变成响应式时，遇到子属性仍然是对象的情况，会递归执行 `Object.defineProperty` 定义子对象的响应式；
- Vue3: 只有在对象属性被访问的时候才会判断子属性的类型来决定要不要递归执行 reactive，这其实是一种延时定义子对象响应式的实现，在性能上会有一定的提升。

相比于 `Vue 2`，`Vue 3`，Vue.js 3.2 这次在响应式性能方面的优化，是真的做到了质的飞跃。



