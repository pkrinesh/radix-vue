<script setup lang="ts">
import {
  ContextMenuCheckboxItem,
  ContextMenuContent,
  ContextMenuItem,
  ContextMenuItemIndicator,
  ContextMenuLabel,
  ContextMenuPortal,
  ContextMenuRadioGroup,
  ContextMenuRadioItem,
  ContextMenuRoot,
  ContextMenuSeparator,
  ContextMenuTrigger,
} from 'radix-vue'
import { Icon } from '@iconify/vue'
import { ref } from 'vue'

const toggleState = ref(false)
const checkboxOne = ref(false)
const checkboxTwo = ref(false)
const person = ref('pedro')

function handleClick() {
  alert('hello!')
}
</script>

<template>
  <div class="absolute left-4 top-3 text-sm">
    <p>Dropdown Open: {{ toggleState ? "open" : "close" }}</p>
    <p>Checkbox 1: {{ checkboxOne ? "checked" : "unchecked" }}</p>
    <p>Checkbox 2: {{ checkboxTwo ? "checked" : "unchecked" }}</p>
    <p>Person: {{ person }}</p>
  </div>
  <ContextMenuRoot v-model="toggleState">
    <ContextMenuTrigger class="block border-2 border-white border-dashed text-white rounded text-[15px] select-none py-[45px] w-[300px] text-center">
      Right click here.
    </ContextMenuTrigger>

    <ContextMenuPortal>
      <ContextMenuContent
        class="min-w-[220px] bg-white rounded-md p-[5px] shadow-[0px_10px_38px_-10px_rgba(22,_23,_24,_0.35),_0px_10px_20px_-15px_rgba(22,_23,_24,_0.2)] will-change-[opacity,transform] data-[side=top]:animate-slideDownAndFade data-[side=right]:animate-slideLeftAndFade data-[side=bottom]:animate-slideUpAndFade data-[side=left]:animate-slideRightAndFade"
        side-offset="{5}"
      >
        <ContextMenuItem class="group text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1" @click="handleClick">
          Back
          <div class="ml-auto pl-5 text-mauve11 group-data-[highlighted]:text-white group-data-[disabled]:text-mauve8">
            ⌘+[
          </div>
        </ContextMenuItem>
        <ContextMenuItem
          class="group text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1"
          disabled
        >
          Foward
          <div class="ml-auto pl-5 text-mauve11 group-data-[highlighted]:text-white group-data-[disabled]:text-mauve8">
            ⌘+]
          </div>
        </ContextMenuItem>
        <ContextMenuItem class="group text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1">
          Reload
          <div class="ml-auto pl-5 text-mauve11 group-data-[highlighted]:text-white group-data-[disabled]:text-mauve8">
            ⌘+R
          </div>
        </ContextMenuItem>
        <ContextMenuItem class="group text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1">
          More Tools
          <div class="ml-auto pl-5 text-mauve11 group-data-[highlighted]:text-white group-data-[disabled]:text-mauve8">
            <Icon icon="radix-icons:chevron-right" />
          </div>
        </ContextMenuItem>
        <ContextMenuSeparator class="h-[1px] bg-violet6 m-[5px]" />
        <ContextMenuCheckboxItem
          v-model="checkboxOne"
          class="group text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1"
          checked="{bookmarksChecked}"
          on-checked-change="{setBookmarksChecked}"
        >
          <ContextMenuItemIndicator class="absolute left-0 w-[25px] inline-flex items-center justify-center">
            <Icon icon="radix-icons:check" />
          </ContextMenuItemIndicator>
          Show Bookmarks
          <div class="ml-auto pl-[20px] text-mauve11 group-data-[highlighted]:text-white group-data-[disabled]:text-mauve8">
            ⌘+B
          </div>
        </ContextMenuCheckboxItem>
        <ContextMenuCheckboxItem
          v-model="checkboxTwo"
          class="text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1"
          checked="{urlsChecked}"
          on-checked-change="{setUrlsChecked}"
        >
          <ContextMenuItemIndicator class="absolute left-0 w-[25px] inline-flex items-center justify-center">
            <Icon icon="radix-icons:check" />
          </ContextMenuItemIndicator>
          Show Full URLs
        </ContextMenuCheckboxItem>
        <ContextMenuSeparator class="h-[1px] bg-violet6 m-[5px]" />

        <ContextMenuLabel class="pl-[25px] text-xs leading-[25px] text-mauve11">
          People
        </ContextMenuLabel>
        <ContextMenuRadioGroup v-model="person">
          <ContextMenuRadioItem
            class="text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1"
            value="pedro"
          >
            <ContextMenuItemIndicator class="absolute left-0 w-[25px] inline-flex items-center justify-center">
              <Icon icon="radix-icons:dot-filled" />
            </ContextMenuItemIndicator>
            Pedro Duarte
          </ContextMenuRadioItem>
          <ContextMenuRadioItem
            class="text-[13px] leading-none text-violet11 rounded-[3px] flex items-center h-[25px] px-[5px] relative pl-[25px] select-none outline-none data-[disabled]:text-mauve8 data-[disabled]:pointer-events-none data-[highlighted]:bg-violet9 data-[highlighted]:text-violet1"
            value="colm"
          >
            <ContextMenuItemIndicator class="absolute left-0 w-[25px] inline-flex items-center justify-center">
              <Icon icon="radix-icons:dot-filled" />
            </ContextMenuItemIndicator>
            Colm Tuite
          </ContextMenuRadioItem>
        </ContextMenuRadioGroup>
      </ContextMenuContent>
    </ContextMenuPortal>
  </ContextMenuRoot>
</template>
