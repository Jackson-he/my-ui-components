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
            return <el-breadcrumb-item to={ item.to }>{ item.label }</el-breadcrumb-item>
          })
        }
      </el-breadcrumb>
    )
  }
}
</script>
