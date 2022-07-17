<template>
  <v-container>
    <v-data-table :headers="headers" :items="workers" sort-by="calories" :search="search">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title class="media-tool">Сотрудники</v-toolbar-title>
          <v-divider class="mx-4 media-tool" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-text-field v-model="search" label="Фильтр"></v-text-field>
          <v-spacer></v-spacer>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="blue" dark class="mb-2" v-bind="attrs" v-on="on">
                Добавить
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">{{ formTitle }}</span>
              </v-card-title>
              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="editedItem.name" label="Имя" :rules="[rules.validName]" hint="Владислав">
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="editedItem.surname" label="Фамилия" :rules="[rules.validName]"
                        hint="Поляков">
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="editedItem.patronymic" label="Отчество" :rules="[rules.validName]"
                        hint="Александрович">
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="editedItem.jobTitle" label="Должность" :rules="[rules.validJob]"
                        hint="Frontend-разработчик">
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-checkbox v-model="editedItem.workBook" :label="'Трудовая книжка сдана'"></v-checkbox>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="editedItem.salary" label="Оклад" prefix="₽" :rules="[rules.validSalary]"
                        hint="50000">
                      </v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-text-field v-model="editedItem.startDate" label="Дата выхода на работу"
                        :rules="[rules.validDate]" hint="25.07.2022"></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="12" md="12">
                      <v-select v-model="editedItem.rate" :items="['полная', 'половина']" label="Ставка"></v-select>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Отмена
                </v-btn>
                <v-btn color="blue darken-1" text @click="save">
                  Сохранить
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="460px">
            <v-card>
              <v-card-title class="text-h5">Вы уверены, что вы хотите удалить сотрудника?</v-card-title>
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="deleteItemConfirm">Да</v-btn>
                <v-btn color="blue darken-1" text @click="closeDelete">Нет</v-btn>
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:item.actions="{ item }">
        <v-icon small class="mr-2" @click="selectWorker(item)">
          mdi-menu
        </v-icon>
        <v-icon small class="mr-2" @click="editItem(item)">
          mdi-pencil
        </v-icon>
        <v-icon small @click="deleteItem(item)">
          mdi-delete
        </v-icon>
      </template>
      <template v-slot:no-data>
        <h2>Нет данных о работниках</h2>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>

export default {
  data: () => ({
    dialog: false,
    dialogDelete: false,
    search: '',
    workers: [],
    worker: 0,
    rules: {
      validName: value => {
        const pattern = /^[А-Я]{1}[а-я]+$/g
        return pattern.test(value) || 'Неверная запись'
      },
      validJob: value => {
        const pattern = /^[А-ЯA-Z]{1}.+$/g
        return pattern.test(value) || 'Неверная запись'
      },
      validSalary: value => {
        const pattern = /^[1-9][0-9]+$/g
        return pattern.test(value) || 'Неверная запись'
      },
      validDate: value => {
        const pattern = /^(0?[1-9]|[12][0-9]|3[01])\.(0[1-9]|1[0-2])\.\d{4}$/gm
        return pattern.test(value) || 'Неверная запись'
      },
    },
    headers: [
      {
        text: 'Имя',
        value: 'name',
      },
      { text: 'Фамилия', value: 'surname' },
      { text: 'Отчество', value: 'patronymic' },
      { text: 'Должность', value: 'jobTitle' },
      { text: 'Действия', value: 'actions', sortable: false },
    ],
    editedIndex: -1,
    editedItem: {
      name: '',
      surname: '',
      patronymic: '',
      jobTitle: '',
      workBook: false,
      salary: '',
      startDate: '',
      rate: 'полная',
    },
    defaultItem: {
      name: '',
      surname: '',
      patronymic: '',
      jobTitle: '',
      workBook: false,
      salary: '',
      startDate: '',
      rate: 'полная',
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? 'Добавить сотрудника' : 'Редактировать сотрудника'
    },
  },

  watch: {
    dialog(val) {
      val || this.close()
    },
    dialogDelete(val) {
      val || this.closeDelete()
    },
  },

  mounted() {
    if (localStorage.workers) {
      this.workers = JSON.parse(localStorage.workers)
    } else {
      this.workers = localStorage.setItem('workers', JSON.stringify([]))
    }
  },

  methods: {
    selectWorker(item) {
      let id = this.workers.indexOf(item)
      this.$router.push({ name: 'worker', params: { id } })

    },

    editItem(item) {
      this.editedIndex = this.workers.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialog = true
    },

    deleteItem(item) {
      this.editedIndex = this.workers.indexOf(item)
      this.editedItem = Object.assign({}, item)
      this.dialogDelete = true
    },

    deleteItemConfirm() {
      this.workers.splice(this.editedIndex, 1)
      let lsworkers = this.workers
      window.localStorage.setItem('workers', JSON.stringify(lsworkers))
      this.workers = JSON.parse(localStorage.workers)
      this.closeDelete()
    },

    close() {
      this.dialog = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    closeDelete() {
      this.dialogDelete = false
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem)
        this.editedIndex = -1
      })
    },

    save() {
      if (this.editedIndex > -1) {
        this.workers[this.editedIndex] = this.editedItem
        let lsworkers = this.workers
        window.localStorage.setItem('workers', JSON.stringify(lsworkers))
        this.workers = JSON.parse(localStorage.workers)
      } else {

        let lsworkers = [...JSON.parse(window.localStorage.workers), this.editedItem]
        window.localStorage.setItem('workers', JSON.stringify(lsworkers))
        this.workers = JSON.parse(localStorage.workers)

      }
      this.close()
    },
  },
}
</script>

<style scoped>
 @media (max-width:560px){
  .media-tool {
    
    display: none;
  }
 }
</style>