<script setup>
import { useRouter } from "vue-router";

const router = useRouter();

const handleContinue = () => {
  router.push("/register");
};
</script>

<template>
  <div
    class="relative flex items-center justify-center h-screen overflow-hidden text-white"
  >
    <div class="absolute inset-0 bg-[#0f0f0f]">
      <div class="absolute inset-0 bg-grid-pattern z-0"></div>

      <div
        class="absolute top-1/4 left-1/2 w-[600px] h-[600px] bg-indigo-500 rounded-full blur-[200px] opacity-30 transform -translate-x-1/2 -translate-y-1/2 z-0"
      ></div>
    </div>

    <div class="relative z-10 text-center space-y-8 px-4">
      <h1 class="text-5xl md:text-6xl font-bold animate-pulse">
        Bem-vindo(a) ao <span class="text-yellow-400">TankBuddy</span>!
      </h1>
      <p class="text-lg md:text-xl text-gray-300">
        Monitore o consumo do seu veículo com estilo 🚀
      </p>
      <button
        @click="handleContinue"
        class="bg-yellow-400 hover:bg-yellow-300 text-gray-900 font-semibold py-3 px-6 rounded-xl shadow-lg transition-all duration-300 hover:scale-105 cursor-pointer"
      >
        Começar
      </button>
    </div>
  </div>
</template>

<style scoped>
.bg-grid-pattern {
  background-image: linear-gradient(
      rgba(255, 255, 255, 0.02) 1px,
      transparent 1px
    ),
    linear-gradient(90deg, rgba(255, 255, 255, 0.02) 1px, transparent 1px);
  background-size: 40px 40px;
  background-position: center;
}
</style>
