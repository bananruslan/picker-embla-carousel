<script setup lang="ts">
import Picker from './components/Picker.vue';
</script>

<template>
  <div class="embla">
    <div class="embla__ios-picker">
      <div class="embla__ios-picker__scene">
        <div
          class="embla__ios-picker__viewport embla__ios-picker__viewport--perspective-right"
        >
          <Picker />
        </div>
      </div>
      <div class="embla__ios-picker__label">min</div>
    </div>
  </div>
</template>

<style scoped>
.logo {
  height: 6em;
  padding: 1.5em;
  will-change: filter;
  transition: filter 300ms;
}
.logo:hover {
  filter: drop-shadow(0 0 2em #646cffaa);
}
.logo.vue:hover {
  filter: drop-shadow(0 0 2em #42b883aa);
}
</style>
