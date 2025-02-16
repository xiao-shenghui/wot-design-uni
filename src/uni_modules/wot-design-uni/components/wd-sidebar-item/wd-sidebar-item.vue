<template>
  <view
    @click="handleClick"
    :class="`wd-sidebar-item ${active ? 'wd-sidebar-item--active' : ''} ${prefix ? 'wd-sidebar-item--prefix' : ''}  ${
      suffix ? 'wd-sidebar-item--suffix' : ''
    } ${disabled ? 'wd-sidebar-item--disabled' : ''} ${customClass}`"
    :style="customStyle"
  >
    <slot name="icon"></slot>
    <template v-if="!$slots.icon && icon">
      <wd-icon custom-class="wd-sidebar-item__icon" :name="icon" size="20px"></wd-icon>
    </template>
    <wd-badge :model-value="badge" :is-dot="isDot" :max="max" v-bind="badgeProps" custom-class="wd-sidebar-item__badge">
      {{ label }}
    </wd-badge>
  </view>
</template>

<script lang="ts">
export default {
  name: 'wd-sidebar-item',
  options: {
    addGlobalClass: true,
    virtualHost: true,
    styleIsolation: 'shared'
  }
}
</script>

<script lang="ts" setup>
import { computed, getCurrentInstance } from 'vue'
import { inject, onBeforeUnmount, onMounted } from 'vue'

type BadgeType = 'primary' | 'success' | 'warning' | 'danger' | 'info'
interface BadgeProps {
  modelValue?: number | string | null
  bgColor?: string
  max?: number
  isDot?: boolean
  hidden?: boolean
  type?: BadgeType
  top?: number
  right?: number
  customClass?: string
  customStyle?: string
}

interface Props {
  // 当前选项标题
  label: string
  // 当前选项的值，唯一标识
  value: number | string
  // 徽标显示值
  badge?: number | string | null
  // 徽标属性，透传给 Badge 组件
  badgeProps?: BadgeProps
  // 图标
  icon?: string
  // 是否点状徽标
  isDot?: boolean
  // 徽标最大值
  max?: number
  // 是否禁用
  disabled?: boolean
  // 自定义样式
  customStyle?: string
  // 自定义样式类
  customClass?: string
}
const props = withDefaults(defineProps<Props>(), {
  modelValue: 0,
  disabled: false,
  max: 99,
  customStyle: '',
  customClass: ''
})

const parent = inject<any>('wdSidebar', null) // 父组件

const { proxy } = getCurrentInstance() as any

const active = computed(() => {
  let active: boolean = false
  if (parent && parent.value === props.value) {
    active = true
  }
  return active
})

const prefix = computed(() => {
  let prefix: boolean = false
  if (parent) {
    let activeIndex: number = parent.children.findIndex((c) => {
      return c.value === parent.value
    })

    let currentIndex: number = parent.children.findIndex((c) => {
      return c.value === props.value
    })

    if (currentIndex === activeIndex - 1) {
      prefix = true
    }
  }
  return prefix
})

const suffix = computed(() => {
  let suffix: boolean = false
  if (parent) {
    let activeIndex: number = parent.children.findIndex((c) => {
      return c.value === parent.value
    })

    let currentIndex: number = parent.children.findIndex((c) => {
      return c.value === props.value
    })

    if (currentIndex === activeIndex + 1) {
      suffix = true
    }
  }
  return suffix
})

onMounted(() => {
  parent && parent.setChild && parent.setChild(proxy)
})

onBeforeUnmount(() => {
  parent && parent.removeChild && parent.removeChild(proxy)
})

function handleClick() {
  if (props.disabled) {
    return
  }
  parent && parent.setChange && parent.setChange(props.value, props.label)
}
</script>

<style lang="scss" scoped>
@import './index.scss';
</style>
