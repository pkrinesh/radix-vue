<script lang="ts">
import type { ComputedRef, InjectionKey, Ref } from 'vue'

export interface ProgressRootProps extends PrimitiveProps {
  modelValue?: number | null
  max?: number
  getValueLabel?: (value: number, max: number) => string
}
export type ProgressRootEmits = {
  'update:modelValue': [value: string[] | undefined]
  'update:max': [value: number]
}

const DEFAULT_MAX = 100

export const PROGRESS_INJECTION_KEY
  = Symbol() as InjectionKey<ProgressProvideValue>

interface ProgressProvideValue {
  modelValue?: Readonly<Ref<ProgressRootProps['modelValue']>>
  max: Readonly<Ref<number>>
  progressState: ComputedRef<ProgressState>
}

export type ProgressState = 'indeterminate' | 'loading' | 'complete'

const isNumber = (v: any): v is number => typeof v === 'number'

function validateValue(value: any, max: number): number | null {
  const isValidValueError
    = value === null
    || (isNumber(value) && !Number.isNaN(value) && value <= max && value >= 0)

  if (isValidValueError)
    return value

  console.error(`Invalid prop \`value\` of value \`${value}\` supplied to \`ProgressRoot\`. The \`value\` prop must be:
  - a positive number
  - less than the value passed to \`max\` (or ${DEFAULT_MAX} if no \`max\` prop is set)
  - \`null\` if the progress is indeterminate.

Defaulting to \`null\`.`)
  return null
}

function validateMax(max: number): number {
  const isValidMaxError = isNumber(max) && !Number.isNaN(max) && max > 0

  if (isValidMaxError)
    return max

  console.error(
    `Invalid prop \`max\` of value \`${max}\` supplied to \`ProgressRoot\`. Only numbers greater than 0 are valid max values. Defaulting to \`${DEFAULT_MAX}\`.`,
  )
  return DEFAULT_MAX
}
</script>

<script setup lang="ts">
import { useVModel } from '@vueuse/core'
import { computed, nextTick, provide, watch } from 'vue'
import { Primitive, type PrimitiveProps } from '@/Primitive'

const props = withDefaults(defineProps<ProgressRootProps>(), {
  max: DEFAULT_MAX,
  getValueLabel: (value: number, max: number) =>
    `${Math.round((value / max) * DEFAULT_MAX)}%`,
})

const emit = defineEmits<ProgressRootEmits>()

const modelValue = useVModel(props, 'modelValue', emit, {
  passive: true,
})

const max = useVModel(props, 'max', emit, {
  passive: true,
})

// ------- Watch for correct values -------
watch(
  () => modelValue.value,
  async (value) => {
    const correctedValue = validateValue(value, props.max)
    if (correctedValue !== value) {
      await nextTick()
      modelValue.value = correctedValue
    }
  },
  { immediate: true },
)

watch(
  () => props.max,
  (newMax) => {
    const correctedMax = validateMax(props.max)
    if (correctedMax !== newMax)
      max.value = correctedMax
  },
  { immediate: true },
)
// ------- End of watch for correct values -------

const progressState = computed<ProgressState>(() => {
  if (!modelValue.value)
    return 'indeterminate'
  if (modelValue.value === max.value)
    return 'complete'
  return 'loading'
})

provide(PROGRESS_INJECTION_KEY, {
  modelValue,
  max,
  progressState,
})
</script>

<template>
  <Primitive
    :as-child="props.asChild"
    :as="as"
    :aria-valuemax="max"
    :aria-valuemin="0"
    :aria-valuenow="isNumber(modelValue) ? modelValue : undefined"
    :aria-valuetext="getValueLabel(modelValue!, max)"
    :aria-label="getValueLabel(modelValue!, max)"
    role="progressbar"
    :data-state="progressState"
    :data-value="modelValue ?? undefined"
    :data-max="max"
  >
    <slot />
  </Primitive>
</template>
