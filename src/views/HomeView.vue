<script setup lang="ts">
import PageTitle from '@/components/PageTitle.vue';
import StarRailMaterialCalculation from '@/components/StarRailMaterialCalculation.vue';
import { RouterLink } from 'vue-router';
</script>

<template>
  <main>
    <RouterLink to="/firefly">Firefly</RouterLink>
    <PageTitle title="SR Material Calculation" />
    
    <StarRailMaterialCalculation />
  </main>
</template>
