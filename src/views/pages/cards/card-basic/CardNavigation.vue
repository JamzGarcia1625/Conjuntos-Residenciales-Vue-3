<script setup>
import axios from 'axios'
import { useI18n } from "vue-i18n"
import TableLite from 'vue3-table-lite'
import Swal from "sweetalert2"

const { t } = useI18n()
const isLoading = ref(false)
const tab = ref(null)

const axio = axios.create({
  baseURL: 'http://localhost:8080', 
})

//table MESAS components

const  labels = {
  pagingInfo: "Mostrando {0}-{1} de {2}",
  pageSizeChangeLabel: "Mostrar :",
  gotoPageLabel: "Por Página, Actual:",
  noDataAvailable: "No hay información para mostrar",
}

const table = reactive({
  columns: [
    {
      label: 'Código Departamento',
      field: "codigoDep",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Departamento',
      field: "nombreDep" ,
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Procesadas',
      field: "procesadas",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Erradas',
      field: "erradas",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Malas',
      field: "malas",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Pendientes',
      field: "pendientes",
      sortable: true,
      isKey: true,
      width: "10%",
    },
  ],
  rows:[],
  filters:[],
  isLoading: false,
  sortable: {
    order: "row",
    sort: "asc",
  },
})

// Table Boletines 

const tableBoletines = reactive({
  columns: [
    {
      label: 'Código Departamento',
      field: "codigoDep",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Departamento',
      field: "nombreDep" ,
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Procesadas',
      field: "procesadas",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Erradas',
      field: "erradas",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Malas',
      field: "malas",
      sortable: true,
      isKey: true,
      width: "10%",
    },
    {
      label: 'Pendientes',
      field: "pendientes",
      sortable: true,
      isKey: true,
      width: "10%",
    },
  ],
  rows:[],
  filters:[],
  isLoading: false,
  sortable: {
    order: "row",
    sort: "asc",
  },
})

// Methods get data Endpoint for MESAS
const getDataRows = async () => {
   
  try {
    

    isLoading.value = true

    axio.get('/api/informe/mesas')
      .then(response => { 

        table.rows = response.data
        table.filters = table.rows

      })
      .catch(error => {
        console.log(error)
      })
    

  } catch (error) {
    isLoading.value = false
    if(error){
      await Swal.fire({ icon: 'warning', text: 'El recurso solicitado no existe'  })
    }
  }finally {
    isLoading.value = false
  }

  try {
    

    isLoading.value = true

    axio.get('/api/informe/boletines')
      .then(response => { 

        tableBoletines.rows = response.data
        tableBoletines.filters = tableBoletines.rows

      })
      .catch(error => {
        console.log(error)
      })
    

  } catch (error) {
    isLoading.value = false
    if(error){
      await Swal.fire({ icon: 'warning', text: 'El recurso solicitado no existe'  })
    }
  }finally {
    isLoading.value = false
  }

  
}

onMounted(() => {
  getDataRows()
})
</script>

<template>
  <VCard
    class="mt-4"
  >
    <VTabs
      v-model="tab"
    >
      <VTab
        value="one"
      >
        MESAS
      </VTab>
      <VTab value="two">
        BOLETINES
      </VTab>
    </VTabs>

    <VCardText>
      <VWindow v-model="tab">
        <VWindowItem value="one">
          <VRow>
            <VCol  
              cols="12" 
              md="12"
            >
              <h5>MESAS</h5>
              <VRow>
                <VCol 
                  cols="12"
                  md="12"
                  class="mt-4"
                >
                  <VCol cols="12">
                    <TableLite
                      is-slot-mode
                      :is-loading="table.isLoading"
                      :columns="table.columns"
                      :rows="table.filters"
                      :total="table.filters.length"
                      :sortable="table.sortable"
                      is-static-mode
                      class="scroll-top"
                      :messages="labels"
                      @is-finished="table.isLoading = false"
                    >
                      <template #procesadas="{ value }">
                        <span> {{ value.infoCarpetas.procesadas }} </span>
                      </template>
                      <template #erradas="{ value }">
                        <span> {{ value.infoCarpetas.erradas }} </span>
                      </template>
                      <template #malas="{ value }">
                        <span> {{ value.infoCarpetas.malas }} </span>
                      </template>
                      <template #pendientes="{ value }">
                        <span> {{ value.infoCarpetas.pendientes }} </span>
                      </template>
                    </TableLite>
                  </VCol>
                </VCol>
              </VRow>
            </VCol>
          </VRow>
        </VWindowItem>
      </VWindow>
    </VCardText>
    <VCardText>
      <VWindow v-model="tab">
        <VWindowItem value="two">
          <VRow>
            <VCol  
              cols="12" 
              md="12"
            >
              <h5>BOLETINES</h5>
              <VRow>
                <VCol 
                  cols="12"
                  md="12"
                  class="mt-4"
                >
                  <VCol cols="12">
                    <TableLite
                      is-slot-mode
                      :is-loading="tableBoletines.isLoading"
                      :columns="tableBoletines.columns"
                      :rows="tableBoletines.filters"
                      :total="tableBoletines.filters.length"
                      :sortable="tableBoletines.sortable"
                      is-static-mode
                      class="scroll-top"
                      :messages="labels"
                      @is-finished="tableBoletines.isLoading = false"
                    >
                      <template #procesadas="{ value }">
                        <span> {{ value.infoCarpetas.procesadas }} </span>
                      </template>
                      <template #erradas="{ value }">
                        <span> {{ value.infoCarpetas.erradas }} </span>
                      </template>
                      <template #malas="{ value }">
                        <span> {{ value.infoCarpetas.malas }} </span>
                      </template>
                      <template #pendientes="{ value }">
                        <span> {{ value.infoCarpetas.pendientes }} </span>
                      </template>
                    </TableLite>
                  </VCol>
                </VCol>
              </VRow>
            </VCol>
          </VRow>
        </VWindowItem>
      </VWindow>
    </VCardText>
  </VCard>
</template>

<style scoped>
.v-progress-linear {
  border-radius: 10px;
}

.container {
  padding-block: 0;
  padding-inline: 15px;
}

.subtitle {
  color: #003057;
  font-size: 12px;
  font-weight: 800;
}

.col-loading {
  margin: auto;
}

.label-center {
  text-align: center;
}

.text-subtitle_1 {
  font-size: 20px;
  font-weight: 800;
}

.text-subtitle_2 {
  font-size: 18px;
}

.label-subtitle {
  text-align: center;
}

.v-btn .v-btn__content {
  font-size: 10rem !important;
}

.center {
  text-align: center;
}

.buttons {
  text-align: center;
}
</style>
