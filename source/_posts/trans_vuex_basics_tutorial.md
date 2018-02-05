title: 【译】vuex 基础：教程和说明
date: 2017-06-04 15:00:00
categories: front-end
tags: vuex
---

原文地址：[Vuex basics: Tutorial and explanation](https://skyronic.com/2016/01/03/vuex-basics-tutorial)

作者注：[2016.11 更新]这篇文章是基于一个非常旧的 vuex api 版本而写的，代码来自于2015年12月。
但是，它仍能针对下面几个问题深入探讨：
1. vuex 为什么重要
2. vuex 如何工作
3. vuex 如何使你的应用更容易维护

vuex 是 vue.js 作者开发的一个原型库，它帮助你创建更大、维护性更强的应用，类似于 Facebook 的 flux 库（以及由社区维护的 redux 库）。
这篇文章不直接跳到 vuex 教你如何使用它，而是从背后的故事开始说起，逐步解释它为什么是优雅的替代方法，以及将如何帮助你。

译者注：[a git repo of vuex-tutorial use vue2.0](https://github.com/letica/vuex-tutorial)
<!-- more -->

## 你想要创建什么应用？

![target](/img/trans_vuex20170531/target.png)

一个拥有`按钮`和`计数器`的简单应用，点击按钮计数器加1。这听起来非常容易理解和完成。

![problem](/img/trans_vuex20170531/problem.dot.png)

我们假设这个应用有两个组件：
1. 按钮 （它是事件的来源）
2. 计数器 （它必须按照事件来反映更新）

这两个组件不知道彼此的存在，也不能相互通信。即使是在最小的 web 应用中，这也是一种非常常见的模式。在更大点儿的应用中，十几个组件相互通信，并时刻关注对方的变化。不相信我？这里是一个基础的 TODOlist 应用的交互清单：

![todo](/img/trans_vuex20170531/todo.dot.png)

## 这篇文章的目标

我们将讨论解决同一个问题的3种方法：
1. 组件之间使用事件广播来通信
2. 使用一个共享的状态对象通信
3. 使用 vuex 通信

读完这篇文章，希望你能理解：
1. 在你的项目中使用 vuex 的一个基本工作流程
2. 它解决了哪些问题
3. 相对其他方法，为什么它是更好的（尽管有些冗长和严格）

## 准备工作

我们将使用3种不同的方法来解决同一个问题。在这之前，需要做一些共同的准备工作。如果你打算跟着我做，我建议你为这个教程创建一个 git repo，这一小节结束后提交一次代码，然后为不同的方法创建不同的分支。

```
$ npm install -g vue-cli
$ vue init webpack vuex-tutorial
$ cd vuex-tutorial
$ npm install
$ npm install --save vuex
$ npm run dev
```

现在你应该能看到 vue 的脚手架页面了，下面来为我们要做的事来修改一些文件。

首先，在文件 `src/components/IncrementButton.vue` 中创建 `IncrementButton` 组件：

```
<template>
    <button @click.prevent="activate">+1</button>
</template>

<script>
export default {
    methods: {
        activate () {
            console.log('+1 Pressed')
        }
    }
}
</script>

<style>
</style>
```

下一步，在文件 `src/components/CounterDisplay.vue` 中创建 `CounterDisplay` 组件来展示计数：

```
<template>
    Count is {{ count }}
</template>

<script>
export default {
    data () {
        return {
            count: 0
        }
    }
}
</script>

<style>
</style>
```

使用下面的内容替换 `App.vue`：

```
<template>
    <div id="app">
        <h3>Increment:</h3>
        <increment></increment>
        <h3>Counter:</h3>
        <counter></counter>
    </div>
</template>

<script>
import Counter from './components/CounterDisplay.vue'
import Increment from './components/IncrementButton.vue'

export default {
    components: {
        Counter,
        Increment
    }
}
</script>

<style>
</style>
```

现在，重新运行 `npm run dev`，在浏览器打开页面，你应该看到一个 `按钮` 和一个 `计数器`。点击按钮，控制台将显示一条信息，其它没什么变化。
现在我们已经来到了起点，开始吧。

## 方法1：事件广播

![solution1](/img/trans_vuex20170531/solution1.dot.png)

来修改组件的代码。
首先在 `IncrementButton.vue` 中，在按钮被点击时使用 `$dispatch` 给父组件发送一个消息。

```
export default {
    methods: {
        activate () {
            // Send an event upwards to be picked up by App
            this.$dispatch('button-pressed')
        }
    }
}
```

在 `App.vue` 中监听来自子组件的这个消息事件，然后广播一个新的事件 `increment` 给所有的子组件：

```
export default {
    components: {
        Counter,
        Increment
    },
    events: {
        'button-pressed': function () {
            // Send a message to all children
            this.$broadcast('increment')
        }
    }
}
```

在 `CounterDisplay.vue` 中，监听 `increment` 事件，并增加状态数据中的变量：

```
export default {
    data () {
        return {
            count: 0
        }
    },
    events: {
        increment () {
            this.count++
        }
    }
}
```

**这个方法的缺点：**

这个方法基本没有什么技术上的错误。此外，在一个文件里实现整个应用的逻辑，专门使用 goto 来跳转也没有错。这只与可维护性有关，这里会讲一下为什么这个方法在可维护性上是糟糕的。

1. 对于每一个操作，父组件都需要将事件分发给正确的组件；
2. 在大型应用中，可能很难理解事件是从哪儿来的；
3. 业务逻辑没有明确的位置。`this.count++` 是在 `CounterDisplay` 中，但业务逻辑可能到处都是，这会导致难以维护。

**让我来举例说明一下这个方法会怎样导致bug：**

1. 你雇了两个实习生： Alice 和 Bob。你告诉 Alice 你需要为另外一个组件实现另一个计数器，告诉 Bob 写一个重置按钮；
2. Alice 写了一个新的组件 `FormattedCounterDisplay`，它能够监听增量，并增加自己的状态数据。Alice 开心的提交了代码；
3. Bob 写了一个新的 `Reset` 组件，它向应用发出一个 `reset` 事件，并重新分发它。他在 `CounterDisplay` 中将 count 重置为0，但是他没有意识到 Alice 的组件也订阅了这个变化；
4. 你的用户点击 `+1` 按钮后看到应用工作正常。但是当他点击 `重置` 按钮，只有一个计数器被重置了。这看起来是一个非常简单的例子，仅仅为了说明状态和业务逻辑绑在一起可能会导致错误。


## 方法2： 共享状态

撤销方法1中的改动，创建一个新文件 `src/store.js`：

```
export default {
    state: {
        counter: 0
    }
}
```

首先修改 `CounterDisplay.vue`：

```
<template>
    Count is {{ sharedState.counter }}
</template>

<script>
import store from '../store'

export default {
    data () {
        return {
            sharedState: store.state
        }
    }
}
</script>
```

**这里我们做了一些有趣的事情：**

1. 获取到一个 store 对象，它仅仅是一个对象常量，但是在不同的文件中定义的；
2. 在本地数据中，我们创建了一个叫 `sharedState` 的数据，它映射到 `store.state`；
3. vue 使用 `store.state` 作为当前组件的一部分数据，这意味着 `store.state` 有任何变化，vue 都会自动更新 `sharedState`。

到目前为止它还不能工作，现在我们来修改 `IncrementButton.vue`：

```
import store from '../store'

export default {
    data () {
        return {
            sharedState: store.state
        }
    },
    methods: {
        activate () {
            this.sharedState.counter += 1
        }
    }
}
```

1. 在这里，我们引入 `store`，并像之前的例子一样监听了数据的状态变化；
2. 当 `activate` 方法被调用时，指向 `store.state` 的 `sharedState` 的计数器 counter 增加；
3. 监听了计数器的所有组件和计算属性都会被更新。

### 它为什么比方法1更好

我们来回顾一下两个实习生 Alice 和 Bob 的问题：

1. Alice 写的用来监听共享数据的 `FormattedComponentDisplay` 组件将会始终显示最新的 counter 数据；
2. Bob 的重置按钮组件将共享数据的 counter 置为0，这将同时影响 `CounterDisplay` 和 Alice 写的 `FormattedCounterDisplay`；
3. 重置按钮符合预期。

### 为什么这样仍然不够好

1. 在 Alice 和 Bob 的实习期内，他们使用不同的格式写了许多计数器、重置按钮，以及增量按钮，它们更新的是同一份共享的数据，生活很美好；
2. 一旦他们回到学校，你需要维护他们的代码；
3. 新任经理 Carol 进来之后说：“我不想看到计数器的数字超过100”

### 你现在该做什么？

1. 你去十几个组件的代码里找到所有更新数据的地方吗？这让人沮丧；
2. 你找到显示数据的地方然后添加一个 `filter/formatter` 来格式化数据吗？这同样让人沮丧；
3. 这里就是这个问题，业务逻辑分散在应用的各个角落，原则上一个很简单的问题，但是维护和调试起来却特别痛苦。

### 稍好一点儿的方法

现在来重构你的代码，重写 `store.js` 如下：

```
var store = {
    state: {
        counter: 0
    },
    increment: function () {
        if (store.state.counter < 100) {
            store.state.counter += 1;
        }
    },
    reset: function () {
        store.state.counter = 0;
    }
}

export default store
```

显式调用 `increment` 并将所有业务逻辑都放进 `store` 后代码看起来清晰了许多。然而，一个新实习生不知道这背后的理论，他发现在应用的其他部分直接写入 `store.state.counter` 更容易，于是一切变得难于调试。
然后，你制定大量严格的规则和代码审查，以确保没有人在 `store.js` 中不使用函数的情况下修改状态数据。如果这都不起作用，那你可以告诉hr结束他的实习了。

## 方法3：vuex

回滚方法2里的修改，原则上 vuex 的工作原理与方法2有些相似。给你看一张稍稍有些可怕的图：

![solution3](/img/trans_vuex20170531/solution3.dot.png)

首先来创建 `src/store.js`，这次用下面的代码：

```
import Vuex from 'vuex'
import Vue from 'vue'

Vue.use(Vuex)

var store = new Vuex.Store({
    state: {
        counter: 0
    },
    mutations: {
        INCREMENT (state) {
            state.counter++
        }
    }
})

export default store
```

**现在来看看这段代码做了什么：**

1. 获取 Vuex 模块，然后使用 `Vue.use` 安装这个插件；
2. `store` 不再是一个普通的 JSON 对象，而是 `Vuex.Store` 的一个实例；
3. 在 `state` 中创建一个计数器 `counter`，设置为0；
4. 创建一个新的变异对象，包含 `INCREMENT` 方法：获取一个状态数据，然后改变它。

**看看这段代码里有哪些有趣的东东：**

1. 所有通过 `require('../store.js')` 或 `import store from '../store.js'` 引入的 `store` 将使用同一个 store 实例；
2. 我们不会修改 `store.state.counter`，但是我们有一份 `state` 的拷贝用来做修改，这在接下来会很重要。

现在我们已经改好了 store，来继续修改 `IncrementButton.vue`：

```
import store from '../store'

export default {
    methods: {
        activate () {
            store.dispatch('INCREMENT')
        }
    }
}
```

这个组件没有任何数据，但是点击的时候调用 `store.dispatch('INCREMENT')`，一会儿再返回来看。

下面更新一下 `CounterDisplay.vue`：

```
<template>
    Count is {{ counter }}
</template>

<script>
import store from '../store'

export default {
    computed: {
        counter () {
            return store.state.counter
        }
    }
}
</script>
```

事情从这儿才真正有趣！我们不再订阅共享的状态数据的变化，而是使用 vue 的计算属性来给 counter 同步 store 中的数据。
Vue 足够聪明来计算出基于 `store.state.counter` 的计算属性 `counter`，无论 store 何时被更新，它将更新所有的关联项。That's it!

**如果你刷新这个页面，你将看到计数器依然正确工作。下面将逐步解释发生了什么：**

1. vue 的事件处理函数是 `activate`，这个方法调用了 `store.dispatch('INCREMENT')`；
2. 在这里，`INCREMENT` 是一个动作的名称。它表示 “这是 state 应该做出的那种改变”。我们还可以传递额外的其他参数给分发函数；
3. vue 指明了分发事件时应该调用哪个函数。现在我们只有一个，但是我们可以为大型应用定制的更复杂；
4. 这个函数接收状态数据的拷贝，并对它进行更新。vue 保留一份旧数据的拷贝用于后续的高级功能；
5. 当状态更新之后，vue 自动更新所有组件；
6. 这些使得你的代码可测试性更强，如果你做了这些的话。

### 这里是比办法2更好的原因

假如在开发过程中所有状态的拷贝都被保存下来，vue 开发者建立起所谓的“时间旅行调试器”是非常有可能的。除了一个听起来超酷的超级英雄的名字，它将允许你在应用中撤销行为、改变逻辑，以及开发的更快。
只要状态改变，你就可以构建中间件。例如，你可以创建一个 logger 来记录用户执行的所有操作。如果他们发现了一个bug，你可以获取到用户日志，重新播放所有的行为，并正确的重现他们的bug。
通过强制你在一个地方（store）进行所有的动作，这是一个很好的参考，你团队中的每一个人都可以使用你应用中所有修改状态数据的方法。

### 还有很长的路要走

这里仅仅接触到了 vuex 表面可以做的事情，它自身仍然是一个早期版本，我相信这将成为未来许多年里最成熟的模式之一。
你可以去网上找到关于如何组织 store 以及 vuex 文档的更多信息。你可能需要花一些时间来理解所有的概念，甚至可能需要一些尝试和错误才能找出正确的方法。

## 结语：处理实习生的代码

你将应用移植到 vue.js，你的实习生仍旧可以找到方法在自己的组件中重写 `store.state.counter`。你明白的，这是最后一根稻草。然后继续在你的 `store.js` 中增加一行代码：

```
var store = new Vuex.Store({
  state: {
    counter: 0
  },
  mutations: {
    INCREMENT (state) {
      state.counter++
    }
  },
  strict: true // Vuex's patent pending anti-intern device
})
```

现在无论何时何人直接修改 store，将会抛出一个错误。请注意这会减慢你的应用运行的时间，这个配置可以在生产环境移除，相关示例请查文档。
