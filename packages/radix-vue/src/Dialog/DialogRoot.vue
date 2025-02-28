<script lang="ts">
import type { InjectionKey, Ref } from 'vue'

export interface DialogRootProps {
  open?: boolean
  defaultOpen?: boolean
  modal?: boolean
}

export type DialogRootEmits = {
  'update:open': [value: boolean]
}

export const DIALOG_INJECTION_KEY
  = Symbol() as InjectionKey<DialogProvideValue>

export interface DialogProvideValue {
  open: Readonly<Ref<boolean>>
  modal: Ref<boolean>
  openModal(): void
  onOpenChange(value: boolean): void
  onOpenToggle(): void
  triggerElement: Ref<HTMLElement | undefined>
  contentElement: Ref<HTMLElement | undefined>
  contentId: string
  titleId: string
  descriptionId: string
}
</script>

<script setup lang="ts">
import { provide, ref, toRefs } from 'vue'
import { useVModel } from '@vueuse/core'
import { useId } from '@/shared'

const props = withDefaults(defineProps<DialogRootProps>(), {
  open: undefined,
  defaultOpen: false,
  modal: true,
})

const emit = defineEmits<DialogRootEmits>()

const open = useVModel(props, 'open', emit, {
  defaultValue: props.defaultOpen,
  passive: true,
})

const triggerElement = ref<HTMLElement>()
const contentElement = ref<HTMLElement>()
const { modal } = toRefs(props)
provide<DialogProvideValue>(DIALOG_INJECTION_KEY, {
  open,
  modal,
  openModal: () => {
    open.value = true
  },
  onOpenChange: (value) => {
    open.value = value
  },
  onOpenToggle: () => {
    open.value = !open.value
  },
  contentId: useId(),
  titleId: useId(),
  descriptionId: useId(),
  triggerElement,
  contentElement,
})
</script>

<template>
  <slot />
</template>
