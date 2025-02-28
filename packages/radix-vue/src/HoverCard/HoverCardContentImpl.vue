<script setup lang="ts">
import { inject, nextTick, onMounted, onUnmounted, ref, watchEffect } from 'vue'
import {
  HOVER_CARD_INJECTION_KEY,
} from './HoverCardRoot.vue'
import { PopperContent, type PopperContentProps } from '@/Popper'
import { DismissableLayer, type DismissableLayerEmits } from '@/DismissableLayer'
import { usePrimitiveElement } from '@/Primitive'
import { getTabbableNodes } from './utils'
import { useForwardProps } from '..'

export interface HoverCardContentImplProps extends PopperContentProps {}
export type HoverCardContentImplEmits = DismissableLayerEmits

const props = defineProps<HoverCardContentImplProps>()
const emits = defineEmits<HoverCardContentImplEmits>()
const forwarded = useForwardProps(props)

const { primitiveElement, currentElement: contentElement } = usePrimitiveElement()
const context = inject(HOVER_CARD_INJECTION_KEY)
const containSelection = ref(false)

let originalBodyUserSelect: string
watchEffect((cleanupFn) => {
  if (containSelection.value) {
    const body = document.body

    // Safari requires prefix
    originalBodyUserSelect = body.style.userSelect || body.style.webkitUserSelect

    body.style.userSelect = 'none'
    body.style.webkitUserSelect = 'none'

    cleanupFn(() => {
      body.style.userSelect = originalBodyUserSelect
      body.style.webkitUserSelect = originalBodyUserSelect
    })
  }
})

function handlePointerUp() {
  containSelection.value = false
  context!.isPointerDownOnContentRef.value = false

  // Delay a frame to ensure we always access the latest selection
  nextTick(() => {
    const hasSelection = document.getSelection()?.toString() !== ''
    if (hasSelection)
      context!.hasSelectionRef.value = true
  })
}
onMounted(() => {
  if (contentElement.value) {
    document.addEventListener('pointerup', handlePointerUp)

    const tabbables = getTabbableNodes(contentElement.value)
    tabbables.forEach(tabbable => tabbable.setAttribute('tabindex', '-1'))
  }
})

onUnmounted(() => {
  document.removeEventListener('pointerup', handlePointerUp)
  context!.hasSelectionRef.value = false
  context!.isPointerDownOnContentRef.value = false
})
</script>

<template>
  <DismissableLayer
    as-child
    :disable-outside-pointer-events="false"
    @escape-key-down="emits('escapeKeyDown', $event)"
    @pointer-down-outside="emits('pointerDownOutside', $event)"
    @focus-outside.prevent="emits('focusOutside', $event)"
    @dismiss="context?.onDismiss"
  >
    <PopperContent
      ref="primitiveElement"
      v-bind="{ ...forwarded, ...$attrs }"
      :data-state="context?.open.value ? 'open' : 'closed'"
      :style="{
        'userSelect': containSelection ? 'text' : undefined,
        // Safari requires prefix
        'WebkitUserSelect': containSelection ? 'text' : undefined,
        // re-namespace exposed content custom properties
        '--radix-hover-card-content-transform-origin': 'var(--radix-popper-transform-origin)',
        '--radix-hover-card-content-available-width': 'var(--radix-popper-available-width)',
        '--radix-hover-card-content-available-height': 'var(--radix-popper-available-height)',
        '--radix-hover-card-trigger-width': 'var(--radix-popper-anchor-width)',
        '--radix-hover-card-trigger-height': 'var(--radix-popper-anchor-height)',
      }"
      @pointerdown="(event) => {
        // Contain selection to current layer
        if (event.currentTarget.contains(event.target as HTMLElement)) {
          containSelection = true
        }
        context!.hasSelectionRef.value = false;
        context!.isPointerDownOnContentRef.value = true;
      }"
    >
      <slot />
    </PopperContent>
  </DismissableLayer>
</template>
