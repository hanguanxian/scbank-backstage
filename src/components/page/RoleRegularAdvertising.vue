<template>
<div class="table">

  <el-form :inline="true" :model="searchForm" ref="searchForm" class="query-form el-form--inline">
    <el-form-item>
      <el-input v-model="searchForm.actAutoId" placeholder="标的ID"></el-input>
    </el-form-item>
    <el-form-item>
      <el-input v-model="searchForm.actName" placeholder="标的名称"></el-input>
    </el-form-item>
    <el-form-item>
      <el-select v-model="searchForm.isOnsale" placeholder="推广位置">
        <el-option label="首页热门推广" value="Bank_Index_Topic"></el-option>
        <el-option label="首页列表推广" value="Bank_Index_List"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" icon="search" @click="searchSubmit('searchForm')">查询</el-button>
      <el-button type="primary" icon="plus" @click="dialogVisible = true">新增</el-button>
    </el-form-item>
  </el-form>

  <el-table :data="tableData" stripe style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
    <!-- <el-table-column type="selection" width="55"></el-table-column> -->
    <el-table-column prop="targetId" label="标的ID">
    </el-table-column>
    <el-table-column prop="targetName" label="标动名称">
    </el-table-column>
    <el-table-column prop="advName" label="位置名称">
    </el-table-column>
    <el-table-column prop="sort" label="排序">
    </el-table-column>
    <el-table-column prop="targetAmount" label="标的金额">
    </el-table-column>
    <el-table-column prop="buyAmount" label="已购买金额">
    </el-table-column>
    <el-table-column prop="beginDate" width="120" label="起息日">
    </el-table-column>
    <el-table-column prop="endDate" width="120" label="结息日">
    </el-table-column>
    <el-table-column prop="state" label="状态">
    </el-table-column>
    <el-table-column label="操作" width="200" fixed="right">
      <template scope="scope">
        <el-button size="small"
            @click="handleEdit(scope.$index, scope.row)">移除</el-button>
      </template>
    </el-table-column>
  </el-table>
  <div class="pagination">
    <el-pagination @current-change="handleCurrentChange" layout="prev, pager, next" :total="1000">
    </el-pagination>
  </div>

  <el-dialog title="固收加息活动" :visible.sync="dialogVisible">
    <el-form :model="dialogForm" ref="dialogForm" label-width="120px" style="width:500px; margin: 0 auto;">
      <el-form-item label="活动名称:" prop="actName" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.actName"></el-input>
      </el-form-item>
      <el-form-item prop="appendLable" label="加息标签:" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.appendLable"></el-input>
      </el-form-item>
      <el-form-item prop="appendYearRate" label="加息年利率(%):" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.appendYearRate"></el-input>
      </el-form-item>
      <el-form-item prop="appendDayCount" label="加息有效天数:" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.appendDayCount"></el-input>
      </el-form-item>
      <el-form-item prop="beginDate" label="有效时间" :rules="{type: 'date', required: true, message: '必填', trigger: 'blur'}">
        <el-col :span="11">
          <el-date-picker type="date" placeholder="有效期开始" v-model="dialogForm.beginDate" style="width: 100%;"></el-date-picker>
        </el-col>
        <el-col class="line" :span="2">&nbsp;</el-col>
        <el-col :span="11">
          <el-date-picker type="date" placeholder="有效期截止" v-model="dialogForm.endDate" style="width: 100%;"></el-date-picker>
        </el-col>
        <!-- <el-date-picker v-model="dialogFormDate" type="daterange" align="right" placeholder="有效期范围" :picker-options="pickerOptions">
        </el-date-picker> -->
      </el-form-item>
      <el-form-item prop="termUpperLimit" label="产品上限期限:" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.termUpperLimit"></el-input>
      </el-form-item>
      <el-form-item prop="termLowerLimit" label="产品下限期限:" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.termLowerLimit"></el-input>
      </el-form-item>
      <el-form-item prop="amountUpperLimit" label="起购金额上限:" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.amountUpperLimit"></el-input>
      </el-form-item>
      <el-form-item prop="amountLowerLimit" label="起购金额下限:" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <el-input v-model="dialogForm.amountLowerLimit"></el-input>
      </el-form-item>
      <el-form-item prop="mark" label="备注" :rules="{ required: true, message: '必填', trigger: 'blur'}">
        <!-- <el-input type="textarea" v-model="dialogForm.mark"></el-input> -->
        <quill-editor ref="myTextEditor" v-model="dialogForm.mark"></quill-editor>
      </el-form-item>
      <el-form-item>
        <el-button @click="dialogVisible = false">取 消</el-button>
        <el-button type="primary" @click="dialogSubmit('dialogForm')">确 定</el-button>
      </el-form-item>
    </el-form>
  </el-dialog>
</div>
</template>

<script>
import { quillEditor } from 'vue-quill-editor';
export default {
  data() {
    return {
      tableData: [],
      page: 1,
      rows: 50,
      multipleSelection: [],//选中的行
      updateBtn: false,
      dialogVisible: false, //编辑框是否显示
      searchForm: {
      },
      dialogForm: {
      },
      listUrl: '../../../static/regular_adv_list.json',//列表页面
      searchFormUrl: "/interface/act/act_vip_append_list.do",//搜索的链接
      newFormUrl:"/interface/act/add_act_vip_append.do",//新建的链接
      updateFormUrl: "/interface/act/modify_act_vip_append.do",//更新列表的链接
      modifyOnsaleUrl: "/interface/act/modify_act_vip_append_onsale.do"//上下架的链接
    }
  },
  created() {
    this.getData();
  },
  components: {
      quillEditor
  },
  computed: {
      editor() {
          return this.$refs.myTextEditor.quillEditor;
      },
      setEndDate(){
          if(this.dialogForm.endDate === "" || this.dialogForm.endDate < this.dialogForm.beginDate) {
              this.dialogForm.endDate = this.dialogForm.beginDate;
          }
          return this.dialogForm.endDate;
      }
  },
  methods: {
    onEditorChange({ editor, html, text }) {
      this.dialogForm.mark = html;
    },handleCurrentChange(val) {
      this.cur_page = val;
      this.getData();
    },searchSubmit(formName) {
        this.$axios.post(this.searchFormUrl, this.searchForm).then((res) => {
            self.tableData = res.data;
        });
    },dialogSubmit(formName) {
        this.$refs[formName].validate((valid) => {
            if (valid) {
                if(this.updateBtn){//是否是更新操作
                    this.$axios.post(this.updateFormUrl, this.dialogForm).then((res) => {
                        this.getData();
                    });
                } else {
                    this.$axios.post(this.newFormUrl, this.dialogForm).then((res) => {
                        this.getData();
                    });
                }
            } else {
                this.$message('error submit!!');
                return false;
            }
        });
    },
    getData() {
      let self = this;
      self.$axios.get(self.listUrl, {
        page: self.page,
        rows: self.rows,
      }).then((res) => {
        self.tableData = res.data.rows;
      })
    },
    handleEdit(index, row) {
      this.dialogForm = row;
      this.dialogVisible = true;
      this.updateBtn = true;
    },
    modifyOnsale(row, onsale) {
        this.$axios.post(this.modifyOnsaleUrl, {actAutoId:row.actAutoId,onsale:onsale}).then((res) => {
            this.getData();
        });
    },
    handleSelectionChange: function(val) {
      this.multipleSelection = val;
    }
  }
}
</script>
