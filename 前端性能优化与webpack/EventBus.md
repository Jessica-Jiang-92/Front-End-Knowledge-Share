# 前言

Vue中非常常见的有父子组件通信，兄弟组件通信。而父子组件通信就很简单，父组件会通过 `props` 向下传数据给子组件，当子组件有事情要告诉父组件时会通过 `$emit` 事件告诉父组件。今天就来说说如果两个页面没有任何引入和被引入关系，该如何通信了？

如果咱们的应用程序不需要类似`Vuex`这样的库来处理组件之间的数据通信，就可以考虑Vue中的 事件总线 ，即 `**EventBus**`来通信。

## 1. EventBus简介

`EventBus` 又称为事件总线。在Vue中可以使用 `EventBus` 来作为沟通桥梁的概念，就像是所有组件共用相同的事件中心，可以向该中心注册发送事件或接收事件，所以组件都可以上下平行地通知其他组件，但也就是太方便所以若使用不慎，就会造成难以维护的“灾难”，因此才需要更完善的`Vuex`作为状态管理中心，将通知的概念上升到共享状态层次。

那如何使用`EventBus`呢？

### 1.1 初始化

首先需要创建事件总线并将其导出，以便其它模块可以使用或者监听它。我们可以通过两种方式来处理。先来看第一种，新创建一个 `.js` 文件，比如 `event-bus.js`。
```
// event-bus.js
import Vue from 'vue'
export const EventBus = new Vue()
```
实质上`EventBus`是一个不具备 `DOM` 的组件，它具有的仅仅只是它实例方法而已，因此它非常的轻便。

另外一种方式，可以直接在项目中的 `main.js` 初始化 `EventBus` :
```
// main.js
Vue.prototype.$EventBus = new Vue()
```
注意，这种方式初始化的EventBus是一个全局的事件总线。稍后再来聊一聊全局的事件总线。现在我们已经创建了 `EventBus` ，接下来你需要做到的就是在你的组件中加载它，并且调用同一个方法，就如你在父子组件中互相传递消息一样。

### 1.2 发送事件

假设你有2个Vue页面需要通信：A和B，A在按钮上绑定了点击事件，发送一则消息，想通知B页面。
```
<!-- A.vue -->
<template>
    <button @click="sendMsg()">-</button>
</template>

<script> 
import { EventBus } from "../event-bus.js";
export default {
  methods: {
    sendMsg() {
      EventBus.$emit("aMsg", '来自A页面的消息');
    }
  }
}; 
</script>
```
接下来，我们需要在B页面中接收这则消息。

### 1.3 接收事件
```
<!-- IncrementCount.vue -->
<template>
  <p>{{msg}}</p>
</template>

<script> 
import { 
  EventBus 
} from "../event-bus.js";
export default {
  data(){
    return {
      msg: ''
    }
  },
  mounted() {
    EventBus.$on("aMsg", (msg) => {
      // A发送来的消息
      this.msg = msg;
    });
  }
};
</script>
```
同理，我们也可以在B页面向A页面发送消息。这里主要用到两个方法：
```
// 发送消息
EventBus.$emit(channel: string, callback(payload1,…))

// 监听接收消息
EventBus.$on(channel: string, callback(payload1,…))
```
前面提到过，如果使用不善，`EventBus`会是一种灾难，到底是什么样的“灾难”了？大家都知道vue是单页应用，如果你在某一个页面刷新了之后，与之相关的`EventBus`会被移除，这样就导致业务走不下去。还有就是如果业务有反复操作的页面，`EventBus`在监听的时候就会触发很多次，也是一个非常大的隐患。这时候我们就需要好好处理`EventBus`在项目中的关系。通常会用到，在vue页面销毁时，同时移除`EventBus`事件监听。

如果想移除事件的监听，可以像下面这样操作：
```
import { 
  eventBus 
} from './event-bus.js'
EventBus.$off('aMsg', {})
```
你也可以使用 `EventBus.$off('aMsg')` 来移除应用内所有对此某个事件的监听。或者直接调用 `EventBus.$off()` 来移除所有事件频道，不需要添加任何参数 。

上面就是 `EventBus` 的使用方法，是不是很简单。上面的示例中我们也看到了，每次使用 `EventBus` 时都需要在各组件中引入 `event-bus.js` 。事实上，我们还可以通过别的方式，让事情变得简单一些。那就是创建一个全局的 `EventBus` 。接下来的示例向大家演示如何在Vue项目中创建一个全局的 `EventBus` 。

## 2. 全局EventBus

它的工作原理是发布/订阅方法，通常称为 Pub/Sub 。

### 2.1 创建全局EventBus

```
var EventBus = new Vue();

Object.defineProperties(Vue.prototype, {
  $bus: {
    get: function () {
      return EventBus
    }
  }
})
```
在这个特定的总线中使用两个方法`$on`和`$emit`。一个用于创建发出的事件，它就是`$emit`；另一个用于订阅`$on`：

```
var EventBus = new Vue();

this.$bus.$emit('nameOfEvent', { ... pass some event data ...});

this.$bus.$on('nameOfEvent',($event) => {
  // ...
})
```
然后我们可以在某个Vue页面使用this.$bus.$emit("sendMsg", '我是一条message');，另一个Vue页面使用:
```
this.$bus.$on('updateMessage', function(value) {
  console.log(value); // 我是web秀
})
```
同时也可以使用`this.$bus.$off('sendMsg')`来移除事件监听。

### 总结

本文主要通过简单的实例学习了Vue中有关于`EventBus`相关的知识点。主要涉及了 `EventBus` 如何实例化，又是如何通过 `$emit` 发送频道信号，又是如何通过 `$on` 来接收频道信号。最后简单介绍了如何创建全局的 `EventBus` 。从实例中我们可以了解到，`EventBus` 可以较好的实现兄弟组件之间的数据通讯。

### 优缺点

#### 优点

- 简单统一的数据传递
- 清晰明了的主次线程
- 使用class来传递数据（是的，最好用的地方用一个class来传递数据，这下传一个class，就可以携带各种各样的数据了，摆脱了用Bundle传递list和数组）
- 在`activity`与`activity`，或者`Service`与`activity`传递大数据时的唯一选择。因为序列化大数据进行传递时，是十分耗时缓慢的。用`EventBus`是最优解法。

#### 缺点

- 滥用它，EventBus可以大量解耦项目，但是如果你大量的使用它会产生一个非常危险的后果，你需要定义大量的常量或者新的实体类来区分接收者。管理EventBus的消息类别将会你的痛苦
- 非前台组件中使用它，不只在Activity或者Service，广播里使用它。 而是将它使用到每一个工具类 或者后台业务类，除了让数据发送与接收更加复杂。别忘记了Java本身就是面对对象语言，它有接口、抽象可以实现来发送与接收数据。你可以用各种设计模式，比如观察者模式，来更好的优化与独立自己的业务。不需要依赖EventBus。
- EventBus，并不是真正的解耦。请不要在你独立的模块里使用EventBus来分发。你这个模块如果那天要直接放入另外一个项目里，你怎么解耦EventBus？ 最好，还是多使用接口与Activity本身的数据传递方式。
- `EventBus`的使用不当容易造成服务器端的内存泄漏问题。那么什么情况下会造成这种情况呢？当你在`created()`中使用`EventBus`时就会出现这种现象，如：
```
 created() {
    EventBus.$on('playTrendVideo', url => {
      this.openVideo(url);
    });
  }
```
这个时候怎么处理呢？可以选择不使用`created()`，或者使用`mounted()`来替换。





