# 模板语法

- Vue 使用一种基于 HTML 的模板语法。
- 在底层机制中，Vue 会将模板编译成高度优化的 JavaScript 代码。结合响应式系统，当应用状态变更时，Vue 能够智能地推导出需要重新渲染的组件的最少数量，并应用最少的 DOM 操作。

## 文本插值

```html
<span>Message: {{ msg }}</span>
```

双大括号标签会被替换为相应组件实例中 `msg` property 的值。同时每次 `msg` property 更改时它也会同步更新。

## 原始 HTML

若想插入 HTML，你需要使用 `v-html` 指令

```javascript
const rawHtml = '<span style="color: red">This should be red.</span>';
```

```html
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

## Attribute 绑定

`v-bind` 指令指示 Vue 将元素的 `id` attribute 与组件的 `dynamicId` property 保持一致。如果绑定的值是 `null` 或者 `undefined`，那么该 attribute 将会从渲染的元素上移除。

```html
<div v-bind:id="dynamicId"></div>
```

简写:

```html
<div :id="dynamicId"></div>
```

## 使用 JavaScript 表达式

Vue 在所有的数据绑定中都支持完整的 JavaScript 表达式:

```javascript
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div :id="`list-${id}`"></div>
```

## 事件绑定

v-on 指令，它将监听 DOM 事件：

```html
<a v-on:click="doSomething"> ... </a>

<!-- 简写 -->
<a @click="doSomething"> ... </a>
```

## 动态参数

同样在指令参数上也可以使用一个 JavaScript 表达式，需要包含在一对方括号内：

```html
<!--
注意，参数表达式有一些约束，
参见下面“动态参数表达式约束”一节的解释
-->
<a v-bind:[attributeName]="url"> ... </a>

<!-- 简写 -->
<a :[attributeName]="url"> ... </a>

<a v-on:[eventName]="doSomething"> ... </a>

<!-- 简写 -->
<a @[eventName]="doSomething"></a>
```

## 修饰符

修饰符是以点开头的特殊后缀，表明指令需要以一些特殊的方式被绑定。

例如 `.prevent` 修饰符告知 `v-on` 指令对触发的事件调用 `event.preventDefault()`：

```html
<form @submit.prevent="onSubmit">...</form>
```

![指令语法](https://staging-cn.vuejs.org/assets/directive.69c37117.png '完整的指令语法')
