<!--suppress TypeScriptValidateTypes -->
<template>
  <div class="container">
    <div class="checkbox-container">
      <el-checkbox
          v-model="checkAll"
          :disabled="checkAllDisabled"
          :indeterminate="isIndeterminate"
          @change="checkAllClick">
        <el-text class="title">{{ title }}</el-text>
      </el-checkbox>
      <el-text class="statistic">
        <el-text class="checked">{{ checkedTotalLength }}</el-text>
        <el-text class="delimiter">/</el-text>
        <el-text class="total">{{ treeTotalLength }}</el-text>
      </el-text>
    </div>
    <div class="tree-container">
      <el-input v-if="showFilter" v-model="filterText" :placeholder="filterPlaceholder" class="filter-input" clearable>
        <template #prefix>
          <el-icon class="el-input__icon">
            <Search />
          </el-icon>
        </template>
      </el-input>
      <el-tree
          ref="elTreeRef"
          :check-on-click-node="true"
          :data="treeData"
          :default-expand-all="defaultExpandAll"
          :empty-text="emptyText"
          :expand-on-click-node="false"
          :filter-node-method="filterNode"
          :highlight-current="false"
          :node-key="nodeKey"
          :props="nodeProps"
          show-checkbox
          @check="updateCheckedItems"
      />
    </div>
  </div>
</template>

<script lang="ts" setup>
import { Search } from '@element-plus/icons-vue'
import { ElTree } from 'element-plus'
import { computed, onMounted, ref, watch } from 'vue'

const props = defineProps({
  modelValue: Array,
  title: {
    type: String,
    required: true
  },
  emptyText: {
    type: String,
    default: null
  },
  data: {
    type: Array,
    required: true
  },
  nodeKey: {
    type: String,
    required: true,
    default: 'id'
  },
  pidKey: {
    type: String,
    required: true,
    default: 'pid'
  },
  nodeProps: {
    type: Object,
    required: true,
    default: {
      label: 'label',
      children: 'children',
      value: 'value',
      disabled: 'disabled'
    }
  },
  defaultExpandAll: {
    type: Boolean,
    required: true,
    default: true
  },
  childrenIsEmpty: {
    type: Boolean,
    required: true,
    default: false
  },
  countKey: {
    type: [String, null],
    required: true
  },
  countValue: {
    type: [Number, String, Boolean, Array, Object, null],
    required: true
  },
  showFilter: {
    type: Boolean,
    default: true
  },
  filterPlaceholder: {
    type: String,
    default: ''
  }
})

const emits = defineEmits(['update:modelValue', 'check'])

const elTreeRef = ref<InstanceType<typeof ElTree>>()
const isIndeterminate = ref(false)

const treeData = ref<any[]>([])
const treeAllItems = ref<any[]>([])
const treeTotalLength = computed(() => {
  return treeAllItems.value.length
})

const filterText = ref<String>('')
watch(filterText, (val) => {
  elTreeRef.value!.filter(val)
})

const checkAll = ref(false)
const checkAllDisabled = computed(() => {
  return treeTotalLength.value === 0
})

const checkedItems = ref([])
const checkedTotalLength = computed(() => {
  return checkedItems.value.length
})

const filterNode = (value: string, data: any) => {
  if (!value) {
    return true
  }
  return data[props.nodeProps.label].includes(value)
}

const isNotNull = (value: any) => {
  return value !== undefined && value !== null
}

const isNotEmpty = (value: string) => {
  return isNotNull(value) && value.trim() !== ''
}

const addCheckedItem = (item) => {
  if (props.childrenIsEmpty === true) {
    if ((!isNotNull(item[props.nodeProps.children])) || item[props.nodeProps?.children].length === 0) {
      checkedItems.value.push(item)
    }
  } else if (isNotEmpty(props.countKey) && isNotNull(props.countValue)) {
    if (item[props.countKey] === props.countValue) {
      checkedItems.value.push(item)
    }
  } else {
    checkedItems.value.push(item)
  }
}

const setCheckedItems = (items: any[]) => {
  checkedItems.value = []
  items.forEach(item => {
    addCheckedItem(item)
  })
}

const clearCheckedItems = () => {
  checkedItems.value = []
  elTreeRef.value.setCheckedNodes([], false)
}

const setCheckAll = (checked: boolean) => {
  checkAll.value = checked
}

const addTreeAllItem = (item: any) => {
  if (props.childrenIsEmpty === true) {
    if ((!isNotNull(item[props.nodeProps?.children])) || item[props.nodeProps?.children].length === 0) {
      treeAllItems.value.push(item)
    }
  } else if (isNotEmpty(props.countKey) && props.countValue !== undefined) {
    if (item[props.countKey] === props.countValue) {
      treeAllItems.value.push(item)
    }
  } else {
    treeAllItems.value.push(item)
  }
}

const clearAllItems = () => {
  treeAllItems.value = []
}

const setTreeAllItems = (treeArr: any[], clear: boolean = true) => {
  if (clear) {
    clearCheckedItems()
    clearAllItems()
  }
  for (const item of treeArr) {
    addTreeAllItem(item)
    if (item[props.nodeProps?.children]) {
      setTreeAllItems(item[props.nodeProps?.children], false)
    }
  }
  emitModelValue()
}

const updateCheckedItems = () => {
  setCheckedItems(elTreeRef.value.getCheckedNodes(true, true))
  emits('check', {
    keys: elTreeRef.value.getCheckedKeys(),
    nodes: elTreeRef.value.getCheckedNodes(false, true),
    remove: () => {
      elTreeRef.value.getCheckedNodes().forEach((node: any) => {
        elTreeRef.value.remove(node)
      })
      setTreeAllItems(treeData.value, true)
    }
  })
}

const emitModelValue = () => {
  emits('update:modelValue', treeAllItems.value)
}

const checkAllClick = (checked: boolean) => {
  if (checked) {
    elTreeRef.value.setCheckedNodes(treeData.value, false)
  } else {
    elTreeRef.value.setCheckedNodes([], false)
  }
  updateCheckedItems()
}

watch(checkedTotalLength, (newCheckedLength) => {
  isIndeterminate.value = newCheckedLength > 0
  setCheckAll(newCheckedLength !== 0 && treeTotalLength.value === newCheckedLength)
})

watch(() => props.data, (newData) => {
  newData?.forEach((node: any) => {
    node.children = []
    if (!elTreeRef.value.getNode(node[props.nodeKey])) {
      elTreeRef.value.append(node, node[props.pidKey])
    }
  })
  setTreeAllItems(treeData.value, true)
})

onMounted(() => {
  treeData.value = props.data
  setTreeAllItems(treeData.value)
})
</script>

<style lang="scss" scoped>
.container {
  width: 100%;
  height: 100%;
  border-radius: var(--el-border-radius-base);
  background-color: var(--el-bg-color);
  border: var(--el-border-width) var(--el-border-style) var(--el-border-color);
  
  .checkbox-container {
    display: flex;
    justify-content: space-between;
    padding: 6px 12px;
    border-bottom: var(--el-border-width) var(--el-border-style) var(--el-border-color);
  }
  
  .tree-container {
    padding: 12px;
    
    .filter-input {
      margin-bottom: 6px;
    }
  }
}
</style>
