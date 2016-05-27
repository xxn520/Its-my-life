## 配置ESLint
ESLint 被设计为完全可配置的，这意味着你可以关闭任意规则，仅仅运行基础的句法检查，或混合和匹配捆绑的规则和你习惯的规则来最适合你的项目。这有两种主要的方式来配置 ESLint 。

1. 配置注释 - 直接把配置信息通过 JavaScript 注释的方式写入文件
2. 配置文件 - 使用一个 JavaScript ，JSON or YAML 文件来具体说明整个文件夹和所有它的子文件夹的配置信息。它可以在一个 `.eslintrc.*` 文件或在 `package.json` 文件的 `eslintConfig` 字段，ESLint 将会自动寻找并读取它们，或者你可以通过命令行具体制定配置文件。

有以下几块信息是可以被配置的：

* Environments - 你的脚本被设计运行在什么环境。每个环境将会带有一组预定义的全局变量。
* Globals - 你的脚本执行期间所需的额外的全局变量。
* Rules - 将打开的规则，以及它的错误级别。

ESLint 所有这些选项使你你有了对你的代码细力度的控制力。

## 制定解析器选项
ESLint 允许你去指定你想要支持的 JavaScript 语言选项。默认，ESLint 支持 ES5 的句法。你可以通过覆盖解析器选项的设置去启用 ES6 和 ES7 甚至JSX的支持。

请注意支持 JSX 句法不等同于支持 React 。React 应用了 ESLint 无法识别的更具体的语法。如果你在使用 React 并想要支持 React 语法，我们推荐你使用 `eslint-plugin-react` 。

解析器选项在你的 `.eslintrc.*` 文件的 `parserOptions` 属性中被指定。这个可用的选项如下：

* ecmaVersion - 设置你想要使用的 ECMAScript 的具体版本，可以为3、5（默认）、6或者7。
* sourceType - 设置 **"script"** （默认），如果你的代码 ECMAScript 模块当中则使用 **"module"** 。
* ecmaFeatures - 一个指示你所想要使用的语言特性的对象：
    * globalReturn - 允许 return 表达式在全局作用域当中
    * impliedStrict - 开启严格模式（如果 ecmaVersion 大于等于5）
    * jsx - 启用 JSX
    * experimentalObjectRestSpread -启用对于实验性的对象剩余/展开参数的支持。（重要：这是一个实验特性，将来可能会出现显著的变化。建议你不要写依赖于这一功能的规则，除非你已经愿意去维护其改变时所引起的问题。）

这有一个例子 `.eslintrc.json` 文件:

```
{
    "parserOptions": {
        "ecmaVersion": 6,
        "sourceType": "module",
        "ecmaFeatures": {
            "jsx": true
        }
    },
    "rules": {
        "semi": 2
    }
}
```

设置解析器选项帮助 ESLint 决定什么是一个解析错误。所有语言选项默认是 false 。

## 指定解析器
ESLint 默认使用 `Espree` 作为它的解析器。你可以在你的配置文件当中可选的指定一个不同的解析器，只要它满足以下的要求：

1. 它必须是一个本地安装的npm模块。
2. 它必须有一个 Esprima 兼容接口（它必须导出一个 `parse()` 方法）。
3. 它必须产出 Esprima 兼容的抽象语法树和表意的对象。

注意甚至有了这些兼容性，我们不保证一个外部的解析器将和 ESLint 正确地工作，ESLint将不会解决这些由其他解析器不兼容造成的问题。

你需要使用 `.eslintrc` 文件中的 `parser` 选项来指定一个npm模块作为你的解析器。例如，下面指定使用 Esprima 来代替 Espree ：

```
{
    "parser": "esprima",
    "rules": {
        "semi": "error"
    }
}
```

下列解析器是兼容 ESLint 的：
* Esprima
* Babel-ESLint - 一个 Babel 解析器的包装，使其兼容 ESLint 。

注意当你使用一个习惯的解析器时， `parserOptions` 配置属性仍然是 ESLint 在非默认的ES5环境工作所必须的。解析器通过了所有的 `parserOptions` ，可以也可以不使用它们去决定启用的特性。

## 指定环境
一个环境预定义了全局变量。可用的环境如下：

* browser - 浏览器全局变量。
* node - Node.js 全局变量和 Node.js 作用域。
* commonjs - CommonJS 全局变量和 CommonJS 作用域（只有在使用 Browserify或WebPack产生的浏览器代码中使用）。
* shared-node-browser - Node and Browser公共的全局变量。
* es6 - 启用所有的 ES6 module 所需要的特性。
* worker - web workers 全局变量.
* amd - defines require() and define() as global variables as per the amd spec.
* mocha - 加入 Mocha 测试的所有全局变量。
* jasmine - 加入 Jasmine 测试的所有全局变量，针对1.3和2.0版本。
* jest - Jest 全局变量。
* phantomjs - PhantomJS 全局变量。
* protractor - Protractor 全局变量。
* qunit - QUnit 全局变量。
* jquery - jQuery 全局变量。
* prototypejs - Prototype.js 全局变量。
* shelljs - ShellJS 全局变量。
* meteor - Meteor 全局变量。
* mongo - MongoDB 全局变量。
* applescript - AppleScript 全局变量。
* nashorn - Java 8 Nashorn 引擎全局变量。
* serviceworker - Service Worker 全局变量。
* atomtest - Atom 测试 helper 变量。
* embertest - Ember 测试 helper 变量。
* webextensions - WebExtensions 变量。
* greasemonkey - GreaseMonkey 变量。

这些环境不相互影响，所以你可以同时定义超过一个环境。

Environments 能够在文件当中，在配置文件中或使用命令行的 `--env` 参数指定。

在你的 JavaScript 中使用注释指定环境，使用下面的格式：

```
/*eslint-env node, mocha */
```

他启用了 Node.js 和 Mocha 的环境。

在配置文件当中指定环境，使用 `env` 键并把你需要启用的环境的属性设为 `true` 。例如，下面例子启用了 brower 和 Node.js 环境：

```
{
    "env": {
        "browser": true,
        "node": true
    }
}
```

或者在 `package.json` 文件

```
{
    "name": "mypackage",
    "version": "0.0.1",
    "eslintConfig": {
        "env": {
            "browser": true,
            "node": true
        }
    }
}
```

在 YAML 文件中：

```
---
  env:
    browser: true
    node: true
```

如果你想去使用一个来自插件的环境，确保指定了插件名在 `plugins` 数组，然后使用不带前缀的插件名，其次是斜杠，再其次是环境名。举例来说：

```
{
    "plugins": ["example"],
    "env": {
        "example/custom": true
    }
}
```

在一个 `package.json` 文件中

```
{
    "name": "mypackage",
    "version": "0.0.1",
    "eslintConfig": {
        "plugins": ["example"],
        "env": {
            "example/custom": true
        }
    }
}
```

YAML 文件：

```
---
  plugins:
    - example
  env:
    example/custom: true
```
