<template>
  <div class="loader" id="loader"></div>
</template>

<script setup></script>

<style scoped>
.loader {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #f7f9fb;
  transition:
    opacity 0.75s,
    visibility 0.75s;
  /* transition-delay: .5s; */
  z-index: 99;
}

.loader::after {
  content: '';
  width: 75px;
  height: 75px;
  border-radius: 50%;
  border: 2px solid #ddd;
  border-top-color: #7449f5;
  border-left-color: #7449f5;
  animation: loading 0.75s infinite;
}

@keyframes loading {
  from {
    transform: rotate(0turn);
  }
  50% {
    transform: rotate(0.5turn);
  }
  100% {
    transform: rotate(1turn);
  }
}
</style>
