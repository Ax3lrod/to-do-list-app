<!-- layouts/default.vue -->
<template>
  <div class="flex flex-col min-h-screen">
    <div class="flex-grow">
      <slot />
    </div>
    <app-footer />
  </div>
</template>

<style scoped>
/* No additional styles needed; it's handled by flexbox */
</style>
