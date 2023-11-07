<script>
import { usePDF, VuePDF } from "@tato30/vue-pdf";

export default {

  components: {
    VuePDF,
  },

  /* ✓ GOOD */
  setup() {
    const { pdf } = usePDF("https://raw.githubusercontent.com/mozilla/pdf.js/ba2edeae/web/compressed.tracemonkey-pldi-09.pdf")
    
    return {
      pdf,
    }
  },
}
</script>

<template>
  <VCard>
    <div class="text-center">
      <VuePDF :pdf="pdf" />
    </div>
  </VCard>
</template>

