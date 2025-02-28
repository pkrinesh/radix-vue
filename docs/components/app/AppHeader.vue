<script setup lang="ts">
defineProps({
  ...variants,
})
const { config } = useDocus()
const { navigation } = useContent()
const { hasDocSearch } = useDocSearch()

const hasDialog = computed(
  () => navigation.value?.length > 1 || navigation.value?.[0]?.children?.length,
)
</script>

<template>
  <header
    :class="{ 'has-dialog': hasDialog, 'has-doc-search': hasDocSearch }"
    class="bg-black/10 border-b border-neutral-600/10"
  >
    <Container :fluid="config?.header?.fluid">
      <div class="section left">
        <AppHeaderDialog v-if="hasDialog" />
        <AppHeaderLogo />
      </div>

      <div class="section center">
        <AppHeaderLogo v-if="hasDialog" />
        <AppHeaderNavigation />
      </div>

      <div class="section right">
        <NuxtLink
          to="/overview/introduction"
          class="text-[15px] text-neutral-400 hover:text-white mr-4"
        >
          Documentation
        </NuxtLink>
        <AppSearch v-if="hasDocSearch" />
        <ThemeSelect />
        <div class="social-icons">
          <AppSocialIcons />
        </div>
      </div>
    </Container>
  </header>
</template>

<style scoped lang="ts">
css({
  ':deep(.icon)': {
    width: '{space.4}',
    height: '{space.4}'
  },

  '.navbar-logo': {
    '.left &': {
      '.has-dialog &': {
        display: 'none',
        '@lg': {
          display: 'block'
        }
      },
    },
    '.center &': {
      display: 'block',
      '@lg': {
        display: 'none'
      }
    }
  },

  header: {
    backdropFilter: '{elements.backdrop.filter}',
    position: 'sticky',
    top: 0,
    zIndex: 10,
    width: '100%',
    height: '48px',

    '.container': {
      display: 'grid',
      height: '100%',
      gridTemplateColumns: 'repeat(12, minmax(0, 1fr))',
      gap: '{space.2}'
    },

    '.section': {
      display: 'flex',
      alignItems: 'center',
      flex: 'none',
      '&.left': {
        gridColumn: 'span 4 / span 4',
        '@lg': {
          marginLeft: 0
        },
      },
      '&.center': {
        gridColumn: 'span 4 / span 4',
        justifyContent: 'center',
        flex: '1',
        zIndex: '1'
      },
      '&.right': {
        display: 'flex',
        gridColumn: 'span 4 / span 4',
        justifyContent: 'flex-end',
        alignItems: 'center',
        flex: 'none',
        marginRight: 'calc(0px - {space.4})',
        '.social-icons': {
          display: 'none',
          '@md': {
            display: 'flex',
            alignItems: 'center',
          }
        }
      }
    }
  }
})
</style>
