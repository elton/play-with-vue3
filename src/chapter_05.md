# 表单输入绑定

## `v-model`

```html
<input :value="text" @input="event => text = event.target.value" />
```

`v-model` 指令帮我们简化了这一步骤：

```html
<input v-model="text" />
```

## 多个复选框

```javascript
const checkedNames = ref([]);
```

```html
<div>Checked names: {{ checkedNames }}</div>

<input type="checkbox" id="jack" value="Jack" v-model="checkedNames" />
<label for="jack">Jack</label>

<input type="checkbox" id="john" value="John" v-model="checkedNames" />
<label for="john">John</label>

<input type="checkbox" id="mike" value="Mike" v-model="checkedNames" />
<label for="mike">Mike</label>
```

## 选择器

```html
<div>Selected: {{ selected }}</div>

<select v-model="selected">
  <option disabled value="">Please select one</option>
  <option>A</option>
  <option>B</option>
  <option>C</option>
</select>
```

> - 如果 v-model 表达式的初始值不匹配任何一个选择项，`<select>` 元素会渲染成一个“未选择”的状态。
> - 在 iOS 上，这将导致用户无法选择第一项，因为 iOS 在这种情况下不会触发一个 change 事件。因此，我们建议提供一个空值的禁用选项，如上面的例子所示。

## 修饰符

### `.lazy`

添加 `lazy` 修饰符来改为在每次 `change` 事件后更新数据：

```html
<!-- 在 "change" 事件后同步更新而不是 "input" -->
<input v-model.lazy="msg" />
```

### `.number`

- 把用户输入自动转换为数字
- 如果该值无法被 parseFloat() 处理，那么将返回原始值。
- `.number` 修饰符会在输入框有 `type="number"` 时自动应用。

```html
<input v-model.number="age" />
```

### `.trim`

自动去除用户输入内容中两端的空格

```html
<input v-model.trim="msg" />
```
