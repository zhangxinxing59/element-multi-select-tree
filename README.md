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

### Main Params
````
selectedData     // the label that shows on selection box
selectedNode     // the nodes array that user checked
selectedIds      // selected id arrays as search params
list             // origin data
````

### Use element tree component as a slot to be el-options in select component
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



### Demo 展示
![](./src/assets/select-tree.gif)
