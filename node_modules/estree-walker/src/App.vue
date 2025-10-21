<template>
  <div id="app">
    <Calendar />
  </div>
</template>

<script>
import Calendar from './components/Calendar.vue'; // caminho correto para o componente

export default {
  name: 'App',
  components: {
    Calendar,
  },
};
</script>

<style>
/* Estilos globais, se precisar */
</style>