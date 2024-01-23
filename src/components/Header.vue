<template>
  <div>
    <nav v-if="isMobile" class="p-4 bg-[#1D172A] md:hidden">
      <div class="text-white text-center">
        <img src="../assets/img/logo-mobile.svg" alt="Logo Horarios del rock" class="mx-auto" width="150" />
      </div>
    </nav>

    <section class="flex md:h-screen lg:h-screen mb-2 lg:mb-10">
      <div class="flex-1 p-6 flex flex-col items-start lg:justify-center">
        <nav v-if="!isMobile" class="absolute top-0 left-0 right-0 p-5">
          <img src="../assets/img/logo.svg" alt="Logo Horarios del rock" width="200" />
        </nav>

        <h1 class="text-4xl md:text-4xl xl:text-7xl font-bold text-center md:text-left mb-4 text-[#1D172A] text-pretty">
          <span class="block text-2xl md:text-6xl lg:text-5xl font-semibold mb-2 text-[#F2002E]">Cosquin a tu
            Manera</span>Personaliza tu <br class="hidden lg:block" />Grilla Musical!
        </h1>

        <p class="text-lg lg:text-2xl w-full lg:w-4/5 text-center md:text-left mb-4 text-gray-700 text-pretty text-">
          Diseña tu grilla ideal para el Cosquin Rock: elige tus bandas y
          horarios, guardalas en PDF. Tu experiencia personalizada a un clic!
        </p>
      </div>

      <div class="flex-1 hidden md:block">
        <img src="../assets/img/banner.jpg" alt="Foto grupal en Cosquin Rock" class="w-full h-full object-cover"
          width="100" height="100" />
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      isMobile: window.innerWidth <= 640,
    };
  },
  mounted() {
    window.addEventListener("resize", this.handleResize);
  },
  destroyed() {
    window.removeEventListener("resize", this.handleResize);
  },
  methods: {
    handleResize() {
      this.isMobile = window.innerWidth <= 640;
    },
  },
};
</script>

<style scoped></style>
