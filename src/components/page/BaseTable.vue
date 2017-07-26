<template>
    <div>
        <el-table :data="tableData" :stripe="options.stripe" style="width: 100%"  @selection-change="handleSelectionChange">
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
                dataUrl: this.childTableOptions.dataUrl,
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
            getData() {
              let self = this;
              self.$axios.get(self.dataUrl, {
                page: self.page,
                rows: self.rows,
              }).then((res) => {
                self.tableData = res.data;
              })
            },
            handleCurrentChange(val) {
              this.page = val;
              this.getData();
            },
            handleEdit(index, row) {
              this.dialogForm = row;
              this.dialogVisible = true;
              this.updateBtn = true;
            },
            handleSelectionChange: function(val) {
                this.multipleSelection = val;
                this.$emit('selectedRows', [val]);
            }
        }
    }
</script>
