<script setup lang="ts">
import PageTitle from '@/components/PageTitle.vue';
import Live2D from '@/components/Live2D.vue';
</script>

<template>
  <main>
    <RouterLink to="/">Home</RouterLink>
    <PageTitle title="Firefly Live2D" />
    <Live2D />
  </main>
</template>
