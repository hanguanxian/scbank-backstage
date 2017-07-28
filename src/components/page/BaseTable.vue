<template>
    <div>
        <div>
            <slot name="topBtns"></slot>
        </div>
        <el-table :data="tableData" :stripe="options.stripe" style="width: 100%" @selection-change="handleSelectionChange" @cell-click="cellClick" :highlight-current-row="true" >
          <el-table-column type="selection"></el-table-column> <!-- 勾选框 -->
          <el-table-column v-for="(column, index) in columns" :key="index" :prop="column.key" :label="column.name" :width="column.width">
          </el-table-column>
          <el-table-column :label="tableActions.name" :width="tableActions.width" :fixed="tableActions.fixed">
              <template scope="scope">
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

    </div>
</template>

<script>
    export default {
        props: ['childTableColumns','childTableOptions',"childTableActions"],
        data: function(){
            return {
                columns:  this.childTableColumns,
                options: this.childTableOptions,
                tableActions: this.childTableActions,
                dataListUrl: this.childTableOptions.dataListUrl,
                page:  this.childTableOptions.page,
                rows:  this.childTableOptions.rows,
                multipleSelection: [], //选中的行
                tableData: []
            }
        },
        created() {
          this.getData();//进入页面获取数据
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
            // newRow(){//新建表格数据
            //     let self = this;
            //     self.dialogVisible = true;
            //     self.dialogOptions.submitUrl = self.newRowUrl;
            //     self.formData = {};
            // },
            // edit(index, row){//编辑表格数据
            //     let self = this;
            //     self.dialogVisible = true;
            //     self.dialogOptions.submitUrl = self.updateRowUrl;
            //     self.formData = row;
            // },
            // delete(index, row){//编辑表格数据
            //     let self = this;
            //     //self.$axios.post(self.options.deleteRowUrl, {id: row.id}).then((res) => {
            //         self.$message.success('删除成功！');
            //     //});
            // },
            handleCurrentChange(val) {//翻页
              this.page = val;
              this.getData();
            },
            cellClick(row, column, cell){
                console.log(row);
                console.log(column);
                console.log(row[column.property]);
            },
            handleSelectionChange: function(val) {//选中表格数据
                this.multipleSelection = val;
                this.$emit('selectedRows', [val]);
            }
        }
    }
</script>
