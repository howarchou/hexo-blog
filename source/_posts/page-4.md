---
title: Eslint入门介绍
date: 2020-04-21 11:01:55
tags:
---


#### Lint 的含义

- 提供编码规范；
- 提供自动检验代码的程序，并打印检验结果：告诉你哪一个文件哪一行代码不符合哪一条编码规范，方便你去修改代码

#### Eslint 的含义
Lint 是检验代码格式工具的一个统称，具体的工具有 Jslint 、 Eslint 等等 ...........

#### 使用 Eslint

创建项目并安装依赖包
`npm init` 指令会在项目根目录下生成 package.json 文件。

```
mkdir lint_test
cd lint_test
npm install
npm install eslint --save-dev
```

--save-dev 会把 eslint 安装到 package.json 文件中的 devDependencies 属性中，意思是只是开发阶段用到这个包，上线时就不需要这个包了。


#### 设置 package.json 文件

打开 package.json 文件，修改 script 属性如下：
```
"scripts": {
    "test": "react-scripts test --env=jsdom",
    "lint": "eslint src",
    "lint:create": "eslint --init"
}
```

- scripts 属性的意思是脚本，使用方法是在 cmd 窗口中输入 npm run 指令 的形式，如：`npm run lint:create`；
- "lint:create": "eslint --init" 这个脚本是为了生成 .eslintrc.js 文件;
- "lint": "eslint src"  Lint 自动检验 src 目录下所有的 .js 文件。

#### 创建 .eslint.js 文件
```
 npm run lint:create
```
创建完成后根目录下应该会出现 .eslintrc.js 文件

#### 创建代码并校验代码


```
npm run lint

```
#### 设置 --fix 参数
 "lint": "eslint src --fix", 加上 --fix 参数，是 Eslint 提供的自动修复基础错误的功能。
 
####  配置文件讲解

```
// .eslintrc.js 
module.exports = {
    "env": {
        "browser": true,
        "commonjs": true,
        "es6": true
    },
    "extends": "eslint:recommended",
    "parserOptions": {
        "ecmaFeatures": {
            "experimentalObjectRestSpread": true,
            "jsx": true
        },
        "sourceType": "module"
    },
    "plugins": [
        "react"
    ],
    "rules": {
        "indent": [
            "error",
            "tab"
        ],
        "linebreak-style": [
            "error",
            "windows"
        ],
        "quotes": [
            "error",
            "double"
        ],
        "semi": [
            "error",
            "always"
        ]
    }
}

```

##### rules

ESLint 附带有大量的规则，修改规则应遵循如下要求：

- "off" 或 0 - 关闭规则
- "warn" 或 1 - 开启规则，使用警告级别的错误：warn (不会导致程序退出)
- "error" 或 2 - 开启规则，使用错误级别的错误：error (当被触发的时候，程序会退出)

#### 全局变量配置
如使用 window 对象，默认情况下会报 no-undef 的错误，需要在 .eslintrc 中进行相应配置。
```
{
    "rules": {
        // ...
    },
    "globals": {
        "window": true
    }
}

```

#### 单行跳过 lint 校验
在实际编码时，可能会出现以下的代码。
```
const apple = "apple";
const balana = "balana";

module.exports = {
    fruit: balana ;
}

```
在最上面定义了两个变量，在底部的配置文件中只可能用到其中一个变量，另一个用不到的在 eslint 校验时就会报错 no-unused-vars 的错误，意思是变量定义了但是没有被用到。

其中一种解决方案是在 .eslintrc 文件中配置 rules `no-unused-vars: 0`，意思是项目中不检验变量定义未使用这条规则。强烈不建议这样做，这个规则十分有用，可以规避编写代码时遗漏的变量。

另一种解决方案就是使用行内注释跳过对 apple 和 balana 两个变量跳过 eslint 校验，只影响这两个变量，不影响外部。

```
const apple = "apple";  // eslint-disable-line
const balana = "balana";  // eslint-disable-line
  
module.exports = {
    fruit: balana ;
}

```

此时使用 eslint 校验时就不会再报错了。
#### 常见规则含义解释

- `object-shorthand` 设置该规则，表示对象属性要简写。
```
var foo = {x: x};    // 会报错
var bar = {a: function () {}};  // 会报错
var foo = {x};    // 不会报错
var bar = {a () {}};    // 不会报错
```

- `prefer-arrow-callback` 要求回调函数使用箭头函数

```
// 回调函数，函数的参数是个函数，这个参数函数就是回调函数
funciton bar () {}   
// 不是回调函数，不会报错
// setTimeout 的第一个参数就是回调函数，不用箭头函数会报错
setTimeout(() => {
    // .......
}, 1000)
```
- `no-param-reassign` 禁止对函数的参数重新赋值

```
function bar ({ data = {} }) {
    data.num = 12;      // 会报错
}
/* 
虽然报错，又不想要将该校验关闭，如果代码中只有 data 这个属性有这种情形，
可以在 .eslintrc.js 中的 rules 属性中添加配置（可忽略 data 属性做此校验）：
'no-param-reassign': ['error', { 'props': true, 'ignorePropertyModificationsFor': ['data'] }],
'no-param-reassign': 0,  // 关闭该属性校验
*/

```


- `no-trailing-spaces` 禁止行尾空格

- `no-shadow` 禁止变量声明与外层作用域的变量同名

```
function sum (num) {
  let num = 2;    // 报错，因为 num 变量作为参数已经申明过了
}

```

#### 常用的几个规则

```
"quotes": [1, "single"],            # 单引号
"quote-props":[2, "as-needed"],     # 双引号自动变单引号
"semi": [2, "never"],               # 一行结尾不要写分号
"comma-dangle": [1,"always-multiline"]  # 对象或数组多行写法时，最后一个值加逗号

```
