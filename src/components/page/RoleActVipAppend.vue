<template>
<div class="table">
  <div class="crumbs">
    <el-breadcrumb separator="/">
      <el-breadcrumb-item><i class="el-icon-menu"></i>活动管理</el-breadcrumb-item>
      <el-breadcrumb-item>固收加息活动列表</el-breadcrumb-item>
    </el-breadcrumb>
  </div>
  <el-form :inline="true" :model="searchForm" ref="searchForm" class="demo-form-inline">
    <el-form-item>
      <el-input v-model="searchForm.actAutoId" placeholder="活动ID"></el-input>
    </el-form-item>
    <el-form-item>
      <el-input v-model="searchForm.actName" placeholder="活动名称"></el-input>
    </el-form-item>
    <el-form-item>
      <el-select v-model="searchForm.isOnsale" placeholder="是否上架">
        <el-option label="是" value="1"></el-option>
        <el-option label="否" value="0"></el-option>
      </el-select>
    </el-form-item>
    <el-form-item>
      <el-date-picker v-model="searchFormDate" type="daterange" align="right" placeholder="选择日期范围" :picker-options="pickerOptions">
      </el-date-picker>
    </el-form-item>
    <el-form-item>
      <el-button type="primary" icon="search" @click="searchSubmit('searchForm')">查询</el-button>
      <el-button type="primary" icon="plus" @click="dialogVisible = true">新增</el-button>
    </el-form-item>
  </el-form>

  <el-table :data="tableData" border style="width: 100%" ref="multipleTable" @selection-change="handleSelectionChange">
    <el-table-column type="selection" width="55"></el-table-column>
    <el-table-column prop="actAutoId" label="活动ID">
    </el-table-column>
    <el-table-column prop="actName" label="活动名称">
    </el-table-column>
    <el-table-column prop="appendLable" label="加息标签">
    </el-table-column>
    <el-table-column prop="appendYearRate" label="加息年利率(%)">
    </el-table-column>
    <el-table-column prop="appendDayRate" label="加息天利率(%)">
    </el-table-column>
    <el-table-column prop="appendDayCount" label="加息有效天数">
    </el-table-column>
    <el-table-column prop="beginDate" label="有效期开始">
    </el-table-column>
    <el-table-column prop="endDate" label="有效期截止">
    </el-table-column>
    <el-table-column prop="isOnsale" label="是否上架">
    </el-table-column>
    <el-table-column prop="termUpperLimit" label="产品上限期限">
    </el-table-column>
    <el-table-column prop="termLowerLimit" label="产品下限期限">
    </el-table-column>
    <el-table-column prop="amountUpperLimit" label="起购金额上限">
    </el-table-column>
    <el-table-column prop="amountLowerLimit" label="起购金额下限">
    </el-table-column>
    <el-table-column prop="remark" label="备注">
    </el-table-column>
    <el-table-column prop="createTime" label="创建时间">
    </el-table-column>
    <el-table-column label="操作" width="200">
      <template scope="scope">
        <el-button size="small"
            @click="handleEdit(scope.$index, scope.row)">编辑</el-button>
        <el-button size="small"
            @click="modifyOnsale(scope.row,1)">上架</el-button>
        <el-button size="small"
            @click="modifyOnsale(scope.row,0)">下架</el-button>
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
      <el-form-item prop="beginDate" label="有效时间" :rules="{ required: true, message: '必填', trigger: 'blur'}">
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
        <el-input type="textarea" v-model="dialogForm.mark"></el-input>
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
export default {
  data() {
    return {
      tableData: [],
      page: 1,
      rows: 50,
      multipleSelection: [],//选中的行
      searchFormDate: [],//查询框日期
      updateBtn: false,
      dialogVisible: false, //编辑框是否显示
      searchForm: {
          actAutoId:"",
          actName:"",
          isOnsale:"",
          startCreateTime:"",
          endCreateTime:"",
          page:"",
          rows:""
      },
      dialogForm: {
          actAutoId:"",
          actName:"",
          appendLable:"",
          appendYearRate:"",
          appendDayCount:"",
          beginDate:"",
          endDate:"",
          termUpperLimit:"",
          termLowerLimit:"",
          amountUpperLimit:"",
          amountLowerLimit:"",
          mark:""
      },
      listUrl: '../../../static/act_vip_append_list.json',//列表页面
      searchFormUrl: "/interface/act/act_vip_append_list.do",//搜索的链接
      newFormUrl:"/interface/act/add_act_vip_append.do",//新建的链接
      updateFormUrl: "/interface/act/modify_act_vip_append.do",//更新列表的链接
      modifyOnsaleUrl: "/interface/act/modify_act_vip_append_onsale.do",//上下架的链接
      pickerOptions: {
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
  created() {
    this.getData();
  },
  methods: {
    handleCurrentChange(val) {
      this.cur_page = val;
      this.getData();
    },
    searchSubmit(formName) {
        this.$axios.post(this.searchFormUrl, this.searchForm).then((res) => {
            self.tableData = res.data;
        });
    },
    dialogSubmit(formName) {
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
        self.tableData = res.data;
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

<style>
.handle-box {
  margin-bottom: 20px;
}

.el-table .cell,
.el-table th>div {
  text-align: center;
  padding-left: 15px;
  padding-right: 15px;
}
</style>
