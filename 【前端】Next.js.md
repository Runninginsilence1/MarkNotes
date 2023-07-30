它的文档非常的好！

我首先回顾了一下原生的JavaScript的编程方式，它是标准的命令式编程。
首先是React的哲学：响应式编程开发速度要快于命令式编程。
两者的区别就好比：前者：我要订购一张披萨，然后🍕就出现在了你的面前。然后怎么处理是你的事情。 而命令式编程需要你从采集材料开始，从0到1的制作一张披萨。

[React](https://beta.reactjs.org/)是一个流行的声明式库，可以帮助开发人员构建用户界面。

它通过jsx（一种组合js和html）的方式来以一种UI人员所熟悉的方式来快速构建页面，而不是使用复杂的命令式过程。

浏览器不能直接理解jsx，所以需要一个js编译器。Babel。

```html
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script type="text/jsx">
      const app = document.getElementById('app');
      // 很明显：接受两个参数：需要展示的标签，父元素。
      ReactDOM.render(<h1>Develop. Preview. Ship. 🚀</h1>, app);
    </script>
```

这样做的话vscode中没有代码高亮。

不过，很好！你已经成功的构建了一个基于React的UI页面。


# React基础
您需要熟悉 React 的三个核心概念
- Componet 组件，代表一个可以复用的，包含样式和逻辑的组件：我喜欢这个说法：就好像乐高的积木一般。
- Prop 
- State 


## Component
### 快速开始
组件化有两个好处，就像一个函数一样：
统一管理，纯JS代码。

首先有一个偏见（尽管我不知道是不是这样）：函数就是组件。
例如下面就是一个返回UI元素的函数：
```html
<script type="text/jsx">
  const app = document.getElementById("app")

  function header() {
     return (<h1>Develop. Preview. Ship. 🚀</h1>)
   }

  ReactDOM.render(header, app)
</script>
```

正常运行？不对。他报错了。

要正确的运行上面的代码，你需要：
React 组件应该大写，以区别于纯 HTML 和 JavaScript。
然后，需要像使用Html标签一样的去使用它。

重构上面的代码：
```html
<script type="text/jsx">
  const app = document.getElementById("app")

// Upper 1st char
  function header() {
     return (<h1>Develop. Preview. Ship. 🚀</h1>)
   }

// Input a tag instead of function;
  ReactDOM.render(<Header />, app)
</script>
```

### 组件嵌套
组件嵌套是必须的。你可以像使用Html一样来完成组件的嵌套。

例如，将Header嵌套进入HomePage中：
```jsx
function Header() {
  return <h1>Develop. Preview. Ship. 🚀</h1>;
}

function HomePage() {
  return (
    <div>
      <Header />
    </div>
  );
}

ReactDOM.render(<HomePage />, app);
```

当然，你可以使用一些更有趣味的，例如如果要实现一个DoubleHeader，可以实现以下的代码：

```jsx
function Header() {
  return <h1>Develop. Preview. Ship. 🚀</h1>;
}

function DoubleHeader() {
  return (
    <div>
      <Header />
      <Header />
    </div>
  );
}

ReactDOM.render(<DoubleHeader />, app);
```

另外，jsx中的注释方式有些不一样了。


一般来说，react中的组件会放在Component文件夹中进行归类，并且每一个组件都会使用`export default`表示该组件可以导出到任意位置进行使用。

并且类似函数的，你的组件不能返回多个标签，你需要用div或者空标签进行包裹。


## Props
Component拥有自己的信息，并且可以根据信息的不同来改变自己的状态。这样，外界就拥有了与他们进行交互的能力。
常规 HTML 元素具有可用于传递更改这些元素行为的信息片段的属性。例如，更改元素`src`的属性`<img>`会更改显示的图像。更改标签`href`的属性`<a>`会更改链接的目标。

同样的，在React中，可以将这些信息传递给组件，并且，与Html相同，他们会被子组件所继承。这被称之为**单向数据流**。

### 数据传递
并且，在父组件中，可以通过类似html attr的方式向里面子组件传输数据；
在jsx中，通过花括号包裹的部分，来解释这是在js的领域中。里面的内容可以是任意一个js表达式：
有意思的是模板文字：
```jsx
function Header({ title }) { // 如果不使用js中的对象结构，则参数为props，或者使用ts的静态类型，来根据class名称来进行匹配
  return <h1>{`Cool ${title}`}</h1>;
}
```

并且，这里牵扯出我的一个疑问：function返回的内容必须包裹在一个根标签中。

### 遍历列表
像在js中一样使用数组。
并且这次的实践向我重复了在react中，必须要了解可以把html的标签当成jsx的一部分来使用。

需要遍历的时候：如下即可：
```jsx
function ArrayUse() {
        const names = ["Ash one", "DragonBorn", "Good Guy Nick"];

        return (
            <div>
                <ul>
                  {names.map(n => (
                    <li>{n}</li>
                  ))}  
                </ul>
            </div>
        )
      }
      ReactDOM.render(<ArrayUse />, app);
```

 在渲染列表的样式时，记得给循环项加上唯一标识，似乎是可以提升性能；
 vue也提到要加上标识。**总之是为了提高性能**

## 添加状态交互与事件处理
首先是事件处理：
例如在HomePage的基础上添加一个按钮，并为他绑定callback函数：

```jsx
function ArrayUse() {
        const names = ["Ash one", "DragonBorn", "Good Guy Nick"];


        function handleClick() {
          console.log("increment like count");
        }
        return (
            <div>
                <ul>
                  {names.map(n => (
                    <li>{n}</li>
                  ))}  
                </ul>

                <button onClick={handleClick}>Like!</button>
            </div>
        )
      }

    
      ReactDOM.render(<ArrayUse />, app);
```

要点在于：指定组件触发的事件；定义callback函数
总结一下：在标签例如`<button onClick={handle}> Button！</button>`中定义事件，然后在jsx的组件内定义回调函数。


React中通过一系列的hooks（俗称的钩子）来在组件的状态（state）发生变化的时候执行某些行为。
他通过`React.useState()`来使用。

state是组件本身的状态信息，可以理解成编程语言中私有成员变量的概念。

state本身没有特别好说的，不过从文档中似乎延伸出来一个更重要的信息：
React里面的hook函数都是类似useXXXX()的形式。

可以认为他们是react内置的功能函数。

## useState()
这个函数用于定义组件的状态：你可以理解成与组件绑定的元信息，并且在他发生变化的时候，自动重新渲染组件。 先暂时只理解这么多。

## useEffect()
俗称副作用函数。 我还没有看过，是凭借ai的回答半知半解的。
这个函数可以将生命周期函数和要调用函数**解耦**。
以下是我和ai的问答：
```
我有点没看懂。我的初步理解是：存在这样一些操作，他们需要在指定的生命周期中执行，正常的使用是在生命周期函数中调用它们。而使用useEffect之后，相当于把重点从生命周期转移到了函数本身上，你只需要定义一个函数，然后指定它会在哪些生命周期的时候就可以了。是这样吗？

2023/7/7 18:17:10

是的，你的理解基本正确。

在传统的类组件中，我们需要根据组件的生命周期来执行一些操作。例如，在 `componentDidMount` 中执行初始化数据请求，在 `componentDidUpdate` 中执行更新操作，在 `componentWillUnmount` 中执行清理操作等。这种方式将生命周期和副作用操作耦合在一起。

而使用 `useEffect`，我们可以将副作用操作与函数组件的定义进行分离，使得代码更加清晰和可读。通过在函数组件中使用 `useEffect`，我们可以在任何需要执行副作用操作的地方定义一个函数，并告诉 React 在哪些生命周期情况下执行该函数。

`useEffect` 的依赖数组参数可以控制何时执行副作用函数。如果依赖数组为空，则副作用函数只会在组件首次渲染后执行一次（相当于 `componentDidMount`）。如果依赖数组中有依赖项，则每当依赖项发生变化时，副作用函数都会被重新执行（相当于 `componentDidUpdate`）。如果希望在组件卸载时执行一些清理操作，可以在副作用函数中返回一个清理函数。

通过使用 `useEffect`，我们可以更灵活地处理副作用操作，不再局限于特定的生命周期方法。这样可以使代码更具可重用性和可维护性，并且提供了更直观、更优雅的方式来处理组件中的副作用。
```


另外关于音趴项目，我发现一点：
组件和对应的方法是分开的，所以对组件进行初始化的操作全部都在useEffect中进行了，根本无法判断是哪个组件的逻辑。
# Next.js




# JS or TS
他们的内容应该在另外的文档上。这里只汇总一些小知识。
## 解构

## vscode在打开tsx文件报错：表达式生成的联合类型过于复杂，无法表示。

需要修改ts的版本，具体看这个博客，我是用最后修改ts的版本实现的。
[解决办法](https://juejin.cn/post/7220331340764168247)

