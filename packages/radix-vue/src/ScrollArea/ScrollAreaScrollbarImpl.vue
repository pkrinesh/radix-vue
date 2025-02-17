<script setup lang="ts">
import { inject, onMounted, onUnmounted, ref } from 'vue'
import { useResizeObserver } from '@vueuse/core'
import { SCROLL_AREA_INJECTION_KEY } from './ScrollAreaRoot.vue'
import { SCROLL_AREA_SCROLLBAR_VISIBLE_INJECTION_KEY } from './ScrollAreaScrollbarVisible.vue'
import { SCROLL_AREA_SCROLLBAR_INJECTION_KEY } from './ScrollAreaScrollbar.vue'
import { toInt } from './utils'
import { Primitive, usePrimitiveElement } from '@/Primitive'

const props = defineProps<ScrollAreaScrollbarImplProps>()
const emit = defineEmits<{
  'onDragScroll': [payload: { x: number; y: number }]
  'onWheelScroll': [payload: { x: number; y: number }]
  'onThumbPointerDown': [payload: { x: number; y: number }]
}>()
const rootContext = inject(SCROLL_AREA_INJECTION_KEY)
const scrollbarVisibleContext = inject(
  SCROLL_AREA_SCROLLBAR_VISIBLE_INJECTION_KEY,
)

const scrollbarContext = inject(SCROLL_AREA_SCROLLBAR_INJECTION_KEY)

export interface ScrollAreaScrollbarImplProps {
  isHorizontal: boolean
}

const { primitiveElement, currentElement: scrollbar } = usePrimitiveElement()
const prevWebkitUserSelectRef = ref('')
const rectRef = ref<DOMRect>()

function handleDragScroll(event: MouseEvent) {
  if (rectRef.value) {
    const x = event.clientX - rectRef.value?.left
    const y = event.clientY - rectRef.value?.top
    emit('onDragScroll', { x, y })
  }
}

function handlePointerDown(event: PointerEvent) {
  const mainPointer = 0
  if (event.button === mainPointer) {
    const element = event.target as HTMLElement
    element.setPointerCapture(event.pointerId)
    rectRef.value = scrollbar.value!.getBoundingClientRect()

    // pointer capture doesn't prevent text selection in Safari
    // so we remove text selection manually when scrolling
    prevWebkitUserSelectRef.value = document.body.style.webkitUserSelect
    document.body.style.webkitUserSelect = 'none'
    if (rootContext?.viewport)
      rootContext.viewport.value!.style.scrollBehavior = 'auto'

    handleDragScroll(event)
  }
}

function handlePointerMove(event: PointerEvent) {
  handleDragScroll(event)
}

function handlePointerUp(event: PointerEvent) {
  const element = event.target as HTMLElement
  if (element.hasPointerCapture(event.pointerId))
    element.releasePointerCapture(event.pointerId)

  document.body.style.webkitUserSelect = prevWebkitUserSelectRef.value
  if (rootContext?.viewport)
    rootContext.viewport.value!.style.scrollBehavior = ''

  rectRef.value = undefined
}

function handleWheel(event: WheelEvent) {
  if (!scrollbarVisibleContext)
    return
  const element = event.target as HTMLElement
  const isScrollbarWheel = scrollbar.value?.contains(element)
  const maxScrollPos
    = scrollbarVisibleContext.sizes.value.content
    - scrollbarVisibleContext.sizes.value.viewport
  if (isScrollbarWheel)
    scrollbarVisibleContext.handleWheelScroll(event, maxScrollPos)
}

onMounted(() => {
  document.addEventListener('wheel', handleWheel, { passive: false })
})
onUnmounted(() => {
  document.removeEventListener('wheel', handleWheel)
})

function handleSizeChange() {
  if (!scrollbar.value)
    return
  if (props.isHorizontal) {
    scrollbarVisibleContext?.handleSizeChange({
      content: rootContext?.viewport.value?.scrollWidth ?? 0,
      viewport: rootContext?.viewport.value?.offsetWidth ?? 0,
      scrollbar: {
        size: scrollbar.value?.clientWidth ?? 0,
        paddingStart: toInt(getComputedStyle(scrollbar.value!).paddingLeft),
        paddingEnd: toInt(getComputedStyle(scrollbar.value!).paddingRight),
      },
    })
  }
  else {
    scrollbarVisibleContext?.handleSizeChange({
      content: rootContext?.viewport.value?.scrollHeight ?? 0,
      viewport: rootContext?.viewport.value?.offsetHeight ?? 0,
      scrollbar: {
        size: scrollbar.value?.clientHeight ?? 0,
        paddingStart: toInt(getComputedStyle(scrollbar.value!).paddingLeft),
        paddingEnd: toInt(getComputedStyle(scrollbar.value!).paddingRight),
      },
    })
  }
}

useResizeObserver(scrollbar, handleSizeChange)
useResizeObserver(rootContext?.content, handleSizeChange)
</script>

<template>
  <Primitive
    ref="primitiveElement"
    style="position: absolute"
    data-scrollbarimpl
    :as="scrollbarContext?.as.value"
    :as-child="scrollbarContext?.asChild.value"
    @pointerdown="handlePointerDown"
    @pointermove="handlePointerMove"
    @pointerup="handlePointerUp"
  >
    <slot />
  </Primitive>
</template>
