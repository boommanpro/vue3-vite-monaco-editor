# Vue3 Vite Monaco Editor

Monaco Editor 官方 rep: https://microsoft.github.io/monaco-editor/

## 整体链路

①创建vite项目

1. nvm use v18.20 确定node版本
2. 安装vite ~  `yarn create vite console --template vue`
3. 查看项目是否正常启动 `yarn run dev`

②安装monaco
1. 安装monaco =>  `yarn add monaco-editor@0.50.0`
2. 创建 `worker.js`
```
import * as monaco from 'monaco-editor';
import EditorWorker from 'monaco-editor/esm/vs/editor/editor.worker?worker';
import JsonWorker from 'monaco-editor/esm/vs/language/json/json.worker?worker';
import CssWorker from 'monaco-editor/esm/vs/language/css/css.worker?worker';
import HtmlWorker from 'monaco-editor/esm/vs/language/html/html.worker?worker';
import TsWorker from 'monaco-editor/esm/vs/language/typescript/ts.worker?worker';

self.MonacoEnvironment = {
    getWorker(_, label) {
        if (label === 'json') {
            return new JsonWorker();
        }
        if (label === 'css' || label === 'scss' || label === 'less') {
            return new CssWorker();
        }
        if (label === 'html' || label === 'handlebars' || label === 'razor') {
            return new HtmlWorker();
        }
        if (label === 'typescript' || label === 'javascript') {
            return new TsWorker();
        }
        return new EditorWorker();
    }
};

monaco.languages.typescript.typescriptDefaults.setEagerModelSync(true);
```
3. main.js 引入 -> `import './worker.js'`
4. 创建组件 MonacoVite.vue 组件实现了双向绑定
```
<template>
  <div :style="{
       height: height+'px',
  width: width+'px'
  }" ref="editorRef"></div>
</template>

<script setup>
import {defineEmits, defineProps, onMounted, ref, toRaw, watch} from 'vue';
import * as monaco from 'monaco-editor/esm/vs/editor/editor.api';

const emits = defineEmits(['update:modelValue']);

const props = defineProps({
  height: {
    type: Number,
    default: 500,
  },
  width: {
    type: Number,
    default: 500,
  },
  modelValue: {
    type: String,
    default: '',
  },
  language: {
    type: String,
    default: 'json',
  },
  theme: {
    type: String,
    default: 'vs-dark',
  }
});

const editorRef = ref(null);
const editorInstance = ref(null);
onMounted(() => {
  if (editorRef.value && !editorInstance.value) {
    editorInstance.value = monaco.editor.create(editorRef.value, {
      value: props.modelValue,
      language: props.language,
      theme: props.theme,
      scrollBeyondLastLine: false,
    });
    editorInstance.value.onDidChangeModelContent((event) => {
      emits('update:modelValue', toRaw(editorInstance.value).getValue());
    });
  }
});


// 监听外部code变化，更新内部状态
watch(() => props.modelValue, (newVal, oldVal) => {
  let currValue = toRaw(editorInstance.value).getValue();
  if (newVal!==currValue){
    toRaw(editorInstance.value).setValue(newVal)
  }
}, {deep: true});

</script>

```
5. 引入组件   <monaco-vite :width="500" :height="500" v-model:="code" language="json"></monaco-vite>
