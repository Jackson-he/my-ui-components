<script>
import _ from '@/util/extend'
import util from '@/util/util'

export default {
  data () {
    return {
      form: {},
      debounceEmitChange: function () {},
      debounceEmitInput: function () {}
    }
  },
  props: {
    value: {
      type: Object,
      default: function () {
        return {}
      }
    },
    searchConfig: {
      type: Array
    }
  },
  watch: {
    value: {
      handler (val) {
        this.form = val
        this.debounceEmitChange()
      },
      immediate: true,
      deep: true
    }
  },
  created () {
    const changeFn = (val) => {
      this.$emit('change', val)
    }
    const inputFn = (val) => {
      this.$emit('input', val)
    }
    this.debounceEmitChange = util.debounce(changeFn, 500)
    this.debounceEmitInput = util.debounce(inputFn, 500)
  },
  render () {
    const props = {
      attrs: { ...this.$attrs },
      on: { ...this.$listeners }
    }

    return (
      <el-form { ...props }>
        {
          this.searchConfig.map(item => {
            // 按钮
            if (item.onClick) {
              const props = {
                attrs: { ...item }
              }
              // 删除props中多余的参数
              delete props.attrs.onClick

              return (
                <el-form-item>
                  <el-button onClick={ item.onClick } { ...props }>{ item.label }</el-button>
                </el-form-item>
              )
            }
            // input, select, 自定义
            return (
              <el-form-item label={ item.label }>
                { this.renderItem(item) }
              </el-form-item>
            )
          })
        }
      </el-form>
    )
  },
  methods: {
    renderItem (item) {
      const form = this.form
      const props = {
        attrs: { ...item }
      }
      // 自定义内容
      if (item.pattern === 'selfDefinition') {
        if (typeof item.render !== 'function') {
          throw new Error('render show be a function')
        }
        return item.render()
      }
      // 下拉框
      if (item.options) {
        const list = item.options.list || []
        // select值自动处理数字和字符串
        let numberType = false
        if (list.length) {
          numberType = typeof list[0][item.options.value || 'value'] === 'number'
        }

        let value
        if (form[item.prop] === '' || form[item.prop] === undefined || form[item.prop] === null) {
          value = form[item.prop]
        } else {
          value = numberType ? +form[item.prop] : form[item.prop]
        }

        return (
          <el-select { ...props } onChange={ this.onChange.bind(this, item.prop) } value={ value }>
            {
              list.map(dataItem => {
                return <el-option value={ dataItem[item.options.value || 'value'] } label={ dataItem[item.options.label || 'label'] }></el-option>
              })
            }
          </el-select>
        )
      }
      // input
      return <el-input { ...props } value={form[item.prop]} onInput={ this.onChange.bind(this, item.prop) }></el-input>
    },
    onChange (key, value) {
      this.$set(this.form, key, value)
      const result = _.extend(true, this.value, this.form)
      this.debounceEmitInput(result)
      this.debounceEmitChange(result)
    }
  }
}
</script>
