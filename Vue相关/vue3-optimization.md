# 背景

Vue3正式发布到现在为止已经快一年了，很多项目也都开始往Vue3进行切换。同时，Vue3.2也已经正式发布了，对于使用上而言没有多大的变化，这个小版本的发布主要体现在了响应式的性能上面：

- More efficient ref implementation (~260% faster read / ~50% faster write)
- ~40% faster dependency tracking
- ~17% less memory usage

也就是说 `ref API` 的读效率提升约为 `260%`，写效率提升约为 `50%` ，依赖收集的效率提升约为 `40%`，同时还减少了约 `17%` 的内存使用。





