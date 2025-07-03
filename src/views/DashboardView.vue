<script lang="ts" setup>
import { ref } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

const goToRegister = () => {
  localStorage.removeItem("tankCapacity");
  localStorage.removeItem("distanceSinceLastRefuel");
  localStorage.removeItem("litersAdded");
  router.push("/register");
};

const tankCapacity = Number(localStorage.getItem("tankCapacity")) || 0;
const distanceSinceLastRefuel =
  Number(localStorage.getItem("distanceSinceLastRefuel")) || 0;
const litersAdded = Number(localStorage.getItem("litersAdded")) || 0;

const consumption = litersAdded > 0 ? distanceSinceLastRefuel / litersAdded : 0;
const consumptionPer100Km = consumption > 0 ? 100 / consumption : 0;
const percentTankUsed =
  tankCapacity > 0 ? (litersAdded / tankCapacity) * 100 : 0;
const estimatedRange = tankCapacity * consumption;
const estimatedDistanceLastRefuel = consumption * litersAdded;

const totalKm = distanceSinceLastRefuel;

const consumptionAlert = consumption > 0 && consumption < 10;

const showTooltip = ref(false);

function toggleTooltip() {
  showTooltip.value = !showTooltip.value;
}
</script>

<template>
  <div
    class="relative flex flex-col min-h-screen overflow-hidden text-white px-6 py-10"
  >
    <div class="absolute inset-0 bg-[#0f0f0f]">
      <div class="absolute inset-0 bg-grid-pattern z-0"></div>
      <div
        class="absolute top-1/4 left-1/2 w-[600px] h-[600px] bg-indigo-500 rounded-full blur-[200px] opacity-30 transform -translate-x-1/2 -translate-y-1/2 z-0"
      ></div>
    </div>

    <header class="mb-12 max-w-5xl mx-auto text-center relative z-10">
      <h1 class="text-5xl md:text-6xl font-extrabold text-white leading-tight">
        <span class="text-gray-300">Aqui está o resumo do</span><br />
        <span class="text-yellow-400">seu veículo</span>
      </h1>
      <p class="mt-4 text-gray-400 text-lg max-w-xl mx-auto">
        Acompanhe seu desempenho de forma moderna e intuitiva
      </p>
    </header>

    <section
      class="max-w-5xl mx-auto grid grid-cols-1 md:grid-cols-2 gap-8 mb-12 relative z-10"
      aria-label="Resumo do consumo da moto"
    >
      <div
        class="bg-gradient-to-tr from-indigo-600/10 to-white/10 backdrop-blur rounded-xl p-6 shadow-lg border border-white/10 text-center hover:scale-105 transition-transform"
      >
        <h2 class="text-xl font-medium text-gray-300 mb-2">Consumo Médio</h2>
        <p class="text-5xl font-bold text-yellow-400">
          {{ consumption.toFixed(1) }} <span class="text-xl">km/l</span>
        </p>
      </div>

      <div
        class="bg-gradient-to-tr from-indigo-600/10 to-white/10 backdrop-blur rounded-xl p-6 shadow-lg border border-white/10 text-center hover:scale-105 transition-transform"
      >
        <h2 class="text-xl font-medium text-gray-300 mb-2">
          Quilômetros Rodados
        </h2>
        <p class="text-5xl font-bold text-yellow-400">
          {{ totalKm.toLocaleString() }} <span class="text-xl">km</span>
        </p>
      </div>
    </section>

    <section
      class="max-w-5xl mx-auto bg-white/5 backdrop-blur rounded-xl p-8 shadow-lg border border-white/10 relative z-10"
    >
      <div class="text-center relative">
        <h2
          class="text-xl font-medium text-gray-300 mb-2 flex justify-center items-center gap-2"
        >
          Autonomia Estimada
          <button
            @mouseenter="showTooltip = true"
            @mouseleave="showTooltip = false"
            @focus="showTooltip = true"
            @blur="showTooltip = false"
            @click="toggleTooltip"
            aria-label="O que é autonomia estimada?"
            class="relative p-1 rounded-full hover:bg-yellow-400 hover:text-gray-900 transition"
            tabindex="0"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="h-5 w-5 text-yellow-400"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
              stroke-width="2"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                d="M13 16h-1v-4h-1m1-4h.01M12 18.5a6.5 6.5 0 100-13 6.5 6.5 0 000 13z"
              />
            </svg>
            <div
              v-if="showTooltip"
              class="absolute bottom-full mb-2 w-64 left-1/2 -translate-x-1/2 rounded-md bg-yellow-400 text-gray-900 p-3 text-sm shadow-lg z-20"
            >
              <p>
                Autonomia estimada é a distância aproximada que seu veículo pode
                percorrer com o tanque cheio, baseada no consumo médio.
              </p>
              <p class="mt-1 italic text-xs text-gray-800">
                (Capacidade do tanque × Consumo médio)
              </p>
            </div>
          </button>
        </h2>
        <p class="text-4xl font-bold text-yellow-400">
          {{
            estimatedRange > 0
              ? estimatedRange.toFixed(0) + " km"
              : "Dados insuficientes"
          }}
        </p>
        <p class="text-sm text-gray-400 mt-1">
          Baseado em {{ tankCapacity }} L e {{ consumption.toFixed(1) }} km/l
        </p>
      </div>
    </section>

    <section
      class="max-w-5xl mx-auto bg-white/5 backdrop-blur rounded-xl p-8 shadow-lg border border-white/10 relative z-10 mt-12"
    >
      <div class="text-center">
        <h2 class="text-xl font-medium text-gray-300 mb-2">
          Consumo Médio Ajustado
        </h2>
        <p class="text-2xl font-semibold text-yellow-400 mb-4">
          {{ consumptionPer100Km.toFixed(1) }} L / 100 km
        </p>

        <h2 class="text-xl font-medium text-gray-300 mb-2">
          Percentual do Tanque Usado
        </h2>
        <p class="text-2xl font-semibold text-yellow-400 mb-4">
          {{ percentTankUsed.toFixed(1) }}%
        </p>

        <div
          v-if="consumptionAlert"
          class="mt-4 p-3 bg-red-600 rounded text-white max-w-sm mx-auto"
        >
          ⚠️ Atenção: Seu consumo médio está abaixo do esperado. Verifique seu
          veículo!
        </div>
      </div>
    </section>

    <div class="max-w-5xl mx-auto mt-12 text-center relative z-10">
      <button
        @click="goToRegister"
        class="bg-yellow-400 cursor-pointer hover:bg-yellow-300 text-gray-900 font-semibold py-3 px-8 rounded-xl shadow-lg transition-transform duration-300 hover:scale-105"
      >
        Registrar novo abastecimento
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

button[aria-label] {
  outline: none;
}

button[aria-label]:focus {
  box-shadow: 0 0 0 3px rgba(253, 224, 71, 0.8);
}
</style>
