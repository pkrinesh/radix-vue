---
metaTitle: Toast
metaDescription: A succinct message that is displayed temporarily.
name: toast
aria: https://www.w3.org/TR/wai-aria/#aria-live
---

<script setup> 
import DemoToast from '../../components/demo/Toast/index.vue' 
</script>

# Toast

<Badge>New</Badge>

<Description>
A succinct message that is displayed temporarily.
</Description>


<HeroContainer>
  <DemoToast />
</HeroContainer>

<HeroCodeBlock folder="Toast" />

 
<Highlights
  :features="[
    'Automatically closes.',
    'Pauses closing on hover, focus and window blur.',
    'Supports hotkey to jump to toast viewport.',
    'Supports closing via swipe gesture.',
    'Exposes CSS variables for swipe gesture animations.',
    'Can be controlled or uncontrolled.',
  ]"
/>


## Installation

Install the component from your command line.

```bash
npm install radix-vue
```

## Anatomy

Import the component.

```vue
<script setup lang="ts">
import { ToastAction, ToastClose, ToastDescription, ToastProvider, ToastRoot, ToastTitle, ToastViewport } from 'radix-vue'
</script>

<template>
  <ToastProvider>
    <ToastRoot>
      <ToastTitle />
      <ToastDescription />
      <ToastAction />
      <ToastClose />
    </ToastRoot>

    <ToastViewport />
  </ToastProvider>
</template>
```

## API Reference

### Provider

The provider that wraps your toasts and toast viewport. It usually wraps the application.

<PropsTable
  :data="[
    {
      name: 'duration',
      type: 'number',
      default: 5000,
      description:
        'The time in milliseconds that should elapse before automatically closing each toast',
    },
    {
      name: 'label',
      required: true,
      type: 'string',
      default: '&quot;Notification&quot;',
      description:
        'An author-localized label for each toast Used to help screen reader users associate the interruption with a toast',
    },
    {
      name: 'swipeDirection',
      type: '&quot;right&quot; | &quot;left&quot; | &quot;up&quot; | &quot;down&quot;',
      default: '&quot;right&quot;',
      description:
        'The direction of the pointer swipe that should close the toast',
    },
    {
      name: 'swipeThreshold',
      type: 'number',
      default: 50,
      description:
        'The distance in pixels that the swipe gesture must travel before a close is triggered.',
    },
  ]"
/>

### Viewport

The fixed area where toasts appear. Users can jump to the viewport by pressing a hotkey. It is up to you to ensure the discoverability of the hotkey for keyboard users.

<PropsTable
  :data="[
    {
      name: 'asChild',
      required: false,
      type: 'boolean',
      default: 'false',
      description: 'Change the default rendered element for the one passed as a child, merging their props and behavior.<br><br>Read our <a href=&quot;/guides/composition&quot;>Composition</a> guide for more details.',
    },
    {
      name: 'hotkey',
      type: 'string[]',
      default: '[&quot;F8&quot;]',
      description: `
        <span>
          The keys to use as the keyboard shortcut that will move focus to the
          toast viewport. Use <Code>event.code</Code> value for each key from
          <a href=&quot;https://keycode.info/&quot; variant=&quot;blue&quot; target=&quot;_blank&quot;>
            keycode.info
          </a>
          . For meta keys, use <Code>ctrlKey</Code>, <Code>shiftKey</Code>, <Code>
            altKey
          </Code> and/or <Code>metaKey</Code>.
        </span>
      `,
    },
    {
      name: 'label',
      type: 'string',
      default: `&quot;Notifications ({hotkey})&quot;`,
      description:
        'An author-localized label for the toast region to provide context for screen reader users when navigating page landmarks. The available `{hotkey}` placeholder will be replaced for you.',
    },
  ]"
/>
 
### Root

The toast that automatically closes. It should not be held open to [acquire a user response](/components/toast#action).

<PropsTable
  :data="[
    {
      name: 'asChild',
      required: false,
      type: 'boolean',
      default: 'false',
      description: 'Change the default rendered element for the one passed as a child, merging their props and behavior.<br><br>Read our <a href=&quot;/guides/composition&quot;>Composition</a> guide for more details.',
    },
    {
      name: 'type',
      type: '&quot;foreground&quot; | &quot;background&quot;',
      default: '&quot;foreground&quot;',
      description: `
        <span>
          Control the
          <a href=&quot;/components/toast#sensitivity&quot;>
            sensitivity
          </a>
          of the toast for accessibility purposes. For toasts that are the
          result of a user action, choose <Code>foreground</Code>. Toasts
          generated from background tasks should use <Code>background</Code>.
        </span>`
    },
    {
      name: 'duration',
      type: 'number',
      description:
        'The time in milliseconds that should elapse before automatically closing the toast This will override the value supplied to the provider.',
    },
    {
      name: 'defaultOpen',
      type: 'boolean',
      default: 'true',
      description:
        'The open state of the dialog when it is initially rendered. Use when you do not need to control its open state.',
    },
    {
      name: 'open',
      type: 'boolean',
      description: `
        <span>
          The controlled open state of the dialog. Must be used in conjunction
          with <Code>onOpenChange</Code>.
        </span>
      `
    },
    {
      name: 'forceMount',
      type: 'boolean',
      description:
        'Used to force mounting when more control is needed. Useful when controlling animation with React animation libraries.',
    },
  ]"
/>

<EmitsTable 
  :data="[
    {
      name: '@update:open',
      type: '(value: boolean) => void',
      description: 'Event handler called when the open state changes'
    },
    {
      name: '@escapeKeyDown',
      type: '(event: KeyboardEvent) => void',
      description: `
        <span>
          Event handler called when the escape key is down. It can be prevented
          by calling <Code>event.preventDefault</Code>.
        </span>
      `
    },
    {
      name: '@pause',
      type: '() => void',
      description:
        'Event handler called when the dismiss timer is paused. This occurs when the pointer is moved over the viewport, the viewport is focused or when the window is blurred.',
    },
    {
      name: '@resume',
      type: '() => void',
      description:
        'Event handler called when the dismiss timer is resumed. This occurs when the pointer is moved away from the viewport, the viewport is blurred or when the window is focused.',
    },
    {
      name: '@swipeStart',
      type: '(event: SwipeEvent) => void',
      description:  `
        <span>
          Event handler called when starting a swipe interaction. It can be
          prevented by calling <Code>event.preventDefault</Code>.
        </span>
     `
    },
    {
      name: '@swipeMove',
      type: '(event: SwipeEvent) => void',
      description:  `
        <span>
          Event handler called during a swipe interaction. It can be prevented
          by calling <Code>event.preventDefault</Code>.
        </span>
      ` 
    },
    {
      name: '@swipeEnd',
      type: '(event: SwipeEvent) => void',
      description: ` 
        <span>
          Event handler called at the end of a swipe interaction. It can be
          prevented by calling <Code>event.preventDefault</Code>.
        </span>
       `
    },
  ]" 
/>
 
<DataAttributesTable
  :data="[
    {
      attribute: '[data-state]',
      values: ['open', 'closed'],
    },
    {
      attribute: '[data-swipe]',
      values: ['start', 'move', 'cancel', 'end'],
    },
    {
      attribute: '[data-swipe-direction]',
      values: ['up', 'down', 'left', 'right'],
    },
  ]"
/>

<CssVariablesTable
  :data="[
    {
      cssVariable: '--radix-toast-swipe-move-x',
      description: 'The offset position of the toast when horizontally swiping',
    },
    {
      cssVariable: '--radix-toast-swipe-move-y',
      description: 'The offset position of the toast when vertically swiping',
    },
    {
      cssVariable: '--radix-toast-swipe-end-x',
      description:
        'The offset end position of the toast after horizontally swiping',
    },
    {
      cssVariable: '--radix-toast-swipe-end-y',
      description:
        'The offset end position of the toast after vertically swiping',
    },
  ]"
/>

### Title

An optional title for the toast

<PropsTable
  :data="[
    {
      name: 'asChild',
      required: false,
      type: 'boolean',
      default: 'false',
      description: 'Change the default rendered element for the one passed as a child, merging their props and behavior.<br><br>Read our <a href=&quot;/guides/composition&quot;>Composition</a> guide for more details.',
    },
  ]"
/>

### Description

The toast message.

<PropsTable
  :data="[
    {
      name: 'asChild',
      required: false,
      type: 'boolean',
      default: 'false',
      description: 'Change the default rendered element for the one passed as a child, merging their props and behavior.<br><br>Read our <a href=&quot;/guides/composition&quot;>Composition</a> guide for more details.',
    },
  ]"
/>

### Action

An action that is safe to ignore to ensure users are not expected to complete tasks with unexpected side effects as a result of a [time limit](https://www.w3.org/TR/UNDERSTANDING-WCAG20/time-limits-required-behaviors.html).

When obtaining a user response is necessary, portal an ["AlertDialog"](/components/alert-dialog) styled as a toast into the viewport instead.

<PropsTable
  :data="[
    {
      name: 'asChild',
      required: false,
      type: 'boolean',
      default: 'false',
      description: 'Change the default rendered element for the one passed as a child, merging their props and behavior.<br><br>Read our <a href=&quot;/guides/composition&quot;>Composition</a> guide for more details.',
    },
    {
      name: 'altText',
      required: true,
      type: 'string',
      description:  `
          Describe an
          <a href=&quot;/toast#alternative-action&quot;>
            alternative way to achieve the action
          </a>
          for screen reader users who cannot access the toast easily.
         `
    },
  ]"
/>

### Close

A button that allows users to dismiss the toast before its duration has elapsed.

<PropsTable
  :data="[
    {
      name: 'asChild',
      required: false,
      type: 'boolean',
      default: 'false',
      description: 'Change the default rendered element for the one passed as a child, merging their props and behavior.<br><br>Read our <a href=&quot;/guides/composition&quot;>Composition</a> guide for more details.',
    },
  ]"
/>


## Examples

### Custom hotkey

Override the default hotkey using the `event.code` value for each key from [keycode.info](https://keycode.info/).

```html line=3
<ToastProvider>
  ...
  <ToastViewport :hotkey="['altKey', 'KeyT']" />
</ToastProvider>
```

### Custom duration

Customise the duration of a toast to override the provider value.

```vue line=1
<ToastRoot :duration="3000">
  <ToastDescription>Saved!</ToastDescription>
</ToastRoot>
```

### Duplicate toasts

When a toast must appear every time a user clicks a button, use state to render multiple instances of the same toast (see below). Alternatively, you can abstract the parts to create your own [imperative API](/components/toast#imperative-api).

```html line=2,7
<div>
  <form  @submit="count++">
    ...
    <button>save</button>
  </form>

  <ToastRoot v-for="(_, index) in count" :key="index">
    <ToastDescription>Saved!</ToastDescription>
  </ToastRoot>
</div>
```

### Animating swipe gesture

Combine `--radix-toast-swipe-move-[x|y]` and `--radix-toast-swipe-end-[x|y]` CSS variables with `data-swipe="[start|move|cancel|end]"` attributes to animate a swipe to close gesture. Here's an example:

```html line=2
<ToastProvider swipeDirection="right">
  <ToastRoot class="ToastRoot">...</ToastRoot>
  <ToastViewport />
</ToastProvider>
```

```css line=2,3,5,9,15
/* styles.css */
.ToastRoot[data-swipe='move'] {
  transform: translateX(var(--radix-toast-swipe-move-x));
}
.ToastRoot[data-swipe='cancel'] {
  transform: translateX(0);
  transition: transform 200ms ease-out;
}
.ToastRoot[data-swipe='end'] {
  animation: slideRight 100ms ease-out;
}

@keyframes slideRight {
  from {
    transform: translateX(var(--radix-toast-swipe-end-x));
  }
  to {
    transform: translateX(100%);
  }
}
```

## Accessibility

Adheres to the [`aria-live` requirements](https://www.w3.org/TR/wai-aria/#aria-live).

### Sensitivity

Control the sensitivity of the toast for screen readers using the `type` prop.

For toasts that are the result of a user action, choose `foreground`. Toasts generated from background tasks should use `background`.

#### Foreground

Foreground toasts are announced immediately. Assistive technologies may choose to clear previously queued messages when a foreground toast appears. Try to avoid stacking distinct foreground toasts at the same time.

#### Background

Background toasts are announced at the next graceful opportunity, for example, when the screen reader has finished reading its current sentence. They do not clear queued messages so overusing them can be perceived as a laggy user experience for screen reader users when used in response to a user interaction.

```html line=1,6
<ToastRoot type="foreground">
  <ToastDescription>File removed successfully.</ToastDescription>
  <ToastClose>Dismiss</ToastClose>
</ToastRoot>

<ToastRoot type="background">
  <ToastDescription>We've just released Radix 1.0.</ToastDescription>
  <ToastClose>Dismiss</ToastClose>
</ToastRoot>
```

### Alternative action

Use the `altText` prop on the `Action` to instruct an alternative way of actioning the toast to screen reader users.

You can direct the user to a permanent place in your application where they can action it or implement your own custom hotkey logic. If implementing the latter, use `foreground` type to announce immediately and increase the duration to give the user ample time.

```html line=4,10,12
<ToastRoot type="background">
  <ToastTitle>Upgrade Available!</ToastTitle>
  <ToastDescription>We've just released Radix 1.0.</ToastDescription>
  <ToastAction altText="Goto account settings to upgrade">
    Upgrade
  </ToastAction>
  <ToastClose>Dismiss</ToastClose>
</ToastRoot>

<ToastRoot type="foreground" :duration="10000">
  <ToastDescription>File removed successfully.</ToastDescription>
  <ToastAction altText="Undo (Alt+U)">
    Undo <kbd>Alt</kbd>+<kbd>U</kbd>
  </ToastAction>
  <ToastClose>Dismiss</ToastClose>
</ToastRoot>
```

### Close icon button

When providing an icon (or font icon), remember to label it correctly for screen reader users.

```html line=3-4
<ToastRoot type="foreground">
  <ToastDescription>Saved!</ToastDescription>
  <ToastClose aria-label="Close">
    <span aria-hidden>×</span>
  </ToastClose>
</ToastRoot>
```

### Keyboard Interactions

<KeyboardTable
  :data="[
    {
      keys: ['F8'],
      description: 'Focuses toasts viewport.',
    },
    {
      keys: ['Tab'],
      description: 'Moves focus to the next focusable element.',
    },
    {
      keys: ['Shift + Tab'],
      description: 'Moves focus to the previous focusable element.',
    },
    {
      keys: ['Space'],
      description: `
        <span>
          When focus is on a <Code>ToastAction</Code> or
          <Code>ToastClose</Code>, closes the toast
        </span>`
    },
    {
      keys: ['Enter'],
      description:  `
        <span>
          When focus is on a <Code>ToastAction</Code> or
          <Code>ToastClose</Code>, closes the toast
        </span>` 
    },
    {
      keys: ['Esc'],
      description:  `
        <span>
          When focus is on a <Code>Toast</Code>, closes the toast
        </span> `
    },
  ]"
/>

## Custom APIs

### Abstract parts

Create your own API by abstracting the primitive parts into your own component.

#### Usage

```vue
<script setup lang="ts">
import Toast from './your-toast.vue'
</script>

<template>
  <Toast title="Upgrade available" content="We've just released Radix 3.0!">
    <button @click="handleUpgrade">
      Upgrade
    </button>
  </Toast>
</template>
```

#### Implementation

```vue
// your-toast.vue
<script setup lang="ts">
import { ToastAction, ToastClose, ToastDescription, ToastRoot, ToastTitle } from 'radix-vue'

defineProps<{
  title: string
  content: string
}>()
</script>

<template>
  <ToastRoot>
    <ToastTitle v-if="title">
      {{ title }}
    </ToastTitle>
    <ToastDescription>{{ content }}</ToastDescription>
    <ToastAction as-child>
      <slot />
    </ToastAction>
    <ToastClose aria-label="Close">
      <span aria-hidden>×</span>
    </ToastClose>
  </ToastRoot>
</template>
```

### Imperative API

Create your own imperative API to allow [toast duplication](/components/toast#duplicate-toasts) if preferred.

#### Usage

```vue
<script setup lang="ts">
import Toast from './your-toast.vue'
import type { InstanceType } from 'vue'

const savedRef = ref<InstanceType<typeof Toast>>()
</script>

<template>
  <div>
    <form @submit="savedRef.publish()">
      ...
    </form>
    <Toast ref="savedRef">
      Saved successfully!
    </Toast>
  </div>
</template>
```


#### Implementation


```vue
// your-toast.vue
<script setup lang="ts">
import { ToastClose, ToastDescription, ToastRoot, ToastTitle } from 'radix-vue'
import { ref } from 'vue'

const count = ref(0)

function publish() {
  count.value++
}

defineExpose({
  publish
})
</script>

<template>
  <ToastRoot v-for="index in count" :key="index">
    <ToastDescription>
      <slot />
    </ToastDescription>
    <ToastClose>Dismiss</ToastClose>
  </ToastRoot>
</template>
```
 