<script setup>
import { RouterView } from 'vue-router'
import CustomNavbar from './components/CustomNavbar.vue';
</script>

<template>
  <CustomNavbar />

  <div class="app-body">
    <RouterView />
  </div>
</template>

