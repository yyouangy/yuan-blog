<script lang="ts" setup>
import NavBar from './components/NavBar.vue'
</script>

<template>
  <header class="p-2em">
    <NavBar />
  </header>
  <main class="flex-1 px-7 py-10 w-100%">
    <div class="max-w-70ch m-auto">
      <slot />
    </div>
  </main>
</template>
