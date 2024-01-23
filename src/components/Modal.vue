<!-- Modal.vue -->
<template>
    <div id="default-modal" tabindex="-1" aria-hidden="true" class="hidden fixed top-0 right-0 bottom-0 left-0 flex items-center justify-center z-50 bg-black bg-opacity-30">
      <div class="relative p-4 w-full max-w-2xl max-h-full">
        <!-- Modal content -->
        <div class="relative bg-white rounded-lg shadow dark:bg-gray-700">
          <!-- Modal header -->
          <div class="flex items-center justify-between p-4 md:p-5 border-b rounded-t dark:border-gray-600">
            <span class="text-xl font-semibold text-gray-900 dark:text-white">
              Advertencia de Proximidad
            </span>
            <button type="button" @click="hideModal"
              class="text-gray-400 bg-transparent hover:bg-gray-200 hover:text-gray-900 rounded-lg text-sm w-8 h-8 ms-auto inline-flex justify-center items-center dark:hover:bg-gray-600 dark:hover:text-white"
              data-modal-hide="default-modal">
              <svg class="w-3 h-3" aria-hidden="true" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 14 14">
                <path stroke="currentColor" stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
                  d="m1 1 6 6m0 0 6 6M7 7l6-6M7 7l-6 6" />
              </svg>
              <span class="sr-only">Cerrar modal</span>
            </button>
          </div>
          <!-- Modal body -->
          <div class="p-4 md:p-5 space-y-4" id="modalMensaje">
            <p style="line-height: 28px;">{{ mensaje }}</p>
          </div>
          <!-- Modal footer -->
          <div class="flex items-center p-4 md:p-5 border-t border-gray-200 rounded-b dark:border-gray-600">
            <button id="aceptarButton" type="button" @click="aceptarModal"
              class="text-white bg-green-400 hover:bg-blue-800 focus:ring-4 focus:outline-none focus:ring-blue-300 font-medium rounded-lg text-sm px-5 py-2.5 text-center dark:bg-blue-600 dark:hover:bg-blue-700 dark:focus:ring-blue-800">
              Si, agregar
            </button>
            <button id="declinarButton" type="button" @click="declinarModal"
              class="ms-3 text-gray-500 bg-white hover:bg-gray-100 focus:ring-4 focus:outline-none focus:ring-blue-300 rounded-lg border border-gray-200 text-sm font-medium px-5 py-2.5 hover:text-gray-900 focus:z-10 dark:bg-gray-700 dark:text-gray-300 dark:border-gray-500 dark:hover:text-white dark:hover:bg-gray-600 dark:focus:ring-gray-600">
              No agregar
            </button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      mensaje: String,
    },
    methods: {
      hideModal() {
        // Lógica para ocultar el modal (puedes agregar más lógica según tus necesidades)
        const modal = document.getElementById("default-modal");
        modal.classList.add("hidden");
      },
      aceptarModal() {
        this.$emit('aceptar'); // Emitir un evento llamado 'aceptar'
      },
      declinarModal() {
        this.$emit('declinar'); // Emitir un evento llamado 'declinar'
      },
    },
  };
  </script>
  
  <style scoped>
  /* ... (código CSS del modal) ... */
  </style>
  