<script lang="ts">
export interface DropdownMenuSubProps {
  open?: boolean
  defaultOpen?: boolean
}

export type DropdownMenuSubEmits = {
  'update:open': [value: boolean]
}
</script>

<script setup lang="ts">
import { useVModel } from '@vueuse/core'
import { MenuSub } from '@/Menu'

const props = defineProps<DropdownMenuSubProps>()
const emit = defineEmits<DropdownMenuSubEmits>()

const open = useVModel(props, 'open', emit, {
  passive: true,
  defaultValue: props.defaultOpen ?? false,
})
</script>

<template>
  <MenuSub v-model:open="open">
    <slot />
  </MenuSub>
</template>
