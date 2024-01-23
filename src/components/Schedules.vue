<template>
    <div class="center  p-5 lg:p-2">
      <section class="mt-5">
        <div class="button-container" ref="buttonContainer">
          <button
            v-for="escenario in escenarios"
            :key="escenario.id"
            :class="{
              'text-white': selectedEscenario === escenario,
              'bg-[#21B4D2]': selectedEscenario === escenario,
              'border-blue-[#21B4D2]': selectedEscenario === escenario,
              'hover:bg-[#21B4D2]': selectedEscenario !== escenario,
              'hover:text-white': selectedEscenario !== escenario,
              'border': true,
              'hover:border-[#21B4D2]': false,
              'font-medium': true,
              'rounded-lg': true,
              'text-lg': true,
              'px-6': true,
              'py-3': true,
              'text-center': true,
              'me-2': true,
              'mb-4': true,
              'whitespace-nowrap': true
            }"
            type="button"
            @click="handleButtonClick(escenario)"
          >
            <h5 class="whitespace-nowrap lg:whitespace-pre-wrap">{{ escenario.nombre }}</h5>
          </button>
        </div>
      </section>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      escenarios: {
        type: Array,
        required: true,
      },
    },
    data() {
      return {
        selectedEscenario: null,
      };
    },
    mounted() {
      if (this.escenarios.length > 0) {
        this.selectedEscenario = this.escenarios[0];
        this.$emit('buttonClick', this.selectedEscenario);
      }
    },
    methods: {
      handleButtonClick(escenario) {
        this.selectedEscenario = escenario;
        this.$emit('buttonClick', escenario);
      },
    },
  };
  </script>
  
  <style scoped>
  .center {
    text-align: center;
  }
  
  .button-container {
    display: flex;
    justify-content: center; 
    overflow-x: auto;
    margin-bottom: -16px; 
  }
  
  @media (max-width: 640px) {
    .button-container {
      justify-content: space-between;
      overflow-x: scroll;
      flex-wrap: nowrap; 
    }
  
    .button-container button {
      padding-top: 1rem;
      padding-bottom: 1rem;
    }
  }
  </style>
  