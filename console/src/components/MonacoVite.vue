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
