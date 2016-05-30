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

## 指定全局变量
no-undef 规则将会警告将要访问的为定义的变量。如果你使用全局变量在一个文件中，它将是值得的去定义 globals 以至于 ESLint 将不会警告它们的使用。你可以定义全局变量使用文件中的注释或者配置文件。

在你的 JavaScript 文件中指定全局变量，使用下面的格式：

```
/* global var1, var2 */
```

定义两个全局变量， `var1` 和 `var2` 。如果你想要去可选的制定这些全局变量不应当被修改（只读），你可以设置每个全局变量一个 false 标记：

```
/* global var1:false, var2:false */
```

在配置文件中配置全局变量，使用 `globals` 键并指出你想要使用的全局变量。设置全局变量为 `true` 来使其可以被覆盖或制定 `false` 使其只读。举例：

```
{
    "globals": {
        "var1": true,
        "var2": false
    }
}
```

YAML中：

```
---
  globals:
    var1: true
    var2: false
```

这个例子允许 `var1` 可以在代码中覆盖，但 `var2` 不允许。

## 配置插件
ESLint 支持使用第三方的插件。在使用插件前，需要用 npm 将它安装。

在配置文件中配置插件，使用 `plugins` 键，它包含一个插件名的列表。 `eslint-plugin-` 的前缀可以从插件名中省略。

```
{
    "plugins": [
        "plugin1",
        "eslint-plugin-plugin2"
    ]
}
```

YAML中：
```
---
  plugins:
    - plugin1
    - eslint-plugin-plugin2
```

注意：一个全局安装的 ESLint 只能改使用全局安装的 ESLint 插件。本地安装的 ESLint 能够使用本地和全局的 ESLint 插件。

## 配置规则
ESlint 自带了大量的规则。你能够通过注释和配置文件的方式修改你项目中的规则。修改一个规则的设置，你必须设置 rule ID 为下列中的一个值：

* `"off"` 或 `0` - 关闭规则
* `"warn"` 或 `1` - 打开规则起提醒作用（不退出代码执行）
* `"error"` 或 `2` - 打开规则起报错作用（将触发退出代码）

在文件中使用注释配置，使用下面的格式：

```
/* eslint eqeqeq: "off", curly: "error" */
```

在这个例子中 `eqeqeq` 被关闭， `curly` 被打开 error 级别。你也可以使用等价的数字表示规则的级别：

```
/* eslint eqeqeq: 0, curly: 2 */
```

这个例子和上一个例子相同，只是它使用数字编号代替了字符串值。`eqeqeq` 被关闭， `curly` 被打开 error 级别。

如果一个规则有可选的选项，你可以使用数组字面量设置它们，例如：

```
/* eslint quotes: ["error", "double"], curly: 2 */
```

这个注释为 `quotes` 规则指定了两个选项。数组的第一项总是严重的规则（数字或字符串）。

在配置文件配置规则，使用 `rules` 键跟着一个 error 级别和任何选项你想要使用的。例如：

```
{
    "rules": {
        "eqeqeq": "off",
        "curly": "error",
        "quotes": ["error", "double"]
    }
}
```

YAML 文件中：

```
---
rules:
  eqeqeq: off
  curly: error
  quotes:
    - error
    - double
```

配置一个在插件中定义的规则，你必须为 rule ID 加上插件名作为前缀，以及 `/`。举例来说：

```
{
    "plugins": [
        "plugin1"
    ],
    "rules": {
        "eqeqeq": "off",
        "curly": "error",
        "quotes": ["error", "double"],
        "plugin1/rule1": "error"
    }
}
```

YAML 文件中：

```
---
plugins:
  - plugin1
rules:
  eqeqeq: 0
  curly: error
  quotes:
    - error
    - "double"
  plugin1/rule1: error
```

在这个配置文件中，规则 `plugin1/rule1` 来自插件 `plugin1` 。你也能够使用如下格式配置注释的方式，例如：

```
/* eslint "plugin1/rule1": "error" */
```

注意：当指定来自插件的规则，确保省略 `eslint-plugin-` 。ESLint 只使用无前缀的名字来定位内部规则。

## 使用行内注释来禁用规则
使用如下的格式来临时地禁用文件中规则的警告：

```
/* eslint-disable */

// Disables all rules between comments
alert('foo');

/* eslint-enable */
```

你也可以禁用或启用指定的规则：

```
/* eslint-disable no-alert, no-console */

// Disables no-alert and no-console warnings between comments
alert('foo');
console.log('bar');

/* eslint-enable no-alert, no-console */
```

在整个文件中禁用规则警告，把 `/* eslint-disable */` 放到文件顶部：

```
/* eslint-disable */

// Disables all rules for the rest of the file
alert('foo');
```

你也可以在整个文件禁用指定的规则：

```
/* eslint-disable no-alert */

// Disables no-alert for the rest of the file
alert('foo');
```

在指定行禁用所有规则：

```
alert('foo'); // eslint-disable-line

// eslint-disable-next-line
alert('foo');
```

在指定行禁用指定的规则：

```
alert('foo'); // eslint-disable-line no-alert

// eslint-disable-next-line no-alert
alert('foo');
```

指定行禁用多个规则：

```
alert('foo'); // eslint-disable-line no-alert, quotes, semi

// eslint-disable-next-line no-alert, quotes, semi
alert('foo');
```

注意：注释仅仅是禁用了一部分警告，告诉 ESlint 不要报告这部分代码违规。 ESlint 解析整个文件，所以禁用的代码仍然需要符合合法的 JavaScript 语法。

## 添加共享的设置
ESlint 支持向配置文件中添加共享的配置。你能够添加 `settings` 对象到 ESLint 配置文件中，它将会提供给每一个将要执行的规则。这也许是有用的如果你添加了习惯的规则并想要它们能够访问相同的信息并轻松配置。

在 JSON 中：

```
{
    "settings": {
        "sharedData": "Hello"
    }
}
```

在 YAML 文件中：

```
---
  settings:
    sharedData: "Hello"
```

## 使用配置文件
这有两种方式去使用配置文件。第一种是保存文件到你喜欢的地方，然后传递它的地址到 CLI 使用 `-c` 选项，例如：

```
eslint -c myconfig.json myfiletotest.js
```

第二种方式是去使用配置文件通过 `.eslintrc.*` 或 `package.json` 文件。ESLint 将会自动在目录中寻找它们，从连续的父目录一路找到文件系统根目录。这个选项时有用的当你想要对项目的不同部分使用不同的配置，或者当你想要别的直接去使用 ESLint 而不需要记住它在配置文件中传递。

两种方式，配置文件的设置都将会覆盖默认的配置。

## 配置文件格式
ESLint 支持各种格式的配置文件：

* **JavaScript** - 使用 `.eslintrc.js` 并导出一个包含你配置信息的对象。
* **YAML** - 使用 `.eslintrc.yaml` 或 `.eslintrc.yml` 来定义配置结构。
* **JSON** - 使用 `.eslintrc.json` 来定义配置结构。 ESLint's JSON 文件也支持 JavaScript 风格的注释。
* **Deprecated** - 使用 `eslintrc`，过时的，可以是 JSON 或 YAML。
* **package.json** - 创建一个 `eslintConfig` 属性在你的 `package.json`文件并在这里定义你的配置。

如果有多种配置文件在同一个目录，ESLint将会使用一个。它们的优先级如下：

1. `.eslintrc.js`
2. `.eslintrc.yaml`
3. `.eslintrc.yml`
4. `.eslintrc.json`
5. `.eslintrc`
6. `package.json`

## 配置及联和层次结构
当使用 `.eslintrc.*` 和 `package.json` 文件来配置，你可以利用配置级联的优势。举例来说，假设你有下列的目录结构：

```
your-project
├── .eslintrc
├── lib
│ └── source.js
└─┬ tests
  ├── .eslintrc
  └── test.js
```

配置级联将会使用最近的 `.eslintrc` 文件作为最高优先级，然后是任何父级目录的配置文件等。当你在项目中运行 ESLint ，所有 `lib/` 目录下的文件将会使用根目录下的 `.eslintrc` 文件作为它们的配置。如果 ESLint 进入 `test/` 目录，它将会使用 `your-project/tests/.eslintrc` 而不是 `your-project/.eslintrc` 。所以 `your-project/tests/test.js` 的检测是基于目录层次结构中的两个 `.eslintrc` 文件的组合的，近的拥有更高的优先级。这样的方式，你可以拥有项目级别的 ESLint 设置，并可以用目录层次的配置来覆盖它。

相同的方式，在 `package.json` 文件中也可行。根目录中 `package.json` 的 `eslintConfig` 字段将在其所有子目录生效，但是在 tests 目录的 `.eslintrc` 文件将会覆盖所有冲突的配置。

```
your-project
├── package.json
├── lib
│ └── source.js
└─┬ tests
  ├── .eslintrc
  └── test.js
```

如果在一个目录中，同时找到了 `.eslintrc` 和 `package.json` 文件，`eslintrc` 文件将有更高的优先级， `package.json` 文件将不被使用。

注意：如果你又一个个人的配置文件在你的用户目录（ `~/.eslintrc` ），它将会仅仅在没有别的配置文件没发现的情况下使用。它将会对你所有用户目录下的文件生效，包括第三方的代码，因此在运行 ESLint 很可能会产生问题。

默认地， ESLint 将会在所有父目录直到根目录寻找配置文件。这将是有用的，如果你想要所有你的项目去跟随某个约定，但是有时候会导致意想不到的结果。限制 ESLint 在一个指定的项目，把 `"root": true` 加到 `package.json` 的 `eslintConfig` 字段或者 `.eslintrc.*` 文件，在你的根目录。 ESLint 将会停止寻找父目录当它找到了 `"root": true` 的配置。

```
{
    "root": true
}
```
YAML 文件中：

```
---
  root: true
```

举例来说， 这个例子当中 `main.js` 将会使用 `lib/` 中的配置，而不会使用 `productA` 目录下的 `.eslintrc` 文件的配置，因为在 `lib/` 中的 `.eslintrc`  有 `"root": true` 的设置。

```
home
└── user
    ├── .eslintrc <- Always skipped if other configs present
    └── projectA
        ├── .eslintrc  <- Not used
        └── lib
            ├── .eslintrc  <- { "root": true }
            └── main.js
```

完整的配置层次，从高到低如下：

1. 行内配置
    1. `/*eslint-disable*/` and `/*eslint-enable*/`
    2. `/*global*/`
    3. `/*eslint*/`
    4. `/*eslint-env*/`
2. 命令行配置
    1. `--global`
    2. `--rule`
    3. `--env`
    4. `-c, --config`
3. 项目级别配置
    1. `.eslintrc.*` 或 `package.json` 文件
    2. 祖先目录中寻找，除非有 `"root": true` 将不会向上寻找
    3. 个人某人的配置在 `~/.eslintrc`

## 扩展配置文件
如果你想要扩展一个指定的配置文件，你可以使用 `extends` 属性，并制定路径。可以是相对的或是绝对的路径。

配置能够被如下文件扩展：

1. YAML 文件
2. JSON 文件
3. JS 文件
4. 共享的配置包

扩展配置提供了基础的规则，并可以覆盖。举例：

```
{
    "extends": "./node_modules/coding-standard/.eslintrc",

    "rules": {
        // Override any settings from the "parent" configuration
        "eqeqeq": 1
    }
}
```

配置也许也可以以数组的方式提供，后面的会覆盖前面的相同的规则的配置。举例：

```
{
    "extends": [
        "./node_modules/coding-standard/eslintDefaults.js",
        // Override eslintDefaults.js
        "./node_modules/coding-standard/.eslintrc-es6",
        // Override .eslintrc-es6
        "./node_modules/coding-standard/.eslintrc-jsx",
    ],

    "rules": {
        // Override any settings from the "parent" configuration
        "eqeqeq": "warn"
    }
}
```

扩展配置能够包涵它们自己的 `extends` ，导致循环的引用。

你也可以使用共享的配置包。你需要使用 npm 去安装它们，例如：

```
{
    "extends": "eslint-config-myrules",

    "rules": {
        // Override any settings from the "parent" configuration
        "eqeqeq": "warn"
    }
}
```

在这个例子， `eslint-config-myrules` 包将会加载为一个对象并作为这个配置的父配置。

注意：你可以省略 `eslint-config-` 前缀， ESLint 将会自动为你添加，和插件工作相似。

ESlint 也支持插件提供的扩展配置：

```
{
    "extends": "plugin:eslint-plugin-myplugin/myConfig",

    "rules": {
        // Override any settings from the "parent" configuration
        "eqeqeq": "warn"
    }
}
```

在这个例子， `eslint-plugin-myplugin` 包包含了名为 `default` 的配置。

**十分重要**：使用插件中的配置时，必须要加上 `plugin:` 前缀来指明哈。但你可以可选地省略 `eslint-plugin` 前缀。

注意：对于根目录或者其它父目录， `extends` 处理路径会用当前工作目录，而不是文件自身。

## 配置文件中的注释
JSON 和 YAML 配置文件都支持注释， `package.json` 不能包括注释。你可以使用 JAVAScript 风格的注释或 YAML 风格的注释， ESLint 将安全地忽略它们。这可以允许你的配置文件跟友好。举例：

```
{
    "env": {
        "browser": true
    },
    "rules": {
        // Override our default settings just for this directory
        "eqeqeq": "warn",
        "strict": "off"
    }
}
```

## 指定需要检测的文件后缀
当前指定后缀需要在命令行选项 `--ext` 后，添加用空格分开的后缀名列表。

## 忽略文件或目录


你可以告诉 ESLint 去忽略指定的文件或目录，通过一个 `.eslintignore` 文件在你的根目录。 `.eslintignore` 文件是一个普通文本文件，每一行定义了要被忽略的文件或目录。举例：下列表达式将会省略所有 JavaScript 文件：

```
**/*.js
```

当 ESLint 运行的时候，它会寻找当前工作目录的 `.eslintignore` 文件在它决定监测之前。如果找到了文件，这里面的配置将会生效。 `.eslintignore` 只会被使用一次，因此目录中其他的 `.eslintignore` 将不会被使用。

* #好开头将会被认为是注释
* 相对于 `.eslintignore` 文件路径或当前工作目录的路径
* 忽略规则参照 `.gitignore` 规范
* !用来取消前面的忽略的匹配

`/node_modules/*` 和 `/bower_components/*` 将默认被忽略。

举例，把下面的 `.eslintignore` 文件放倒工作目录下将会忽略`/node_modules/*` 和 `/bower_components/*` 目录，任何扩展名为 `.ts.js` 或 `.coffee.js` 可能被转换，任何在 `build/*` 目录除了 `build/index.js` 将被忽略：

```
# /node_modules/* and /bower_components/* ignored by default

# Ignore built files except build/index.js
build/*
!build/index.js
```

## 使用替换的文件
如果你喜欢去使用别的文件而不是 `.eslintignore` 来工作，你在命令行中可以指定 `--ignore-path` 选项。举例来说，你可以使用 `.jshintignore` ，因为两者的配置相同：

```
eslint --ignore-path .jshintignore file.js
```

你也可以使用 `.gitignore` 文件：

```
eslint --ignore-path .gitignore file.js
```

任何标准的 ignore 文件可以被使用。

## 忽略文件警告
当你 eslint 一个被忽略的文件或目录时，会发出警告，你可以使用 `--no-ignore` 来进行省略。
