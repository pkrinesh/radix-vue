<script lang="ts">
export type TooltipTriggerDataState =
  | 'closed'
  | 'delayed-open'
  | 'instant-open'

export interface TooltipTriggerProps extends PrimitiveProps {}
</script>

<script setup lang="ts">
import { onMounted, ref } from 'vue'
import { injectTooltipRootContent } from './TooltipRoot.vue'
import { PopperAnchor } from '@/Popper'
import {
  Primitive,
  type PrimitiveProps,
  usePrimitiveElement,
} from '@/Primitive'
import { injectTooltipProviderContext } from './TooltipProvider.vue'

const props = withDefaults(defineProps<TooltipTriggerProps>(), {
  as: 'button',
})
const context = injectTooltipRootContent()
const providerContext = injectTooltipProviderContext()

const { primitiveElement, currentElement: triggerElement }
  = usePrimitiveElement()

const isPointerDown = ref(false)
const hasPointerMoveOpened = ref(false)

function handlePointerUp() {
  isPointerDown.value = false
}

function handlePointerDown() {
  isPointerDown.value = true
  document.addEventListener('pointerup', handlePointerUp, { once: true })
}

onMounted(() => {
  context.onTriggerChange(triggerElement.value)
})
</script>

<template>
  <PopperAnchor as-child>
    <Primitive
      ref="primitiveElement"
      :aria-describedby="
        context.open.value ? context.contentId : undefined
      "
      :data-state="context.stateAttribute.value"
      :as="as"
      :as-child="props.asChild"
      @pointermove="(event) => {
        if (event.pointerType === 'touch') return;
        if (
          !hasPointerMoveOpened && !providerContext.isPointerInTransitRef.value
        ) {
          context.onTriggerEnter();
          hasPointerMoveOpened = true;
        }
      }"
      @pointerleave="(event) => {
        context.onTriggerLeave();
        hasPointerMoveOpened = false;
      }"
      @pointerdown="handlePointerDown"
      @focus="() => {
        if (!isPointerDown) context.onOpen()
      }"
      @blur="context.onClose()"
      @click="context.onClose()"
    >
      <slot />
    </Primitive>
  </PopperAnchor>
</template>
