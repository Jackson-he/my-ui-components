<script>
export default {
  data () {
    return {
      form: {}
    }
  },
  props: {
    searchConfig: {
      type: Array
    }
  },
  render () {
    const searchItems = this.searchConfig.map(item => {
      let itemContent
      // 普通文本框
      if (item.type === 'input') {
        item.type = item.inputType
        const props = {
          attrs: { ...item }
        }
        itemContent = <el-input { ...props } onChange={ this.onChange.bind(this, item.prop) }></el-input>
      }
      // 下拉框
      if (item.type === 'select') {
        const props = {
          attrs: { ...item }
        }
        const list = item.options.list || []
        itemContent =
        <el-select { ...props } onChange={ this.onChange.bind(this, item.prop) } value={ item.value }>
          {
            list.map(dataItem => {
              return <el-option value={ dataItem[item.options.value || 'value'] } label={ dataItem[item.options.label || 'label'] }></el-option>
            })
          }
        </el-select>
      }
      // 自定义内容
      if (item.type === 'selfDefinition') {
        if (typeof item.render !== 'function') {
          throw new Error('render show be a function')
        }
        itemContent = item.render()
      }
      // 按钮区域
      if (item.type === 'button') {
        item.type = item.btnType
        const props = {
          attrs: { ...item }
        }
        return (
          <el-form-item>
            <el-button onClick={ item.onClick } { ...props }>{ item.label }</el-button>
          </el-form-item>
        )
      }

      return (
        <el-form-item label={ item.label }>
          { itemContent }
        </el-form-item>
      )
    })

    const props = {
      attrs: { ...this.$attrs },
      on: { ...this.$listeners }
    }

    return (
      <el-form { ...props }>
        { searchItems }
      </el-form>
    )
  },
  methods: {
    onChange (key, value) {
      this.$set(this.form, key, value)
      this.$listeners.change(key, value, this.form)
    },
    onClear () {

    }
  }
}
</script>
