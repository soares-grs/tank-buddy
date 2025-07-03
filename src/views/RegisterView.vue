<script lang="ts" setup>
import { ref, computed } from "vue";
import { useRouter } from "vue-router";

const step = ref(0);
const router = useRouter();

const questions = [
  {
    title: "Qual a capacidade do tanque (em litros)?",
    field: "tankCapacity",
    type: "number",
    placeholder: "Digite a capacidade do tanque",
  },
  {
    title: "Quantos quilômetros você rodou desde o último abastecimento?",
    field: "distanceSinceLastRefuel",
    type: "number",
    placeholder: "Digite os km rodados",
  },
  {
    title:
      "Quantos litros de combustível você abasteceu no último abastecimento?",
    field: "litersAdded",
    type: "number",
    placeholder: "Digite os litros abastecidos",
  },
];

const answers = ref({
  tankCapacity: null,
  distanceSinceLastRefuel: null,
  litersAdded: null,
});

const errorMessage = ref("");

const isCurrentAnswerValid = computed(() => {
  const currentField = questions[step.value].field;
  // @ts-ignore
  const currentValue = answers.value[currentField];

  if (
    currentValue === null ||
    currentValue === "" ||
    isNaN(currentValue) ||
    Number(currentValue) <= 0
  ) {
    errorMessage.value = "";
    return false;
  }

  if (currentField === "litersAdded") {
    if (
      answers.value.tankCapacity !== null &&
      answers.value.tankCapacity !== 0
    ) {
      if (Number(currentValue) > Number(answers.value.tankCapacity)) {
        errorMessage.value = `O abastecimento (${currentValue} L) não pode ser maior que a capacidade do tanque (${answers.value.tankCapacity} L).`;
        return false;
      } else {
        errorMessage.value = "";
      }
    }
  } else {
    errorMessage.value = "";
  }

  return true;
});

const nextStep = () => {
  if (!isCurrentAnswerValid.value) return;

  const currentField = questions[step.value].field;
  // @ts-ignore
  const currentValue = answers.value[currentField];

  localStorage.setItem(currentField, JSON.stringify(currentValue));

  if (step.value < questions.length - 1) {
    step.value++;
  } else {
    console.log("Final answers:", answers.value);
    router.push("/dashboard");
  }
};

const preventNegativeInput = (event: any, field: any) => {
  const value = Number(event.target.value);
  if (value < 0) {
    // @ts-ignore
    answers.value[field] = null;
  }
};

const blockInvalidChars = (event: any) => {
  if (["e", "E", "+", "-"].includes(event.key)) {
    event.preventDefault();
  }
};
</script>

<template>
  <div
    class="relative flex items-center justify-center h-screen overflow-hidden text-white px-6"
  >
    <div class="absolute inset-0 bg-[#0f0f0f]">
      <div class="absolute inset-0 bg-grid-pattern z-0"></div>
      <div
        class="absolute top-1/4 left-1/2 w-[600px] h-[600px] bg-indigo-500 rounded-full blur-[200px] opacity-30 transform -translate-x-1/2 -translate-y-1/2 z-0"
      ></div>
    </div>

    <div class="relative z-10 w-full max-w-xl text-center">
      <transition name="fade-slide" mode="out-in">
        <div :key="step" class="space-y-6">
          <h2 class="text-3xl font-bold">{{ questions[step].title }}</h2>
          <!-- @vue-ignore -->
          <input
            v-if="questions[step].type === 'number'"
            type="number"
            v-model.number="answers[questions[step].field]"
            :placeholder="questions[step].placeholder"
            class="w-full p-3 rounded-lg border border-white/10 bg-white/5 text-white placeholder-gray-400 focus:outline-none focus:ring-2 focus:ring-indigo-500 transition backdrop-blur"
            min="0"
            @input="
              (event) => preventNegativeInput(event, questions[step].field)
            "
            @keydown="blockInvalidChars"
          />

          <p
            v-if="errorMessage && questions[step].field === 'litersAdded'"
            class="mt-2 text-red-500 font-semibold text-sm bg-red-900 bg-opacity-50 p-2 rounded"
          >
            {{ errorMessage }}
          </p>

          <div class="flex justify-between mt-8">
            <button
              v-if="step > 0"
              @click="step--"
              class="px-4 py-2 bg-white text-indigo-800 rounded-lg hover:bg-gray-200 transition"
            >
              Voltar
            </button>

            <button
              @click="nextStep"
              :disabled="!isCurrentAnswerValid"
              :class="[
                'ml-auto px-6 py-2 rounded-lg font-semibold transition',
                isCurrentAnswerValid
                  ? 'bg-yellow-400 text-gray-900 hover:bg-yellow-300 cursor-pointer'
                  : 'bg-gray-700 text-gray-400 cursor-not-allowed',
              ]"
            >
              {{ step === questions.length - 1 ? "Finalizar" : "Próximo" }}
            </button>
          </div>
        </div>
      </transition>
    </div>
    <footer class="absolute bottom-0 z-10 mt-6 mb-4 text-gray-500 text-sm">
      Desenvolvido por
      <a
        href="https://github.com/soares-grs"
        target="_blank"
        rel="noopener noreferrer"
        class="underline hover:text-yellow-400 transition-colors"
      >
        soares-grs
      </a>
    </footer>
  </div>
</template>

<style scoped>
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition: all 0.4s ease;
}
.fade-slide-enter-from {
  opacity: 0;
  transform: translateY(20px);
}
.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(-20px);
}

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
