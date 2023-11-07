<script setup>
import axios from "axios"
import { useI18n } from "vue-i18n"
import TableLite from 'vue3-table-lite'
import Swal from "sweetalert2"

const { t } = useI18n()
const isLoading = ref(false)
const dataRowsManuals = ref([])

 

// tabs
const tab = ref(null)

//table components

const  labels = {
  pagingInfo: "Mostrando {0}-{1} de {2}",
  pageSizeChangeLabel: "Mostrar :",
  gotoPageLabel: "Por Página, Actual:",
  noDataAvailable: "No hay información para mostrar",
}

const table = reactive({
  isLoading: false,
    
  columns: [
    {
      label: 'Nombre Manual',
      field: "docName" ,
      sortable: true,
      isKey: true,
      width: "15%",

    },
    {
      label: 'Acciones',
      field: "Actions",
      sortable: true,
      isKey: true,
      width: "10%",

    },
  ],
  rows: computed(() => {
    return dataRowsManuals
  }),
        
  totalRecordCount: computed(() => {
    return table.rows.length
  }),

  sortable: {
    order: "row",
    sort: "asc",
  },
})

// Methods

// get data Endpoint for Manuals
const getDataRows = async () => {
   
  try {
    // get route PDF
    const response = await axios.get (`localhost:8080/api/informe`)

    console.log("response", response.data)

    isLoading.value = true

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
                      :rows="dataRowsManuals"
                      :total="dataRowsManuals.length"
                      :sortable="table.sortable"
                      is-static-mode
                      class="scroll-top"
                      :messages="labels"
                      @is-finished="table.isLoading = false"
                    />
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
