### 第 85 题：react-router 里的 <Link> 标签和 a 标签有什么区别

从最终渲染的 DOM 来看，这两者都是链接，都是 <a> 标签，区别是：
<Link> 是 react-router 里实现路由跳转的链接，一般配合 <Route> 使用，react-router 接管了其默认的链接跳转行为，区别于传统的页面跳转，<Link> 的“跳转”行为只会触发相匹配的 <Route> 对应的页面内容更新，而不会刷新整个页面。

Link 的本质也是a 标签。只不过在Link 中禁用了 a 标签的默认事件，改用了history对象提供的方法进行跳转。

而 a 标签就是普通的超链接了，用于从当前页面跳转到 href 指向的另一个页面（非锚点情况）。

##### Link点击事件handleClick部分源码

 
```
if (_this.props.onClick) _this.props.onClick(event);

      if (!event.defaultPrevented && // onClick prevented default
      event.button === 0 && // ignore everything but left clicks
      !_this.props.target && // let browser handle "target=_blank" etc.
      !isModifiedEvent(event) // ignore clicks with modifier keys
      ) {
          event.preventDefault();

          var history = _this.context.router.history;
          var _this$props = _this.props,
              replace = _this$props.replace,
              to = _this$props.to;


          if (replace) {
            history.replace(to);
          } else {
            history.push(to);
          }
        }
```
Link做了3件事情：

有onclick那就执行onclick

click的时候阻止a标签默认事件（这样子点击<a href="/abc">123</a>就不会跳转和刷新页面）

再取得跳转href（即是to），用history（前端路由两种方式之一，history & hash）跳转，此时只是链接变了，并没有刷新页面


---

#### ['1', '2', '3'].map(parseInt) what & why ?

第一眼看到这个题目的时候，脑海跳出的答案是 [1, 2, 3]，但是真正的答案是[1, NaN, NaN]。

- 首先让我们回顾一下，map函数的第一个参数callback：

```
var new_array = arr.map(function callback(currentValue[, index[, array]]) { 
// Return element for new_array }[, thisArg])
```


这个callback一共可以接收三个参数，其中第一个参数代表当前被处理的元素，而第二个参数代表该元素的索引。

- 而parseInt则是用来解析字符串的，使字符串成为指定基数的整数。
parseInt(string, radix)
接收两个参数，第一个表示被处理的值（字符串），第二个表示为解析时的基数。

- 了解这两个函数后，我们可以模拟一下运行情况

parseInt('1', 0) //radix为0时，且string参数不以“0x”和“0”开头时，按照10为基数处理。这个时候返回1

parseInt('2', 1) //基数为1（1进制）表示的数中，最大值小于2，所以无法解析，返回NaN

parseInt('3', 2) //基数为2（2进制）表示的数中，最大值小于3，所以无法解析，返回NaN
map函数返回的是一个数组，所以最后结果为[1, NaN, NaN]


---
#### 第 81 题：打印出 1 - 10000 之间的所有对称数 例如：121、1331 等

```
[...Array(10000).keys()].filter((x) => { 
  return x.toString().length > 1 && x === Number(x.toString().split('').reverse().join('')) 
})
```


#### JS快速建立一个数组，长度100，值是下标或者0

1.Object.keys和Array.apply和map


```
var arr = Object.keys(Array.apply(null, { length: 100 })).map(function(
   item
) {
   return +item;
});
```


2.Object.keys和Array.from和map


```
var arr = Object.keys(Array.from({ length: 100 })).map(function(item) {
console.log(item)
    return +item;
  });
```


3.扩展运算符和Array.from


```
var arr=[...Array.from({ length: 100 }).keys()]
```


4.扩展运算符和Array.apply


```
var arr=[...Array.apply({ length: 100 }).keys()]
```


5.扩展运算符和new Array()


```
var arr = [...new Array(100).keys()];
```


6.扩展运算符和Array()


```
var arr = [...Array(100).keys()];
```


7.Array.from =>k


```
var arr = Array.from({ length: 100 }, (v, k) => k);
// 值为0
 var arr = Array.from({ length: 100 }, ()=>0);
```


8.递归调用


```
var s = [];
var i =0;
function array(num){
   if(i<100){
    s[i] = i++
    array(num);
  }
  return s;
}
console.log(array(100));
```


值为0


```
var arr = new Array(100).fill(0);   

var arr =  [...Array(100).fill(0)];

var arr = new Array(101).join(0).split("").map(item => {
    return +item;
 });
 
var arr = new Array(100);arr = arr.map(item=>0);

Array.apply(null, { length: 100 }).fill(0);
```
###### String转换为Number有很多种方式

```
parseInt(num); // 默认方式 (没有基数)
parseInt(num, 10); // 传入基数 (十位数)
parseFloat(num) // 浮点数
Number(num); // Number 构造器
~~num //按位非
num / 1 // 除一个数
num * 1 // 乘一个数
num - 0 // 减去0
+num // 一元运算符 "+"
```
*parseInt*
 
 根据JsPerf.com的基准测试，大多数浏览器对parseInt的响应最佳。虽然它是最快的方式，但使用preseInt会碰到一些常见陷阱：

```
parseInt('08') // returns 0 部分老浏览器.
parseInt('44.jpg') // returns 44
```
parseInt: 没有传入基数时，默认是传入的基数为10 parseInt(num, 10)，如果你不知道num属性的类型，不要使用parseInt进行字符串转数字。


*parseFloat*

如果你不解析16进制数，这是一个非常好的选择。例如：

```
parseInt(-0xFF) // returns -255
parseInt("-0xFF") // returns -255
parseFloat(-0xFF) // returns -255
parseFloat("-0xFF") // returns 0
parseFloat('44.jpg') // return 44
```

注意：字符串中的负十六进制数字是一个特殊情况，如果你用parseFloat解析，结果是不正确的。为了避免程序出现NaN的情况，应该检查转化后的值。


parseFloat: 转换十六进制数时要小心，如果你不知道要转换对象的类型，不要使用parseFloat。

*按位非*

可以把字符串转换成整数，但他不是浮点数。如果是一个字符串转换，它将返回0；



```
~~1.23 // returns 1
~~"1.23" // returns 1
~~"23" // returns 23
~~"Hello world" // returns 0
```

这是什么原理？通过翻转)每个位，也称为数字的A1补码。你可以使用它，但注意只能用来存储整数。所以通常情况不要用它，除非你能确定这个数是在32位整数之间的值（因为调用的ToInt32的规范）。

按位非：用它确保输入中没有字符，仅用于整数。

*Number*

Number与以上提及的转换方式一样存在这样的问题，解析时试图找出你给他的数字：

```
Number("023") // returns 23
Number(023) // returns 19
```
注意：023实际上是一个八进制数，无论你怎么做，都是返回19；对于没有单引号或双引号的十六进制数一样。

Number也是JsPerf中最慢的之一。

Number：几乎不用它。

*一元云算符*


```
"1.23" * 1 // returns 1.23
"0xFF" - 0 // returns 255
"0xFF.jpg" / 1 // returns NaN
+"023" // returns 23
```

一元运算符与其它的解析方式不同，如果是一个NaN值，那么返回的也是NaN 。这是我最喜欢的数值转换方式，因为我认为任何带有字符的对象都不应该被视为0或者根据他有多少位来“猜”。我基本使用 + 操作符，因为这个方式不容易混淆。虽然 -0 的用法也很好，但它并没有很好的表达转换为数字的本意。

字符串转换为数字的方式总结

负十六进制数字符串转换为数字时。应首先将任何其转换为String（例如通过 + "" ），然后使用一元运算符或带基数的parseInt解析为数字。但是结果不是NaN的数值时，使用parseFloat更为合适。



---
### 首先看看柯里化到底是什么？

柯里化，英语：Currying，是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

看这个解释有一点抽象，我们就拿被做了无数次示例的add函数，来做一个简单的实现



```
// 普通的add函数
function add(x, y) {
    return x + y
}

// Currying后
function curryingAdd(x) {
    return function (y) {
        return x + y
    }
}

add(1, 2)           // 3
curryingAdd(1)(2)   // 3
```
实际上就是把add函数的x，y两个参数变成了先用一个函数接收x然后返回一个函数去处理y参数。现在思路应该就比较清晰了，就是只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数。

#### Currying有哪些好处呢

##### 1. 参数复用
```
// 正常正则验证字符串 reg.test(txt)

// 函数封装后
function check(reg, txt) {
    return reg.test(txt)
}

check(/\d+/g, 'test')       //false
check(/[a-z]+/g, 'test')    //true

// Currying后
function curryingCheck(reg) {
    return function(txt) {
        return reg.test(txt)
    }
}

var hasNumber = curryingCheck(/\d+/g)
var hasLetter = curryingCheck(/[a-z]+/g)

hasNumber('test1')      // true
hasNumber('testtest')   // false
hasLetter('21212')      // false
```
上面的示例是一个正则的校验，正常来说直接调用check函数就可以了，但是如果我有很多地方都要校验是否有数字，其实就是需要将第一个参数reg进行复用，这样别的地方就能够直接调用hasNumber，hasLetter等函数，让参数能够复用，调用起来也更方便
##### 2. 提前确认


```
var on = function(element, event, handler) {
    if (document.addEventListener) {
        if (element && event && handler) {
            element.addEventListener(event, handler, false);
        }
    } else {
        if (element && event && handler) {
            element.attachEvent('on' + event, handler);
        }
    }
}

var on = (function() {
    if (document.addEventListener) {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.addEventListener(event, handler, false);
            }
        };
    } else {
        return function(element, event, handler) {
            if (element && event && handler) {
                element.attachEvent('on' + event, handler);
            }
        };
    }
})();

//换一种写法可能比较好理解一点，上面就是把isSupport这个参数给先确定下来了
var on = function(isSupport, element, event, handler) {
    isSupport = isSupport || document.addEventListener;
    if (isSupport) {
        return element.addEventListener(event, handler, false);
    } else {
        return element.attachEvent('on' + event, handler);
    }
}
```
我们在做项目的过程中，封装一些dom操作可以说再常见不过，上面第一种写法也是比较常见，但是我们看看第二种写法，它相对一第一种写法就是自执行然后返回一个新的函数，这样其实就是提前确定了会走哪一个方法，避免每次都进行判断。

##### 3. 延迟运行


```
Function.prototype.bind = function (context) {
    var _this = this
    var args = Array.prototype.slice.call(arguments, 1)
 
    return function() {
        return _this.apply(context, args)
    }
}
```


#### 有没有什么通用的封装方法


```
// 初步封装
var currying = function(fn) {
    // args 获取第一个方法内的全部参数
    var args = Array.prototype.slice.call(arguments, 1)
    return function() {
        // 将后面方法里的全部参数和args进行合并
        var newArgs = args.concat(Array.prototype.slice.call(arguments))
        // 把合并后的参数通过apply作为fn的参数并执行
        return fn.apply(this, newArgs)
    }
}
```

这边首先是初步封装,通过闭包把初步参数给保存下来，然后通过获取剩下的arguments进行拼接，最后执行需要currying的函数。

但是好像还有些什么缺陷，这样返回的话其实只能多扩展一个参数，currying(a)(b)(c)这样的话，貌似就不支持了（不支持多参数调用），一般这种情况都会想到使用递归再进行封装一层。

// 支持多参数传递
function progressCurrying(fn, args) {

```
var _this = this
    var len = fn.length;
    var args = args || [];

    return function() {
        var _args = Array.prototype.slice.call(arguments);
        Array.prototype.push.apply(args, _args);

        // 如果参数个数小于最初的fn.length，则递归调用，继续收集参数
        if (_args.length < len) {
            return progressCurrying.call(_this, fn, _args);
        }

        // 参数收集完毕，则执行fn
        return fn.apply(this, _args);
    }
}
```
这边其实是在初步的基础上，加上了递归的调用，只要参数个数小于最初的fn.length，就会继续执行递归。

#### curry的性能

curry的一些性能问题你只要知道下面四点就差不多了：

- 存取arguments对象通常要比存取命名参数要慢一点


- 一些老版本的浏览器在arguments.length的实现上是相当慢的
- 使用fn.apply( … ) 和 fn.call( … )通常比直接调用fn( … ) 稍微慢点
- 创建大量嵌套作用域和闭包函数会带来花销，无论是在内存还是速度上

其实在大部分应用中，主要的性能瓶颈是在操作DOM节点上，这js的性能损耗基本是可以忽略不计的，所以curry是可以直接放心的使用。

#### 扩展一道经典面试题
// 实现一个add方法，使计算结果能够满足如下预期：

```
add(1)(2)(3) = 6;
add(1, 2, 3)(4) = 10;
add(1)(2)(3)(4)(5) = 15;

function add() {
    // 第一次执行时，定义一个数组专门用来存储所有的参数
    var _args = Array.prototype.slice.call(arguments);

    // 在内部声明一个函数，利用闭包的特性保存_args并收集所有的参数值
    var _adder = function() {
        _args.push(...arguments);
        return _adder;
    };

    // 利用toString隐式转换的特性，当最后执行时隐式转换，并计算最终的值返回
    _adder.toString = function () {
        return _args.reduce(function (a, b) {
            return a + b;
        });
    }
    return _adder;
}

add(1)(2)(3)                // 6
add(1, 2, 3)(4)             // 10
add(1)(2)(3)(4)(5)          // 15
add(2, 6)(1)                // 9
```

---
### 什么是防抖和节流？有什么区别？如何实现？

防抖和节流的作用都是防止函数多次调用。区别在于，假设一个用户一直触发这个函数，且每次触发函数的间隔小于wait，防抖的情况下只会调用一次，而节流的 情况会每隔一定时间（参数wait）调用函数

#### 防抖
- 触发高频事件后n秒内函数只会执行一次，如果n秒内高频事件再次被触发，则重新计算时间

你是否在日常开发中遇到一个问题，在滚动事件中需要做个复杂计算或者实现一个按钮的防二次点击操作。

这些需求都可以通过函数防抖动来实现。尤其是第一个需求，如果在频繁的事件回调中做复杂计算，很有可能导致页面卡顿，不如将多次计算合并为一次计算，只在一个精确点做操作。

#### 袖珍版的防抖理解一下防抖的实现
```
// func是用户传入需要防抖的函数
// wait是等待时间
const debounce = (func, wait = 50) => {
  // 缓存一个定时器id
  let timer = 0
  // 这里返回的函数是每次用户实际调用的防抖函数
  // 如果已经设定过定时器了就清空上一次的定时器
  // 开始一个新的定时器，延迟执行用户传入的方法
  return function(...args) {
    if (timer) clearTimeout(timer)
    timer = setTimeout(() => {
      func.apply(this, args)
    }, wait)
  }
}
// 不难看出如果用户调用该函数的间隔小于wait的情况下，上一次的时间还未到就被清除了，并不会执行函数
```
这是一个简单版的防抖，但是有缺陷，这个防抖只能在最后调用。一般的防抖会有immediate选项，表示是否立即调用。这两者的区别，举个栗子来说：

- 例如在搜索引擎搜索问题的时候，我们当然是希望用户输入完最后一个字才调用查询接口，这个时候适用延迟执行的防抖函数，它总是在一连串（间隔小于wait的）函数触发之后调用。


- 例如用户给interviewMap点star的时候，我们希望用户点第一下的时候就去调用接口，并且成功之后改变star按钮的样子，用户就可以立马得到反馈是否star成功了，这个情况适用立即执行的防抖函数，它总是在第一次调用，并且下一次调用必须与前一次调用的时间间隔大于wait才会触发。


#### 带有立即执行选项的防抖函数

```
// 这个是用来获取当前时间戳的
function now() {
  return +new Date()
}
/**
 * 防抖函数，返回函数连续调用时，空闲时间必须大于或等于 wait，func 才会执行
 *
 * @param  {function} func        回调函数
 * @param  {number}   wait        表示时间窗口的间隔
 * @param  {boolean}  immediate   设置为ture时，是否立即调用函数
 * @return {function}             返回客户调用函数
 */
function debounce (func, wait = 50, immediate = true) {
  let timer, context, args

  // 延迟执行函数
  const later = () => setTimeout(() => {
    // 延迟函数执行完毕，清空缓存的定时器序号
    timer = null
    // 延迟执行的情况下，函数会在延迟函数中执行
    // 使用到之前缓存的参数和上下文
    if (!immediate) {
      func.apply(context, args)
      context = args = null
    }
  }, wait)

  // 这里返回的函数是每次实际调用的函数
  return function(...params) {
    // 如果没有创建延迟执行函数（later），就创建一个
    if (!timer) {
      timer = later()
      // 如果是立即执行，调用函数
      // 否则缓存参数和调用上下文
      if (immediate) {
        func.apply(this, params)
      } else {
        context = this
        args = params
      }
    // 如果已有延迟执行函数（later），调用的时候清除原来的并重新设定一个
    // 这样做延迟函数会重新计时
    } else {
      clearTimeout(timer)
      timer = later()
    }
  }
}
```

- 对于按钮防点击来说的实现：如果函数是立即执行的，就立即调用，如果函数是延迟执行的，就缓存上下文和参数，放到延迟函数中去执行。一旦我开始一个定时器，只要我定时器还在，你每次点击我都重新计时。一旦你点累了，定时器时间到，定时器重置为 null，就可以再次点击了。
- 对于延时执行函数来说的实现：清除定时器ID，如果是延迟调用就调用函数

#### 节流
高频事件触发，但在n秒内只会执行一次，所以节流会稀释函数的执行频率


```
/**
 * underscore 节流函数，返回函数连续调用时，func 执行频率限定为 次 / wait
 *
 * @param  {function}   func      回调函数
 * @param  {number}     wait      表示时间窗口的间隔
 * @param  {object}     options   如果想忽略开始函数的的调用，传入{leading: false}。
 *                                如果想忽略结尾函数的调用，传入{trailing: false}
 *                                两者不能共存，否则函数不能执行
 * @return {function}             返回客户调用函数
 */
_.throttle = function(func, wait, options) {
    var context, args, result;
    var timeout = null;
    // 之前的时间戳
    var previous = 0;
    // 如果 options 没传则设为空对象
    if (!options) options = {};
    // 定时器回调函数
    var later = function() {
      // 如果设置了 leading，就将 previous 设为 0
      // 用于下面函数的第一个 if 判断
      previous = options.leading === false ? 0 : _.now();
      // 置空一是为了防止内存泄漏，二是为了下面的定时器判断
      timeout = null;
      result = func.apply(context, args);
      if (!timeout) context = args = null;
    };
    return function() {
      // 获得当前时间戳
      var now = _.now();
      // 首次进入前者肯定为 true
	  // 如果需要第一次不执行函数
	  // 就将上次时间戳设为当前的
      // 这样在接下来计算 remaining 的值时会大于0
      if (!previous && options.leading === false) previous = now;
      // 计算剩余时间
      var remaining = wait - (now - previous);
      context = this;
      args = arguments;
      // 如果当前调用已经大于上次调用时间 + wait
      // 或者用户手动调了时间
 	  // 如果设置了 trailing，只会进入这个条件
	  // 如果没有设置 leading，那么第一次会进入这个条件
	  // 还有一点，你可能会觉得开启了定时器那么应该不会进入这个 if 条件了
	  // 其实还是会进入的，因为定时器的延时
	  // 并不是准确的时间，很可能你设置了2秒
	  // 但是他需要2.2秒才触发，这时候就会进入这个条件
      if (remaining <= 0 || remaining > wait) {
        // 如果存在定时器就清理掉否则会调用二次回调
        if (timeout) {
          clearTimeout(timeout);
          timeout = null;
        }
        previous = now;
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      } else if (!timeout && options.trailing !== false) {
        // 判断是否设置了定时器和 trailing
	    // 没有的话就开启一个定时器
        // 并且不能不能同时设置 leading 和 trailing
        timeout = setTimeout(later, remaining);
      }
      return result;
    };
  };
```
