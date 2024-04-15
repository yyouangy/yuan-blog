<script setup lang="ts">
import { projectList } from '@/site.config'
import { getPosts } from '@/utils'

const posts = Object.assign({}, ...await Promise.all(projectList.map(async (item) => ({ [item.floder]: await getPosts(`notes/${item.floder}`) }))))

</script>

<template>
  <div>
    <article v-for="(series, index) in projectList" :key="index" slide-enter :style="{ '--stagger': index + 1 }">
      <div class="text-center mt-2em mb-1em text-gray-700:60" font-bold text-lg>
        {{ series.name }}
      </div>
      <div class="grid grid-cols-1 md:grid-cols-2 gap-1em">
        <div v-for="(article, index) in posts[series.floder]" :key="article._path">
          <Cell :isNotes="true" :article="article" slide-enter :style="{ '--stagger': index + 1 }" />
        </div>
      </div>
    </article>
  </div>
</template>
