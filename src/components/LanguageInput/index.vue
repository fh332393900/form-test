<template>
  <el-tabs type="border-card">
    <el-tab-pane
      v-for="(item, index) in options"
      :key="index"
      :label="item.label"
    >
      <el-input
        v-model="item.text"
        :rows="2"
        type="textarea"
        @input="changeValue"
      ></el-input>
    </el-tab-pane>
  </el-tabs>
  <div v-if="needConfirm">
    <el-button @click="cancel">取消</el-button>
    <el-button type="primary" @click="confirm">确认</el-button>
  </div>
</template>

<script setup lang="ts" name="LanguageInput">
import { ref } from 'vue';
import { languageOptions } from '../type';

interface PropsTypes {
  value: any;
  needConfirm: boolean;
}
type optionsItem = {
  label: string;
  value: any;
  text: string;
}

const props = withDefaults(defineProps<PropsTypes>(), {
  value: {},
  needConfirm: false
});
const emit = defineEmits(['changeValue', 'cancel']);
const options = ref<optionsItem[]>([]);

options.value = languageOptions.map(item => {
  const res: optionsItem = {
    label: item.label,
    value: item.value,
    text: props.value[item.value] || '',
  };
  return res;
});

const changeValue = () => {
  if (!props.needConfirm) {
    emit('changeValue', options)
  }
};
const cancel = () => {
  emit('cancel', options)
}
const confirm = () => {
  emit('changeValue', options)
};
</script>