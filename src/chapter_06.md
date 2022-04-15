# 生命周期钩子和侦听器

## 生命周期钩子

![生命周期图示](https://staging-cn.vuejs.org/assets/lifecycle.16e4c08e.png '生命周期图示')

- Vue 会自动将注册的回调函数与当前活动组件实例相关联。这就要求这些钩子在组件设置时同步注册。不能放在异步函数里面（如`setTimeout()`）

### 注册钩子

```javascript
<script setup>
  import {onMounted} from 'vue' onMounted(() =>
  {console.log(`the component is now mounted.`)})
</script>
```

## 侦听器

- 有时候我们需要运行些“副作用”：例如更改 DOM，或者根据异步操作的结果，去修改另一处的状态。
- 我们可以使用 `watch` 函数在每次响应式状态发生变化时触发回调函数

```html
<script setup>
  import { ref, watch } from 'vue';

  const question = ref('');
  const answer = ref('Questions usually contain a question mark. ;-)');

  // 可以直接侦听一个 ref
  watch(question, async (newQuestion, oldQuestion) => {
    if (newQuestion.indexOf('?') > -1) {
      answer.value = 'Thinking...';
      try {
        const res = await fetch('https://yesno.wtf/api');
        answer.value = (await res.json()).answer;
      } catch (error) {
        answer.value = 'Error! Could not reach the API. ' + error;
      }
    }
  });
</script>

<template>
  <p>
    Ask a yes/no question:
    <input v-model="question" />
  </p>
  <p>{{ answer }}</p>
</template>
```

### 侦听来源类型

watch 的第一个参数可以是不同形式的“来源”：它可以是一个 `ref` (包括计算属性)、一个响应式对象、一个 getter 函数、或多个来源组成的数组：

```javascript
const x = ref(0);
const y = ref(0);

// 单个 ref
watch(x, (newX) => {
  console.log(`x is ${newX}`);
});

// getter 函数
watch(
  () => x.value + y.value,
  (sum) => {
    console.log(`sum of x + y is: ${sum}`);
  }
);

// 多个来源组成的数组
watch([x, () => y.value], ([newX, newY]) => {
  console.log(`x is ${newX} and y is ${newY}`);
});
```

注意，你不能侦听响应式对象的 property，例如:

```javascript
const obj = reactive({ count: 0 });

// 这不起作用，因为你是向 watch() 传入了一个 number
watch(obj.count, (count) => {
  console.log(`count is: ${count}`);
});
```

而是用 getter 函数：

```javascript
// 提供一个 getter 函数
watch(
  () => obj.count,
  (count) => {
    console.log(`count is: ${count}`);
  }
);
```

### `watchEffect()`

`watch()` 是懒执行的：仅在侦听源变化时，才会执行回调。但在某些场景中，我们希望在创建侦听器时，立即执行一遍回调。

```javascript
const url = ref('https://...');
const data = ref(null);

async function fetchData() {
  const response = await fetch(url.value);
  data.value = await response.json();
}

// 立即获取
fetchData();
// ...再侦听 url 变化
watch(url, fetchData);
```

`watchEffect()` 会立即执行一遍回调函数，如果这时函数产生了副作用，Vue 会自动追踪副作用的依赖关系，自动分析出响应源。

```javascript
watchEffect(async () => {
  const response = await fetch(url.value);
  data.value = await response.json();
});
```
