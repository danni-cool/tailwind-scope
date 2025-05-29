# Tailwind Scope

一个使用 Web Components 为 Tailwind CSS 创建隔离作用域的工具库。它结合了 [Twind](https://twind.style/)（一个 Tailwind CSS-in-JS 解决方案）和 Shadow DOM 来提供样式隔离。

## 目录

- [特点](#特点)
- [安装](#安装)
  - [NPM](#npm)
  - [CDN](#cdn)
- [使用方法](#使用方法)
- [开发](#开发)
- [构建](#构建)
- [工作原理](#工作原理)
- [依赖项](#依赖项)

## 特点

- 🔍 **CSS 隔离作用域**：在 shadow DOM 中使用 Tailwind 类实现真正的样式隔离
- 🎯 **组件特定样式**：只对特定组件应用样式，不影响其他组件
- 🧩 **基于 Web Component**：使用带有 shadow DOM 的自定义元素
- ⚡ **Alpine.js 集成**：包含 Alpine.js 以支持响应式组件
- 🔄 **动态配置**：通过数据属性配置组件

## 安装

### NPM

通过 npm 安装:

```bash
npm install @danni-cool/twind-scope
```

或者通过 pnpm 安装:

```bash
pnpm add @danni-cool/twind-scope
```

### CDN

你也可以直接通过 UNPKG CDN 引入:

```html
<script src="https://unpkg.com/@danni-cool/twind-scope/dist/twind-scope.umd.cjs"></script>
```

或者使用特定版本：

```html
<script src="https://unpkg.com/@danni-cool/twind-scope@0.0.1/dist/twind-scope.umd.cjs"></script>
```

使用 ES 模块方式引入：
```html
<script type="module">
  import TwindScope from 'https://unpkg.com/@danni-cool/twind-scope/dist/twind-scope.js'
</script>
```

## 使用方法

### 基本用法

```html
<twind-scope>
  <h1 class="text-3xl font-bold underline">Hello world!</h1>
</twind-scope>
```

### 带组件属性

```html
<twind-scope data-props='{"type":"hero-image-horizontal", "id":"hero_1"}'>
  <h1 class="text-3xl font-bold underline">Hello world!</h1>
</twind-scope>
```

`data-props` 属性接受一个 JSON 字符串，包含以下属性：

- `type`：给 shadow DOM 中的第一个元素添加类名
- `id`：设置 shadow DOM 中第一个元素的 ID（格式为 `${type}-${id}`）
- `script`：可选的内联 JavaScript，在组件内执行

### 将脚本与 Tailwind 结合使用

#### 组件中的内联脚本

你可以通过在 data-props 中包含 script 属性来添加组件特定的 JavaScript：

```html
<twind-scope data-props='{
  "type": "interactive-card",
  "id": "card_1",
  "script": "
    const button = this.querySelector(\'.btn\');
    button.addEventListener(\'click\', () => {
      button.classList.toggle(\'bg-green-500\');
      button.classList.toggle(\'bg-blue-500\');
    });
  "
}'>
  <div class="p-6 max-w-sm mx-auto bg-white rounded-xl shadow-md">
    <h2 class="text-xl font-medium text-black">交互式卡片</h2>
    <button class="mt-4 px-4 py-2 bg-blue-500 text-white rounded btn">
      切换颜色
    </button>
  </div>
</twind-scope>
```

#### 将 Alpine.js 与 Tailwind 结合使用

[Alpine.js](https://alpinejs.dev/) 是一个轻量级的 JavaScript 框架，提供响应式和声明式功能。Tailwind Scope 自动在每个组件内初始化 Alpine.js，使您能够轻松创建具有隔离样式的交互式 UI 元素。

#### 基本 Alpine.js 示例

```html
<twind-scope>
  <div x-data="{ count: 0 }" class="p-4 bg-white rounded shadow">
    <h2 class="text-lg font-medium mb-2">Alpine.js 计数器</h2>
    <p class="mb-4">当前计数: <span x-text="count" class="font-bold"></span></p>
    <div class="flex space-x-2">
      <button
        @click="count++"
        class="px-3 py-1 bg-blue-500 text-white rounded hover:bg-blue-600"
      >
        增加
      </button>
      <button
        @click="count--"
        class="px-3 py-1 bg-red-500 text-white rounded hover:bg-red-600"
      >
        减少
      </button>
    </div>
  </div>
</twind-scope>
```

#### 基于 Alpine.js 状态切换 Tailwind 类

您可以根据 Alpine.js 状态动态切换 Tailwind 类：

```html
<twind-scope>
  <div x-data="{ isActive: false }" class="p-4 bg-white rounded shadow">
    <div
      @click="isActive = !isActive"
      :class="isActive ? 'bg-blue-100 border-blue-500' : 'bg-gray-100 border-gray-300'"
      class="p-4 border-2 rounded cursor-pointer transition-colors duration-200"
    >
      <h3
        class="font-medium"
        x-text="isActive ? '激活状态' : '未激活状态'"
      ></h3>
      <p
        class="mt-2 text-sm"
        x-text="isActive ? '点击取消激活' : '点击激活'"
      ></p>
    </div>
  </div>
</twind-scope>
```

#### Alpine.js 下拉菜单示例

以下是如何使用 Alpine.js 和 Tailwind 类创建下拉菜单：

```html
<twind-scope>
  <div x-data="{ open: false }" class="relative">
    <button
      @click="open = !open"
      class="px-4 py-2 bg-blue-500 text-white rounded"
    >
      切换下拉菜单
    </button>

    <div x-show="open" class="absolute mt-2 w-48 bg-white rounded shadow-lg">
      <a href="#" class="block px-4 py-2 text-gray-800 hover:bg-blue-100"
        >选项 1</a
      >
      <a href="#" class="block px-4 py-2 text-gray-800 hover:bg-blue-100"
        >选项 2</a
      >
    </div>
  </div>
</twind-scope>
```

#### 复杂示例：带验证的表单

```html
<twind-scope>
  <div
    x-data="{ 
    email: '',
    password: '',
    errors: {},
    validateForm() {
      this.errors = {};
      if (!this.email) this.errors.email = '邮箱不能为空';
      if (!this.password) this.errors.password = '密码不能为空';
      return Object.keys(this.errors).length === 0;
    },
    submitForm() {
      if (this.validateForm()) {
        alert('表单提交成功！');
      }
    }
  }"
    class="max-w-md mx-auto p-6 bg-white rounded shadow-md"
  >
    <h2 class="text-xl font-semibold mb-4">登录表单</h2>

    <div class="mb-4">
      <label class="block text-gray-700 mb-1">邮箱</label>
      <input
        type="email"
        x-model="email"
        class="w-full px-3 py-2 border rounded focus:outline-none focus:ring-2 focus:ring-blue-300"
        :class="errors.email ? 'border-red-500' : 'border-gray-300'"
      />
      <p
        x-show="errors.email"
        x-text="errors.email"
        class="mt-1 text-sm text-red-500"
      ></p>
    </div>

    <div class="mb-6">
      <label class="block text-gray-700 mb-1">密码</label>
      <input
        type="password"
        x-model="password"
        class="w-full px-3 py-2 border rounded focus:outline-none focus:ring-2 focus:ring-blue-300"
        :class="errors.password ? 'border-red-500' : 'border-gray-300'"
      />
      <p
        x-show="errors.password"
        x-text="errors.password"
        class="mt-1 text-sm text-red-500"
      ></p>
    </div>

    <button
      @click="submitForm()"
      class="w-full bg-blue-500 text-white py-2 rounded hover:bg-blue-600 focus:outline-none"
    >
      登录
    </button>
  </div>
</twind-scope>
```

#### 在脚本中访问 Alpine.js

您还可以在组件的脚本中访问 Alpine.js 功能：

```html
<twind-scope data-props='{
  "script": "
    const container = this.querySelector(\'.alpine-container\');

    // 获取元素上的 Alpine.js 数据
    const getData = () => Alpine.$data(container);

    // 注册自定义事件处理程序
    this.querySelector(\'.special-action\').addEventListener(\'click\', () => {
      const data = getData();
      data.count += 10; // 直接更新 Alpine 状态
    });
  "
}'>
  <div x-data="{ count: 0 }" class="alpine-container p-4 bg-white rounded shadow">
    <p class="mb-4">计数: <span x-text="count"></span></p>
    <div class="flex space-x-2">
      <button @click="count++" class="px-3 py-1 bg-blue-500 text-white rounded">
        增加 1
      </button>
      <button class="special-action px-3 py-1 bg-green-500 text-white rounded">
        增加 10
      </button>
    </div>
  </div>
</twind-scope>
```

有关 Alpine.js 功能和指令的更多信息，请参阅[官方 Alpine.js 文档](https://alpinejs.dev/start-here)。

#### 使用 Alpine.js 的全局配置

```html
<script>
  window.TwindScope = {
    config: {
      // Twind 配置
    },
    style: [
      // 样式配置
    ],
    script: [
      // 自定义 Alpine.js 组件或插件
      `
        document.addEventListener('alpine:init', () => {
          Alpine.data('counter', () => ({
            count: 0,
            increment() { this.count++ },
            decrement() { this.count-- }
          }))
        })
      `,
    ],
  }
</script>

<!-- 然后在组件中使用 -->
<twind-scope>
  <div x-data="counter" class="p-4 bg-white rounded shadow">
    <p>计数: <span x-text="count"></span></p>
    <button
      @click="increment()"
      class="px-3 py-1 bg-blue-500 text-white rounded"
    >
      +
    </button>
    <button
      @click="decrement()"
      class="px-3 py-1 bg-red-500 text-white rounded"
    >
      -
    </button>
  </div>
</twind-scope>
```

## 全局配置

你可以在组件加载前通过设置 `window.TwindScope` 全局配置 Twind：

```html
<script>
  window.TwindScope = {
    // Twind 配置
    config: {
      // 自定义主题或其他 Twind 选项
    },
    // 应用到所有 twind-scope 组件的全局样式
    style: [
      // 内联样式或 CSS 文件 URL
      'https://example.com/styles.css',
      'body { margin: 0; }',
    ],
    // 包含在所有 twind-scope 组件中的全局脚本
    script: [
      // 内联脚本或 JS 文件 URL
      'https://example.com/script.js',
      'console.log("Hello from twind-scope!")',
    ],
  }
</script>
```

## 开发

```bash
# 启动开发服务器
pnpm dev

# 构建生产版本
pnpm build
```

## 工作原理

Tailwind Scope 创建了一个名为 `<twind-scope>` 的自定义元素，它：

1. 使用 Shadow DOM 创建一个隔离的 DOM 树
2. 在该 shadow DOM 中应用 Twind（Tailwind CSS-in-JS）
3. 将你的内容移入 shadow DOM
4. 初始化 shadow DOM 中的 Alpine.js（如果存在）
5. 将指定的 type/ID 应用到第一个子元素

这种方法允许你在隔离环境中使用 Tailwind 类，防止应用程序不同部分之间的样式冲突。

## 依赖项

- [@twind/core](https://twind.style/)：核心 Twind 功能
- [@twind/preset-autoprefix](https://twind.style/)：Twind 的自动前缀
- [@twind/preset-tailwind](https://twind.style/)：Twind 的 Tailwind CSS 预设
- [@twind/with-web-components](https://twind.style/)：Twind 的 Web Component 集成
- [Alpine.js](https://alpinejs.dev/)：轻量级 JavaScript 框架用于响应式功能
