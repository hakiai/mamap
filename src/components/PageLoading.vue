<template>
  <div class="page_wrapper">
    <IonSpinner
      color="primary"
    ></IonSpinner>
  </div>
</template>
<script lang="ts">
import { defineComponent } from "vue";
import { IonSpinner } from "@ionic/vue";

export default defineComponent({
  name: "PageLoading",
  components: {
    IonSpinner,
  }
});
</script>
<style scoped>
ion-spinner {
  width: 5rem;
  height: 5rem;
}
.page_wrapper {
  width: 100%;
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
