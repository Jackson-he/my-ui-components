<script>
/** 详情展示组件：标题对应内容
*
* @param { Number, String } columns 自定义几列布局
* @param { Array } config 每一项的配置
*      Exg1: { label: '时间', prop: 'time', width: '300', interval: '10' }
*      Exg2: { label: '时间', render: scope => this.$moment(+scope.time).format('YYYY-MM-DD HH:mm:ss') }
*      width和interval可以单独配置或者在组件中传参时统一配置，分别未 'label-width'和'line-interval'
* @param { Number, String } columns 自定义几列布局
* @param { Object } content 数据
* @param { Number, String } 'line-interval' 行间隔统一配置，也可在config中单独配置
* @param { Number, String } 'label-width' label宽度隔统一配置，也可在config中单独配置
*/
export default {
  data () {
    return {}
  },
  props: {
    // 行间隔
    'line-interval': {
      type: [ Number, String ]
    },
    // label宽度
    'label-width': {
      type: [ Number, String ]
    },
    // 列数
    columns: {
      type: [ Number, String ]
    },
    // 配置
    config: {
      type: Array,
      default: function () {
        return []
      }
    },
    // 数据
    content: {
      type: Object,
      default: function () {
        return {}
      }
    }
  },
  render () {
    let content = this.content

    let config = this.config.map(item => {
      const width = item.width || this.labelWidth
      const interval = item.interval || this.lineInterval

      return (
        <el-col span={ 24 / this.columns } class="each-col" style={ `margin-bottom: ${interval}px`}>
          <label class="name" style={`width:${width}px`}>{ item.label }</label>
          {
            item.render
              ? item.render(content)
              : <div class="content">{ content[item.prop] }</div>
          }
        </el-col>
      )
    })

    const ret = this._splitCol(this.columns, config)

    const map = ret.map(item => {
      return (
        <el-row gutter={24}>
          { item }
        </el-row>
      )
    })

    return <div class="description-list">{ map }</div>
  },
  methods: {
    _splitCol (columns, targetArr) {
      const ret = []
      let colArr = []
      let count = 1
      while (targetArr.length) {
        colArr.push(targetArr.splice(0, 1))
        if (count === columns || targetArr.length === 0) {
          ret.push(colArr)
          count = 0
          colArr = []
        }
        count++
      }
      return ret
    }
  }
}
</script>
<style lang="scss" scoped>
  .description-list {
    .each-col {
      display: flex;
      align-items: center;
    }
    .name {
      color: black;
    }
  }
</style>
