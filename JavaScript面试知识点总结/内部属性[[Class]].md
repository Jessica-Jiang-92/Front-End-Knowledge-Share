## 参考文章
1. [内部属性[[class]]](https://blog.csdn.net/baibaider/article/details/80660993)

## 内部属性[[class]]

所有typeof返回值为Object的对象都包含一个内部属性。这个属性无法直接访问，一般通过Object.prototype.toString(..)
来查看。如：
```
Object.prototype.toString.call(['a', 'b', 'c'])
=>"[object Array]"

Object.prototype.toString.call(/regex-literal/i/);
=>"[object RegExp]"
```
如下图所示：
![内部属性](https://user-images.githubusercontent.com/10249805/109093831-f9b03e00-7753-11eb-92cb-503971078303.png)

但是我们自己创建的类就不会有这样的待遇，因为toString()找不到toStringTag属性，只能返回默认的Object标签。
（PS：有时候也可以用来判断数据的类型）
默认情况下，类的[[Class]]返回[object Object]，如：
class Person {}
Object.prototype.toString.call(new Person());
=>"[object Object]"
**这个时候需要定制我们自己的[[Class]]
class Person1 {
  get [Symbol.toStringTag]() {
    return 'Person1';
  }
 }
 Object.prototype.toString.call(new Person1());
 => "[object Person1]"
