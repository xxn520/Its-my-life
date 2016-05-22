## compose.js

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=27566686&auto=1&height=66"></iframe>

听宝宝的话要早点睡，所以就先写最简单的一个哈😳！废话不多说先贴源码：

```
export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  } else {
    const last = funcs[funcs.length - 1]
    const rest = funcs.slice(0, -1)
    return (...args) => rest.reduceRight((composed, f) => f(composed), last(...args))
  }
}
```

先来看看代码的意思，如果没有传入函数，则返回了一个接受参数并原样返回的函数；如果传入的函数数组长度大于0，则取出右边的函数为`last`，其余的为`rest`，然后返回一个函数。这个函数用`last(...args)`为高阶函数`reduceRight`迭代的初始值进行迭代，每次迭代的结果将被传入下一个函数充当参数。
> 注：`reduceRight`为es5引入的方法，使用形式如下Array.reduceRight((prev,current,index,array) => (...), initialValue)，它会为数组中的元素从右向左执行回调方法。

举例来说，有三个函数`a,b,c`，参数为args，`f=compose(a,b,c)`，`f(...args)`执行的效果就和`a(b(c(...args)))`一样，目的就是将多个函数组合起来。在redux中像`middleware`、`reducer`等多处使用到了compose。不早了，今天先到这哈😀！

