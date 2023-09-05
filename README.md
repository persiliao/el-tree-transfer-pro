# el-tree-transfer-pro

[![](https://img.shields.io/npm/v/el-tree-transfer-pro)](https://www.npmjs.com/package/el-tree-transfer-pro)
[![](https://img.shields.io/npm/l/el-tree-transfer-pro)](https://github.com/persiliao/el-tree-transfer-pro/blob/master/LICENSE)
[![使用WebStorm开发维护](https://img.shields.io/badge/WebStorm-提供支持-blue.svg)](https://www.jetbrains.com/?from=el-tree-transfer-pro)

> A Vue3 + Element Plus based tree shuttle component. Styles inherit from Element Plus

> 一个基于Vue3 + Element Plus 树形穿梭组件。样式继承Element Plus


#### Installation

```shell
npm i el-tree-transfer-pro
```

#### Usage

```vue
<template>
  <el-tree-transfer-pro
      v-model="value"
      :data="data"
      :children-is-empty="true"
      :show-filter="true"
      :default-props="defaultProps"
  />
</template>

<script setup lang="ts">
import ElTreeTransferPro from "el-tree-transfer-pro"
import {ref} from 'vue'

const defaultProps = ref({
  label: 'label',
  children: 'children',
  value: 'id',
  disabled: 'disabled'
})

const value = ref([])

const data = ref([
  {
    id: 1,
    label: 'Level one 1',
    pid: 0,
    children: [
      {
        id: 4,
        pid: 1,
        label: 'Level two 1-1',
        children: [
          {
            id: 9,
            pid: 4,
            label: 'Level three 1-1-1'
          },
          {
            id: 10,
            pid: 4,
            label: 'Level three 1-1-2'
          }
        ]
      }
    ]
  },
  {
    id: 2,
    pid: 0,
    label: 'Level one 2',
    children: [
      {
        id: 5,
        pid: 2,
        label: 'Level two 2-1'
      },
      {
        id: 6,
        pid: 2,
        label: 'Level two 2-2'
      }
    ]
  },
  {
    id: 3,
    pid: 0,
    label: 'Level one 3',
    children: [
      {
        id: 7,
        pid: 3,
        label: 'Level two 3-1'
      },
      {
        id: 8,
        pid: 3,
        label: 'Level two 3-2'
      }
    ]
  }
])
</script>
```



## Attrs

| 属性名               | 说明                                                         | 类型    | 默认值          |
| :------------------- | :----------------------------------------------------------- | :------ | :-------------- |
| data                 | 展示数据                                                     | array   | —               |
| empty-text           | 数据为空的时候展示的文本                                     | string  | —               |
| titles               | 树形选择框上的标题                                           | array   | ['待选','已选'] |
| buttonTexts          | 穿梭左右按钮文本                                             | array   | —               |
| children-is-empty    | 统计只有children节点为空的数据项                             | boolean | true            |
| count-key            | 用于统计的数据key                                            | string  | —               |
| count-value          | 用于统计的数据值Value                                        | any     | —               |
| show-filter          | 显示过滤输入框                                               | boolean | true            |
| filter-placeholder   | 过滤输入框提示语                                             | string  | —               |
| node-key             | 每个树节点用来作为唯一标识的属性，整棵树应该是唯一的         | string  | —               |
| node-props           | 配置选项，具体看下表                                         | object  | —               |
| default-expand-all   | 是否默认展开所有节点                                         | boolean | true            |
| expand-on-click-node | 是否在点击节点的时候展开或者收缩节点， 默认值为 true，如果为 false，则只有点箭头图标的时候才会展开或者收缩节点。 | boolean | false           |
| check-on-click-node  | 是否在点击节点的时候选中节点，默认值为 false，即只有在点击复选框时才会选中节点。 | boolean | false           |
|                      |                                                              |         |                 |

## Node Props

| Props    | 说明                                                      | 类型                         | 可选值 | 默认值   |
| :------- | :-------------------------------------------------------- | :--------------------------- | :----- | :------- |
| label    | 指定节点标签为节点对象的某个属性值                        | string, function(data, node) | —      | label    |
| children | 指定子树为节点对象的某个属性值                            | string                       | —      | children |
| disabled | 指定节点选择框是否禁用为节点对象的某个属性值              | string, function(data, node) | —      | —        |
| value    | 指定获取的值为数据对象中某个字段, 如果为空将返回node 对象 | string                       | —      | value    |
| class    | 自定义节点类名                                            | string, function(data, node) | —      | —        |
