<template>
    <div>
        <div style="margin: 20px">
          <el-button @click="newRow">新建</el-button>
        </div>
        <el-table :data="tableData" :stripe="options.stripe" style="width: 100%"  @selection-change="handleSelectionChange">
          <el-table-column type="selection"></el-table-column> <!-- 勾选框 -->
          <el-table-column v-for="(column, index) in columns" v-if="column.useType%2==0" :key="index" :prop="column.key" :label="column.name" :width="column.width">
          </el-table-column>
          <el-table-column :label="tableActions.name" :width="tableActions.width" :fixed="tableActions.fixed">
              <template scope="scope">
                  <el-button  size="small" @click="edit(scope.$index, scope.row)">编辑</el-button>
                  <!-- <el-button  size="small" @click="delete(scope.$index, scope.row)">删除</el-button> -->
                 <el-button v-for="(button, index) in tableActions.buttons"
                      :key="index" size="small"
                      @click="button.event(scope.row)">{{ button.name }}</el-button>
              </template>
          </el-table-column>
        </el-table>

        <div class="pagination">
          <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
          </el-pagination>
        </div>
        <el-dialog :visible.sync="dialogVisible">
          <base-form :child-form-items="columns.filter(column => column.useType % 3 == 0)"
                        :child-form-options="dialogOptions"
                        :child-form-data="formData"
                        @submitCallBack="dialogCallBack">
          </base-form>
        </el-dialog>
    </div>
</template>

<script>
import BaseForm from "./BaseForm.vue"
    export default {
        props: ['childTableColumns','childTableOptions',"childTableActions","childDialogOptions"],
        data: function(){
            return {
                columns:  this.childTableColumns,
                options: this.childTableOptions,
                tableActions: this.childTableActions,
                dataListUrl: this.childTableOptions.dataListUrl,
                page:  this.childTableOptions.page,
                rows:  this.childTableOptions.rows,
                dialogOptions: this.childDialogOptions,
                newRowUrl: this.childTableOptions.newRowUrl,//新建地址
                updateRowUrl: this.childTableOptions.updateRowUrl,//编辑地址
                dialogVisible: false,
                formData: {},
                multipleSelection: [], //选中的行
                tableData: []
            }
        },
        created() {
          this.getData();//进入页面获取数据
        },
        components: {
          BaseForm
        },
        methods: {
            getData() {//初始化表格数据
              let self = this;
              self.$axios.get(self.dataListUrl, {
                page: self.page,
                rows: self.rows,
              }).then((res) => {
                self.tableData = res.data;
              })
            },
            newRow(){//新建表格数据
                let self = this;
                self.dialogVisible = true;
                self.dialogOptions.submitUrl = self.newRowUrl;
                self.formData = {};
            },
            edit(index, row){//编辑表格数据
                let self = this;
                self.dialogVisible = true;
                self.dialogOptions.submitUrl = self.updateRowUrl;
                self.formData = row;
            },
            delete(index, row){//编辑表格数据
                let self = this;
                //self.$axios.post(self.options.deleteRowUrl, {id: row.id}).then((res) => {
                    self.$message.success('删除成功！');
                //});
            },
            handleCurrentChange(val) {//翻页
              this.page = val;
              this.getData();
            },
            handleSelectionChange: function(val) {//选中表格数据
                this.multipleSelection = val;
                this.$emit('selectedRows', [val]);
            },
            dialogCallBack(formData){//弹框回调函数
                console.log(formData);
                this.dialogVisible = false;
                this.getData();
            }
        }
    }
</script>
