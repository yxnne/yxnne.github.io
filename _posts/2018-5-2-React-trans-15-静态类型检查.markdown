---
layout: post
title:  React-官网学习-Advanced-Guides3-静态类型检查
date:   2018-5-2
categories: react_learnning
tags: [translations_React.js]
---
<big>静态类检查器</big>就像Flow或者TypeScript在运行前就定义了确定的问题类型。同时，他们也能添加自动补全以改善开发者的工作流程。这点上来说，我们推荐大项目使用flow或者typeScript代替proptypes。

#### Flow
Flow，是js静态类型检查器。它是Facebook开发的并且常用于React。它通过在变量，函数以及组件上的注解，来在早期暴露问题。你可以阅读Flow的介绍来学习它[introduction of Flow](https://flow.org/en/docs/getting-started/)。

要使用Flow，你需要：
* 添加Flow的依赖到项目中；
* 保证Flow的语法与编译过的代码脱离；
* 添加注解，运行Flow检查他们；

##### 给项目添加Flow
首先，从终端到你的项目下，你需要运行：
使用Yarn，运行：
```
yarn add --dev flow-bin
```
使用npm 运行：
```
npm install --save-dev flow-bin
```
这些命令保证最新版本的Flow安装到你的项目中。
现在，添加flow到package.json的script部分，确保它能在终端运行起来：
```
{
  // ...
  "scripts": {
    "flow": "flow",
    // ...
  },
  // ...
}
```
最后，运行下面命令中的一个：
Yarn的话用这个：
```
yarn run flow init
```
npm运行这个：
```
npm run flow init
```
这个命令会创造一个需要你确认的Flow的配置文件。

##### 将Flow从编译代码中剥离
Flow通过一种特殊的类型注解扩展了js的语法，但是浏览器可不认识这种语法，所有我们需要确保他们最终不会被打包到发送给浏览器的js bundle编译文件中。

**Create React App**

如果你的项目使用Creact React App构建起来的，恭喜，Flow的注解已经存在并默认脱离，这样你就在这一步不需要做任何事情了。

**Babel**
>注意：这里的介绍可不是给使用create react app 的用户的。即使create react app 底层是使用了babel，它依旧配置好Flow了。没有用create react app的用户，一步一步看这里：

如果你是人工配置babel的，你需要安装一个特殊的Flow preset。

Yarn：
```
yarn add --dev babel-preset-flow
```
npm:
```
npm install --save-dev babel-preset-flow
```
接着，添加flow的preset到babel 的配置中，如果你用.babelrc配置babel的话，就像这样：
```
{
  "presets": [
    "flow",
    "react"
  ]
}

```
这会让你在代码中使用Flow 的语法。
>注意：Flow是不需要React的，但是他们经常在一起使用，Flow自己理解JSX也非常好。

**其他安装**
如果你既没有使用create react app 也没有使用babel的话，你可以使用[flow-remove-types ](https://github.com/flowtype/flow-remove-types)。

##### 添加Flow类型注解
默认的，Flow只检查使用了下面注解的文件：
```
// @flow
```
通常，这个在文件顶部。试试添加它到项目文件中并允许yarn flow 或者 npm run flow ，看看是否Flow准备好了去发现问题。

当然，这里也有一个强制检查所有文件的设置[选择](https://flow.org/en/docs/config/options/#toc-all-boolean)，这种情况就忽略这个注解了。这对于老项目来说太乱了，但是，对于新项目，要是你想完整的使用flow规范项目是很合理的。

现在，一切就绪！我们推荐跟着下面的资源来学习Flow：

* [Flow Documentation: Type Annotations](https://flow.org/en/docs/types/)

* [Flow Documentation: Editors](https://flow.org/en/docs/editors/)

* [Flow Documentation: React](https://flow.org/en/docs/react/)

* [Linting in Flow](https://medium.com/flow-type/linting-in-flow-7709d7a7e969)

#### TypeScript

TypeScript是一种微软开发的编程语言，是一种JavaScript的超集，并且有自己的编译器。作为强类型语言，TypeScript能在构建时发现错误和bug，早在APP上线之前。你能学到更多关于用TypeScript写React的知识，[这里](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter)。

要使用TypeScript，你需要：
* 添加TypeScript的依赖；
* 配置TypeScript编译器选项；
* 使用正确的文件扩展;
* 添加你使用的库的定义；
来，我们看看这些细节：

##### 添加TypeScript到项目

都是从终端命令开始：
yarn,
```
yarn add --dev typescript
```
或者npm
```
npm install --save-dev typescript
```
很好，你已经安装最新版本的TypeScript到你的项目中了，安装了TypeScript，我们就能用tsc命令了，让我们在配置前，先添加tsc命令到package.json中script区域中：
```
{
  // ...
  "scripts": {
    "build": "tsc",
    // ...
  },
  // ...
}
```

##### 配置TypeScript的编译器
在我们告诉编译器要它做什么之前，它真的毫无用处。在TypeScript中，这些规则定义在文件tsconfig.json中。使用这个命令生成该文件：
```
tsc --init
```
看看现在生成tsconfig.json，你会发现你可以用那么多配置选项来配置编译器。[这里](https://www.typescriptlang.org/docs/handbook/tsconfig-json.html)是关于选项的详细的描述。

众多的选项中，我们关注rootDir和outDir。它真实的方式是将TypeScript文件生成JavaScript文件，但是，我们并不想将源文件和产生的输出文件弄混。
我们将分两步来分配他们的位置：
* 首先，我们将我们的项目目录编程这样。我们将用src放置源文件：
```
├── package.json
├── src
│   └── index.ts
└── tsconfig.json

```

* 接着，我们将要告诉编译器哪里是源文件的地方，哪里是输出文件的地方：

```
// tsconfig.json

{
  "compilerOptions": {
    // ...
    "rootDir": "src",
    "outDir": "build"
    // ...
  },
}
```
很好，现在起，当我们运行build脚本时，编译器将会输出JavaScript文件到build目录下。[TypeScript React Starter](https://github.com/Microsoft/TypeScript-React-Starter/blob/master/tsconfig.json)提供了供你开始一些不错的规则参考，在一个tsconfig.json中。
通常情况下，你无需将生产的JavaScript添加到版本控制中，所以，记得给你的.gitignore里面添加下build目录。

##### 文件扩展名
React中，你大多数见到的是组件写在.js的文件中，在TypeScript中，你有两种扩展：
.ts是默认扩展名，同时，.tsx是应用于那些包含了jsx的文件。

##### 运行TypeScript
如果你一路跟着指导到这里，你应该可以运行TypeScript了：
```
yarn build
```
npm,
```
npm run build
```
要是没什么输出（控制台），这就表示顺利完成了。

##### 类型定义

为了能显示错误和建议，编译器需要依赖声明文件。声明文件提供了一个库的所有类型信息。这使得我们使用JavaScript库就像那些库在npm中一样。
有两种主要的方式得到库的声明：

**Bundled**：这是库自身的声明文件。我们只需要安装这些库，并且正确的使用它们。检查一个库，是否包含了bundled types,看看项目目录下的index.d.ts文件。一些库h会把它在package.json文件的typings或types域中指定。

**定义类型DefinitelyTyped**定义类型是一个庞大的仓库来定义那些没有bundle的库.这些定义是来自群众（众包）并有微软管理的开源者们。比方说，React没有Bundle声明文件。但是我们可以通过DefinitelyTyped获取。在你的终端敲下下面命令：
```
# yarn
yarn add --dev @types/react

# npm
npm i --save-dev @types/react
```

**本地声明**:有些你想用的库，既没有bundled声明，也没有可用的定义类型。这种情形下，就得靠我们自己写本地声明文件了。新建一个eclarations.d.ts文件在你的根目录。一个简单的声明就像这样：
```
declare module 'querystring' {
  export function stringify(val: object): string
  export function parse(val: string): object
}
```

##### 在create react app 中使用 TypeScript

react-scripts-ts自动配置create-react-app项目来支持TypeScript,这样用：
```
create-react-app my-app --scripts-version=react-scripts-ts
```
注意，这是一个第三方项目，它不是create-react-app的一部分。

你也可以试试[typescript-react-starter](https://github.com/Microsoft/TypeScript-React-Starter#typescript-react-starter)。

现在你可以准备编码了，我们推荐你看看下面这些资源来学习更多关于TypeScript的东西：

* [TypeScript Documentation: Basic Types](https://www.typescriptlang.org/docs/handbook/basic-types.html)
* [TypeScript Documentation: Migrating from Javascript](http://www.typescriptlang.org/docs/handbook/migrating-from-javascript.html)
* [TypeScript Documentation: React and Webpack](http://www.typescriptlang.org/docs/handbook/react-&-webpack.html)

#### Reason 

Reason不是一种语言；它是一种由经受考验的OCaml语言驱动的新的语法和工具链。Reason提供了OCaml熟悉的语法来面向JavaScript开发者，并迎合现有的npm/yarn工作流。

Reason在Facebook发展，并且应用在其他的产品中，比如Messager。它依旧是实验性的，但是有专门的React绑定并由Facebook维护，以及充满活力的社区支持。

#### Kotlin

Kotlin是由JetBrains开发的静态强类型语言。它的面向平台是JVM，Android和LLVM和JavaScript。

JetBrains给React社区开发和维护了若干工具：React Bindings 和 Creact React Kotlin App。 后者帮你构件一个不用你配置的Kotlin开发的ReactApp。

#### 其他语言

注意，还有其他静态类型语言能编译成js并且和React 兼容。如elmish-react，F#/Fable。去看看他们各自的网站来获得更多信息，并可以任意编辑这一页来添加更多能用React的静态类型语言。

[官网文章 Advanced Guides :Static Type Checking](https://reactjs.org/docs/static-type-checking.html)


