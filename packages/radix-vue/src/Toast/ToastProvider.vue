<script lang="ts">
import type { SwipeDirection } from './utils'

type ToastProviderContextValue = {
  label: Ref<string>
  duration: Ref<number>
  swipeDirection: Ref< SwipeDirection>
  swipeThreshold: Ref<number>
  toastCount: Ref<number>
  viewport: Ref<HTMLElement | undefined>
  onViewportChange(viewport: HTMLElement): void
  onToastAdd(): void
  onToastRemove(): void
  isFocusedToastEscapeKeyDownRef: Ref<boolean>
  isClosePausedRef: Ref<boolean>
}

export const TOAST_PROVIDER_INJECTION_KEY = Symbol() as InjectionKey<ToastProviderContextValue>

export interface ToastProviderProps {
  /**
   * An author-localized label for each toast. Used to help screen reader users
   * associate the interruption with a toast.
   * @defaultValue 'Notification'
   */
  label?: string
  /**
   * Time in milliseconds that each toast should remain visible for.
   * @defaultValue 5000
   */
  duration?: number
  /**
   * Direction of pointer swipe that should close the toast.
   * @defaultValue 'right'
   */
  swipeDirection?: SwipeDirection
  /**
   * Distance in pixels that the swipe must pass before a close is triggered.
   * @defaultValue 50
   */
  swipeThreshold?: number
}
</script>

<script setup lang="ts">
import { type InjectionKey, type Ref, provide, ref, toRefs } from 'vue'

const props = withDefaults(defineProps<ToastProviderProps>(), {
  label: 'Notification',
  duration: 5000,
  swipeDirection: 'right',
  swipeThreshold: 50,
})
const { label, duration, swipeDirection, swipeThreshold } = toRefs(props)

const viewport = ref<HTMLElement>()
const toastCount = ref(0)
const isFocusedToastEscapeKeyDownRef = ref(false)
const isClosePausedRef = ref(false)

if (props.label && typeof props.label === 'string' && !props.label.trim()) {
  const error = 'Invalid prop `label` supplied to `ToastProvider`. Expected non-empty `string`.'
  throw new Error(error)
}

provide(TOAST_PROVIDER_INJECTION_KEY, {
  label,
  duration,
  swipeDirection,
  swipeThreshold,
  toastCount,
  viewport,
  onViewportChange(el) {
    viewport.value = el
  },
  onToastAdd() {
    toastCount.value++
  },
  onToastRemove() {
    toastCount.value--
  },
  isFocusedToastEscapeKeyDownRef,
  isClosePausedRef,

})
</script>

<template>
  <slot />
</template>
