### Reactjs 的 PropTypes 使用方法

<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="http://music.163.com/outchain/player?type=2&id=35528482&auto=1&height=66"></iframe>

propTypes 使用来规范组件Props的类型

```
 var Test = React.createClass({
  propTypes: {
    // required
    requiredFunc: React.PropTypes.func.isRequired,
    requiredAny: React.PropTypes.any.isRequired,

    // primitives, optional by default
    bool: React.PropTypes.bool,
    func: React.PropTypes.func,
    number: React.PropTypes.number,
    string: React.PropTypes.string,
  },
  render:function(){
  return <div/>
  }
});

var component = React.render(
        <Test requiredFunc="bar" bool="true" requiredAny="a"/>, 
    document.body
);
```

若没有按照规范，会显示警告。

```
React.PropTypes 的种类
React.PropTypes.array           // 数组
React.PropTypes.bool.isRequired // Boolean 且必要。
React.PropTypes.func            // 函数
React.PropTypes.number          // 数字
React.PropTypes.object          // 对象
React.PropTypes.string          // 字符串
React.PropTypes.any            // 任何类型的: numbers, strings, elements等
React.PropTypes.element         // React 元素
React.PropTypes.instanceOf(XXX) // 某种XXX类型的实体
React.PropTypes.oneOf(['foo', 'bar']) // 其中一个字符串
React.PropTypes.oneOfType([React.PropTypes.string, React.PropTypes.array]) // 其中一种类型
React.PropTypes.arrayOf(React.PropTypes.string)  // 某种类型的数组
React.PropTypes.objectOf(React.PropTypes.string) // 某种类型的对象
React.PropTypes.shape({                          // 是否符合指定的格式
  color: React.PropTypes.string,
  fontSize: React.PropTypes.number
});
React.PropTypes.any.isRequired  // 可以是任何格式，且必要。

// 自定义格式(当不符合的时候，会显示Error) 
// 不要用`console.warn` 或者 throw, 因为它在`oneOfType` 的情况下无效。
customPropType: function(props, propName, componentName) {
  if (!/^[0-9]/.test(props[propName])) {
    return new Error('Validation failed!');
  }
}
```

getDefaultProps

当父组件没有提供props的属性时，可以采用getDefaultProps，预设props属性的方式，让元件使用预设的值，确保有props带入。

```
 var ComponentWithDefaultProps = React.createClass({ 
    getDefaultProps : function () { 
        return {
                value : 'default value' 
              }; 
    }, 
    /* ... */ 
});
```

Posted by James Yang March 19, 2015   ReactJS