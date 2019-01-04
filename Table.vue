<script>
/* eslint-disable */
export default {
  data () {
    return {
    }
  },
  props: {
    tableData: {
      type: Array
    },
    tableColumn: {
      type: Array
    }
  },
  render () {
    const props = {
      attrs: { ...this.$attrs },
      on: { ...this.$listeners }
    }
    const getTableColumn = (target) => {
      return target.map(item => {
        if (item.content && Object.prototype.toString.call(item.content) === '[object Array]') {
          return (
            <el-table-column label={ item.label }>
              { getTableColumn(item.content) }
            </el-table-column>
          )
        }

        const props = {
          attrs: { ...item }
        }
        const scopedSlots = {
          scopedSlots: {
            default: scope => {
              return item.render(scope)
            }
          }
        }
        let slotsTemplate = item.render ? scopedSlots : {}

        return (
          <el-table-column
            { ...props }
            { ...slotsTemplate }
            >
          </el-table-column>
        )
      })
    }

    return (
      <el-table
        data={ this.tableData }
        stripe border
        ref="table"
        { ...props }
        >
        {
          getTableColumn(this.tableColumn)
        }
      </el-table>
    )
  },
  mounted () {
    // 把Element扩展到组件实例上的Table Methods方法扩展到父组件实例上
    const tableMethods = ['clearSelection', 'toggleRowSelection', 'toggleAllSelection', 'toggleRowExpansion', 'setCurrentRow', 'clearSort', 'clearFilter', 'doLayout', 'sort']
    tableMethods.forEach(item =>{
      this[item] = this.$refs.table[item]
    })
  }
}
</script>
