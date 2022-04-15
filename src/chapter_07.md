# 模板 ref 和 组件

## 访问模板 ref

```html
<script setup>
  import { ref, onMounted } from 'vue';

  // 声明一个 ref 来存放该元素的引用
  // 必须和模板 ref 同名
  const input = ref(null);

  onMounted(() => {
    input.value.focus();
  });
</script>

<template>
  <input ref="input" />
</template>
```

## 组件

### 定义一个组件

将 Vue 组件定义在一个单独的 .vue 文件中，这被叫做单文件组件 (简称 SFC)：

### 使用组件

- 要使用一个子组件，我们需要在父组件中导入它。假设我们把计数器组件放在了一个叫做 ButtonCounter.vue 的文件中，这个组件将会以默认导出的形式被暴露给外部。
- 通过 `<script setup>`，导入的组件都在模板中直接可用。
- 在单文件组件中，推荐为子组件使用 PascalCase 的标签名，以此来和原生的 HTML 元素作区分。

```html
<script setup>
  import ButtonCounter from './ButtonCounter.vue';
</script>

<template>
  <h1>Here is a child component!</h1>
  <ButtonCounter />
</template>
```

### 传递 props

- `defineProps` 是一个仅 `<script setup>` 中可用的编译宏命令，并不需要显式地导入

```html
<!-- BlogPost.vue -->
<script setup>
  defineProps(['title']);
</script>

<template>
  <h4>{{ title }}</h4>
</template>
```

- 声明的 props 会自动暴露给模板。`defineProps` 会返回一个对象，其中包含了可以传递给组件的所有 props，因此我们在 JavaScript 中使用：

```javascript
const props = defineProps(['title']);
console.log(props.title);
```

### 监听事件

- 通过 `v-on` 或 `@` 来选择性地监听子组件上抛的事件
- 子组件可以通过调用内置的 `$emit` 方法，通过传入事件名称来抛出一个事件
- 子组件中通过 `defineEmits` 宏来选择性地声明需要抛出的事件

```html
<!-- App.vue -->
<script setup>
  import { ref } from 'vue';
  import BlogPost from './BlogPost.vue';

  const posts = ref([
    { id: 1, title: 'My journey with Vue' },
    { id: 2, title: 'Blogging with Vue' },
    { id: 3, title: 'Why Vue is so fun' },
  ]);

  const postFontSize = ref(1);
</script>

<template>
  <div :style="{ fontSize: postFontSize + 'em' }">
    <BlogPost
      v-for="post in posts"
      :key="post.id"
      :title="post.title"
      @enlarge-text="postFontSize += 0.1"
    ></BlogPost>
  </div>
</template>
```

```html
<!-- BlogPost.vue -->
<script setup>
  defineProps(['title']);
  defineEmits(['enlarge-text']);
</script>

<template>
  <div class="blog-post">
    <h4>{{ title }}</h4>
    <button @click="$emit('enlarge-text')">Enlarge text</button>
  </div>
</template>
```

- 和 `defineProps` 类似，`defineEmits` 仅可用于 `<script setup>` 之中，并且不需要导入，返回的 `emit` 函数可以被用于在 JavaScript 代码中抛出事件

```javascript
const emit = defineEmits(['enlarge-text']);

emit('enlarge-text');
```

### 动态组件

- 当使用 `<component :is="...">` 来在多个组件间作切换时，组件会在被切换掉后卸载。我们可以通过 `<KeepAlive>` 组件强制不活跃的组件仍然保持“存活”的状态。

```html
<script setup>
  import Home from './Home.vue';
  import Posts from './Posts.vue';
  import Archive from './Archive.vue';
  import { ref } from 'vue';

  const currentTab = ref('Home');

  const tabs = {
    Home,
    Posts,
    Archive,
  };
</script>

<template>
  <div class="demo">
    <button
      v-for="(_, tab) in tabs"
      :key="tab"
      :class="['tab-button', { active: currentTab === tab }]"
      @click="currentTab = tab"
    >
      {{ tab }}
    </button>
    <component :is="tabs[currentTab]" class="tab"></component>
  </div>
</template>
```
