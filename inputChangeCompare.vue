<script>
/** 数据源对比组件：记录第一次传入的值，当改变该值时，内容红色高亮，同时做了两位有限小数的限制
*
* @param { Number } precision 精度
*/
export default {
  data () {
    return {
      val: '', // 组件内维护的输入框的值
      dataChanged: false, // 数据是否有更改
      tracked: false, // 数据是否已追踪
      originVal: '', // 原始值
      typeofValue: '', // 输入值的类型
      reg: '' // 匹配值的正则
    }
  },
  props: {
    value: {
      type: [ String, Number ]
    },
    // 数字精度
    precision: {
      type: Number,
      default: 0
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
  created () {
    this.reg = this.precision === 0
      ? new RegExp(`^\\d*`, 'g')
      : new RegExp(`^\\d*(\\.?\\d{0,${this.precision}})`, 'g')
  },
  render () {
    const props = {
      attrs: { ...this.$attrs }
    }

    let step
    if (this.precision === 0) {
      step = 1
    } else {
      step = 1 / (Math.pow(10, this.precision))
    }

    return <input {...props} value={ this.val } onInput={ this.OnInput } type="number" on-keydown={ this.OnKeydown } step={ step } class={ this.dataChanged ? 'red-for-change-record my-input' : 'my-input'}></input>
  },
  methods: {
    OnInput (e) {
      e.target.value = (e.target.value.match(this.reg)[0]) || null

      const val = e.target.value
      this.val = this.typeofValue === 'number' ? +val : val
      this.$emit('input', this.val)
    },
    OnKeydown (e) {
      if (e.key === '.') {
        if (this.precision === 0) {
          e.returnValue = false
        } else {
          if (this.val.split('.').length > 1) {
            e.returnValue = false
          }
        }
      }
    }
  }
}
</script>
<style lang="scss">
  .red-for-change-record {
    color: red!important;
  }
  .my-input {
    -webkit-appearance: none;
    background-color: #fff;
    background-image: none;
    border-radius: 4px;
    border: 1px solid #dcdfe6;
    box-sizing: border-box;
    color: #606266;
    display: inline-block;
    font-size: inherit;
    height: 32px;
    line-height: 32px;
    outline: none;
    padding: 0 15px;
    transition: border-color .2s cubic-bezier(.645,.045,.355,1);
    width: 100%;
    &:disabled {
      background-color: #f5f7fa;
      border-color: #e4e7ed;
      color: #c0c4cc;
      cursor: not-allowed;
    }
  }
</style>
