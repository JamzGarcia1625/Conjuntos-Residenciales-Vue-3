<script setup>
import axios from "axios"
import { useI18n } from "vue-i18n"
import Swal from "sweetalert2"

const { t } = useI18n()
const isLoading = ref(false)
const tab = ref(1)
const lastUpdate = ref('')
const darkMode = ref(false) // Variable para controlar el modo claro y oscuro

// vars temporals
const send = ref(true)
const pending = ref(false)
const doesNotExist = ref(false)
const wrong = ref(false)
const bad = ref(false)

const axioss = axios.create({
  baseURL: "http://localhost:8080",
})

const toggleDarkMode = () => {
  darkMode.value = !darkMode.value
}

const setDarkMode = value => {
  darkMode.value = value
}

const dataRows = ref([]) // for manuals
const dataRowsBoletines = ref([]) 
const viewTable = ref(false)

const table = reactive({
  rows: computed(() => {
    return dataRows
  }),
})

const tableBoletines = reactive({
  rows: computed(() => {
    return dataRowsBoletines
  }),
})

// Número de items por página
const itemsPerPage = 20

// Página actual
const currentPage = ref(1)
const currentPageBoletines = ref(1)


// Calcular el número total de páginas
const totalPages = computed(() =>
  Math.ceil(dataRows.value.length / itemsPerPage),
)

// boletines
const totalPagesBoletines = computed(() =>
  Math.ceil(dataRowsBoletines.value.length / itemsPerPage))

// Calcular los items que se mostrarán en la página actual
const paginatedData = computed(() => {
  const startIndex = (currentPage.value - 1) * itemsPerPage
  const endIndex = startIndex + itemsPerPage

  return dataRows.value.slice(startIndex, endIndex)
})

// Calcular los items que se mostrarán en la tabla boletines
const paginatedDataBoletines = computed(() => {
  const startIndex = (currentPageBoletines.value - 1) * itemsPerPage
  const endIndex = startIndex + itemsPerPage

  return dataRowsBoletines.value.slice(startIndex, endIndex)
})

// Methods get data Endpoint for MESAS
const getDataRows = () => {
  console.log("se monto ...")

  lastUpdate.value = new Date().toLocaleTimeString() // Almacena la hora actual
  try {
    isLoading.value = true

    axioss
      .get("/api/informe/mesas")
      .then(response => {

        if (response.data.length > 0) {
          viewTable.value = true
        } else {
          Swal.fire({
            icon: "warning",
            text: "No se encontraron resultados",
            showConfirmButton: false,
            timer: 1500,
          })
          viewTable.value = false
        }

        // Set data to table
        dataRows.value = response.data
      })
      .catch(e => {
        console.log(e)
      })
  } catch (error) {
    isLoading.value = false
    if (error) {
      Swal.fire({ icon: "warning", text: "El recurso solicitado no existe" })
    }
  } finally {
    isLoading.value = false
  }

  // data para boletines

  try {
    isLoading.value = true

    axioss
      .get("/api/informe/boletines")
      .then(response => {

        if (response.data.length > 0) {
          viewTable.value = true
        } else {
          Swal.fire({
            icon: "warning",
            text: "No se encontraron resultados",
            showConfirmButton: false,
            timer: 1500,
          })
          viewTable.value = false
        }

        // Set data to table
        dataRowsBoletines.value = response.data
      })
      .catch(e => {
        console.log(e)
      })
  } catch (error) {
    isLoading.value = false
    if (error) {
      Swal.fire({ icon: "warning", text: "El recurso solicitado no existe" })
    }
  } finally {
    isLoading.value = false
  }
}

// FOR MESAS
const disabledAll = () => {
  console.log("dio clic en deshabilitar todo")
  Swal.fire({
    title: '¿Estás seguro?',
    text: '¿Deseas inhabilitar todos los departamentos?',
    icon: 'warning',
    showCancelButton: true,
    confirmButtonText: 'Sí, inhabilitar',
    cancelButtonText: 'Cancelar',
    customClass: {
      confirmButton: 'my-custom-confirm-button-class', 
      cancelButton: 'my-custom-cancel-button-class', 
    },
  }).then(result => {
    if (result.isConfirmed) {
      console.log("confirmo")

      // Si el usuario hace clic en "Sí, inhabilitar", realiza la solicitud Axios
      try {
        axios.post('URL_de_tu_solicitud', { /* datos para la solicitud */ })
          // eslint-disable-next-line promise/no-nesting
          .then(response => {
          // Maneja la respuesta exitosa aquí
            Swal.fire('Éxito', 'Los elementos han sido inhabilitados', 'success')
          })
          // eslint-disable-next-line promise/no-nesting
          .catch(error => {
          // Maneja errores de la solicitud Axios
            Swal.fire('Error', 'Hubo un problema al inhabilitar los elementos', 'error')
          })
      } catch (error) {
        console.log(error)
      }
    }else{
      console.log("No confirmo")
    }
  })

}

// FOR Boletines
const disabledAllBoletines = () => {
  console.log("dio clic en deshabilitar todo boletines")
  Swal.fire({
    title: '¿Estás seguro?',
    text: '¿Deseas inhabilitar todos los departamentos para los boletines?',
    icon: 'warning',
    showCancelButton: true,
    confirmButtonText: 'Sí, inhabilitar',
    cancelButtonText: 'Cancelar',
    customClass: {
      confirmButton: 'my-custom-confirm-button-class', 
      cancelButton: 'my-custom-cancel-button-class', 
    },
  }).then(result => {
    if (result.isConfirmed) {
      console.log("confirmo")

      // Si el usuario hace clic en "Sí, inhabilitar", realiza la solicitud Axios
      try {
        axios.post('URL_de_tu_solicitud', { /* datos para la solicitud */ })
          // eslint-disable-next-line promise/no-nesting
          .then(response => {
          // Maneja la respuesta exitosa aquí
            Swal.fire('Éxito', 'Los elementos han sido inhabilitados', 'success')
          })
          // eslint-disable-next-line promise/no-nesting
          .catch(error => {
          // Maneja errores de la solicitud Axios
            Swal.fire('Error', 'Hubo un problema al inhabilitar los elementos', 'error')
          })
      } catch (error) {
        console.log(error)
      }
    }else{
      console.log("No confirmo")
    }
  })

}

// for mesas
const toggleDepartment = async department => {
  const action = department.activo ? 'desactivar' : 'activar'

  Swal.fire({
    title: `¿Estás seguro de ${action} el departamento ${department.nombreDep}?`,
    icon: 'question',
    showCancelButton: true,
    confirmButtonText: 'Sí',
    cancelButtonText: 'No',
  }).then(result => {
    if (result.isConfirmed) {
      // Cambia el estado de activo/inactivo del departamento
      department.activo = !department.activo

      // Realiza aquí la lógica para activar o desactivar el departamento
      // Puedes utilizar Axios u otra forma de actualizar los datos en el servidor
    }
  })
}

// for boletines
const toggleDepartmentBoletines = async department => {
  const action = department.activo ? 'desactivar' : 'activar'

  Swal.fire({
    title: `¿Estás seguro de ${action} el departamento ${department.nombreDep}?`,
    icon: 'question',
    showCancelButton: true,
    confirmButtonText: 'Sí',
    cancelButtonText: 'No',
  }).then(result => {
    if (result.isConfirmed) {
      // Cambia el estado de activo/inactivo del departamento
      department.activo = !department.activo

      // Realiza aquí la lógica para activar o desactivar el departamento
      // Puedes utilizar Axios u otra forma de actualizar los datos en el servidor
    }
  })
}

const intervalId = setInterval(() => {
  getDataRows() // Llama a la función cada 5 segundos
}, 5000)




onMounted(() => {
  getDataRows()

  // Asegúrate de despejar el intervalo cuando el componente se desmonte
  onBeforeUnmount(() => {
    clearInterval(intervalId)
  })
})
</script>

<template>
  <!-- Agrega un botón para cambiar el modo -->
  <button 
    @click="toggleDarkMode" 
    :class="{ 'dark-mode': darkMode }"
  >
    Cambiar Modo
  </button>
  <div :class="{ 'dark-mode': darkMode }">
    <VRow 
      no-gutters 
      class="auth-wrapper"
    >
      <VCol 
        cols="12" 
        lg="12" 
        class="d-flex align-center justify-center"
      >
        <VCard 
          flat 
          class="mt-12 mt-sm-0 pa-4" 
          :class="{ 'dark-mode': darkMode }"
        > 
          <div>
            <h1 :class="{ 'dark-mode': darkMode }">
              Seguimiento Departamentos
            </h1>
            <VAlert
              :class="{ 'dark-mode': darkMode }"
            >
              La última actualización fue a las: {{ lastUpdate }}
            </VAlert>
          </div>  
          <VTabs v-model="tab">
            <VTab 
              :class="{ 'dark-mode': darkMode }"  
              value="one"
            > 
              MESAS
            </VTab>
            <VTab 
              :class="{ 'dark-mode': darkMode }"
              value="two"
            > 
              BOLETINES 
            </VTab>
          </VTabs>
          <VCardText>
            <VWindow v-model="tab">
              <VWindowItem value="one">
                <VRow>
                  <VCol cols="12">
                    <h3 
                      :class="{ 'dark-mode': darkMode }"
                    >
                      MESAS
                    </h3>
                    <div class="table-container mt-5">
                      <button
                        @click="disabledAll"
                      >
                        Inhabilitar Todos 
                      </button>
                      <table 
                        class="custom-table"
                        :class="{ 'dark-mode': darkMode }"
                      >
                        <thead>
                          <tr>
                            <th 
                              rowspan="2" 
                              class="info-header" 
                              style="background: #003057; color: white"
                            >
                              Departamento
                            </th>
                            <th 
                              colspan="4" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              TOTALES
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              ALCALDE
                            </th>
                            <th 
                              colspan="3" 
                              class="ciudadanos-header" 
                              style="background: #003057; color: white"
                            >
                              GOBERNADOR
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              CONCEJO
                            </th>
                            <th 
                              colspan="3" 
                              class="rechazados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              ASAMBLEA
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              JAL
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              ACCIONES
                            </th>
                          </tr>
                          <tr>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Malas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Estado Boletin Cero
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Habilitar / Inhabilitar
                            </th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr 
                            v-for="(row, index) in paginatedData" 
                            :key="index"
                          >
                            <td>{{ row.codigoDep }} - {{ row.nombreDep }}</td>
                            <td>{{ row.totalProcesadas }}</td>
                            <td>{{ row.totalErradas }}</td>
                            <td>{{ row.totalMalas }}</td>
                            <td>{{ row.totalPendientes }}</td>
                            <td>{{ row.infoCarpetas.ALCALDE.procesadas }}</td>
                            <td>{{ row.infoCarpetas.ALCALDE.erradas }}</td>
                            <td>{{ row.infoCarpetas.ALCALDE.pendientes }}</td>
                            <td>{{ row.infoCarpetas.GOBERNADOR.procesadas }}</td>
                            <td>{{ row.infoCarpetas.GOBERNADOR.erradas }}</td>
                            <td>{{ row.infoCarpetas.GOBERNADOR.pendientes }}</td>
                            <td>{{ row.infoCarpetas.CONCEJO.procesadas }}</td>
                            <td>{{ row.infoCarpetas.CONCEJO.erradas }}</td>
                            <td>{{ row.infoCarpetas.CONCEJO.pendientes }}</td>
                            <td>{{ row.infoCarpetas.ASAMBLEA.procesadas }}</td>
                            <td>{{ row.infoCarpetas.ASAMBLEA.erradas }}</td>
                            <td>{{ row.infoCarpetas.ASAMBLEA.pendientes }}</td>
                            <td>{{ row.infoCarpetas.JAL.procesadas }}</td>
                            <td>{{ row.infoCarpetas.JAL.erradas }}</td>
                            <td>{{ row.infoCarpetas.JAL.pendientes }}</td> 
                            <td>
                              <div
                                v-if="send"
                                class="var1"
                              >
                                Enviado
                              </div>
                              <div
                                v-if="pending"
                                class="var2"
                              >
                                Pendiente
                              </div>
                              <div 
                                v-if="doesNotExist"
                                class="var3"
                              >
                                No Existe
                              </div>
                              <div 
                                v-if="wrong"
                                class="var3"
                              >
                                Errado
                              </div>
                              <div 
                                v-if="bad"
                                class="var3"
                              >
                                Malo
                              </div>
                            </td>    
                            <td>
                              <button @click="toggleDepartment(row)">
                                {{ row.activo ? 'Desactivar' : 'Activar' }}
                              </button>
                            </td>    
                          </tr>
                        </tbody>
                      </table>

                      <!-- Pagination -->
                      <div class="pagination mt-5 d-flex justify-center">
                        <VPagination 
                          v-model="currentPage" 
                          :length="totalPages" 
                          :total-visible="5" 
                        />
                      </div>

                      <!-- Show data count information -->
                      <div class="data-count-info mt-5 d-flex justify-center">
                        Mostrando {{ paginatedData.length }} de
                        {{ dataRows.length }} registros.
                      </div>
                    </div>
                  </VCol>
                </VRow>
              </VWindowItem>
              <VWindowItem value="two">
                <VRow>
                  <VCol cols="12">
                    <h3 
                      :class="{ 'dark-mode': darkMode }"
                    >
                      BOLETINES
                    </h3>
                    <div class="table-container mt-5">
                      <button
                        @click="disabledAllBoletines"
                      >
                        Inhabilitar Todos 
                      </button>
                      <table 
                        class="custom-table"
                        :class="{ 'dark-mode': darkMode }"
                      >
                        <thead>
                          <tr>
                            <th 
                              rowspan="2" 
                              class="info-header" 
                              style="background: #003057; color: white"
                            >
                              Departamento
                            </th>
                            <th 
                              colspan="4" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              TOTALES
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              ALCALDE
                            </th>
                            <th 
                              colspan="3" 
                              class="ciudadanos-header" 
                              style="background: #003057; color: white"
                            >
                              GOBERNADOR
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              CONCEJO
                            </th>
                            <th 
                              colspan="3" 
                              class="rechazados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              ASAMBLEA
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              JAL
                            </th>
                            <th 
                              colspan="3" 
                              class="jurados-sub-header" 
                              style="background: #003057; color: white"
                            >
                              ACCIONES
                            </th>
                          </tr>
                          <tr>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Malas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Procesadas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Erradas
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Pendientes
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Estado Boletin Cero
                            </th>
                            <th 
                              class="sub-header" 
                              style="background: #003057; color: white"
                            >
                              Habilitar / Inhabilitar
                            </th>
                          </tr>
                        </thead>
                        <tbody>
                          <tr 
                            v-for="(row, index) in paginatedDataBoletines" 
                            :key="index"
                          >
                            <td>{{ row.codigoDep }} - {{ row.nombreDep }}</td>
                            <td>{{ row.totalProcesadas }}</td>
                            <td>{{ row.totalErradas }}</td>
                            <td>{{ row.totalMalas }}</td>
                            <td>{{ row.totalPendientes }}</td>
                            <td>{{ row.infoCarpetas.ALCALDE.procesadas }}</td>
                            <td>{{ row.infoCarpetas.ALCALDE.erradas }}</td>
                            <td>{{ row.infoCarpetas.ALCALDE.pendientes }}</td>
                            <td>{{ row.infoCarpetas.GOBERNADOR.procesadas }}</td>
                            <td>{{ row.infoCarpetas.GOBERNADOR.erradas }}</td>
                            <td>{{ row.infoCarpetas.GOBERNADOR.pendientes }}</td>
                            <td>{{ row.infoCarpetas.CONCEJO.procesadas }}</td>
                            <td>{{ row.infoCarpetas.CONCEJO.erradas }}</td>
                            <td>{{ row.infoCarpetas.CONCEJO.pendientes }}</td>
                            <td>{{ row.infoCarpetas.ASAMBLEA.procesadas }}</td>
                            <td>{{ row.infoCarpetas.ASAMBLEA.erradas }}</td>
                            <td>{{ row.infoCarpetas.ASAMBLEA.pendientes }}</td>
                            <td>{{ row.infoCarpetas.JAL.procesadas }}</td>
                            <td>{{ row.infoCarpetas.JAL.erradas }}</td>
                            <td>{{ row.infoCarpetas.JAL.pendientes }}</td> 
                            <td>
                              <div
                                v-if="send"
                                class="var1"
                              >
                                Enviado
                              </div>
                              <div
                                v-if="pending"
                                class="var2"
                              >
                                Pendiente
                              </div>
                              <div 
                                v-if="doesNotExist"
                                class="var3"
                              >
                                No Existe
                              </div>
                              <div 
                                v-if="wrong"
                                class="var3"
                              >
                                Errado
                              </div>
                              <div 
                                v-if="bad"
                                class="var3"
                              >
                                Malo
                              </div>
                            </td>    
                            <td>
                              <button @click="toggleDepartmentBoletines(row)">
                                {{ row.activo ? 'Desactivar' : 'Activar' }}
                              </button>
                            </td>    
                          </tr>
                        </tbody>
                      </table>

                      <!-- Pagination -->
                      <div class="pagination mt-5 d-flex justify-center">
                        <VPagination 
                          v-model="currentPageBoletines" 
                          :length="totalPagesBoletines" 
                          :total-visible="5" 
                        />
                      </div>

                      <!-- Show data count information -->
                      <div class="data-count-info mt-5 d-flex justify-center">
                        Mostrando {{ paginatedDataBoletines.length }} de
                        {{ dataRowsBoletines.length }} registros.
                      </div>
                    </div>
                  </VCol>
                </VRow>
              </VWindowItem>
            </VWindow>
          </VCardText>
        </VCard>
      </VCol>
    </VRow>
  </div>
</template>

<style lang="scss">
@use "@core/scss/template/pages/page-auth.scss";

.table-container {
  max-inline-size: 100%;
  overflow-x: auto;
}

.custom-table {
  border: 1px solid #ccc;
  border-collapse: collapse;
  font-size: 14px;
  inline-size: 100%;
  text-align: center;
}

.custom-table thead tr th {
  padding: 8px;
  border: 1px solid #ccc;
  background-color: #f1f1f1;
}

.custom-table tbody tr td {
  padding: 8px;
  border: 1px solid #ccc;
}

.ciudadanos-header {
  background-color: #f1f1f1;
}

.rechazados-sub-header,
.jurados-sub-header {
  background-color: #e0e0e0;
}

/* Estilo para el botón */
  button {
    background: #003057;
    color: white;
    padding: 10px 20px; /* Ajusta el padding según tus preferencias */
    border: none;
    border-radius: 5px; /* Añade bordes redondeados al botón */
    cursor: pointer;
  }
  
  /* Estilo para el botón al pasar el ratón por encima */
  button:hover {
    background: #002040; /* Cambia el color al pasar el ratón por encima */
  }

  /* Archivo CSS personalizado o dentro del componente Vue */
.swal2-popup .swal2-confirm {
  background: #003057;
  color: white;
}

/* Opcional: Cambiar el color del botón de cancelar */
.swal2-popup .swal2-cancel {
  background: #ccc; /* Cambia el color a tu elección */
  color: #333; /* Cambia el color a tu elección */
}

.var1{
  background-color: green;
  color: #e0e0e0;
}
.var2{
  background-color: yellow;
  color: #000;
}
.var3{
  background-color: red;
  color: #e0e0e0;
}

/* Estilo para el modo oscuro */
.dark-mode {
  background-color: #003057; /* Cambia el color de fondo en modo oscuro */
  color: #fff; /* Cambia el color del texto en modo oscuro */
}

/* Estilo para el botón en modo oscuro */
.dark-mode button {
  background: white;
  color: #003057;
  /* Otros estilos en modo oscuro... */
}
</style>

<route lang="yaml">
meta:
  layout: blank
  action: read
  subject: Auth
  redirectIfLoggedIn: true
</route>
