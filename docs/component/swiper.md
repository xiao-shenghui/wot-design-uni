<frame/>

# Swiper 轮播图 <el-tag text style="vertical-align: middle;margin-left:8px;" effect="plain">0.1.22</el-tag>

用于创建轮播图，它支持水平和垂直方向的滑动，可以自定义样式和指示器位置。

## 基础用法

通过设置 `autoplay` 属性控制轮播图是否自动播放，设置 `current` 属性初始化轮播图展示的滑块，监听滑块 `click` 来处理点击事件，而每一页轮播结束后，会触发 `change` 事件。通过

```html
<wd-swiper :list="swiperList" autoplay :current="0" @click="handleClick" @change="onChange"></wd-swiper>
```

```ts
const swiperList = ref([
  'https://unpkg.com/wot-design-uni-assets/redpanda.jpg',
  'https://unpkg.com/wot-design-uni-assets/capybara.jpg',
  'https://unpkg.com/wot-design-uni-assets/panda.jpg',
  'https://img.yzcdn.cn/vant/cat.jpeg',
  'https://unpkg.com/wot-design-uni-assets/meng.jpg'
])
function handleClick(e) {
  console.log(e)
}
function onChange(e) {
  console.log(e)
}
```

## 点条状指示器

```html
<wd-swiper :list="swiperList" autoplay :current="1" :indicator="{ type: 'dots-bar' }" @click="handleClick" @change="onChange"></wd-swiper>
```

## 数字指示器

```html
<wd-swiper
  :list="swiperList"
  autoplay
  :current="2"
  :indicator="{ type: 'fraction' }"
  indicatorPosition="bottom-right"
  @click="handleClick"
  @change="onChange"
></wd-swiper>
```

## 手动切换

```html
<wd-swiper
  :list="swiperList"
  :autoplay="false"
  :current="3"
  :indicator="{ showControls: true }"
  :loop="false"
  @click="handleClick"
  @change="onChange"
></wd-swiper>
```

## 卡片样式

设置 `previousMargin` 和 `nextMargin` 实现卡片轮播的样式。

```html
<view class="card-swiper">
  <wd-swiper
    autoplay
    :current="4"
    custom-indicator-class="custom-indicator-class"
    custom-image-class="custom-image"
    custom-next-image-class="custom-image-prev"
    custom-prev-image-class="custom-image-prev"
    :indicator="{ type: 'dots' }"
    :list="swiperList"
    previousMargin="24px"
    nextMargin="24px"
  ></wd-swiper>
</view>
```

```scss
.card-swiper {
  --wot-swiper-radius: 0;
  --wot-swiper-item-padding: 0 24rpx;
  --wot-swiper-nav-dot-color: #e7e7e7;
  --wot-swiper-nav-dot-active-color: #4d80f0;
  padding-bottom: 24rpx;
  :deep(.custom-indicator-class) {
    bottom: -16px;
  }
  :deep(.custom-image) {
    border-radius: 12rpx;
  }
  :deep(.custom-image-prev) {
    height: 168px !important;
  }
}
```

## 同时展示 2 个滑块

设置 `display-multiple-items` 属性，控制同时展示的滑块数量。

```html
<view class="card-swiper">
  <wd-swiper
    autoplay
    :current="4"
    :display-multiple-items="2"
    custom-indicator-class="custom-indicator-class"
    custom-image-class="custom-image"
    custom-next-image-class="custom-image-prev"
    custom-prev-image-class="custom-image-prev"
    :indicator="{ type: 'dots' }"
    :list="swiperList"
    previousMargin="24px"
    nextMargin="24px"
  ></wd-swiper>
</view>
```

```scss
.card-swiper {
  --wot-swiper-radius: 0;
  --wot-swiper-item-padding: 0 24rpx;
  --wot-swiper-nav-dot-color: #e7e7e7;
  --wot-swiper-nav-dot-active-color: #4d80f0;
  padding-bottom: 24rpx;
  :deep(.custom-indicator-class) {
    bottom: -16px;
  }
  :deep(.custom-image) {
    border-radius: 12rpx;
  }
  :deep(.custom-image-prev) {
    height: 168px !important;
  }
}
```

## 垂直方向

`direction` 设置为 `vertical` 后滑块会纵向排列。

```html
<wd-swiper
  :list="swiperList"
  direction="vertical"
  indicatorPosition="right"
  autoplay
  :current="1"
  :indicator="{ type: 'dots-bar' }"
  @click="handleClick"
  @change="onChange"
></wd-swiper>
```

## 自定义指示器

通过 `indicator` 插槽可以自定义指示器的样式。

```html
<wd-swiper :list="swiperList" direction="vertical" indicatorPosition="right" autoplay :current="1" @click="handleClick" @change="onChange">
  <template #indicator="{ current, total }">
    <view class="custom-indicator" style="position: absolute; bottom: 24rpx; right: 24rpx">{{ current + 1 }}/{{ total }}</view>
  </template>
</wd-swiper>
```

```scss
.custom-indicator {
  padding: 0 12rpx;
  height: 48rpx;
  line-height: 48rpx;
  border-radius: 45%;
  background: rgba(0, 0, 0, 0.6);
  color: #ffffff;
  font-size: 24rpx;
}
```

## Attributes

| 参数                 | 说明                                                   | 类型                      | 可选值      | 默认值     | 最低版本 |
| -------------------- | ------------------------------------------------------ | ------------------------- | ---------- | ---------- | -------- |
| autoplay             | 是否自动播放                                           | `boolean`                   | -          | true       | 0.1.22   |
| current              | 当前轮播在哪一项（下标）                               | `number`                    | -          | 0          | 0.1.22   |
| direction            | 轮播滑动方向                                           | `DirectionType`             | `horizontal, vertical`     | horizontal | 0.1.22   |
| displayMultipleItems | 同时显示的滑块数量                                     | `number`                    | -          | 1          | 0.1.22   |
| duration             | 滑动动画时长                                           | `number`                    | -          | 300        | 0.1.22   |
| easingFunction       | 切换缓动动画类型（微信小程序、快手小程序、京东小程序） | `EasingType`                | -          | default    | 0.1.22   |
| height               | 轮播的高度                                             | `string \| number`          | -          | 192        | 0.1.22   |
| interval             | 轮播间隔时间                                           | `number`                    | -          | 5000       | 0.1.22   |
| list                 | 图片列表                                               | `string[] \| SwiperList[]`  | -          | -          | 0.1.22   |
| loop                 | 是否循环播放                                           | `boolean`                   | -          | true       | 0.1.22   |
| nextMargin           | 后边距                                                 | `string \| number`          | -          | 0          | 0.1.22   |
| indicatorPosition   | 指示器展示位置                                       | `IndicatorPositionType`    | `left, top-left, top, top-right, bottom-left, bottom, bottom-right, right` | bottom | 0.1.22   |
| previousMargin       | 前边距                                                 | `string \| number`          | -          | 0          | 0.1.22   |
| snapToEdge           | 边距是否应用到第一个、最后一个元素                     | `boolean`                   | -          | false      | 0.1.22   |
| indicator           | 指示器全部配置                                         | `SwiperIndicatorProps \| boolean` | -          | true       | 0.1.22   |
| customStyle          | 外部自定义样式                                         | `string`                    | -          | ''         | 0.1.22   |


### DirectionType

轮播滑动方向，可选值为 `'horizontal'` 和 `'vertical'`。

### EasingType

切换缓动动画类型，可选值为 `'default'`、`'linear'`、`'easeInCubic'`、`'easeOutCubic'` 和 `'easeInOutCubic'`。

### IndicatorPositionType

页码信息展示位置，可选值为 `'left'`、`'top-left'`、`'top'`、`'top-right'`、`'bottom-left'`、`'bottom'`、`'bottom-right'` 和 `'right'`。

### SwiperList

轮播图项的列表配置，包括 `value` 和 `ariaLabel` 属性。

### SwiperIndicatorProps

| 参数                | 说明                       | 类型                   | 可选值                                                                   | 默认值     | 最低版本 |
| ------------------- | -------------------------- | ---------------------- | ------------------------------------------------------------------------ | ---------- | -------- | 
| current             | 当前轮播在哪一项（下标）   | Number                 | -                                                                        | 0          | 0.1.22   |
| direction           | 轮播滑动方向               | DirectionType          | `horizontal, vertical`                                                   | horizontal | 0.1.22   |
| min-show-num        | 小于这个数字不会显示导航器 | Number                 | -                                                                        | 2          | 0.1.22   |
| pagination-position | 页码信息展示位置           | IndicatorPositionType | `left, top-left, top, top-right, bottom-left, bottom, bottom-right, right` | bottom     | 0.1.22   |
| show-controls       | 是否显示控制按钮           | Boolean                | -                                                                        | false      | 0.1.22   |
| total               | 总共的项数                 | Number                 | -                                                                        | 0          | 0.1.22   |
| type                | 导航器类型                 | SwiperIndicatorType   | `dots, dots-bar, fraction `                                        | dots       | 0.1.22   |
| autoplay            | 是否自动播放               | boolean                | -                                                                        | true       | 0.1.22   | 

## Events

| 事件名称 | 说明             | 参数                                                        | 最低版本 |
| -------- | ---------------- | ----------------------------------------------------------- | -------- |
| click    | 点击轮播项时触发 | `(index: number)`                                           | 0.1.22   |
| change   | 轮播切换时触发   | `(current: number, source: 'autoplay' \| 'touch' \| 'nav')	` | 0.1.22   |

## Slot

| name      | 说明         | 参数                                 | 最低版本 |
| --------- | ------------ | ------------------------------------ | -------- |
| indicator | 自定义指示器 | `{ current: number, total: number }` | 0.1.22   |

## 外部样式类

| 类名                 | 说明                 | 最低版本 |
| -------------------- | -------------------- | -------- |
| customClass          | 外部自定义类名       | 0.1.22   |
| customIndicatorClass       | 自定义指示器类名     | 0.1.22   |
| customImageClass     | 自定义图片类名       | 0.1.22   |
| customPrevImageClass | 自定义上一个图片类名 | 0.1.22   |
| customNextImageClass | 自定义下一个图片类名 | 0.1.22   |
