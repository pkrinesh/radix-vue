<script setup lang="ts">
import { computed, inject, onMounted, ref } from 'vue'
import { MENUBAR_INJECTION_KEY } from './MenubarRoot.vue'
import { MENUBAR_MENU_INJECTION_KEY } from './MenubarMenu.vue'
import {
  Primitive,
  type PrimitiveProps,
  usePrimitiveElement,
} from '@/Primitive'
import { MenuAnchor } from '@/Menu'
import { RovingFocusItem } from '@/RovingFocus'

export interface MenubarTriggerProps extends PrimitiveProps {
  disabled?: boolean
}

withDefaults(defineProps<MenubarTriggerProps>(), {
  as: 'button',
})
const context = inject(MENUBAR_INJECTION_KEY)
const menuContext = inject(MENUBAR_MENU_INJECTION_KEY)

const { primitiveElement, currentElement: triggerElement }
  = usePrimitiveElement()

const isFocused = ref(false)

const open = computed(() => context?.modelValue.value === menuContext?.value)

onMounted(() => {
  menuContext!.triggerElement = triggerElement
})
</script>

<template>
  <RovingFocusItem
    as-child
    :focusable="!disabled"
    :tab-stop-id="menuContext?.value"
  >
    <MenuAnchor as-child>
      <Primitive
        :id="menuContext?.triggerId"
        ref="primitiveElement"
        :as="as"
        :type="as === 'button' ? 'button' : undefined"
        role="menuitem"
        aria-haspopup="menu"
        :aria-expanded="open"
        :aria-controls="open ? menuContext?.contentId : undefined"
        :data-highlighted="isFocused ? '' : undefined"
        :data-state="open ? 'open' : 'closed'"
        :data-disabled="disabled ? '' : undefined"
        :disabled="disabled"
        :data-value="menuContext?.value"
        data-radix-vue-collection-item
        @pointerdown="(event) => {
          // only call handler if it's the left button (mousedown gets triggered by all mouse buttons)
          // but not when the control key is pressed (avoiding MacOS right click)
          if (!disabled && event.button === 0 && event.ctrlKey === false) {
            context!.onMenuOpen(menuContext!.value);
            // prevent trigger focusing when opening
            // this allows the content to be given focus without competition
            if (!open) event.preventDefault();
          }
        }"
        @pointerenter="() => {
          const menubarOpen = Boolean(context!.modelValue.value);
          if (menubarOpen && !open) {
            context!.onMenuOpen(menuContext!.value);
            triggerElement?.focus()
          }
        }"
        @keydown.enter.space.arrow-down="(event) => {
          if (disabled) return;
          if (['Enter', ' '].includes(event.key)) context?.onMenuToggle(menuContext!.value);
          if (event.key === 'ArrowDown') context?.onMenuOpen(menuContext!.value);
          // prevent keydown from scrolling window / first focused item to execute
          // that keydown (inadvertently closing the menu)
          if (['Enter', ' ', 'ArrowDown'].includes(event.key)) {
            menuContext!.wasKeyboardTriggerOpenRef.value = true;
            event.preventDefault();
          }
        }"
        @focus="isFocused = true"
        @blur="isFocused = false"
      >
        <slot />
      </Primitive>
    </MenuAnchor>
  </RovingFocusItem>
</template>
