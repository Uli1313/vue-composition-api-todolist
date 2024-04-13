<script setup>
import { RouterView } from 'vue-router'
</script>

<template>
  <div class="bg-custom vh-100">
    <div class="container-xl pt-5">
      <div class="row">
        <div class="col-12 col-md-6 mx-auto">
          <RouterView />
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
</style>
