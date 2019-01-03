<script>
/** 数据源对比组件：记录第一次传入的值，当改变该值时，内容红色高亮，同时做了两位有限小数的限制
*
* @param { any } 所有el-input组件原生支持的参数都可以
*/
export default {
  data () {
    return {
      val: '', // 组件内维护的输入框的值
      dataChanged: false, // 数据是否有更改
      tracked: false, // 数据是否已追踪
      originVal: '', // 原始值
      typeofValue: '', // 输入值的类型
      DASHED: ''
    }
  },
  props: {
    value: {
      type: [ String, Number ]
    }
  },
  watch: {
    'value': {
      handler (newVal) {
        this.val = newVal
        this.typeofValue = typeof newVal

        !this.tracked && (this.originVal = newVal)

        this.dataChanged = +newVal !== +this.originVal

        this.tracked = true
      },
      immediate: true
    }
  },
  render () {
    const props = {
      attrs: { ...this.$attrs }
    }

    return <el-input {...props} value={ this.val } onInput={ this.OnInput } step="0.01" nativeOn-keydown={ this.OnKeydown } class={ this.dataChanged ? 'red-for-change-record' : ''}></el-input>
  },
  created () {
    const reg = /([0-9]*\.?)(.*)/
    this.DASHED = String(this.val).match(reg)[2]
  },
  methods: {
    OnInput (val) {
      const reg = /([0-9]*\.?)(.*)/
      this.DASHED = val.match(reg)[2]

      this.val = this.typeofValue === 'number' ? +val : val
      this.$emit('input', this.val)
    },
    OnKeydown (event) {
      const deleteKeyCode = 46
      const backSpaceKeyCode = 8
      const isEmptyKey = [deleteKeyCode, backSpaceKeyCode].includes(event.keyCode)

      if (this.DASHED.length > 0 && !isEmptyKey) {
        event.returnValue = /^\d*$/.test(event.key)
        if (this.DASHED.length > 1) {
          event.returnValue = false
        }
      }
    }
  }
}
</script>
<style lang="scss">
  .red-for-change-record {
    input {
      color: red!important;
    }
  }
</style>
