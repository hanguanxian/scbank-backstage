<template>
<div class="table">
  <!-- 搜索区域开始 -->
  <base-form :child-form-items="pageConfig.columns.filter(column => column.useType % 5 == 0)" :child-form-options="pageConfig.searchOptions" @submitCallBack="searchCallBack">
  </base-form>
  <!-- 搜索区域结束 -->
  <!-- 页面table开始 -->
  <base-table :child-table-columns="pageConfig.columns.filter(column => column.useType % 2 == 0)" :child-table-options="pageConfig.tableOptions" :child-table-actions="pageConfig.tableActions" @selectedRows="getRows">
  </base-table>
  <!-- 页面table结束 -->
  <!-- 新建 编辑 页面弹出开始 -->
  <el-dialog title="固收加息活动" :visible.sync="dialogVisible">
    <base-form :child-form-items="pageConfig.columns.filter(column => column.useType % 3 == 0)" :child-form-options="pageConfig.dialogOptions" :child-form-data="formData" @submitCallBack="dialogCallBack">
    </base-form>
  </el-dialog>
  <!-- 新建 编辑 页面弹出结束 -->
</div>
</template>

<script>
import BaseForm from "./BaseForm.vue"
import BaseTable from "./BaseTable.vue"
export default {
  data() {
    const self = this;
    return {
      // tablebConfig 页面展示字段配置
      //   name 页面字段显示的label文字
      //   key  取值的key
      //   insearch 是否在搜索框出现
      //   inDialog 是否在弹出页面显示
      //   type 字段输入时的类型 默认inpute  目前有 date select editor inpute
      //   selectOptions 当type 为select的时候 option的枚举值
      //   tableActions 表格里面的操作按钮以及事件
      //   dialogActions 弹出框里面的操作按钮以及事件
      //   useType 能被2整除 显示在表格 能被3整除显示在表格新建编辑列表页面 能被5整除显示在查询框
      pageConfig: {
        columns: [{
          name: '活动ID',
          useType: 2,
          key: 'actAutoId'
        }, {
          name: '活动名称',
          useType: 2 * 3 * 5,
          placeholder: '活动名称',
          key: 'actName'
        },{
          name: '时间范围',
          useType: 5,
          type: 'daterange',
          placeholder: '时间范围',
          daterange:[],
          beginkey: 'beginDate',
          endkey: 'endDate'
        }, {
          name: '加息标签',
          useType: 2 * 3,
          key: 'appendLable'
        }, {
          name: '加息年利率(%)',
          useType: 2 * 3,
          rules: {
            type: 'number',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'appendYearRate'
        }, {
          name: '加息天利率(%)',
          useType: 2 * 3,
          rules: {
            type: 'number',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'appendDayRate'
        }, {
          name: '加息有效天数',
          useType: 2 * 3,
          rules: {
            type: 'number',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'appendDayCount'
        }, {
          name: '有效期开始',
          useType: 2 * 3,
          type: "date",
          rules: {
            type: 'date',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'beginDate'
        }, {
          name: '有效期截止',
          useType: 2 * 3,
          type: "date",
          rules: {
            type: 'date',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'endDate'
        }, {
          name: '是否上架',
          useType: 2 * 5,
          placeholder: '是否上架',
          type: 'select',
          selectOptions: [{
            label: "是",
            value: "1"
          }, {
            label: "否",
            value: "0"
          }],
          key: 'isOnsale'
        }, {
          name: '产品上限期限',
          useType: 2 * 3,
          rules: {
            type: 'number',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'termUpperLimit'
        }, {
          name: '产品下限期限',
          useType: 2 * 3,
          rules: {
            type: 'number',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'termLowerLimit'
        }, {
          name: '起购金额上限',
          useType: 2 * 3,
          rules: {
            type: 'number',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'amountUpperLimit'
        }, {
          name: '起购金额下限',
          useType: 2 * 3,
          rules: {
            type: 'number',
            required: true,
            message: '必填',
            trigger: 'blur'
          },
          key: 'amountLowerLimit'
        }, {
          name: '备注',
          useType: 2 * 3,
          type: 'editor',
          key: 'remark'
        }, {
          name: '创建时间',
          useType: 2,
          key: 'createTime'
        }],
        tableActions: {
          name: '操作',
          key: 'actions',
          width: "200",
          fixed: "right",
          buttons: [{
            name: "编辑",
            event(row) {
              console.log(row);
               self.formData = row;
            //   self.dialogForm.beginDate = new Date(row.beginDate);
            //   self.dialogForm.endDate = new Date(row.endDate);
            self.dialogVisible = true;
            //   self.updateBtn = true;
            }
          }, {
            name: "上架",
            event(index, row) {
              self.modifyOnsale(row, 1);
            }
          }, {
            name: "下架",
            event(index, row) {
              self.modifyOnsale(row, 0);
            }
          }]
        },
        tableOptions: {
          stripe: true,
          dataUrl: '../../../static/act_vip_append_list.json'
        },
        searchOptions: {
          submitUrl: "/interface/act/add_act_vip_append.do", //新建的链接
          formClass: 'query-form', //向表单添加样式
          showLabel: false,
          inline: true, //输入框是否在一行内
          submitName: '搜索' //提交按钮文字
        },
        dialogOptions: {
          defaultRules: {
            required: true,
            message: '必填',
            trigger: 'blur'
          }, //表单默认校验规则
          submitUrl: "/interface/act/add_act_vip_append.do", //新建的链接
          submitName: '确定' //提交按钮文字
        }
      },
      formData: {},
      searchForm: {},
      dialogForm: {},
      dialogVisible: false,
      listUrl: '../../../static/act_vip_append_list.json', //列表页面
      searchFormUrl: "/interface/act/act_vip_append_list.do", //搜索的链接
      newFormUrl: "/interface/act/add_act_vip_append.do", //新建的链接
      updateFormUrl: "/interface/act/modify_act_vip_append.do", //更新列表的链接
      modifyOnsaleUrl: "/interface/act/modify_act_vip_append_onsale.do" //上下架的链接
    }
  },
  components: {
    BaseForm,
    BaseTable //富文本组件
  },
  methods: {
    getRows(val) {
      console.log('getRows');
      console.log(val);
    },
    searchCallBack(res) {
      console.log('searchCallBack');
      console.log(res);
    },
    dialogCallBack(res) {
      console.log('dialogCallBack');
      console.log(res);
    }
  }
}
</script>
