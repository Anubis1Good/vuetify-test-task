<template>
  <v-container>
    <v-card class="media">
      <v-card-title class="pb-3 pl-11">
        <span class="text-h5 mb-0"> Ф.И.О.: {{ worker.name + ' ' + worker.surname + ' ' + worker.patronymic }}</span>
      </v-card-title>
      <v-container class="pt-0 ">
        <v-row class="ma-5 mt-0">
          <v-col cols="12" sm="12" md="12" xs="12">
            <span><b>Должность:</b> {{ worker.jobTitle }}</span>
          <v-divider class="mx-4" inset></v-divider>
          </v-col>
          <v-col cols="12" sm="12" md="12" xs="12">
            <span><b>Трудовая книжка:</b> {{ workbook }}</span>
          <v-divider class="mx-4" inset></v-divider>
          </v-col>
          <v-col cols="12" sm="12" md="12" xs="12">
            <span><b>Оклад:</b> {{ worker.salary }}</span>
          <v-divider class="mx-4" inset></v-divider>
          </v-col>
          <v-col cols="12" sm="12" md="12" xs="12">
            <span><b>Дата выхода на работу:</b> {{ worker.startDate }}</span>
          <v-divider class="mx-4" inset></v-divider>
          </v-col>
          <v-col cols="12" sm="12" md="12" xs="12">
            <span><b>Ставка:</b> {{ worker.rate }}</span>
          <v-divider class="mx-4" inset></v-divider>
          </v-col>
        </v-row>
      </v-container>
    </v-card>

  </v-container>
</template>
<script>
export default {
  data: () => ({
    index: 0,
    worker: {},
    workbook: ''
  }),
  mounted() {
    this.index = this.$route.params.id
    this.worker = JSON.parse(localStorage.workers)[this.index]
    if (this.worker.workBook) {
      this.workbook = 'сдана'
    } else {
      this.workbook = 'не сдана'
    }
  }
}
</script>
<style>

</style>