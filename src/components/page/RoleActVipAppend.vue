<template>
<div class="table">
  <!-- 搜索区域开始 -->
  <base-form :child-form-items="searchForm.items"
            :child-form-options="searchForm.options"
            @submitCallBack="searchCallBack">
  </base-form>
  <!-- 搜索区域结束 -->
  <!-- 页面table开始 -->
  <base-table ref="myTable"
        :child-table-columns="tableConfig.columns"
        :child-table-options="tableConfig.tableOptions"
        :child-table-actions="tableConfig.tableActions"
        :child-table-data="tableData"
        @cellClick="cellClickd"
        @pageChange="pageChanged">
        <!-- 顶部工具栏 -->
        <div slot="topBtns">
            <group-btns :child-btns="topBtnsConfig"></group-btns>
        </div>
  </base-table>
  <!-- 页面table结束 -->
  <!-- 新建 编辑 页面弹出开始 -->
  <el-dialog :visible.sync="dialogVisible" size="large">
    <base-form :child-form-items="dialogForm.items"
               :child-form-options="dialogForm.options"
               :child-form-data="dialogFormData"
               @submitCallBack="dialogCallBack">
    </base-form>
  </el-dialog>
  <!-- 新建 编辑 页面弹出结束 -->
</div>
</template>

<script>
import BaseForm from "./BaseForm.vue"
import BaseTable from "./BaseTable.vue"
import GroupBtns from "./GroupBtns.vue"
export default {
  data() {
    const self = this;
    return {
      // tableConfig 页面展示字段配置
      //   name 页面字段显示的label文字
      //   key  取值的key
      //   insearch 是否在搜索框出现
      //   inDialog 是否在弹出页面显示
      //   type 字段输入时的类型 默认inpute  目前有 date select editor inpute
      //   selectOptions 当type 为select的时候 option的枚举值
      //   tableActions 表格里面的操作按钮以及事件
      //   dialogActions 弹出框里面的操作按钮以及事件
      //   useType 能被2整除 显示在表格 能被3整除显示在表格新建编辑列表页面 能被5整除显示在查询框
      //查询框配置
      searchForm: {
        items: [{name: '活动名称',placeholder: '活动名称',key: 'actName'},
                {name: '是否上架',placeholder: '是否上架',type: 'select',selectOptions: [{label: "是",value: "1"},{label: "否",value: "0"}],key: 'isOnsale'},
                {name: '时间范围',type: 'daterange',placeholder: '时间范围',daterange: [],beginkey: 'beginDate', endkey: 'endDate'}],
        options: {
          submitUrl: "/interface/act/act_vip_append_list.do", //新建的链接
          submitIcon: "search",//搜索按钮
          formClass: 'query-form', //向表单添加样式
          showLabel: false, //是否显示label标签
          inline: true, //输入框是否在一行内
          submitName: '搜索' //提交按钮文字
        }
      },
      //弹出框配置
      dialogForm: {
        items: [{
          name: '活动名称',
          placeholder: '活动名称',
          key: 'actName'
        }, {
          name: '加息标签',
          key: 'appendLable'
        }, {
          name: '加息年利率(%)',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'appendYearRate'
        }, {
          name: '加息天利率(%)',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'appendDayRate'
        }, {
          name: '加息有效天数',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'appendDayCount'
        }, {
          name: '有效期开始',
          type: "date",
          rules: {type: 'date',required: true,message: '必填',trigger: 'blur'},
          key: 'beginDate'
        }, {
          name: '有效期截止',
          type: "date",
          rules: {type: 'date',required: true,message: '必填',trigger: 'blur'},
          key: 'endDate'
        }, {
          name: '产品上限期限',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'termUpperLimit'
        }, {
          name: '产品下限期限',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'termLowerLimit'
        }, {
          name: '起购金额上限',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'amountUpperLimit'
        }, {
          name: '起购金额下限',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'amountLowerLimit'
        }, {
          name: '备注',
          type: 'editor',
          key: 'remark'
        }],
        options: {
          submitUrl: "/interface/act/add_act_vip_append.do", //新建的链接
          submitRow: true,//提交按钮是否单独占一行
          defaultRules: {
            required: true,
            message: '必填',
            trigger: 'blur'
          }, //表单默认校验规则
          inline: true, //输入框是否在一行内
          labelWidth: "130px",
          submitName: '确定' //提交按钮文字
        }
      },
      //表格配置
      tableConfig: {
        columns: [{
          name: '活动ID',
          key: 'actAutoId'
        }, {
          name: '活动名称',
          placeholder: '活动名称',
          key: 'actName'
        }, {
          name: '加息标签',
          key: 'appendLable'
        }, {
          name: '加息年利率(%)',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'appendYearRate'
        }, {
          name: '加息天利率(%)',
          type: 'number',
          rules: {type: 'number',required: true,message: '必填',trigger: 'blur'},
          key: 'appendDayRate'
        }, {
          name: '加息有效天数',
          key: 'appendDayCount'
        }, {
          name: '有效期开始',
          type: "date",
          rules: {type: 'date',required: true,message: '必填',trigger: 'blur'},
          key: 'beginDate'
        }, {
          name: '有效期截止',
          type: "date",
          rules: {type: 'date',required: true,message: '必填',trigger: 'blur'},
          key: 'endDate'
        }, {
          name: '是否上架',
          placeholder: '是否上架',
          type: 'select',
          selectOptions: [{label: "是",value: "1"}, {label: "否",value: "0"}],
          key: 'isOnsale'
        }, {
          name: '产品上限期限',
          key: 'termUpperLimit'
        }, {
          name: '产品下限期限',
          key: 'termLowerLimit'
        }, {
          name: '起购金额上限',
          key: 'amountUpperLimit'
        }, {
          name: '起购金额下限',
          key: 'amountLowerLimit'
        }, {
          name: '备注',
          type: 'editor',
          key: 'remark'
        }, {
          name: '创建时间',
          key: 'createTime'
        }],
        tableActions: {
          name: '操作',
          key: 'actions',
          width: "220",
          fixed: "right",
          buttons: [{
            name: "编辑",
            icon: "fa-pencil",
            event(row) {
              console.log('编辑');
              console.log(row);
              if(row.isOnsale!='否'){
 			      self.$message.error('已上架活动不可以修改');
         	  } else {
                  self.dialogVisible = true;
                  self.dialogForm.options.submitUrl = self.updateRowUrl;
                  self.msg = "修改固收加息活动成功";
                  self.dialogFormData = row;
              }
            }
          },{
            name: "上架",
            icon: "fa-hand-o-up",
            event(row) {
              console.log('上架');
              let data = {
				"actAutoId":row.actAutoId,
				"onsale":1
		      }
              self.$axios.post(self.dialogForm.options.submitUrl, data).then((res) => {
                  self.$message.success('上架成功');
              }).catch(function (error) {
                  self.$message.error('上架失败');
              });
            }
          }, {
            name: "下架",
            icon: "fa-hand-o-down",
            event(row) {
              console.log('下架');
              let data = {
				"actAutoId":row.actAutoId,
				"onsale":0
		      }
              self.$axios.post(self.dialogForm.options.submitUrl, data).then((res) => {
                  self.$message.success('下架成功');
              }).catch(function (error) {
                  self.$message.error('下架失败');
              });
            }
          }]
        },
        tableOptions: {}
      },
      //表格顶部按钮区域
      topBtnsConfig: [{
          name: "新建",
          event() {
            console.log('新建');
            self.dialogForm.options.submitUrl = self.newRowUrl;
            self.msg = "新增固收加息活动成功";
            self.dialogFormData = {};
            self.dialogVisible = true;
          }
      }],
      dataListUrl: '../../../static/act_vip_append_list.json',
      newRowUrl: '/interface/act/add_act_vip_append.do', //表格全部数据请求地址
      updateRowUrl: "/interface/act/modify_act_vip_append.do", //更新列表的行链接
      onsaleUrl: "/interface/act/modify_act_vip_append_onsale.do",
      deleteRowUrl: "/interface/act/modify_act_vip_append_onsale.do", //更新列表的行链接
      dialogFormData: {},//弹出框formData
      selectedTableRows:[],//选中的table 行
      tableData: [],//让搜索框的数据赋值到表格
      msg: '',
      tablePage: 1,
      tableRows: 50,
      dialogVisible: false
    }
  },
  components: {
    GroupBtns,
    BaseForm,
    BaseTable //富文本组件
  },
  created() {
      this.getTableData();
  },
  methods: {
    searchCallBack(formData) {//搜索事件
      const self = this;
      let data = {
        "actAutoId":"",
        "isOnsale": formData.isOnsale || -1,
        "startCreateTime": formData.beginDate || "",
        "endCreateTime": formData.endDate || "",
        page: self.tablePage,
        rows: self.tableRows
      }
      self.$axios.post(self.searchForm.options.submitUrl, data).then((res) => {
          console.log(res);
          self.getTableData();
      }).catch(function (error) {
          self.$message.error('搜索失败');
      });
    },
    pageChanged(value) {//翻页的事件
        this.tablePage = value;
        this.getTableData();
    },
    getTableData() {//初始化表格数据
      const self = this;
      self.$axios.get(self.dataListUrl, {
        page: self.tablePage,
        rows: self.tableRows,
      }).then((res) => {
        this.tableData = res.data;
      })
    },
    cellClickd(value) {//选中单格的事件
        console.log(value);
        if(value) {
            this.$message(value + '');
        }
    },
    dialogCallBack(value) {//弹出框的提交事件
      console.log('dialogCallBack');
      const self = this;
    //   let formData = new FormData();
    //   for (let it in value) {
    //     formData.append(it,value[it]);
    //   }
    //   let config = {headers: {'Content-Type': 'application/x-www-form-urlencoded'}};

      self.dialogVisible = false;
      self.$axios.post(self.dialogForm.options.submitUrl, self.$qs.stringify(value)).then((res) => {
          self.$message.success(self.msg);
          this.getTableData();
      }).catch(function (error) {
          self.$message.error('失败');
      });
    }
  }
}
</script>
