<script lang="ts" setup>
import { useVueFlow } from '@vue-flow/core'
import { computed, toRef } from 'vue'
import { BackgroundVariant } from './types'
import type { BackgroundProps } from './types'
import { DefaultBgColors, DotPattern, LinePattern } from './patterns'

const {
  id,
  variant = BackgroundVariant.Dots,
  gap = 20,
  size = 1,
  lineWidth = 1,
  height = 100,
  width = 100,
  x = 0,
  y = 0,
  bgColor,
  patternColor: initialPatternColor,
  color: _patternColor,
  offset = 0,
} = defineProps<BackgroundProps>()

const { id: vueFlowId, viewport } = useVueFlow()

const background = computed(() => {
  const zoom = viewport.value.zoom
  const [gapX, gapY] = Array.isArray(gap) ? gap : [gap, gap]
  const scaledGap: [number, number] = [gapX * zoom || 1, gapY * zoom || 1]
  const scaledSize = size * zoom
  const [offsetX, offsetY]: [number, number] = Array.isArray(offset) ? offset : [offset, offset]

  const scaledOffset: [number, number] = [offsetX * zoom || 1 + scaledGap[0] / 2, offsetY * zoom || 1 + scaledGap[1] / 2]

  return {
    scaledGap,
    offset: scaledOffset,
    size: scaledSize,
  }
})

// when there are multiple flows on a page we need to make sure that every background gets its own pattern.
const patternId = toRef(() => `pattern-${vueFlowId}${id ? `-${id}` : ''}`)

const patternColor = toRef(() => _patternColor || initialPatternColor || DefaultBgColors[variant || BackgroundVariant.Dots])
</script>

<script lang="ts">
export default {
  name: 'Background',
  compatConfig: { MODE: 3 },
}
</script>

<template>
  <svg
    class="vue-flow__background vue-flow__container"
    :style="{
      height: `${height > 100 ? 100 : height}%`,
      width: `${width > 100 ? 100 : width}%`,
    }"
  >
    <slot :id="patternId" name="pattern-container">
      <pattern
        :id="patternId"
        :x="viewport.x % background.scaledGap[0]"
        :y="viewport.y % background.scaledGap[1]"
        :width="background.scaledGap[0]"
        :height="background.scaledGap[1]"
        :patternTransform="`translate(-${background.offset[0]},-${background.offset[1]})`"
        patternUnits="userSpaceOnUse"
      >
        <slot name="pattern">
          <template v-if="variant === BackgroundVariant.Lines">
            <LinePattern :size="lineWidth" :color="patternColor" :dimensions="background.scaledGap" />
          </template>

          <template v-else-if="variant === BackgroundVariant.Dots">
            <DotPattern :color="patternColor" :radius="background.size / 2" />
          </template>

          <svg v-if="bgColor" height="100" width="100">
            <rect width="100%" height="100%" :fill="bgColor" />
          </svg>
        </slot>
      </pattern>
    </slot>

    <rect :x="x" :y="y" width="100%" height="100%" :fill="`url(#${patternId})`" />

    <slot :id="patternId" />
  </svg>
</template>
