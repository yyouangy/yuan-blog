<script setup lang="ts">
import { getIncludedYearPosts } from "@/utils";

const posts = await getIncludedYearPosts("blog");
</script>

<template>
  <post-list :posts="posts" />
</template>
