<template>
  <el-row>
    <el-col :span="24">
      <el-select 
        v-model="selectedData"
        multiple
        :clearable="clearable"
        @remove-tag="handleTagChange"
        @clear="clearHandle">
        <el-input
          class="selectInput"
          :placeholder="placeholder"
          v-model="filterText">
        </el-input>
        <el-option :value="0" class="options">
          <el-tree id="tree-option"
            ref="selectTree"
            show-checkbox
            :accordion="accordion"
            :data="options"
            :props="defaultProps"
            :node-key="defaultProps.value"
            :default-expanded-keys="defaultExpandedKey"
            :filter-node-method="filterNode"
            @check="handleCheckChange">
          </el-tree>
        </el-option>
      </el-select>
      ID: {{selectedIds}}
    </el-col>
  </el-row>
</template>

<script>
export default {
  name: "el-tree-select2",
  props:{
    /* 配置项 */
    defaultProps:{
      type: Object,
      default:()=>{
        return {
          value: 'id',             // 每个树节点用来作为唯一标识的属性，整棵树应该是唯一的
          label: 'name',           // 显示名称
          children: 'children'     // 子级字段名
        }
      }
    },
    /* 可清空选项 */
    clearable:{
      type:Boolean,
      default:()=>{ return true }
    },
    /* 自动收起 */
    accordion:{
      type:Boolean,
      default:()=>{ return false }
    },
    placeholder:{
      type:String,
      default:()=>{return "检索关键字"}
    }
  },
  data() {
    return {
      filterText: '',
      selectedData:[],            // select结构上选中的数组
      defaultExpandedKey:[],
      selectedNode:[],          // tree结构上选中的节点
      selectedIds:[],            //选中的id集合
      list: [
        { id: 1, parentId: 0, name: "一级菜单A", rank: 1 },
        { id: 2, parentId: 0, name: "一级菜单B", rank: 1 },
        { id: 3, parentId: 0, name: "一级菜单C", rank: 1 },
        { id: 4, parentId: 1, name: "二级菜单A-A", rank: 2 },
        { id: 5, parentId: 1, name: "二级菜单A-B", rank: 2 },
        { id: 6, parentId: 2, name: "二级菜单B-A", rank: 2 },
        { id: 7, parentId: 4, name: "三级菜单A-A-A", rank: 3 },
        { id: 8, parentId: 7, name: "四级菜单A-A-A-A", rank: 4 },
        { id: 11, parentId: 7, name: "四级菜单A-A-A-A", rank: 4 },
        { id: 9, parentId: 0, name: "一级菜单C", rank: 1 },
        { id: 10, parentId: 0, name: "一级菜单end", rank: 1 },
      ],
    }
  },
  created(){
    let obj = {}
    // id去重
    this.list = this.list.reduce((item, next) => {
      obj[next.id] ? '' : (obj[next.id] = true && item.push(next))
      return item
    }, [])
  },
  mounted(){},
  methods: {
    handleCheckChange: function(node, checkedNode) {
      console.log(node, checkedNode)
      this.selectedIds = this.$refs.selectTree.getCheckedKeys().filter(_ => _);
      this.selectedNode = this.$refs.selectTree.getCheckedNodes().filter(_ => _);
      this.selectedData = this.selectedNode.reduce((acc, cur)=>{
        acc.push(cur.name+ '(' + cur.id + ')')
        return acc
      }, [])
      // this.$emit('getValue',this.selectedData)
    },
    handleTagChange: function(node) {
      let selectKeys = node.match(/\((.+?)\)/)[1]
      console.log(selectKeys)
      this.$refs.selectTree.setChecked(selectKeys, false)
      this.selectedIds = this.$refs.selectTree.getCheckedKeys().filter(_ => _);
      this.selectedNode = this.$refs.selectTree.getCheckedNodes().filter(_ => _);
      this.selectedData = this.selectedNode.reduce((acc, cur)=>{
        acc.push(cur.name+ '(' + cur.id + ')')
        return acc
      }, [])
      // this.$emit('getValue',this.selectedData)
    },
    // 清除选中
    clearHandle(){
      this.selectedData = []
      this.selectedIds = []
      this.defaultExpandedKey = []
      this.$refs.selectTree.setCheckedKeys([]);
      // this.$emit('getValue',null)
    },
    filterNode(value, data) {
      if (!value) return true;
      return data.name.indexOf(value) !== -1;
    },
  },
  watch: {
    filterText(val) {
      this.$refs.selectTree.filter(val);
    }
  },
  computed: {
    /* 转树形数据 */
    options() {
      let cloneData = JSON.parse(JSON.stringify(this.list)); // 对源数据深度克隆
      return cloneData.filter(father => {
        // 循环所有项，并添加children属性
        let branchArr = cloneData.filter(child => father.id == child.parentId); // 返回每一项的子级数组
        branchArr.length > 0 ? (father.children = branchArr) : ""; //给父级添加一个children属性，并赋值
        return father.parentId == 0; //返回第一层
      });
    }
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .el-scrollbar .el-scrollbar__view .el-select-dropdown__item{
    height: auto;
    max-height: 274px;
    padding: 0;
    overflow: hidden;
    overflow-y: auto;
  }
  .el-select-dropdown__item.selected{
    font-weight: normal;
  }
  ul li >>>.el-tree .el-tree-node__content{
    height:auto;
    padding: 0 20px;
  }
  .el-tree-node__label{
    font-weight: normal;
  }
  .el-tree >>>.is-current .el-tree-node__label{
    color: #409EFF;
    font-weight: 700;
  }
  .el-tree >>>.is-current .el-tree-node__children .el-tree-node__label{
    color:#606266;
    font-weight: normal;
  }
  .selectInput{
    padding: 0 5px;
    box-sizing: border-box;
  }
  /* 开发禁用 */
  /* .el-tree-node:focus>.el-tree-node__content{
    background-color:transparent;
    background-color: #f5f7fa;
    color: #c0c4cc;
    cursor: not-allowed;
  }
  .el-tree-node__content:hover{
    background-color: #f5f7fa;
  } */
</style>