<script setup lang="ts">

const meta = useProjectCreateMeta()

const options = computed(() => {
  return meta.indexingStrategy.vectorDimensions
})
</script>



<template>
  <USelectMenu :search-input="false" :items="options" class="w-full primary-dropdown mt-1" value-key="value" multiple />
</template>
