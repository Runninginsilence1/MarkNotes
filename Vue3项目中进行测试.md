参考的vue.js官网的指南。
[传送面板已部署](https://cn.vuejs.org/guide/scaling-up/testing.html#unit-testing)

# 什么时候测试？
越早测试越好。越拖到后面测试越困难。

# 测试的类型
- 单元测试，检查给定函数、类或组合式函数的输入是否产生预期的输出或副作用。
- **组件测试**：检查你的组件是否正常挂载和渲染、是否可以与之互动，以及表现是否符合预期。这些测试比单元测试导入了更多的代码，更复杂，需要更多时间来执行。换言之，组件由单元组成，更加复杂。
- 端到端的测试


# 单元测试
Vue中使用某种测试框架（待补充）。
单元测试如下：

```js
// helpers.js
// 返回输入的+1值，除非已经到达最大值
export function increment (current, max = 10) {
  if (current < max) {
    return current + 1
  }
  return current
}
```

在vue3中测试：
```js
// helpers.spec.js
import { increment } from './helpers'

describe('increment', () => {
  test('increments the current number by 1', () => {
    expect(increment(0, 10)).toBe(1)
  })

  test('does not increment the current number over the max', () => {
    expect(increment(10, 10)).toBe(10)
  })

  test('has a default max of 10', () => {
    expect(increment(10)).toBe(10)
  })
})
```

1. 导入待测试函数
2. 在describe函数中进行测试，个人推测descripe通常用于进行一个单元测试，内部包含多个测试用例用来增加覆盖率。
3. 参数1：描述信息，通常用来描述对什么部分进行单元测试；
4. 参数2：函数，内部调用test函数对测试结果进行断言；
5. test内部使用expect来断言。

通常用来描述输入和输出。

如前所述，单元测试通常适用于独立的业务逻辑、组件、类、模块或函数，**不涉及 UI 渲染、网络请求或其他环境问题。**

这是其他语言通用的测试方式。


# vue中的组合式函数和组件
## 什么是组合式函数
个人认为这个对应响应式的概念：
普通的js函数是无响应的，接收一个输入后返回一个无状态的输出；
而组合式可能是函数内部有响应式的对象，然后将响应式的数据返回；

一个鼠标跟踪器的demo：
定义
```js
// mouse.js
import { ref, onMounted, onUnmounted } from 'vue'

// 按照惯例，组合式函数名以“use”开头
export function useMouse() {
  // 被组合式函数封装和管理的状态
  const x = ref(0)
  const y = ref(0)

  // 组合式函数可以随时更改其状态。
  function update(event) {
    x.value = event.pageX
    y.value = event.pageY
  }

  // 一个组合式函数也可以挂靠在所属组件的生命周期上
  // 来启动和卸载副作用
  onMounted(() => window.addEventListener('mousemove', update))
  onUnmounted(() => window.removeEventListener('mousemove', update))

  // 通过返回值暴露所管理的状态
  return { x, y }
}
```

然后在组件中使用：
```js
<script setup>
import { useMouse } from './mouse.js'

const { x, y } = useMouse()
</script>

<template>Mouse position is at: {{ x }}, {{ y }}</template>
```

组合式有蛮多应用的感觉：
我感觉可以封装一个axios的请求：
axios对应就是：
用响应式数据接收原本的函数里面的数据，然后将其暴露给组件；

具体以后去研究一下组合式函数看看。
这种才能是编程之美。


## 怎么进行组件测试

todo： 先搞懂怎么进行组件化开发，再去搞组件的测试；不然容易学习的迷糊；
