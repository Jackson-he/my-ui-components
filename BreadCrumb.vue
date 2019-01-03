<script>
export default {
  props: {
    crumbData: {
      type: Array
    }
  },
  render () {
    return (
      <el-breadcrumb separator-class="el-icon-arrow-right">
        {
          this.crumbData.map(item => {
            return <el-breadcrumb-item to={ item.to } replace={ item.replace } separator={ item.separator } separator-class={ item['separator-class'] }>{ item.label }</el-breadcrumb-item>
          })
        }
      </el-breadcrumb>
    )
  }
}
</script>
