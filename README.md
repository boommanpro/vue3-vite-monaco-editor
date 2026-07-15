<div align="center">

  <img src="docs/logo.png" alt="Vue3 Vite Monaco Editor Logo" width="120" height="120">

  # Vue3 Vite Monaco Editor

  基于 Vue 3 + Vite 集成 Monaco Editor 的代码编辑器组件

  [![Deploy to GitHub Pages](https://github.com/boommanpro/vue3-vite-monaco-editor/actions/workflows/deploy.yml/badge.svg)](https://github.com/boommanpro/vue3-vite-monaco-editor/actions/workflows/deploy.yml)
  [![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
  [![Vue](https://img.shields.io/badge/Vue-3.4-42b883.svg)](https://vuejs.org/)
  [![Vite](https://img.shields.io/badge/Vite-5-646cff.svg)](https://vite.dev/)
  [![Monaco](https://img.shields.io/badge/Monaco-0.50-orange.svg)](https://microsoft.github.io/monaco-editor/)

  🌐 [English](README-En.md) | **中文**

</div>

## 项目简介

Vue3 Vite Monaco Editor 是一个将 [Monaco Editor](https://microsoft.github.io/monaco-editor/)（VS Code 的核心编辑器）集成到 Vue 3 + Vite 项目中的示例组件。

该组件封装了 Monaco Editor 的常用功能，支持 `v-model` 双向绑定、多语言语法高亮、主题切换，并正确配置了 Web Worker 以确保编辑器在 Vite 环境下正常运行。适用于需要在 Web 应用中嵌入代码编辑器的场景。

## 功能特性

- **v-model 双向绑定** — 组件外部可实时获取编辑器内容，外部修改也能同步到编辑器
- **多语言支持** — 支持 JSON、JavaScript、TypeScript、CSS、HTML 等 Monaco 全部语言
- **主题切换** — 支持 `vs-dark`、`vs-light` 等 Monaco 内置主题
- **Web Worker 配置** — 正确配置 EditorWorker、JsonWorker、CssWorker、HtmlWorker、TsWorker
- **尺寸自定义** — 通过 `width` / `height` 属性灵活控制编辑器尺寸

## 演示效果

在线演示：**https://boommanpro.github.io/vue3-vite-monaco-editor/**

![演示截图](docs/screenshot.png)

## 技术栈

| 技术 | 说明 |
| --- | --- |
| Vue 3 | 前端框架（Composition API） |
| Vite 5 | 构建工具与开发服务器 |
| Monaco Editor 0.50 | VS Code 核心编辑器 |
| Yarn | 包管理工具 |

## 快速开始

### 环境要求

- Node.js >= 18
- Yarn

### 安装依赖

```sh
cd console
yarn install
```

### 开发模式

```sh
yarn dev
```

启动后访问 http://localhost:5173 即可查看。

### 构建生产版本

```sh
yarn build
```

构建产物输出至 `console/dist/` 目录。

### 本地预览构建产物

```sh
yarn preview
```

## 组件使用

### 安装 Monaco Editor

```sh
yarn add monaco-editor@0.50.0
```

### 配置 Web Worker

创建 `worker.js`，配置各语言的 Worker：

```js
import * as monaco from 'monaco-editor';
import EditorWorker from 'monaco-editor/esm/vs/editor/editor.worker?worker';
import JsonWorker from 'monaco-editor/esm/vs/language/json/json.worker?worker';
import CssWorker from 'monaco-editor/esm/vs/language/css/css.worker?worker';
import HtmlWorker from 'monaco-editor/esm/vs/language/html/html.worker?worker';
import TsWorker from 'monaco-editor/esm/vs/language/typescript/ts.worker?worker';

self.MonacoEnvironment = {
    getWorker(_, label) {
        if (label === 'json') return new JsonWorker();
        if (label === 'css' || label === 'scss' || label === 'less') return new CssWorker();
        if (label === 'html' || label === 'handlebars' || label === 'razor') return new HtmlWorker();
        if (label === 'typescript' || label === 'javascript') return new TsWorker();
        return new EditorWorker();
    }
};

monaco.languages.typescript.typescriptDefaults.setEagerModelSync(true);
```

在 `main.js` 中引入：`import './worker.js'`

### 使用组件

```vue
<template>
  <monaco-vite :width="500" :height="500" v-model="code" language="json" />
</template>

<script setup>
import { ref } from 'vue'
import MonacoVite from './components/MonacoVite.vue'

const code = ref('{}')
</script>
```

### 组件属性

| 属性 | 类型 | 默认值 | 说明 |
| --- | --- | --- | --- |
| `modelValue` | String | `''` | 编辑器内容，支持 v-model |
| `language` | String | `'json'` | 语言模式 |
| `theme` | String | `'vs-dark'` | 主题 |
| `width` | Number | `500` | 宽度（px） |
| `height` | Number | `500` | 高度（px） |

## 部署

### GitHub Pages

本项目已配置 GitHub Actions 自动部署至 GitHub Pages，推送到 `main` 分支即会自动构建并部署。

#### 手动配置说明

1. 进入仓库 **Settings → Pages**
2. Source 选择 **GitHub Actions**
3. 推送代码到 `main` 分支，Actions 将自动构建部署

## License

[MIT](LICENSE)
