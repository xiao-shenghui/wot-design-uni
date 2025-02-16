<template>
  <view :class="`wd-table ${border ? 'is-border' : ''}`" :style="tableStyle">
    <scroll-view
      :enable-flex="true"
      :throttle="false"
      :scrollLeft="reactiveState.scrollLeft"
      :scroll-x="true"
      class="wd-table__header"
      @scroll="scroll"
      v-if="showHeader"
    >
      <view id="table-header" class="wd-table__content" :style="realWidthStyle" style="position: sticky; top: 0; z-index: 2">
        <view
          :class="`wd-table__cell ${border ? 'is-border' : ''} ${column.fixed ? 'is-fixed' : ''} ${stripe ? 'is-stripe' : ''} is-${column.align} ${
            isLastFixed(column) && reactiveState.scrollLeft ? 'is-shadow' : ''
          }`"
          :style="headerCellStyle(index)"
          v-for="(column, index) in reactiveState.columns"
          :key="index"
        >
          <wd-sort-button
            v-model="column.sortDirection"
            allow-reset
            :line="false"
            :title="column.label"
            @change="({ value }) => handleSortChange(value, index)"
            v-if="column.sortable"
          />
          <text v-else :class="`wd-table__value ${ellipsis ? 'is-ellipsis' : ''}`">{{ column.label }}</text>
        </view>
      </view>
    </scroll-view>
    <scroll-view
      class="wd-table__body"
      :style="bodyStyle"
      :enable-flex="true"
      :throttle="false"
      :scroll-x="true"
      @scroll="scroll"
      :scrollLeft="reactiveState.scrollLeft"
    >
      <view id="table-body" class="wd-table__content" :style="realWidthStyle">
        <slot></slot>
      </view>
    </scroll-view>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-table',
  options: {
    addGlobalClass: true,
    virtualHost: true,
    styleIsolation: 'shared'
  }
}
</script>

<script lang="ts" setup>
import { type CSSProperties, computed, provide, watch, reactive } from 'vue'
import { addUnit, debounce, deepClone, isDef, objToStyle } from '../common/util'
import type { SortDirection, TableColumn } from '../wd-table-col/types'

interface Props {
  // 显示的数据
  data: Array<any>
  // 是否带有边框
  border?: boolean
  // 是否为斑马纹 table
  stripe?: boolean
  // Table 的高度
  height?: string
  // 行高
  rowHeight?: number | string
  // 是否显示表头
  showHeader?: boolean
  // 是否超出2行隐藏
  ellipsis?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  // 显示的数据
  data: () => [],
  // table行是否为斑马纹
  stripe: true,
  // 是否显示边框
  border: true,
  // table高度
  height: '80vh',
  // 行高
  rowHeight: 50,
  // 是否显示表头
  showHeader: true,
  // 是否超出2行隐藏
  ellipsis: true
})

watch(
  () => props.data,
  (newValue) => {
    reactiveState.data = newValue
  },
  { deep: true }
)

watch(
  () => props.stripe,
  (newValue) => {
    reactiveState.stripe = newValue
  },
  { deep: true }
)

watch(
  () => props.border,
  (newValue) => {
    reactiveState.border = newValue
  },
  { deep: true }
)

watch(
  () => props.height,
  (newValue) => {
    reactiveState.height = newValue
  },
  { deep: true }
)

watch(
  () => props.rowHeight,
  (newValue) => {
    reactiveState.rowHeight = newValue
  },
  { deep: true }
)
watch(
  () => props.showHeader,
  (newValue) => {
    reactiveState.showHeader = newValue
  },
  { deep: true }
)

watch(
  () => props.ellipsis,
  (newValue) => {
    reactiveState.ellipsis = newValue
  },
  { deep: true }
)

const reactiveState = reactive({
  data: props.data,
  stripe: props.stripe,
  border: props.border,
  height: props.height,
  rowHeight: props.rowHeight,
  showHeader: props.showHeader,
  ellipsis: props.ellipsis,
  scrollLeft: 0,
  columns: [] as TableColumn[],
  setRowClick,
  setColumns
})

const scroll = debounce(handleScroll, 100, false) // 滚动事件

provide('wdTable', reactiveState)

const emit = defineEmits(['click', 'sort-method', 'row-click'])

/**
 * 容器样式
 */
const tableStyle = computed(() => {
  const style: CSSProperties = {}
  if (isDef(props.height)) {
    style['max-height'] = addUnit(props.height)
  }
  return objToStyle(style)
})

const realWidthStyle = computed(() => {
  const style: CSSProperties = {
    display: 'flex'
  }
  let width: string | number = ''
  reactiveState.columns.forEach((column) => {
    width = width ? `${width} + ${addUnit(column.width)}` : addUnit(column.width)
  })
  style['width'] = `calc(${width})`
  return objToStyle(style)
})

const bodyStyle = computed(() => {
  const style: CSSProperties = {}
  if (isDef(props.height)) {
    style['height'] = isDef(props.rowHeight) ? `calc(${props.data.length} * ${addUnit(props.rowHeight)})` : `calc(${props.data.length} * 50px)`
  }
  return `${objToStyle(style)};`
})

/**
 * 是否最后一个固定元素
 * @param column 列数据
 */
function isLastFixed(column: TableColumn) {
  let isLastFixed: boolean = false
  if (column.fixed && isDef(reactiveState.columns)) {
    const columns = reactiveState.columns.filter((column) => {
      return column.fixed
    })
    if (columns.length && columns[columns.length - 1].prop === column.prop) {
      isLastFixed = true
    }
  }
  return isLastFixed
}

/**
 * 设置列
 * @param column 列
 */
function setColumns(column: TableColumn) {
  reactiveState.columns = deepClone([...reactiveState.columns, column])
}

/**
 * 表头单元格样式
 */
function headerCellStyle(columnIndex: number) {
  const style: CSSProperties = {}
  if (isDef(reactiveState.columns[columnIndex].width)) {
    style['width'] = addUnit(reactiveState.columns[columnIndex].width)
  }
  if (reactiveState.columns[columnIndex].fixed) {
    if (columnIndex > 0) {
      let left: string | number = ''
      reactiveState.columns.forEach((column, index) => {
        if (index < columnIndex) {
          left = left ? `${left} + ${addUnit(column.width)}` : addUnit(column.width)
        }
      })
      style['left'] = `calc(${left})`
    } else {
      style['left'] = 0
    }
  }
  return objToStyle(style)
}

/**
 * 排序
 * @param value
 * @param index
 */
function handleSortChange(value: SortDirection, index: number) {
  reactiveState.columns[index].sortDirection = value
  reactiveState.columns.forEach((col, i) => {
    if (index != i) {
      col.sortDirection = 0
    }
  })
  emit('sort-method', reactiveState.columns[index])
}

/**
 * 滚动事件
 */
function handleScroll(event) {
  if (!props.showHeader) {
    return
  }
  reactiveState.scrollLeft = event.detail.scrollLeft
}

function setRowClick(index: number) {
  emit('row-click', { rowIndex: index })
}
</script>

<!-- <script module="touch" lang="wxs">
var xDown = null;
var yDown = null;

function touchstart(event) {
  xDown = event.touches[0].clientX;
  yDown = event.touches[0].clientY;
}

function touchmove(event , ownerInstance) {
    if (!xDown || !yDown) {
        return;
    }
    var xUp = event.touches[0].clientX;
    var yUp = event.touches[0].clientY;

    var xDiff = xDown - xUp;
    var yDiff = yDown - yUp;

    if (Math.abs(xDiff) > Math.abs(yDiff)) {
        // 沿着x轴移动
        if (xDiff > 0) {
            // 向左移动
            disableScrollY(ownerInstance);
        } else {
            // 向右移动
            disableScrollY(ownerInstance);
        }
    } else {
        // 沿着y轴移动
        if (yDiff > 0) {
            // 向上移动
            disableScrollX(ownerInstance);
        } else {
            // 向下移动
            disableScrollX(ownerInstance);
        }
    }
}

function touchend(event , ownerInstance) {
    enableScroll(ownerInstance);
}

function disableScrollX(ins) {
  ins.setStyle({
    'overflow-x':"hidden",
    "color":"red"
  })
  // console.log(ins,'disableScrollX');
    // document.documentElement.style.overflowX = "hidden";
}

function disableScrollY(ins) {
  // console.log(ins,'disableScrollY');
  ins.setStyle({
    'overflow-y':"hidden"
  })
    // document.documentElement.style.overflowY = "hidden";
}

function enableScroll(ins) {
  // console.log(ins,'enableScroll');
  ins.setStyle({
    'overflow-x':"auto",
    'overflow-y':"auto"
  })
  console.log(ins,'enableScroll');
    // document.documentElement.style.overflowX = "auto";
    // document.documentElement.style.overflowY = "auto";
}


function scroll(event, ins) {
  const scrollLeft = event.detail.scrollLeft
}

function transitionend(event, ins) {
  console.log(event);
  console.log(ins);
}

module.exports= {
  scroll:scroll,
  touchstart:touchstart,
  touchend:touchend,
  touchmove:touchmove
}
</script> -->
<style lang="scss" scoped>
@import './index.scss';

// scroll-view::-webkit-scrollbar {
//   display: none;
//   width: 0;
//   height: 0;
//   border-radius: 0;
//   background-color: transparent;
//   color: transparent;
// }
</style>
