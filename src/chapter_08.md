# 深入组件

## 全局注册

- 使用 app.component() 方法，让组件在当前 Vue 应用中全局可用。
- 全局注册的组件可以在此应用的任意组件的模板中使用

```javascript
import MyComponent from './App.vue';
import { createApp } from 'vue';

const app = createApp({});

app.component('MyComponent', MyComponent);
```

### 局部注册

- 在单文件组件中使用了 `<script setup>`，导入的组件会自动进行局部注册

```html
<script setup>
  import ComponentA from './ComponentA.vue';
</script>

<template>
  <ComponentA />
</template>
```

## 组件名格式

- 使用 PascalCase 作为组件名的注册格式

## Prop 声明

使用 defineProps() 宏来定 prop

```html
<script setup>
  const props = defineProps(['foo']);

  console.log(props.foo);
</script>
```

### Prop 名字格式

使用 camelCase 形式

### 传递不同的值类型

#### Number

```html
<!-- 虽然 `42` 是静态的值，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :likes="42" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :likes="post.likes" />
```

#### Boolean

```html
<!-- 仅写上 prop 但不传值，会隐式表达为 `true` -->
<BlogPost is-published />

<!-- 虽然 `false` 是静态的值，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :is-published="false" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :is-published="post.isPublished" />
```

#### Array

```html
<!-- 虽然这个数组是静态不变的值，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost :comment-ids="[234, 266, 273]" />

<!-- 根据一个变量的值动态传入 -->
<BlogPost :comment-ids="post.commentIds" />
```

#### Object

```html
<!-- 虽然这个对象字面量是静态不变的值，我们还是需要使用 v-bind -->
<!-- 因为这是一个 JavaScript 表达式而不是一个字符串 -->
<BlogPost
  :author="{
    name: 'Veronica',
    company: 'Veridian Dynamics'
  }"
/>

<!-- 根据一个变量的值动态传入 -->
<BlogPost :author="post.author" />
```

## 组件事件

### 触发与监听事件

- 组件的模板表达式中，可以直接使用 `$emit` 函数触发自定义事件

```html
<!-- MyComponent -->
<button @click="$emit('someEvent')">click me</button>
```

- 父组件可以通过 `v-on` (缩写为 `@`) 来监听事件：

```html
<MyComponent @some-event="callback" />
```

- 同样，组件的事件监听器也支持 `.once` 修饰符：

```html
<MyComponent @some-event.once="callback" />
```

> 像组件与 prop 一样，事件的名字也提供了自动的转换。请注意，我们触发了一个以 camelCase 形式命名的事件，但在父组件中可以使用 kebab-case 形式来监听。
> 在模板中我们也推荐使用 kebab-case 形式来编写监听器。

### 事件参数

子组件中：

```html
<button @click="$emit('increaseBy', 1)">Increase by 1</button>
```

父组件中

```html
<MyButton @increase-by="(n) => count += n" />
```

> 所有传入 `$emit()` 的额外参数都会被直接传向监听器。

### 声明触发的事件

组件要触发的事件可以显式地通过 defineEmits() 宏来声明。

```html
<script setup>
  const emit = defineEmits(['inFocus', 'submit']);
</script>
```

返回的 emit 函数可以用来在 JavaScript 代码中触发事件。

尽管是**可选的**，我们还是推荐你定义所有要触发的事件，以此更好地在代码中描述和呈现组件的作用。

### 事件校验

```html
<script setup>
  const emit = defineEmits({
    // 没有校验
    click: null,

    // 校验 submit 事件
    submit: ({ email, password }) => {
      if (email && password) {
        return true;
      } else {
        console.warn('Invalid submit event payload!');
        return false;
      }
    },
  });

  function submitForm(email, password) {
    emit('submit', { email, password });
  }
</script>
```

### 作用在组件上的事件

```html
<CustomInput
  :modelValue="searchText"
  @update:modelValue="newValue => searchText = newValue"
/>
```

或

```html
<CustomInput v-model="searchText" />
```

子组件：

```html
<!-- CustomInput.vue -->
<script setup>
  defineProps(['modelValue']);
  defineEmits(['update:modelValue']);
</script>

<template>
  <input
    :value="modelValue"
    @input="$emit('update:modelValue', $event.target.value)"
  />
</template>
```

> - `v-model` 的参数  
>    默认情况下，`v-model` 在组件上都是使用 `modelValue` 作为 prop，以 `update:modelValue` 作为对应的事件。  
>    我们可以通过给 `v-model` 指定一个参数来更改这些名字  
>   `<MyComponent v-model:title="bookTitle" />`
