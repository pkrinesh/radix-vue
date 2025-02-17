<script setup lang="ts">
import { computed, h, inject, onMounted } from 'vue'
import { SELECT_INJECTION_KEY, SELECT_NATIVE_OPTIONS_INJECTION_KEY } from './SelectRoot.vue'
import { SELECT_CONTENT_INJECTION_KEY, SelectContentDefaultContextValue } from './SelectContentImpl.vue'
import { SELECT_ITEM_INJECTION_KEY } from './SelectItem.vue'
import {
  Primitive,
  type PrimitiveProps,
  usePrimitiveElement,
} from '@/Primitive'

export interface SelectItemTextProps extends PrimitiveProps {}

const props = withDefaults(defineProps<SelectItemTextProps>(), {
  as: 'span',
})

const context = inject(SELECT_INJECTION_KEY)
const contentContext = inject(SELECT_CONTENT_INJECTION_KEY, SelectContentDefaultContextValue)
const nativeOptionContext = inject(SELECT_NATIVE_OPTIONS_INJECTION_KEY)
const itemContext = inject(SELECT_ITEM_INJECTION_KEY)

const { primitiveElement, currentElement: itemTextElement }
  = usePrimitiveElement()

const nativeOption = computed(() => {
  return h('option', {
    key: itemContext?.value,
    value: itemContext?.value,
    disabled: itemContext?.disabled.value,
    innerHTML: itemTextElement.value?.textContent,
  })
})

const { onNativeOptionAdd, onNativeOptionRemove } = nativeOptionContext!
onMounted(() => {
  if (!itemTextElement.value)
    return
  itemContext?.onItemTextChange(itemTextElement.value)
  contentContext?.itemTextRefCallback(
    itemTextElement.value,
    itemContext!.value,
    itemContext!.disabled.value,
  )
  onNativeOptionAdd(nativeOption.value)
})

// onBeforeUnmount(() => {
//   onNativeOptionRemove(nativeOption.value);
// });
</script>

<script lang="ts">
export default {
  inheritAttrs: false,
}
</script>

<template>
  <Primitive :id="itemContext?.textId" ref="primitiveElement" v-bind="{ ...props, ...$attrs }">
    <slot />
  </Primitive>

  <!-- Portal the select item text into the trigger value node -->
  <Teleport v-if="itemContext?.isSelected.value && context?.valueElement.value && !context.valueElementHasChildren.value" :to="context?.valueElement.value">
    <slot />
  </Teleport>
</template>
