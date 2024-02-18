<template>
  <el-form
    ref="ruleFormRef"
    :model="form"
    :rules="rules"
    label-width="120px"
    class="demo-ruleForm"
    status-icon
  >
    <el-form-item label="问题类型" prop="type">
      <el-select v-model="form.type" clearable placeholder="请选择问题类型">
        <el-option
          v-for="(item, index) in questionOptions"
          :key="index"
          :label="item.label"
          :value="item.value"
        />
      </el-select>
    </el-form-item>
    <el-form-item label="问题题目" prop="questionTitle">
      <LanguageInput :value="form.questionTitle" @changeValue="changeTitle"></LanguageInput>
    </el-form-item>
    <el-form-item label="问题选项" prop="questionOptions">
      <el-button type="success" @click="addOption">添加选项</el-button>
      <div
        v-for="(item, index) in form.questionOptions"
        :key="index"
      >
        <div class="option">
          <span>{{ item.zh }}</span>
          <div class="option-btn">
            <el-button type="primary" @click="item.visible = true">编辑</el-button>
            <el-button type="danger" @click="deleteOption(index)">删除</el-button>
          </div>
        </div>
        <LanguageInput
          v-if="item.visible"
          :value="item"
          :need-confirm="true"
          @changeValue="(options) => changeOptions(item, options, ruleFormRef)"
          @cancel="item.visible = false"
        ></LanguageInput>
      </div>
    </el-form-item>
    <div class="form-btn">
      <el-button @click="cancel">取消</el-button>
      <el-button type="primary" @click="submitForm(ruleFormRef)">确认</el-button>
    </div>
  </el-form>
</template>

<script setup lang="ts" name="FormTest">
import { reactive, ref } from 'vue';
import type { FormInstance, FormRules } from 'element-plus'
import { questionOptions, languageOptions } from '../type';
import LanguageInput from '../LanguageInput/index.vue';

type LanguageTypes = 'zh' | 'en' | 'id' | 'es-mx';

interface Form {
  type: string;
  questionTitle: Record<LanguageTypes, any>;
  questionOptions: Record<LanguageTypes | 'visible', any>[];
}

const form = reactive<Form>({
  type: '',
  questionTitle: {
    zh: '',
    en: '',
    id: '',
    'es-mx': '',
  },
  questionOptions: [],
});
const ruleFormRef = ref<FormInstance>()
const rules = reactive<FormRules<Form>>({
  type: [{ required: true, message: '请选择问题类型', trigger: 'change' }],
  questionTitle: [{ required: true, validator: validateQuestionTitle, trigger: 'change' }],
  questionOptions: [{ required: true, validator: validateQuestionOptions, trigger: 'change' }],
});

const cancel = () => {};
const changeTitle = (options) => {
  options.value.forEach(item => {
    form.questionTitle[item.value] = item.text
  });
};
const addOption = () => {
  form.questionOptions.push({
    zh: '',
    en: '',
    id: '',
    'es-mx': '',
    visible: true,
  });
};
const deleteOption = (index) => {
  form.questionOptions.splice(index);
};
const changeOptions = (item, options, formEl: FormInstance | undefined) => {
  item.visible = false;
  options.value.forEach(option => {
    item[option.value] = option.text;
  });
  formEl?.validateField('questionOptions')
};
const submitForm = (formEl: FormInstance | undefined) => {
  if (!formEl) return;
  formEl.validate((valid) => {
    if (!valid) {
      return;
    }
    const params = Object.assign({}, form);
    params.questionOptions.forEach(item => {
      delete item.visible;
    });
    console.log(params, 'params');
  });
};
function validateQuestionTitle(rule: any, value: any, callback: any) {
  Object.keys(form.questionTitle).forEach(key => {
    const keyLabel = languageOptions.find(item => item.value === key)?.label;
    if (!form.questionTitle[key]) {
      callback(new Error(`${keyLabel}不能为空`));
    }
  })
  callback()
}
function validateQuestionOptions(rule: any, value: any, callback: any) {
  if (!form.questionOptions.length) {
    callback(new Error(`问题选项不能为空`));
  }
  form.questionOptions.forEach(item => {
    Object.keys(item).forEach(key => {
      const keyLabel = languageOptions.find(option => option.value === key)?.label;
      if (!item[key] && key !== 'visible') {
        callback(new Error(`${keyLabel}不能为空`));
      }
    });
  });
  callback()
}
</script>

<style scoped>
.option {
  display: flex;
  align-items: center;
  justify-content: space-between;
}
.option-btn {
  margin-bottom: 5px;
}
.form-btn {
  text-align: right;
}
</style>