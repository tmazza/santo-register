<script setup>
import { RouterLink, RouterView } from "vue-router";
import HelloWorld from "@/components/HelloWorld.vue";
</script>

<template>
  <header>
    <h2 style="text-align: center; font-weight: bold">Santo Food Park</h2>
  </header>

  <RouterView />
</template>

<style>
@import "@/assets/base.css";
@media print {
  header {
    display: none;
  }
}
</style>
