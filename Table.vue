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
        { ...props }
        >
        {
          getTableColumn(this.tableColumn)
        }
      </el-table>
    )
  }
}
</script>
