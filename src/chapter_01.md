# 响应式基础

## 声明响应式状态

- 响应式对象其实是 JavaScript Proxy，其行为表现与一般对象相似。不同之处在于 Vue 能够跟踪对响应式对象 property 的访问与更改操作。
- 要在组件模板中使用响应式状态，请在 `setup()` 函数中定义并返回。
- 使用 `reactive()` 函数创建一个响应式**对象**或**数组**

```javascript
<template>
  <button @click="increment">
    {{ state.count }}
  </button>
</template>
<script>
import { reactive } from 'vue'

export default {
  // `setup` 是一个专门用于组合式 API 的特殊钩子
  setup() {
    const state = reactive({ count: 0 })

    function increment() {
      state.count++
    }

    // 暴露 state 和 increment 函数到模板
    return {
      state,
      increment
    }
  }
}
</script>
```

## `<script setup>`

- 当使用单文件组件（SFC）时，我们可以使用 `<script setup>` 来简化大量样板代码。
- `<script setup>` 中的顶层的导入和变量声明可在同一组件的模板中自动使用。

```javascript
<script setup>
import { reactive } from 'vue'

const state = reactive({ count: 0 })

function increment() {
  state.count++
}
</script>

<template>
  <button @click="increment">
    {{ state.count }}
  </button>
</template>
```

## DOM 更新时机

- 当你更改响应式状态后，DOM 也会自动更新。然而，你得注意 DOM 的更新并不是同步的。相反，Vue 将缓冲它们直到更新周期的 “下个时机” 以确保无论你进行了多少次声明更改，每个组件都只需要更新一次。

## 响应式代理 vs. 原始对象

- reactive() 返回的是一个原始对象的 Proxy，它和原始对象是不相等的

```javascript
const raw = {};
const proxy = reactive(raw);

// 代理和原始对象不是全等的
console.log(proxy === raw); // false
```

- 只有代理是响应式的，更改原始对象不会触发更新。因此，使用 Vue 的响应式系统的最佳实践是 仅使用你**声明对象的代理版本**。

## reactive() 的局限性

1. 仅对对象类型有效（对象、数组和 Map、Set 这样的集合类型
2. 因为 Vue 的响应式系统是通过 property 访问进行追踪的，因此我们必须始终保持对该响应式对象的相同引用。这意味着我们不可以随意地 “替换” 一个响应式对象：

```javascript
let state = reactive({ count: 0 });

// 这行不通！
state = reactive({ count: 1 });
```

3. 同时这也意味着当我们将响应式对象的 property 赋值或解构至本地变量时，或是将该 property 传入一个函数时，我们会失去响应性：

```javascript
const state = reactive({ count: 0 });

// n 是一个局部变量，同 state.count
// 失去响应性连接
let n = state.count;
// 不影响原始的 state
n++;

// count 也和 state.count 失去了响应性连接
let { count } = state;
// 不会影响原始的 state
count++;

// 该函数接收一个普通数字，并且
// 将无法跟踪 state.count 的变化
callSomeFunction(state.count);
```

## `ref()` 定义响应式变量

- `ref()` 方法来允许我们创建可以使用任何值类型的响应式 ref
- `ref()` 从参数中获取到值，将其包装为一个带 `.value` property 的 ref 对象：

```javascript
const count = ref(0);

console.log(count); // { value: 0 }
console.log(count.value); // 0

count.value++;
console.log(count.value); // 1
```

- ref 可以响应式地替换整个对象

```javascript
const objectRef = ref({ count: 0 });

// 这是响应式的替换
objectRef.value = { count: 1 };
```

- `ref()` 使我们能创造一种任意值的 “引用” 并能够不丢失响应性地随意传递。

## ref 在模板中的解包

- 当 ref 在模板中作为顶层 property 被访问时，它们会被自动“解包”，所以不需要使用 `.value`

```javascript
<script setup>
import { ref } from 'vue'

const count = ref(0)

function increment() {
  count.value++
}
</script>

<template>
  <button @click="increment">
    {{ count }} <!-- 无需 .value -->
  </button>
</template>
```

## 响应性语法糖

不得不对 ref 使用 `.value` 是一个受限于 JavaScript 语言限制的缺点。Vue 提供了一种编译时转换的语法糖：

```javascript
<script setup>
let count = $ref(0)

function increment() {
  // 无需 .value
  count++
}
</script>

<template>
  <button @click="increment">{{ count }}</button>
</template>
```
