<script setup lang="ts">
import { Primitive, type PrimitiveProps } from '@/Primitive'

export interface LabelProps extends PrimitiveProps {
  for?: string
}

const props = withDefaults(defineProps<LabelProps>(), {
  as: 'label',
})
</script>

<template>
  <Primitive
    v-bind="props"
    @mousedown="(event) => {
      // prevent text selection when double clicking label
      if (!event.defaultPrevented && event.detail > 1) event.preventDefault();
    }"
  >
    <slot />
  </Primitive>
</template>
