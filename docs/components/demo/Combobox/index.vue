<script setup lang="ts">
import { ref } from 'vue'
import { ComboboxContent, ComboboxEmpty, ComboboxGroup, ComboboxHeader, ComboboxInput, ComboboxItem, ComboboxItemIndicator, ComboboxLabel, ComboboxRoot, ComboboxSeparator, ComboboxTrigger, ComboboxViewport } from 'radix-vue'
import { Icon } from '@iconify/vue'

const v = ref('')
const options = ['Apple', 'Banana', 'Blueberry', 'Grapes', 'Pineapple']
const vegetables = ['Aubergine', 'Broccoli', 'Carrot', 'Courgette', 'Leek']
</script>

<template>
  <ComboboxRoot v-model="v" class="relative">
    <ComboboxHeader class="min-w-[160px] inline-flex items-center justify-between rounded px-[15px] text-[13px] leading-none h-[35px] gap-[5px] bg-white text-grass11 shadow-[0_2px_10px] shadow-black/10 hover:bg-mauve3 focus:shadow-[0_0_0_2px] focus:shadow-black data-[placeholder]:text-grass9 outline-none">
      <ComboboxInput class="bg-transparent outline-none text-grass11 h-full selection:bg-grass5 placeholder-mauve8" placeholder="Placeholder..." />
      <ComboboxTrigger>
        <Icon icon="radix-icons:chevron-down" class="h-4 w-4 text-grass11" />
      </ComboboxTrigger>
    </ComboboxHeader>

    <ComboboxContent class="absolute z-10 w-full mt-2 min-w-[160px] bg-white overflow-hidden rounded shadow-[0px_10px_38px_-10px_rgba(22,_23,_24,_0.35),_0px_10px_20px_-15px_rgba(22,_23,_24,_0.2)] will-change-[opacity,transform] data-[side=top]:animate-slideDownAndFade data-[side=right]:animate-slideLeftAndFade data-[side=bottom]:animate-slideUpAndFade data-[side=left]:animate-slideRightAndFade">
      <ComboboxViewport class="p-[5px]">
        <ComboboxEmpty class="text-mauve8 text-xs font-medium text-center py-2" />

        <ComboboxGroup>
          <ComboboxLabel class="px-[25px] text-xs leading-[25px] text-mauve11">
            Fruits
          </ComboboxLabel>

          <ComboboxItem
            v-for="(option, index) in options" :key="index"
            class="text-[13px] leading-none text-grass11 rounded-[3px] flex items-center h-[25px] pr-[35px] pl-[25px] relative select-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:outline-none data-[highlighted]:bg-grass9 data-[highlighted]:text-grass1"
            :value="option"
          >
            <ComboboxItemIndicator
              class="absolute left-0 w-[25px] inline-flex items-center justify-center"
            >
              <Icon icon="radix-icons:check" />
            </ComboboxItemIndicator>
            <span>
              {{ option }}
            </span>
          </ComboboxItem>
          <ComboboxSeparator class="h-[1px] bg-grass6 m-[5px]" />
        </ComboboxGroup>

        <ComboboxGroup>
          <ComboboxLabel
            class="px-[25px] text-xs leading-[25px] text-mauve11"
          >
            Vegetables
          </ComboboxLabel>
          <ComboboxItem
            v-for="(option, index) in vegetables" :key="index"
            class="text-[13px] leading-none text-grass11 rounded-[3px] flex items-center h-[25px] pr-[35px] pl-[25px] relative select-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:outline-none data-[highlighted]:bg-grass9 data-[highlighted]:text-grass1"
            :value="option"
          >
            <ComboboxItemIndicator
              class="absolute left-0 w-[25px] inline-flex items-center justify-center"
            >
              <Icon icon="radix-icons:check" />
            </ComboboxItemIndicator>
            <span>
              {{ option }}
            </span>
          </ComboboxItem>
        </ComboboxGroup>
      </ComboboxViewport>
    </ComboboxContent>
  </ComboboxRoot>
</template>
