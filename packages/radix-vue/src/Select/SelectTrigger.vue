<script lang="ts">
export interface SelectTriggerProps extends PrimitiveProps {
  disabled?: boolean
}
</script>

<script setup lang="ts">
import { computed, inject, onMounted } from 'vue'
import {
  SELECT_INJECTION_KEY,
  type SelectProvideValue,
} from './SelectRoot.vue'
import { OPEN_KEYS, shouldShowPlaceholder } from './utils'
import {
  Primitive,
  type PrimitiveProps,
  usePrimitiveElement,
} from '@/Primitive'
import { PopperAnchor } from '@/Popper'
import { useCollection, useTypeahead } from '@/shared'

const props = withDefaults(defineProps<SelectTriggerProps>(), {
  as: 'button',
})
const context = inject<SelectProvideValue>(SELECT_INJECTION_KEY)

const isDisabled = computed(() => context?.disabled?.value || props.disabled)

const { primitiveElement, currentElement: triggerElement }
  = usePrimitiveElement()

onMounted(() => {
  context!.triggerElement = triggerElement
})

const { injectCollection } = useCollection()
const collectionItems = injectCollection()

const { search, handleTypeaheadSearch, resetTypeahead }
  = useTypeahead(collectionItems)
function handleOpen() {
  if (!isDisabled.value) {
    context!.onOpenChange(true)
    // reset typeahead when we open
    resetTypeahead()
  }
}
</script>

<template>
  <PopperAnchor as-child>
    <Primitive
      ref="primitiveElement"
      role="combobox"
      :type="as === 'button' ? 'button' : undefined"
      :aria-controls="context?.contentId"
      :aria-expanded="context?.open.value || false"
      :aria-required="context?.required?.value"
      aria-autocomplete="none"
      :dir="context?.dir.value"
      :data-state="context?.open.value ? 'open' : 'closed'"
      :data-disabled="isDisabled ? '' : undefined"
      :data-placeholder="
        shouldShowPlaceholder(context?.modelValue?.value) ? '' : undefined
      "
      :as-child="asChild"
      :as="as"
      @click="
        (event: MouseEvent) => {
          // Whilst browsers generally have no issue focusing the trigger when clicking
          // on a label, Safari seems to struggle with the fact that there's no `onClick`.
          // We force `focus` in this case. Note: this doesn't create any other side-effect
          // because we are preventing default in `onPointerDown` so effectively
          // this only runs for a label 'click'
          (event?.currentTarget as HTMLElement)?.focus();
        }
      "
      @pointerdown="
        (event: PointerEvent) => {
          // prevent implicit pointer capture
          // https://www.w3.org/TR/pointerevents3/#implicit-pointer-capture
          const target = event.target as HTMLElement;
          if (target.hasPointerCapture(event.pointerId)) {
            target.releasePointerCapture(event.pointerId);
          }

          // only call handler if it's the left button (mousedown gets triggered by all mouse buttons)
          // but not when the control key is pressed (avoiding MacOS right click)
          if (event.button === 0 && event.ctrlKey === false) {
            handleOpen();
            context!.triggerPointerDownPosRef.value = {
              x: Math.round(event.pageX),
              y: Math.round(event.pageY),
            };
            // prevent trigger from stealing focus from the active item after opening.
            event.preventDefault();
          }
        }
      "
      @pointerup.prevent
      @keydown="
        (event) => {
          const isTypingAhead = search !== '';
          const isModifierKey = event.ctrlKey || event.altKey || event.metaKey;
          if (!isModifierKey && event.key.length === 1)
            if (isTypingAhead && event.key === ' ') return;
          handleTypeaheadSearch(event.key);
          if (OPEN_KEYS.includes(event.key)) {
            handleOpen();
            event.preventDefault();
          }
        }
      "
    >
      <slot />
    </Primitive>
  </PopperAnchor>
</template>
