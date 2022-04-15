# 条件渲染与列表渲染

## `v-if`、 `v-else` 和 `v-else-if`

- `v-if` 指令被用于按条件渲染一个区块。这个区块只会在指令的表达式为真时才被渲染。
- 使用 `v-else` 为 `v-if` 添加一个“else 区块”。
- `v-else-if` 提供的是相应于 `v-if` 的“else if 区块”
- `v-else-if` 的元素必须紧跟在一个 `v-if` 或一个 `v-else-if` 元素后面。
- 一个 `v-else` 元素必须跟在一个 `v-if` 或者 `v-else-if` 元素后面
- `<template>` 元素上也可以使用 `v-if`

```html
<div v-if="type === 'A'">A</div>
<div v-else-if="type === 'B'">B</div>
<div v-else-if="type === 'C'">C</div>
<div v-else>Not A/B/C</div>
```

## `v-show`

- `v-show` 仅切换了该元素上名为 `display` 的 CSS 属性。
- `v-show` 不支持在 `<template>` 元素上使用，也没有 `v-else` 来配合。
- `v-if` 在首次渲染时的切换成本比 `v-show` 更高。因此当你需要非常频繁切换时 `v-show` 会更好，而运行时不常改变的时候 `v-if` 会更合适。

## `v-for`

- `v-for` 指令需要一种特殊的语法形式 `item in items`，其中 `items` 是源数据的数组，而 `item` 是迭代项的别名
- 推荐在任何可行的时候为 `v-for` 提供一个 key attribute

```javascript
const parentMessage = ref('Parent');
const items = ref([{ message: 'Foo' }, { message: 'Bar' }]);
```

```html
<li v-for="item in items">{{ item.message }}</li>

<li v-for="(item, index) in items">
  {{ parentMessage }} - {{ index }} - {{ item.message }}
</li>
```

### `v-for` 遍历对象

```javascript
const myObject = reactive({
  title: 'How to do lists in Vue',
  author: 'Jane Doe',
  publishedAt: '2016-04-10',
});
```

```html
<li v-for="(value, key, index) in myObject">
  {{ index }}. {{ key }}: {{ value }}
</li>
```

### 在 `v-for` 里使用值范围

```html
<span v-for="n in 10">{{ n }}</span>
```

> 注意此处 `n` 的初值是从 `1` 开始而非 `0`。
