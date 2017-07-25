<template>
    <div>
        <el-form :model="baseForm" ref="baseForm" :inline="options.inline" :label-width="options.labelWidth" :class="options.formClass">
          <el-form-item v-for="(item, index) in items"
            :key="index" :label="item.name" :prop="item.key"
            :rules="item.rules == undefined ? defaultRules : item.rules">
            <template v-if="item.type == 'date'">
                  <el-date-picker type="date" v-model="baseForm[item.key]" style="width: 100%;" :placeholder="item.placeholder"></el-date-picker>
            </template>
            <template v-else-if="item.type == 'editor'">
                  <quill-editor ref="myTextEditor" v-model="baseForm[item.key]"></quill-editor>
            </template>
            <template v-else-if="item.type == 'select'">
                  <el-select v-model="baseForm[item.key]" clearable :placeholder="item.placeholder">
                      <el-option v-for="option in item.selectOptions"
                          :key="option.value"
                          :label="option.label"
                          :value="option.value">
                        </el-option>
                  </el-select>
            </template>
            <template v-else>
                  <el-input v-model="baseForm[item.key]" :placeholder="item.placeholder"></el-input>
            </template>
          </el-form-item>
          <el-form-item>
            <el-button  @click="onSubmit('baseForm')">{{ submitName }}</el-button>
          </el-form-item>
        </el-form>
    </div>
</template>

<script>
import { quillEditor } from 'vue-quill-editor';//富文本编辑器
    export default {
        props: ['childFormItems','childFormOptions'],
        data: function(){
            return {
                items: this.childFormItems,
                submitName: this.childFormOptions.submitName,
                defaultRules: this.childFormOptions.defaultRules,
                options: this.childFormOptions,
                submitUrl: this.childFormOptions.submitUrl,
                baseForm: {}
            }
        },
        components: {
          quillEditor,//富文本组件
        },
        computed: {
          editor() {
            return this.$refs.myTextEditor.quillEditor;
          }
        },
        methods: {
            onSubmit(formName) {
                this.$refs[formName].validate((valid) => {
                  if (valid) {
                      this.$axios.post(this.submitUrl, this.baseForm).then((res) => {
                        this.$message.success('提交成功！');
                      });
                  } else {
                    this.$message('error submit!!');
                    return false;
                  }
                });

            }
        }
    }
</script>
