<script setup lang="ts">
import { formattedDate } from '@/utils'

const props = defineProps(['article', 'isNotes'])
</script>

<template>
  <NuxtLink :to="`/p${props.article._path}`">

    <li v-if="props.isNotes" class="flex items-center py-0.5em px-1em rounded-sm hover:bg-gray-400:10">
      <div class="hover w-full">
        <div class="text-lg">{{ props.article.title }}
        </div>
      </div>

    </li>

    <li v-else :title="props.article.title"
      class="hover list-none mb-6 mt-2 flex flex-col md:(gap-2 flex-row items-center)">
      <span class="text-lg">
        {{ props.article.title }}
      </span>
      <span class="text-sm op-70">
        {{ formattedDate(props.article.date) }}
      </span>
    </li>




  </NuxtLink>
</template>
