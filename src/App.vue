<script setup lang="ts">
import Picker from "./components/Picker.vue";
</script>

<template>
  <div class="embla">
    <div class="embla-picker">
      <div class="embla-picker__scene">
        <Picker />
      </div>
    </div>
  </div>
</template>

<style scoped>
.embla {
  position: relative;
  display: flex;
  width: 100%;
  height: 22.2rem;
  max-width: 30rem;
  margin-left: auto;
  margin-right: auto;
  border: 1px solid var(--text-high-contrast);
  overflow: hidden;
  border-radius: 16px;
}
.embla:before,
.embla:after {
  position: absolute;
  left: 0;
  right: 0;
  content: "";
  display: block;
  height: calc(50% - 32px / 2);
  z-index: 1;
  pointer-events: none;
}
.embla:before {
  top: -0.5px;
  border-bottom: 0.5px solid rgba(var(--text-high-contrast-rgb-value), 0.3);
  background: linear-gradient(
    to top,
    rgba(var(--background-site-rgb-value), 0.65) 0%,
    rgba(var(--background-site-rgb-value), 1) 100%
  );
}
.embla:after {
  bottom: -0.5px;
  border-top: 0.5px solid rgba(var(--text-high-contrast-rgb-value), 0.3);
  background: linear-gradient(
    to bottom,
    rgba(var(--background-site-rgb-value), 0.65) 0%,
    rgba(var(--background-site-rgb-value), 1) 100%
  );
}
.embla-picker {
  height: 100%;
  display: flex;
  align-items: center;
  min-width: 100%;
  justify-content: center;
  line-height: 1;
  font-size: 1.8rem;
}
.embla-picker__scene {
  min-width: 100%;
  height: 100%;
  overflow: hidden;
  display: flex;
  align-items: center;
  touch-action: pan-x;
}
</style>
