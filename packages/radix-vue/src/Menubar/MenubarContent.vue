<script setup lang="ts">
import { inject, ref } from 'vue'
import { MENUBAR_INJECTION_KEY } from './MenubarRoot.vue'
import { MENUBAR_MENU_INJECTION_KEY } from './MenubarMenu.vue'
import { MenuContent, type MenuContentEmits, type MenuContentProps } from '@/Menu'
import { useCollection, useForwardPropsEmits } from '@/shared'
import { wrapArray } from '@/shared/useTypeahead'

export interface MenubarContentProps extends MenuContentProps {}
export type MenubarContentEmits = MenuContentEmits

const props = withDefaults(defineProps<MenubarContentProps>(), {
  align: 'start',
})
const emits = defineEmits<MenubarContentEmits>()
const forwarded = useForwardPropsEmits(props, emits)

const context = inject(MENUBAR_INJECTION_KEY)
const menuContext = inject(MENUBAR_MENU_INJECTION_KEY)

const { injectCollection } = useCollection('menubar')
const collections = injectCollection()

const hasInteractedOutsideRef = ref(false)

function handleArrowNavigation(event: KeyboardEvent) {
  const target = event.target as HTMLElement
  const targetIsSubTrigger = target.hasAttribute(
    'data-radix-menubar-subtrigger',
  )

  const prevMenuKey = context?.dir.value === 'rtl' ? 'ArrowRight' : 'ArrowLeft'
  const isPrevKey = prevMenuKey === event.key
  const isNextKey = !isPrevKey

  // Prevent navigation when we're opening a submenu
  if (isNextKey && targetIsSubTrigger)
    return

  let candidateValues = collections.value.map(i => i.dataset.value)
  if (isPrevKey)
    candidateValues.reverse()

  const currentIndex = candidateValues.indexOf(menuContext?.value)

  candidateValues = context?.loop.value
    ? wrapArray(candidateValues, currentIndex + 1)
    : candidateValues.slice(currentIndex + 1)

  const [nextValue] = candidateValues
  if (nextValue)
    context?.onMenuOpen(nextValue)
}
</script>

<template>
  <MenuContent
    :id="menuContext?.contentId"
    :aria-labelledby="menuContext?.triggerId"
    data-radix-menubar-content=""
    v-bind="forwarded"
    :style="{
      '--radix-menubar-content-transform-origin':
        'var(--radix-popper-transform-origin)',
      '--radix-menubar-content-available-width':
        'var(--radix-popper-available-width)',
      '--radix-menubar-content-available-height':
        'var(--radix-popper-available-height)',
      '--radix-menubar-trigger-width': 'var(--radix-popper-anchor-width)',
      '--radix-menubar-trigger-height': 'var(--radix-popper-anchor-height)',
    }"
    @close-auto-focus="(event) => {
      const menubarOpen = Boolean(context?.modelValue.value);
      if (!menubarOpen && !hasInteractedOutsideRef) {
        menuContext!.triggerElement.value?.focus();
      }

      hasInteractedOutsideRef = false;
      // Always prevent auto focus because we either focus manually or want user agent focus
      event.preventDefault();
    }"
    @focus-outside="(event) => {
      const target = event.target as HTMLElement;
      const isMenubarTrigger = collections.some((item) => item.contains(target));
      if (isMenubarTrigger) event.preventDefault();
    }"
    @interact-outside="
      (event) => {
        hasInteractedOutsideRef = true;
      }
    "
    @open-auto-focus="
      (event) => {
        if (!menuContext?.wasKeyboardTriggerOpenRef.value)
          event.preventDefault();
      }
    "
    @entry-focus="(event) => {
      if (!menuContext?.wasKeyboardTriggerOpenRef.value) event.preventDefault()
    }"
    @keydown.arrow-right.arrow-left="handleArrowNavigation"
  >
    <slot />
  </MenuContent>
</template>
