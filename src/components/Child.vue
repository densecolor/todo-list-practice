<template>
  <div id="inputbox">
    <el-input
      ref="inputb"
      v-model.lazy="input"
      placeholder="What needs to be done?"
      @keyup.enter.native="sendMsg"
      id="el-inputChild"
      >
    </el-input>
    <el-button @click="sendMsg" plain>+</el-button>
  </div>
</template>

<script>
import uuid from 'js-shortid'
export default {
  name: 'Child',
  data: function () {
    return {
      checked: false,
      input: '',
      inputs: []
    }
  },
  // 让子组件的input自动获取焦点
  mounted: function () {
    this.$refs.inputb.focus()
  },
  watch: {
    input: function () {
      this.$refs.inputb.focus()
    }
  },
  methods: {
    onChange (val) {
      this.input = val
    },
    sendMsg (event) {
      if (this.input !== '') {
        this.inputs.push(
          {
            item: this.input,
            status: 0,
            id: uuid.gen()
          })
      } else {
      }
      this.$emit('getInfo', this.inputs)
      this.input = ''
    }
  }
}
</script>
<style lang="less">
    #inputbox {
      display: flex;
      flex-direction: row;
      width: 400px;
      text-align: center;
    }
    #el-inputChild {
      width: 100%;
      border-top-width: 1px;
      border-left-width: 1px;
      border-right-width: 1px;
      border-bottom-width: 1px;
}
</style>
