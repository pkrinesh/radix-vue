<script setup lang="ts">
import { format } from 'date-fns'

function formatDate(dateString: string) {
  const date = new Date(dateString)
  return format(date, 'MMMM d, yyyy')
}
</script>

<template>
  <div>
    <GithubReleases v-slot="{ releases }">
      <div v-for="release in releases" :key="release.name" class="flex flex-col">
        <div class="mt-8 -mb-6 flex items-center gap-3">
          <h2 class="text-3xl font-semibold">
            {{ formatDate(release.date) }}
          </h2>
          <div class="rounded-full bg-neutral-800 px-2">
            {{ release.name }}
          </div>
        </div>
        <div>
          <h3 class="sr-only">
            Description
          </h3>

          <ContentRenderer :value="release">
            <template #empty>
              <p>No description provided</p>
            </template>
          </ContentRenderer>
        </div>
      </div>
    </GithubReleases>
  </div>
</template>
