<template>
  <main class="flex justify-center bg-[#34308f] h-screen">
    <div>
      <div class="w-80">
        <img src="../assets/images/cloud.png" alt="" />
      </div>
      <div>
        <h1 class="text-center text-7xl font-bold text-yellow-600">Breeze</h1>
        <h2 class="text-3xl font-medium text-white text-center mt-4">Weather App</h2>
        <div class="flex items-center justify-center mt-16">
          <button
            @click="$emit('switch-view')"
            id="btn"
            class="bg-yellow-500 p-2 px-8 text-lg rounded-lg font-medium text-white"
          >
            Get Started
          </button>
        </div>
      </div>
    </div>
  </main>
</template>

<script setup></script>

<style lang="scss" scoped></style>
