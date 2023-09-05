<template>
  <div class="tree-transfer-container">
    <el-tree-transfer-select
        :children-is-empty="childrenIsEmpty"
        :countKey="countKey"
        :countValue="countValue"
        :data="leftData"
        :default-expand-all="defaultExpandAll"
        :empty-text="emptyText"
        :filter-placeholder="filterPlaceholder"
        :node-key="nodeKey"
        :node-props="nodeProps"
        :pid-key="pidKey"
        :show-filter="showFilter"
        :style="{minWidth: minWidth}"
        :title="leftTitle"
        class="tree-transfer-select-container"
        @check="onLeftCheckClick"
    />
    <div class="tree-transfer-button-container">
      <el-button
          v-if="toLeftButtonText"
          :disabled="toLeftDisabled"
          :icon="ArrowLeft"
          type="primary"
          @click="transferToLeft"
      >
        <el-text>{{ toLeftButtonText }}</el-text>
      </el-button>
      <el-button
          v-else
          :disabled="toLeftDisabled"
          :icon="ArrowLeft"
          type="primary"
          @click="transferToLeft"
      />
      <el-button
          v-if="toRightButtonText"
          :disabled="toRightDisabled"
          :icon="ArrowRight"
          type="primary"
          @click="transferToRight"
      >
        <el-text>{{ toRightButtonText }}</el-text>
      </el-button>
      <el-button
          v-else
          :disabled="toRightDisabled"
          :icon="ArrowRight"
          type="primary"
          @click="transferToRight"
      />
    </div>
    <el-tree-transfer-select
        v-model="rightCheckedValue"
        :children-is-empty="childrenIsEmpty"
        :countKey="countKey"
        :countValue="countValue"
        :data="rightData"
        :default-expand-all="defaultExpandAll"
        :empty-text="emptyText"
        :filter-placeholder="filterPlaceholder"
        :node-key="nodeKey"
        :node-props="nodeProps"
        :pid-key="pidKey"
        :show-filter="showFilter"
        :style="{minWidth: minWidth}"
        :title="rightTitle"
        class="tree-transfer-select-container"
        @check="onRightCheckClick"
    />
  </div>
</template>

<script lang="ts" setup>
import { ArrowLeft, ArrowRight } from '@element-plus/icons-vue'
import { computed, ref, watch } from 'vue'
import ElTreeTransferSelect from './el-tree-transfer-select.vue'

const props = defineProps({
  modelValue: Array,
  titles: {
    type: Array,
    default: ['待选', '已选']
  },
  emptyText: {
    type: String,
    default: null
  },
  buttonTexts: {
    type: Array,
    default: []
  },
  minWidth: {
    type: [String, Number],
    default: ''
  },
  data: {
    type: Array,
    required: true
  },
  nodeKey: {
    type: String,
    default: 'id'
  },
  pidKey: {
    type: String,
    default: 'pid'
  },
  nodeProps: {
    type: Object,
    default: {
      label: 'label',
      children: 'children',
      value: 'value',
      disabled: 'disabled'
    }
  },
  defaultExpandAll: {
    type: Boolean,
    default: true
  },
  childrenIsEmpty: {
    type: Boolean,
    default: true
  },
  countKey: {
    type: String,
    default: ''
  },
  countValue: {
    type: [Number, String, Boolean, Array, Object],
    default: ''
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

const emits = defineEmits(['update:modelValue'])

const deepCopy = (value) => {
  return JSON.parse(JSON.stringify(value))
}

const leftTitle = computed(() => {
  return props.titles[0] ?? ''
})
const toLeftButtonText = computed(() => {
  return props.buttonTexts[0] ?? ''
})
const leftData = ref([])
const leftCheckedObj = ref<any>(null)
const leftCheckedValue = ref<any>(null)
const leftCheckedKeys = computed(() => {
  return leftCheckedObj.value?.keys ?? []
})

const toRightDisabled = computed(() => {
  return (leftData.value && leftData.value?.length === 0) || (leftCheckedKeys.value && leftCheckedKeys.value.length === 0)
})

const onLeftCheckClick = (value) => {
  leftCheckedObj.value = value
}

const transferToRight = () => {
  leftCheckedObj.value!.remove()
  rightData.value = deepCopy(leftCheckedObj.value!.nodes ?? [])
  leftCheckedObj.value = null
}

const rightTitle = computed(() => {
  return props.titles[1] ?? ''
})
const toRightButtonText = computed(() => {
  return props.buttonTexts[1] ?? ''
})
const rightData = ref([])
const rightCheckedObj = ref<any>(null)
const rightCheckedValue = ref<any>(null)
const rightCheckedKeys = computed(() => {
  return rightCheckedObj.value?.keys ?? []
})

const toLeftDisabled = computed(() => {
  return (rightData.value && rightData.value?.length === 0) || (rightCheckedKeys.value && rightCheckedKeys.value?.length === 0)
})

const onRightCheckClick = (value) => {
  rightCheckedObj.value = value
}

const transferToLeft = () => {
  rightCheckedObj.value!.remove()
  leftData.value = deepCopy(rightCheckedObj.value!.nodes ?? [])
  rightCheckedObj.value = null
}

watch(rightCheckedValue, (newRightCheckedValue) => {
  emits('update:modelValue', newRightCheckedValue.map(item => {
    if (props.nodeProps.value === null || props.nodeProps.value === '') {
      return item
    }
    return item[props.nodeProps.value]
  }))
})

watch(() => props.data, (newData) => {
  leftData.value = newData
}, { immediate: true })

</script>

<style lang="scss" scoped>
.tree-transfer-container {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  height: 100%;
  width: 100%;
  
  .tree-transfer-button-container {
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100%;
    width: 100%;
    margin-left: 12px;
    margin-right: 12px;
  }
}
</style>
