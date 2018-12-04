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
    return (
      <el-table
        data={ this.tableData }
        stripe border
        { ...props }
        >
        {
          this.tableColumn.map(item => {
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
      </el-table>
    )
  }
}
</script>
