<script>
/** 数字输入组件
*
* @param { Number } precision 精度 （默认）0 => 整数， 1 => 0.1, 2 => 0.01 依次类推
*/
export default {
  data () {
    return {
      val: '', // 组件内维护的输入框的值
      typeofValue: '', // 输入值的类型
      reg: '' // 匹配值的正则
    }
  },
  props: {
    value: {
      type: [String, Number]
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
      },
      immediate: true
    }
  },
  created () {
    this.reg = this.precision === 0
      ? new RegExp('^\\d*', 'g')
      : new RegExp(`^\\d*(\\.?\\d{0,${this.precision}})`, 'g')
  },
  render () {
    const props = {
      attrs: { ...this.$attrs }
    }

    let step = 1 / (Math.pow(10, this.precision))

    return <input {...props} value={this.val} onInput={this.OnInput} type="number" on-keydown={this.OnKeydown} step={step} class="my-input"></input>
  },
  methods: {
    OnInput (e) {
      e.target.value = (e.target.value.match(this.reg)[0]) || null
      if (this.$attrs.maxlength) {
        e.target.value = e.target.value.slice(0, this.$attrs.maxlength)
      }

      let val = e.target.value
      if (val !== '') {
        val = this.typeofValue === 'number' ? +val : val
      }
      this.val = val
      this.$emit('input', this.val)
    },
    OnKeydown (e) {
      if (e.key === '.' || e.key === '。') {
        if (this.precision === 0) {
          e.preventDefault()
        } else {
          if (String(this.val).split('.').length > 1) {
            e.preventDefault()
          }
        }
      }
    }
  }
}
</script>
<style lang="scss" scoped>
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
  transition: border-color 0.2s cubic-bezier(0.645, 0.045, 0.355, 1);
  width: 100%;
  &:disabled {
    background-color: #f5f7fa;
    border-color: #e4e7ed;
    color: #c0c4cc;
    cursor: not-allowed;
  }
}
</style>
