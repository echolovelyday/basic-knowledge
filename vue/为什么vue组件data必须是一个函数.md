#  why data in vue component must be a function

[官网说明](https://cn.vuejs.org/v2/guide/components.html#data-%E5%BF%85%E9%A1%BB%E6%98%AF%E4%B8%80%E4%B8%AA%E5%87%BD%E6%95%B0/)

vue组件中data 的写法如下
```
data: function () {
  return {
    count: 0
  }
}
```
一句话回答：简单来说了为了不让多处的组件共享同一data对象，只能返回函数。如果data的值是一个对象， 是引用数据类型,如果不用函数return ,每个组件的data 都是同一个对象,一个数据改变了其他也改变了。
具体分析：
在JavaScript中，函数具有独立作用域块，外部是无法访问其内部变量。
组件是可复用的vue实例，一个组件被创建好之后，就可能被用在各个地方，而组件不管被复用了多少次，组件中的data数据都应该是相互隔离，互不影响的，如果我们组件中的data属性是一个函数，他的每个实例就会有自己的作用域空间，也就是独立的数据，每个实例之间不会相互影响。
而new Vue()中的data除外，因为new Vue中只有一个data属性。