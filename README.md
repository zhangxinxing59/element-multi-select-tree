# element-multi-select-tree

## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Main Parameter and Function
````
selectedData     // the label that shows on selection box
selectedNode     // the nodes array that user checked
selectedIds      // selected id arrays as search params
list             // origin data
handleTagChange  // a function to handle select items
handleCheckChange// a function to handle tree node check
````

### Use element tree component as a slot in a select component
````
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
````

### When you check a tree node, you should update the selection box 
````
handleCheckChange: function(node, checkedNode) {
    this.selectedIds = this.$refs.selectTree.getCheckedKeys().filter(_ => _);
    this.selectedNode = this.$refs.selectTree.getCheckedNodes().filter(_ => _);
    this.selectedData = this.selectedNode.reduce((acc, cur)=>{
        acc.push(cur.name+ '(' + cur.id + ')')
        return acc
    }, [])
},
````

### When you change your selected items, you should update the tree node status 
````
handleTagChange: function(node) {
    let selectKeys = node.match(/\((.+?)\)/)[1]
    this.$refs.selectTree.setChecked(selectKeys, false)
    this.selectedIds = this.$refs.selectTree.getCheckedKeys().filter(_ => _);
    this.selectedNode = this.$refs.selectTree.getCheckedNodes().filter(_ => _);
    this.selectedData = this.selectedNode.reduce((acc, cur)=>{
        acc.push(cur.name+ '(' + cur.id + ')')
        return acc
    }, [])
},
````
### Demo 展示
![](./src/assets/select-tree.gif)

----

>参考文献

>[Element UI框架中巧用树选择器](https://juejin.im/post/5c107290e51d4536425c8195)

>[vue实现 Element-UI 的 Tree Select 树形选择器组件（一）组件封装](https://blog.csdn.net/qq_36410795/article/details/89885659)
