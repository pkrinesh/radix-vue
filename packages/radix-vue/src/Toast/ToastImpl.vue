<script lang="ts">
export const TOAST_INTERACTIVE_CONTEXT = Symbol() as InjectionKey<{
  onClose(): void
}>

export default {
  inheritAttrs: false,
}
</script>

<script setup lang="ts">
import { Primitive, type PrimitiveProps, usePrimitiveElement } from '@/Primitive'
import { computed, inject, onMounted, onUnmounted, provide, ref, watchEffect } from 'vue'
import type { ComponentPublicInstance, InjectionKey } from 'vue'
import { TOAST_PROVIDER_INJECTION_KEY } from './ToastProvider.vue'
import { getAnnounceTextContent, handleAndDispatchCustomEvent, isDeltaInDirection } from './utils'
import { type SwipeEvent, TOAST_SWIPE_CANCEL, TOAST_SWIPE_END, TOAST_SWIPE_MOVE, TOAST_SWIPE_START, VIEWPORT_PAUSE, VIEWPORT_RESUME } from './utils'
import ToastAnnounce from './ToastAnnounce.vue'
import { useForwardRef } from '@/shared'
import { onKeyStroke } from '@vueuse/core'

export interface ToastImplProps extends PrimitiveProps {
  type?: 'foreground' | 'background'
  open?: boolean
  /**
   * Time in milliseconds that toast should remain visible for. Overrides value
   * given to `ToastProvider`.
   */
  duration?: number
}

export type ToastImplEmits = {
  'close': []
  'escapeKeyDown': [event: KeyboardEvent]
  'pause': []
  'resume': []
  'swipeStart': [event: SwipeEvent]
  'swipeMove': [event: SwipeEvent]
  'swipeCancel': [event: SwipeEvent]
  'swipeEnd': [event: SwipeEvent]
}

const props = withDefaults(defineProps<ToastImplProps>(), {
  open: false,
  as: 'li',
})

const emits = defineEmits<ToastImplEmits>()

const forwardRef = useForwardRef()
const { primitiveElement, currentElement } = usePrimitiveElement()
const context = inject(TOAST_PROVIDER_INJECTION_KEY)
const pointerStartRef = ref<{ x: number; y: number } | null>(null)
const swipeDeltaRef = ref<{ x: number; y: number } | null>(null)
const duration = computed(() => props.duration || context!.duration.value)

const closeTimerStartTimeRef = ref(0)
const closeTimerRemainingTimeRef = ref(duration.value)
const closeTimerRef = ref(0)

function startTimer(duration: number) {
  if (!duration || duration === Number.POSITIVE_INFINITY)
    return
  window.clearTimeout(closeTimerRef.value)
  closeTimerStartTimeRef.value = new Date().getTime()
  closeTimerRef.value = window.setTimeout(handleClose, duration)
}

function handleClose() {
  // focus viewport if focus is within toast to read the remaining toast
  // count to SR users and ensure focus isn't lost
  const isFocusInToast = currentElement.value?.contains(document.activeElement)
  if (isFocusInToast)
    context!.viewport.value?.focus()
  emits('close')
}

const announceTextContent = computed(() => currentElement.value ? getAnnounceTextContent(currentElement.value) : null)

if (props.type && !['foreground', 'background'].includes(props.type)) {
  const error = 'Invalid prop `type` supplied to `Toast`. Expected `foreground | background`.'
  throw new Error(error)
}

watchEffect((cleanupFn) => {
  const viewport = context?.viewport.value
  if (viewport) {
    const handleResume = () => {
      startTimer(closeTimerRemainingTimeRef.value)
      emits('resume')
    }
    const handlePause = () => {
      const elapsedTime = new Date().getTime() - closeTimerStartTimeRef.value
      closeTimerRemainingTimeRef.value = closeTimerRemainingTimeRef.value - elapsedTime
      window.clearTimeout(closeTimerRef.value)
      emits('pause')
    }
    viewport.addEventListener(VIEWPORT_PAUSE, handlePause)
    viewport.addEventListener(VIEWPORT_RESUME, handleResume)
    return () => {
      viewport.removeEventListener(VIEWPORT_PAUSE, handlePause)
      viewport.removeEventListener(VIEWPORT_RESUME, handleResume)
    }
  }
})

// start timer when toast opens or duration changes.
// we include `open` in deps because closed !== unmounted when animating
// so it could reopen before being completely unmounted
watchEffect(() => {
  if (props.open && !context?.isClosePausedRef.value)
    startTimer(duration.value)
})

onKeyStroke('Escape', (event) => {
  emits('escapeKeyDown', event)
  if (!event.defaultPrevented) {
    context!.isFocusedToastEscapeKeyDownRef.value = true
    handleClose()
  }
})

onMounted(() => {
  context?.onToastAdd()
})
onUnmounted(() => {
  context?.onToastRemove()
})

provide(TOAST_INTERACTIVE_CONTEXT, {
  onClose: handleClose,
})
</script>

<template>
  <ToastAnnounce
    v-if="announceTextContent"
    role="status"
    :aria-live="type === 'foreground' ? 'assertive' : 'polite'"
    aria-atomic
  >
    {{ announceTextContent }}
  </ToastAnnounce>

  <Teleport :to="context?.viewport.value">
    <Primitive
      :ref="v => {
        forwardRef(v)
        primitiveElement = v as ComponentPublicInstance
      }"
      role="status"
      aria-live="off"
      aria-atomic
      tabindex="0"
      data-radix-vue-collection-item
      v-bind="$attrs"
      :as="as"
      :as-child="asChild"
      :data-state="open ? 'open' : 'closed'"
      :data-swipe-direction="context!.swipeDirection.value"
      :style="{ userSelect: 'none', touchAction: 'none' }"
      @pointerdown.left="(event: PointerEvent) => {
        pointerStartRef = { x: event.clientX, y: event.clientY };
      }"
      @pointermove="(event: PointerEvent) => {
        if (!pointerStartRef) return;
        const x = event.clientX - pointerStartRef.x;
        const y = event.clientY - pointerStartRef.y;
        const hasSwipeMoveStarted = Boolean(swipeDeltaRef);
        const isHorizontalSwipe = ['left', 'right'].includes(context!.swipeDirection.value);
        const clamp = ['left', 'up'].includes(context!.swipeDirection.value)
          ? Math.min
          : Math.max;
        const clampedX = isHorizontalSwipe ? clamp(0, x) : 0;
        const clampedY = !isHorizontalSwipe ? clamp(0, y) : 0;
        const moveStartBuffer = event.pointerType === 'touch' ? 10 : 2;
        const delta = { x: clampedX, y: clampedY };
        const eventDetail = { originalEvent: event, delta };
        if (hasSwipeMoveStarted) {
          swipeDeltaRef = delta;
          handleAndDispatchCustomEvent(TOAST_SWIPE_MOVE, (ev: SwipeEvent) => emits('swipeMove', ev), eventDetail);
        }
        else if (isDeltaInDirection(delta, context!.swipeDirection.value, moveStartBuffer)) {
          swipeDeltaRef = delta;
          handleAndDispatchCustomEvent(TOAST_SWIPE_START, (ev: SwipeEvent) => emits('swipeStart', ev), eventDetail);
          (event.target as HTMLElement).setPointerCapture(event.pointerId);
        }
        else if (Math.abs(x) > moveStartBuffer || Math.abs(y) > moveStartBuffer) {
          // User is swiping in wrong direction so we disable swipe gesture
          // for the current pointer down interaction
          pointerStartRef = null;
        }
      }"
      @pointerup="(event: PointerEvent) => {
        const delta = swipeDeltaRef;
        const target = event.target as HTMLElement;
        if (target.hasPointerCapture(event.pointerId)) {
          target.releasePointerCapture(event.pointerId);
        }
        swipeDeltaRef = null;
        pointerStartRef = null;
        if (delta) {
          const toast = event.currentTarget;
          const eventDetail = { originalEvent: event, delta };
          if (
            isDeltaInDirection(delta, context!.swipeDirection.value, context!.swipeThreshold.value)
          ) {
            handleAndDispatchCustomEvent(TOAST_SWIPE_END, (ev: SwipeEvent) => emits('swipeEnd', ev), eventDetail);
          }
          else {
            handleAndDispatchCustomEvent(TOAST_SWIPE_CANCEL, (ev: SwipeEvent) => emits('swipeCancel', ev), eventDetail);
          }
          // Prevent click event from triggering on items within the toast when
          // pointer up is part of a swipe gesture
          toast?.addEventListener('click', (event) => event.preventDefault(), {
            once: true,
          });
        }
      }"
    >
      <slot />
    </Primitive>
  </Teleport>
</template>
