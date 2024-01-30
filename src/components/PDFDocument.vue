<template>
  <section class="flex flex-col items-center container mx-auto bg-white hidden">
    <div
      class="mt-4"
      v-for="(diaData, diaKey) in selectedBands"
      :key="diaKey"
      :id="`${diaKey}Day`"
    >
      <div
        v-for="(banda, escenarioKey) in agruparPorEscenario(diaData)"
        :key="escenarioKey"
      >
        <div class="day1_container">
          <div class="day1">
            <img
              v-if="diaKey == 1"
              src="../assets/img/banner-day1.png" 
              alt="Dia 1 Banner"
            />
            <img
              v-if="diaKey == 2"
              src="../assets/img/banner-day2.png"
              alt="Dia 2 Banner"
            />
          </div>
        </div>
        <div class="text-center">
          <h2 class="text-4xl mb-10 mt-10 inline-block relative">
            {{ escenarioKey }}
            <div class="mt-5"></div>
            <div
              class="absolute bottom-0 left-0 right-0 border-b-2 border-red-500"
            ></div>
          </h2>
        </div>
        <ul class="mt-4 w-full">
          <li
            class="flex justify-between items-center border-b border-gray-300 mb-4 w-full"
            style="color: #21b4d2; padding-bottom: 10px"
          >
            <h2 class="text-2xl font-semibold">Banda</h2>
            <h2 class="text-2xl font-semibold">Horario</h2>
          </li>
          <li
            v-for="band in banda"
            :key="band.nombre"
            class="flex justify-between items-center mb-4 w-full"
            style="
              color: rgb(0, 0, 0);
              border-bottom: 2px solid #88888839;
              padding: 1px;
              background-color: #fff;
              padding-bottom: 10px;
            "
          >
            <h4 class="text-2xl">{{ band.nombre }}</h4>
            <h4 class="text-2xl">{{ band.horario }}</h4>
          </li>
        </ul>
        <div
          v-if="
            escenarioKey !== Object.keys(agruparPorEscenario(diaData)).pop()
          "
          style="page-break-after: always"
        ></div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  props: {
    selectedBands: {
      type: Object,
      required: true,
    },
    agruparPorEscenario: {
      type: Function,
      required: true,
    },
  },
};
</script>

<style scoped>
.day1_container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 20vh;
  margin-top: 10px;
}

.day1 {
  width: 100%;
  height: 100%;
  max-width: 100%;
  max-height: 100%;
}

.day1 img {
  width: 100%;
  height: 100%;
  object-fit: contain;
}
</style>
