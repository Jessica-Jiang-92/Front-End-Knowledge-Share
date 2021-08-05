# 前言

Vue中非常常见的有父子组件通信，兄弟组件通信。而父子组件通信就很简单，父组件会通过 `props` 向下传数据给子组件，当子组件有事情要告诉父组件时会通过 `$emit` 事件告诉父组件。今天就来说说如果两个页面没有任何引入和被引入关系，该如何通信了？

如果咱们的应用程序不需要类似`Vuex`这样的库来处理组件之间的数据通信，就可以考虑Vue中的 事件总线 ，即 `**EventBus**`来通信。

## 1. EventBus简介

`EventBus` 又称为事件总线。在Vue中可以使用 `EventBus` 来作为沟通桥梁的概念，就像是所有组件共用相同的事件中心，可以向该中心注册发送事件或接收事件，所以组件都可以上下平行地通知其他组件，但也就是太方便所以若使用不慎，就会造成难以维护的“灾难”，因此才需要更完善的`Vuex`作为状态管理中心，将通知的概念上升到共享状态层次。

那如何使用`EventBus`呢？

### 1-1 初始化

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

### 1-2 发送事件

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

### 1-3 接收事件
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





