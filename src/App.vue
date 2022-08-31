<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <Navbar/>
    <router-view v-slot="{Component}">
      <Transition name="page" mode="out-in">
        <component :is="Component" />
      </Transition>
    </router-view>
  </div>
</template>

<script setup>
import Navbar from '@/components/Navbar.vue'
</script>

<style>
  .page-enter-active,
  .page-leave-active{
    transition: 600ms ease all;
  }
  .page-enter-from,
  .page-leave-to{
    opacity: 0;
  }
</style>
