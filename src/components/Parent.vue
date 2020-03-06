<template>
  <div id="table">
    <Child @getInfo="getInfos" />
    <br />
    <el-progress
      :text-inside="true"
      :stroke-width="15"
      :percentage="percentCal"
    ></el-progress>
    <span>{{ restCalcule }} items left</span>
    <el-radio-group v-model="radio">
      <el-radio-button label="All"></el-radio-button>
      <el-radio-button label="Active"></el-radio-button>
      <el-radio-button label="Completed"></el-radio-button>
    </el-radio-group>
    <el-table
      ref="multipleTable"
      :data="tableDataCal"
      :row-key="getRowKey"
      tooltip-effect="dark"
      style="width: 100%"
      @selection-change="handleSelectionChange"
      @cell-dblclick="handleDbclick">
      <el-table-column
        type="selection"
        :reserve-selection="isTrue"
        width="50">
      </el-table-column>
      <el-table-column
        label="items"
        width="270"
        show-overflow-tooltip>
        <template slot-scope="scope">
        <el-input
          ref="inputa"
          type="text"
          v-if="scope.row._isEdit"
          v-model="scope.row.item"
          @keyup.enter.native="e => saveItem(scope.row)"
          @blur="e => saveItem(scope.row)"
        >
          <el-button type="text" slot="suffix" class="el-icon-check" @click="saveItem(scope.row)"></el-button>
        </el-input>
        {{ scope.row.item }}</template>
      </el-table-column>
      <el-table-column
        prop="name"
        label="delete"
        width="80">
        <template slot-scope="scope">
        <!-- scope slot 自定义列的内容，参数为 { row, column, $index } -->
        <el-button
            size="mini"
            type="danger"
            @click="handleDelete(scope.$index, scope.row)"
            icon="el-icon-close">
        </el-button>
        </template>
      </el-table-column>
    </el-table>
    <div
      style="margin-top: 20px">
      <el-button
      v-if="ok"
      @click="handleDeleteMul">clear completed</el-button>
    </div>
  </div>
</template>

<script>
import Child from './Child'
export default {
  name: 'Parent',
  components: {
    Child
  },
  data: function () {
    return {
      isTrue: true,
      radio: 'All',
      checked: false,
      input: '',
      tableData: []
    }
  },
  // 让input自动获取焦点
  // mounted: function () {
  //   this.$refs.inputa.focus()
  // },
  computed: {
    // 计算未被选中的剩余item总数
    restCalcule: function () {
      return this.tableData.length - this.tableData.filter(element => element.status === 1).length
    },
    // 计算未被选中的剩余item数量占总数的百分比
    percentCal: function () {
      let percent = 0
      if (this.tableData.filter(element => element.status === 1).length > 0) {
        percent = Math.floor((this.tableData.filter(element => element.status === 1).length / this.tableData.length) * 10000) / 100
      } else if (this.tableData.filter(element => element.status === 1).length === this.tableData.length & this.tableData.length !== 0) {
        percent = 100
      }
      return percent
    },
    ok: function () {
      let ok = false
      if (this.tableData.filter(element => element.status === 1).length > 0) {
        ok = true
      }
      return ok
    },
    // 根据不同选项过滤tableData数组数据
    tableDataCal: function () {
      let tableDataGroup = []
      if (this.radio === 'All') {
        tableDataGroup = this.tableData
      } else if (this.radio === 'Completed') {
        tableDataGroup = this.tableData.filter(element => element.status === 1)
      } else {
        tableDataGroup = this.tableData.filter(element => element.status === 0)
      }
      return tableDataGroup
    }
  },
  methods: {
    getInfos (data) {
      this.tableData = data
    },
    handleDbclick (row, column, cell, event) {
      this.$set(row, '_isEdit', true)
    },
    handleDelete (index, row) {
      // 不能直接使用这里的index，因为这里的index对应的是table组件绑定的data，但是这里要删除的是整体数组中的元素
      const uid = (element) => element.id === row.id
      const index2 = this.tableData.findIndex(uid)
      this.tableData.splice(index2, 1)
    },
    handleSelectionChange (val) {
      this.tableData.forEach(element => {
        if (val.indexOf(element) >= 0) {
          element.status = 1
        } else {
          element.status = 0
        }
      })
    },
    handleDeleteMul () {
      let deleteMul = []
      this.tableData.forEach(element => {
        if (element.status === 1) {
          deleteMul.push(element)
        }
      })
      deleteMul.forEach(element => {
        // 用splice批量删除数组元素，必须从后往前删，否则index会改变造成错误
        for (let i = this.tableData.length - 1; i >= 0; i--) {
          if (this.tableData[i] === element) {
            this.tableData.splice(i, 1)
          }
        }
      })
      // 如果item没有选中，则不显示批量删除按钮
      if (deleteMul.length === 0) {
        this.okk = false
      } else {
        this.okk = true
      }
    },
    // 获取当前行索引
    getRowKey (row) {
      let index = this.tableData.indexOf(row)
      return index
    },
    // 向对象中添加一个响应式属性  布尔类型的_isEdit，默认是true
    // 当双击事件发生时为true，用v-if绑定此变量用于控制input的显隐
    saveItem (row) {
      console.log(row)
      this.$set(row, '_isEdit', false)
    }
  }
}
</script>
<style lang="less">

</style>
