## 前言
在使用CSS隐藏一些元素的时候，我们经常用到display: none和visibility: hidden。那么2者之间有什么不一样呢。

## 占据空间与否
- display: none 不占据任何空间，文档渲染时，这个元素就像不存在一样。
- visibility: hidden 该元素空间仍然存在。

## 是否渲染
- display: none 会触发reflow回流，进行渲染。
- visibility: hidden 只会触发repaint重绘，因为没有发现位置改变，不进行渲染。

## 是否有继承属性
- display: none，display不是继承属性，元素及其子元素都会消失。
- visibility: hidden，visibility是继承属性，如果子元素使用了visibility: visible，则不继承，这个子元素又会显示出来。




