<!--
 * @Author: 一尾流莺
 * @Description:
 * @Date: 2022-05-19 16:14:52
 * @LastEditTime: 2022-05-31 10:37:08
 * @FilePath: \lego-fe\src\components\LText.vue
-->
<template>
  <!-- 使用动态组件进行渲染 -->
  <component :is="tag"
             :style="styleProps"
             @click="handleClick"
             class="l-text-component">
    {{ text }}
  </component>
</template>

<script  lang="ts">

import { defineComponent } from 'vue';
import { transformToComponentProps, textDefaultProps, textStylePropNames } from '../defaultProps'

import useComponentCommon from '../hooks/useComponentCommon'
const defaultProps = transformToComponentProps(textDefaultProps)
export default defineComponent({
  // 合并 props
  props: {
    tag: {
      type: String,
      default: 'div'
    },
    ...defaultProps
  },
  setup(props) {
    // 获取到样式属性
    const { styleProps, handleClick } = useComponentCommon(props, textStylePropNames)
    return {
      styleProps,
      handleClick
    }
  }
})
</script>

<style scoped>
h2.l-text-component,
p.l-text-component {
  margin-bottom: 0;
}

button.l-text-component {
  padding: 5px 10px;
  cursor: pointer;
}

.l-text-component {
  box-sizing: border-box;
  white-space: pre-wrap;
  position: relative !important;
}
</style>