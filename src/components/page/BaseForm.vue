<template>
    <div>
        <el-form :model="baseForm" ref="baseForm" :inline="options.inline" :label-width="options.labelWidth" :class="options.formClass">
          <el-form-item v-for="(item, index) in items"
            :key="index"
            :label="options.showLabel == false ? '' : item.name"
            :prop="item.key"
            :rules="item.rules == undefined ? defaultRules : item.rules">
            <template v-if="item.type == 'date'">
                  <el-date-picker type="date" v-model="baseForm[item.key]" style="width: 100%;" :placeholder="options.showPlaceholder == false ? '' : item.placeholder"></el-date-picker>
            </template>
            <template v-else-if="item.type == 'daterange'">
                  <el-date-picker type="daterange" v-model="item.daterange" @change="daterangeChange(item)" style="width: 100%;" :placeholder="options.showPlaceholder == false ? '' : item.placeholder" :picker-options="pickerOptions"></el-date-picker>
            </template>
            <template v-else-if="item.type == 'editor'">
                  <quill-editor ref="myTextEditor" v-model="baseForm[item.key]"></quill-editor>
            </template>
            <template v-else-if="item.type == 'number'">
                  <el-input-number v-model="baseForm[item.key]" :controls="false" :placeholder="options.showPlaceholder == false ? '' : item.placeholder"></el-input-number>
            </template>
            <template v-else-if="item.type == 'select'">
                  <el-select v-model="baseForm[item.key]" clearable :placeholder="options.showPlaceholder == false ? '' : item.placeholder">
                      <el-option v-for="option in item.selectOptions"
                          :key="option.value"
                          :label="option.label"
                          :value="option.value">
                        </el-option>
                  </el-select>
            </template>
            <template v-else>
                  <el-input v-model="baseForm[item.key]" :placeholder="options.showPlaceholder == false ? '' : item.placeholder"></el-input>
            </template>
          </el-form-item>
          <el-form-item>
            <el-button  @click="onSubmit('baseForm')">{{ submitName || '确定' }}</el-button>
          </el-form-item>
        </el-form>
    </div>
</template>

<script>
import { quillEditor } from 'vue-quill-editor';//富文本编辑器
Date.prototype.Format = function (fmt) { //时间format 函数
    var o = {
        "M+": this.getMonth() + 1, //月份
        "d+": this.getDate(), //日
        "h+": this.getHours(), //小时
        "m+": this.getMinutes(), //分
        "s+": this.getSeconds(), //秒
        "q+": Math.floor((this.getMonth() + 3) / 3), //季度
        "S": this.getMilliseconds() //毫秒
    };
    if (/(y+)/.test(fmt)) fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
    for (var k in o)
    if (new RegExp("(" + k + ")").test(fmt)) fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
    return fmt;
}
    export default {
        props: ['childFormItems','childFormOptions',"childFormData"],
        data: function(){
            return {
                items: this.childFormItems,
                submitName: this.childFormOptions.submitName,
                defaultRules: this.childFormOptions.defaultRules,
                options: this.childFormOptions,
                pickerOptions: {//搜索区域时间快捷键配置
                  shortcuts: [{
                    text: '最近一周',
                    onClick(picker) {
                      const end = new Date();
                      const start = new Date();
                      start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
                      picker.$emit('pick', [start, end]);
                    }
                  }, {
                    text: '最近一个月',
                    onClick(picker) {
                      const end = new Date();
                      const start = new Date();
                      start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
                      picker.$emit('pick', [start, end]);
                    }
                  }, {
                    text: '最近三个月',
                    onClick(picker) {
                      const end = new Date();
                      const start = new Date();
                      start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
                      picker.$emit('pick', [start, end]);
                    }
                  }]
                }
            }
        },
        components: {
          quillEditor,//富文本组件
        },
        computed: {
            baseForm(){
                return this.childFormData;
            },
            editor() {
                return this.$refs.myTextEditor.quillEditor;
            }
        },
        methods: {
            daterangeChange(item){
                this.baseForm[item.beginkey] = item.daterange[0];
                this.baseForm[item.endkey] = item.daterange[1];
            },
            setFormData(){
                let self = this;
                self.items.filter(function(item) {
                    for (var key in self.baseForm) {
                        if(key == item.key || key == item.beginkey || key == item.endkey) {
                            if(item.type && (item.type == "date" || item.type == "daterange")) {
                                 self.baseForm[key] = new Date(self.baseForm[key]);
                            }
                        }
                    }
                })
            },
            onSubmit(formName) {
                let self = this;
                self.setFormData();
                self.$refs[formName].validate((valid) => {
                  if (valid) {
                      var form = {};
                      self.items.filter(function(item) {
                          for (var key in self.baseForm) {
                              if(key == item.key || key == item.beginkey || key == item.endkey) {
                                  if(item.type == "date" || item.type == "daterange") {
                                       form[key] = new Date(self.baseForm[key]).Format("yyyy-MM-dd");
                                  } else {
                                       form[key] = self.baseForm[key];
                                  }
                              }
                          }
                      })
                      this.baseForm = form;
                      self.$emit('submitCallBack', [form]);
                    //   self.$axios.post(self.childFormOptions.submitUrl, self.baseForm).then((res) => {
                    //     self.$message.success('提交成功！');
                    //   });
                  } else {
                    self.$message('请输入必填项!');
                    return false;
                  }
                });
            }
        }
    }
</script>
