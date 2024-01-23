<template>
  <div v-if="loading" class="loading-screen">
    <img
      src="./assets/img/loader.gif"
      alt="Loading Logo"
      class="center-image"
      width="200"
      height="100"
    />
  </div>

  <div
    v-else
    class="allPage"
    style="background-color: rgba(160, 232, 245, 0.098)"
  >
    <Header />

    <body>
      <div
        v-for="(day, index) in eventData.evento"
        :key="index"
        class="center mt-10 mb-10"
        :id="'day-section-' + day.dia"
      >
        <section class="day-wrapper mt-14 mb-5">
          <h2 class="day-number text-5xl md:text-xl lg:text-5xl mb-6">
            Dia {{ day.dia }}
          </h2>
        </section>

        <Schedules
          :escenarios="day.escenarios"
          @buttonClick="(escenario) => handleButtonClick(escenario, day.dia)"
        />

        <div v-if="stageSelected[day.dia]" class="container mx-auto p-2">
          <span
            class="flex text-right whitespace-nowrap justify-end p-3 mb-5 text-gray-600"
            ><span class="text-red-600 mr-1">*</span>Haz clic en la banda para
            seleccionar/deseleccionar!</span
          >
          <ul class="text-lg">
            <div
              class="relative overflow-x-auto shadow-md sm:rounded-lg rounded-lg lg:shadow-2xl"
            >
              <table
                class="w-full text-sm text-left rtl:text-right text-gray-500"
              >
                <thead class="text-lg text-white uppercase bg-[#F2002E]">
                  <tr>
                    <th scope="col" class="px-6 py-3">
                      <h5>Nobre de la banda</h5>
                    </th>
                    <th scope="col" class="px-6 py-3">
                      <h5>Horario</h5>
                    </th>
                  </tr>
                </thead>
                <tbody>
                  <tr
                    v-for="band in stageSelected[day.dia].bandas"
                    :key="band.id"
                    :class="{ 'selected-band': isSelectedBand(day.dia, band) }"
                    @click="handleBandClick(day.dia, band)"
                    class="bg-white border-b hover:bg-gray-50 cursor-pointer"
                  >
                    <th
                      scope="row"
                      class="px-6 py-4 font-medium whitespace-nowrap"
                    >
                      {{ band.nombre }}
                    </th>
                    <td class="px-6 py-4">
                      {{ band.horario }}
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </ul>
          <div
            class="flex justify-center lg:justify-end gap-5 flex-col lg:flex-row"
          >
            <button
              @click="downloadPDF(day.dia)"
              :disabled="disableDownloadButton(day.dia)"
              :class="{ 'disabled-button': disableDownloadButton(day.dia) }"
              class="mt-4 bg-[#21b4d2] text-white font-bold py-4 px-4 rounded"
            >
              Descargar PDF
            </button>

            <button
              @click="cleanLocalStorage(day.dia)"
              :class="{ 'disabled-button': disableDownloadButton(day.dia) }"
              class="mt-0 lg:mt-4 bg-red-500 text-white font-bold py-4 px-4 rounded"
            >
              Limpiar dia completo
            </button>
          </div>
        </div>
      </div>

      <PDFDocument
        :selectedBands="selectedBands"
        :agruparPorEscenario="agruparPorEscenario"
      />
      <Modal
        :mensaje="modalMessage"
        @aceptar="acceptModal"
        @declinar="declineModal"
      />
      <Footer />
    </body>
  </div>
</template>

<script>
import DataJson from "../src/data/line-up.json";
import Header from "./components/Header.vue";
import Schedules from "./components/Schedules.vue";
import PDFDocument from "./components/PDFDocument.vue";
import Modal from "./components/Modal.vue";
import Footer from "./components/Footer.vue";
import html2pdf from "html2pdf.js";

export default {
  components: {
    Header,
    Schedules,
    Footer,
    Modal,
    PDFDocument,
  },
  data() {
    return {
      eventData: DataJson,
      stageSelected: {},
      selectedBands: {},
      loading: true,
      modalMessage: "",
    };
  },
  mounted() {
    const storedData = localStorage.getItem("selectedBands");

    if (storedData) {
      this.selectedBands = JSON.parse(storedData);
    }

    setTimeout(() => {
      this.loading = false;
    }, 1500);
  },
  computed: {
    disableDownloadButton() {
      return (day) => {
        return !this.selectedBands[day] || this.selectedBands[day].length === 0;
      };
    },
  },

  methods: {
    /**
     * Descarga un archivo PDF que contiene el contenido de la sección correspondiente al día especificado.
     *
     * @param {string} day - El día para el cual se generará el archivo PDF.
     */
    downloadPDF(day) {
      const sectionId = `${day}Day`;
      const element = document.getElementById(sectionId);

      if (!element) {
        return;
      }

      const jsPDFConfig = {
        unit: "mm",
        format: "a4",
        orientation: "portrait",
        compress: true,
        precision: 16,
        render: "quality",
      };

      const options = {
        margin: 5,
        filename: `Dia_${day}_Horarios_Del_Rock.pdf`,
        image: { type: "jpeg", quality: 1 },
        html2canvas: { scale: 1, letterRendering: true, logging: true },
        jsPDF: jsPDFConfig,
      };

      html2pdf(element, options);
    },

    /**
     * Limpia el almacenamiento local eliminando la información relacionada con las bandas seleccionadas para un día específico.
     *
     * @param {string} day - El día para el cual se limpiará el almacenamiento local.
     */
    cleanLocalStorage(day) {
      localStorage.removeItem(`selectedBands_${day}`);

      this.selectedBands[day] = [];

      this.saveInLocalStorage();
    },

    /**
     * Guarda la información de las bandas seleccionadas en el almacenamiento local.
     *
     */
    saveInLocalStorage() {
      localStorage.setItem("selectedBands", JSON.stringify(this.selectedBands));
    },

    /**
     * Compara si dos horarios pertenecen al mismo día (misma hora).
     *
     * @param {string} horario1 - El primer horario en formato HH:MM.
     * @param {string} horario2 - El segundo horario en formato HH:MM.
     * @returns {boolean} True si ambos horarios pertenecen al mismo día (misma hora), de lo contrario, false.
     */
    isSameDay(schedule_1, schedule_2) {
      return schedule_1.split(":")[0] === schedule_2.split(":")[0];
    },

    /**
     * Calcula la diferencia en minutos entre dos horarios.
     *
     * @param {string} horario1 - El primer horario en formato HH:MM.
     * @param {string} horario2 - El segundo horario en formato HH:MM.
     * @returns {number} La diferencia en minutos entre los dos horarios.
     */
    calculateDifferenceMinutes(schedule_1, schedule_2) {
      const time_1 = this.convertScheduleToMinutes(schedule_1);
      const time_2 = this.convertScheduleToMinutes(schedule_2);

      return time_1 - time_2;
    },

    /**
     * Convierte un horario en formato HH:MM a minutos.
     *
     * @param {string} horario - El horario en formato HH:MM.
     * @returns {number} El tiempo convertido a minutos.
     */
    convertScheduleToMinutes(schedule) {
      const parts = schedule.split(":");
      const hours = parseInt(parts[0]);
      const minutes = parseInt(parts[1]);

      return hours * 60 + minutes;
    },

    /**
     * Maneja el clic en un botón de escenario, almacenando la información de la etapa seleccionada.
     *
     * @param {object} stage - Información de la etapa seleccionada.
     * @param {string} day - El día correspondiente a la etapa seleccionada.
     * @returns {void}
     */
    handleButtonClick(stage, day) {
      this.stageSelected[day] = {
        nombre: stage.nombre,
        bandas: stage.bandas.map((band) => ({
          nombre: band.nombre,
          horario: band.horario,
          escenario: stage.nombre,
        })),
      };
    },

    /**
     * Maneja el clic en una banda, seleccionándola o deseleccionándola y validando la diferencia de horario.
     *
     * @param {string} day - El día correspondiente a la banda.
     * @param {object} band - La información de la banda que se está manipulando.
     * @returns {void}
     */
    handleBandClick(day, band) {
      if (!this.selectedBands[day]) {
        this.selectedBands[day] = [];
      }

      const stageInfo = this.stageSelected[day];
      const bandInfo = stageInfo.bandas.find((b) => b.nombre === band.nombre);

      const index = this.selectedBands[day].findIndex(
        (b) => b.nombre === band.nombre
      );

      if (index !== -1) {
        this.selectedBands[day].splice(index, 1);
      } else {
        this.selectedBands[day].push(bandInfo);
      }

      this.validarDiferenciaHorario(day, band);
    },

    /**
     * Verifica si una banda específica está seleccionada para un día dado.
     *
     * @param {string} day - El día para el cual se desea verificar la selección de la banda.
     * @param {object} band - La información de la banda que se está verificando.
     * @returns {boolean} True si la banda está seleccionada, de lo contrario, false.
     */
    isSelectedBand(day, band) {
      return (
        this.selectedBands[day] &&
        this.selectedBands[day].some((b) => b.nombre === band.nombre)
      );
    },

    /**
     * Muestra un modal con un mensaje específico.
     *
     * @param {string} message - El mensaje que se mostrará en el modal.
     * @returns {void}
     */
    showModal(message) {
      this.modalMessage = message;

      const modal = document.getElementById("default-modal");
      modal.classList.remove("hidden");
    },

    /**
     * Acción realizada al aceptar el modal. Guarda la información en el almacenamiento local y oculta el modal.
     *
     * @returns {void}
     */
    acceptModal() {
      this.saveInLocalStorage();

      const modal = document.getElementById("default-modal");
      modal.classList.add("hidden");
    },

    /**
     * Acción realizada al declinar el modal. Restaura el estado a la última vez que se guardó en localStorage y oculta el modal.
     *
     * @returns {void}
     */
    declineModal() {
      const storedData = localStorage.getItem("selectedBands");
      if (storedData) {
        this.selectedBands = JSON.parse(storedData);
      }

      const modal = document.getElementById("default-modal");
      modal.classList.add("hidden");
    },

    agruparPorEscenario(bands) {
      const escenarios = {};

      // Iterar sobre las bandas y agrupar por escenario
      bands.forEach((band) => {
        const escenarioKey = band.escenario;
        if (!escenarios[escenarioKey]) {
          escenarios[escenarioKey] = [];
        }
        escenarios[escenarioKey].push(band);
      });

      return escenarios;
    },

    validarDiferenciaHorario(dia, band) {
      const bandasSeleccionadas = this.selectedBands[dia];

      if (bandasSeleccionadas && bandasSeleccionadas.length >= 2) {
        const nuevaBanda = bandasSeleccionadas.find(
          (b) => b.nombre === band.nombre
        );
        if (nuevaBanda) {
          const otrasBandas = bandasSeleccionadas.filter(
            (b) => b.nombre !== band.nombre
          );

          for (const otraBanda of otrasBandas) {
            if (this.isSameDay(nuevaBanda.horario, otraBanda.horario)) {
              const diferenciaMinutos = this.calculateDifferenceMinutes(
                otraBanda.horario,
                nuevaBanda.horario
              );

              if (
                Math.abs(diferenciaMinutos) <= 30 ||
                diferenciaMinutos === 0
              ) {
                const diferenciaMinutos = Math.abs(
                  this.calculateDifferenceMinutes(
                    otraBanda.horario,
                    nuevaBanda.horario
                  )
                );

                let message 
                if(diferenciaMinutos === 0){
                  message = `${nuevaBanda.nombre} (${nuevaBanda.escenario}) a las ${nuevaBanda.horario} está al horario que  ${otraBanda.nombre} (${otraBanda.escenario}) a las ${otraBanda.horario}. Puedes agregar ambas bandas si lo deseas.`;
                }else{
                  message = `${nuevaBanda.nombre} (${nuevaBanda.escenario}) a las ${nuevaBanda.horario} está a solo ${diferenciaMinutos} minutos de ${otraBanda.nombre} (${otraBanda.escenario}) a las ${otraBanda.horario}. Puedes agregar ambas bandas si lo deseas.`;
                }
                this.modalMessage = message;
                this.showModal(message);
                return;
              }
            }
          }
        }
      }
      this.saveInLocalStorage();
    },
  },
};
</script>

<style scoped>
.selected-band {
  color: white;
  font-weight: bold;
  border-bottom: 1px solid #ffffffd7;
  background-color: #21b4d2;
  cursor: pointer;
  transition: 0.2s;
}

.disabled-button {
  background-color: #ccc;
  cursor: not-allowed;
}

.loading-screen {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: #fff;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
  opacity: 1;
}

.center-image {
  max-width: 100%;
  max-height: 100%;
}

.allPage {
  animation: fadeInAnimation ease 1s;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

@keyframes fadeInAnimation {
  0% {
    opacity: 0;
  }

  100% {
    opacity: 1;
  }
}
</style>
