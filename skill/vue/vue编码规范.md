# VUE-编码规范

### Vue 组件
#### 组件文件
* 只要有能够拼接文件的构建系统，就把每个组件单独分成文件。
   `例：`

   ```html
   components/
   |- TodoList.vue
   |- TodoItem.vue
   ```

#### 组件命名
* 遵循大驼峰命名规则 `TodoList.vue`
* 应用特定样式和约定的基础组件 (也就是展示类的、无逻辑的或无状态的组件) 应该全部以一个特定的前缀开头，比如 Base、App 或 V。
`例：`

   ```html
   components/
   |- TodoList.vue
   |- TodoItem.vue

   |- AppButton.vue
   |- AppTable.vue
   |- AppIcon.vue

   |- VButton.vue
   |- VTable.vue
   |- VIcon.vue
   ```
* 只应该拥有单个活跃实例的组件应该以 `The` 前缀命名，以示其唯一性(而是每个页面只使用一次。这些组件永远不接受任何 prop)
  `例：`

   ```html
   components/
   |- TheHeading.vue
   |- TheSidebar.vue
   ```
* 和父组件紧密耦合的子组件应该以父组件名作为前缀命名。
`例：`

  `不推荐`（防止文件的名字相同）
  ``` html
  components/
  |- TodoList/
    |- Item/
      |- Button.vue
    |- Item.vue
  |- TodoList.vue
  ```

  `推荐`
   ```html
   components/
   |- TodoList.vue
   |- TodoListItem.vue
   |- TodoListItemButton.vue
   ```

#### 组件 props 原子化

通过 props 属性，尽量只使用 JavaScript 原始类型（字符串、数字、布尔值）和函数。尽量避免复杂的对象。

* 使得组件 API 清晰直观。
* 只使用原始类型和函数作为 props 使得组件的 API 更接近于 HTML(5) 原生元素。
* 其它开发者更好的理解每一个 prop 的含义、作用。
* 传递过于复杂的对象使得我们不能够清楚的知道哪些属性或方法被自定义组件使用，这使得代码难以重构和维护。

`例：`

```html
<!-- 推荐 -->
<range-slider
  :values="[10, 20]"
  min="0"
  max="100"
  step="5"
  :on-slide="updateInputs"
  :on-end="updateResults">
</range-slider>
```

#### 验证组件的 props

* 验证组件props可以保证你的组件永远是可用的（防御性编程）。即使其他开发者并未按照你预想的方法使用时也不会出错。
* 使用 props 之前先检查该 prop 是否存在。

`例：`

```javascript
<template>
  <input type="range" v-model="value" :max="max" :min="min">
</template>
<script type="text/javascript">
  export default {
    props: {
      max: {
        type: Number, // 这里添加了数字类型的校验
        default() { return 10; },
      },
      status: {
        type: String,
        required: true,
        validator: function (value) {
          return [
            'syncing',
            'synced',
            'version-conflict',
            'error'
          ].indexOf(value) !== -1
        }
      }
    }
  };
</script>
```

### 组件结构化

* 导出一个清晰、组织有序的组件，使得代码易于阅读和理解。同时也便于标准化。
* 按首字母排序 properties、data、computed、watches 和 methods 使得这些对象内的属性便于查找。
* 合理组织，使得组件易于阅读。（name; extends; props, data 和 computed; components; watch 和 methods; lifecycle methods 等）。
* 使用 `name` 属性，方便测试。
* 使用单文件 .vue 文件格式来组件代码。

`例：`

```html
<template lang="html">
  <div class="Ranger__Wrapper">
    <!-- ... -->
  </div>
</template>

<script type="text/javascript">
  export default {
    // 添加 name 属性
    name: 'RangeSlider',
    // 组合其它组件
    extends: {},
    // 组件属性、变量
    props: {
      bar: {}, // 按字母顺序
      foo: {},
      fooBar: {},
    },
    // 变量
    data() {},
    computed: {},
    // 使用其它组件
    components: {},
    // 方法
    watch: {},
    methods: {},
    // 生命周期函数
    beforeCreate() {},
    mounted() {},
  };
</script>

<style scoped>
  .Ranger__Wrapper { /* ... */ }
</style>
```

---

## VUE优化

#### v-pre 指令

显示原始插值标签
跳过大量没有指令的节点以降低编译时间
编写较复杂的 Vue.js 模板时可以适当使用 v-pre 指令，提升编译效率。


#### v-cloak指令

这个指令保持在元素上直到关联实例结束编译。和 CSS 规则
如
```css
[v-cloak]  { display: none }
```
一起用时，这个指令可以隐藏未编译的 Mustache 标签直到实例准备完毕。


#### v-once 指令

对低开销的静态组件使用 v-once
当组件中包含大量静态内容时，可以考虑使用 v-once 将渲染结果缓存起来
v-once

#### v-on 指令 以 { passive: true } 模式添加侦听器 （提升页面滑动的流畅度）
详细见：[移动端Web界面滚动性能优化: Passive event listeners](http://blog.csdn.net/shenlei19911210/article/details/70198771)


#### keep-alive
* include - 字符串或正则表达式。只有匹配的组件会被缓存。
* exclude - 字符串或正则表达式。任何匹配的组件都不会被缓存。
* [vue实现前进刷新，后退不刷新](https://www.jianshu.com/p/0b0222954483)


#### watch实例方法
1. **dee选项：**

   在选项参数中指定 deep: true, 发现对象内部值的变化（如：监听vuex数据变化），。监听数组的变动不需要这么做。
2. **immediate**:
   在选项参数中指定 immediate: true, 将立即以表达式的当前值触发回调

#### key使用
1. **如果一组 v-if + v-else 的元素类型相同，最好使用 key (比如两个 <div> 元素)。：**

`例：`
```html
<div
  v-if="error"
  key="search-status"
>
  错误：{{ error }}
</div>
<div
  v-else
  key="search-results"
>
  {{ results }}
</div>
```

2. **总是用 key 配合 v-for。**:
`例：`
```html
<ul>
  <li
    v-for="todo in todos"
    :key="todo.id"
  >
    {{ todo.text }}
  </li>
</ul>
```

