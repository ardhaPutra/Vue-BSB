<!-- eslint-disable vue/no-unused-vars -->
<!-- eslint-disable vue/no-duplicate-attributes -->
<!-- eslint-disable vue/valid-v-bind -->
<!-- eslint-disable no-undef -->
<!-- eslint-disable vue/max-attributes-per-line -->
<!-- eslint-disable no-undef -->
<!-- eslint-disable vue/valid-v-model -->
<template>
  <v-container
    id="tbl"
    fluid
    tag="section"
  >
    <div class="py-3" />

    <!-- Alert Simpan Data -->
    <template>
      <div>
        <v-dialog v-model="dialog" max-width="500px" />

        <v-alert
          ref="successAlert"
          color="success"
          icon="mdi-check-circle-outline"
          :value="alertVisible"
          dismissible
          dark
          @click:close="alertVisible = false"
        >
          <template #close="{ toggle }">
            <v-btn icon @click="hideSuccessAlert">
              <v-icon class="white--text">
                mdi-close-circle
              </v-icon>
            </v-btn>
          </template>
          <span class="white--text">Data berhasil disimpan.</span>
        </v-alert>
      </div>
    </template>

    <!-- Alert Delete Data -->
    <template>
      <div>
        <v-dialog v-model="dialog" max-width="500px" />

        <v-alert
          ref="deletedAlert"
          color="success"
          icon="mdi-check-circle-outline"
          :value="alertDeletedVisible"
          dismissible
          dark
          @click:close="alertDeletedVisible = false"
        >
          <template #close="{ toggle }">
            <v-btn icon @click="hideDeletedAlert">
              <v-icon class="white--text">
                mdi-close-circle
              </v-icon>
            </v-btn>
          </template>
          <span class="white--text">Data berhasil dihapus.</span>
        </v-alert>
      </div>
    </template>

    <!-- Alert Update Data -->
    <template>
      <div>
        <v-dialog v-model="dialog" max-width="500px" />

        <v-alert
          ref="updatedAlert"
          color="success"
          icon="mdi-check-circle-outline"
          :value="alertUpdatedVisible"
          dismissible
          dark
          @click:close="alertUpdatedVisible = false"
        >
          <template #close="{ toggle }">
            <v-btn icon @click="hideUpdatedAlert">
              <v-icon class="white--text">
                mdi-close-circle
              </v-icon>
            </v-btn>
          </template>
          <span class="white--text">Data berhasil diubah.</span>
        </v-alert>
      </div>
    </template>

    <v-card>
      <v-card-title>
        Data Satuan
        <v-spacer />
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Cari"
          single-line
          hide-details
        />
        <v-spacer />

        <v-dialog
          v-model="dialog"
          max-width="500px"
        >
          <template v-slot:activator="{ on }">
            <v-btn
              dark color="primary"
              v-on="on"
            >
              Tambah Satuan
            </v-btn>
          </template>

          <v-card>
            <v-card-title>
              <span class="text-h5">Tambah Satuan</span>
            </v-card-title>
            <v-card-text>
              <v-form ref="form">
                <v-container>
                  <v-row>
                    <v-col
                      cols="12"
                    >
                      <v-text-field
                        v-model="addItem.ns"
                        label="Nama Satuan*"
                        required
                      />
                    </v-col>
                  </v-row>
                </v-container>
                <small> Field dengan keterangan <span style="color:red;">*</span> wajib diisi</small>
              </v-form>
            </v-card-text>
            <v-card-actions>
              <v-spacer />
              <v-btn
                type="button"
                color="error"
                variant="text"
                @click="closeDialog"
              >
                Batal
              </v-btn>
              <v-btn
                type="button"
                color="success"
                variant="text"
                @click="saveData"
              >
                Simpan
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-card-title>
      <v-data-table
        v-model="selected"
        :headers="headers"
        :items="items"
        :search="search"
        :loading="loading"
        :footer-props.items-per-page-options="[5, 10, 25]"
        :server-items-length="totalItems"
        :items-per-page="itemsPerPage"
        :footer-props="{
          'itemsPerPageOptions': [5, 10, 25],
          'itemsPerPageText': 'Items per page:',
          'show-current-page': true,
          'showFirstLastPage': true,
          'hide-default-footer': true
        }"
        @add-item="openDialog"
        @update:pagination="loadItems"
      >
        <template v-slot:item.actions="{ item }">
          <v-icon small class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
          <v-icon small @click="deleteItem(item)">
            mdi-delete
          </v-icon>
        </template>
      </v-data-table>
    </v-card>

    <v-dialog :key="editedItem.pk" v-model="editDialog" max-width="500px">
      <v-card>
        <v-card-title>
          Edit Satuan
        </v-card-title>

        <v-card-text>
          <v-form ref="form">
            <v-text-field v-if="hideIdField" :value="editedItem.pk" label="satuan" readonly disabled />
            <v-text-field v-model="editedItem.ns" label="Satuan" />
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn type="button" color="blue darken-1" text @click="closeDialogEdit">
            Cancel
          </v-btn>
          <v-btn type="button" color="blue darken-1" text @click="updateItem">
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-container>
</template>

<script>
  import axios from 'axios'
  export default {
    data () {
      return {
        dialog: false,
        editDialog: false,
        items: [],
        headers: [
          { text: 'ID', value: 'pk' },
          { text: 'Nama', value: 'ns' },
          { text: 'Action', value: 'actions', sortable: false },
        ],
        totalItems: 0,
        loading: false,
        itemsPerPage: 10,
        currentPage: 1,
        search: '',
        editedIndex: null,
        editedItem: {
          pk: '',
          ns: '',
        },
        defaultItem: {
          pk: '',
          ns: '',
        },
        addItem: {
          ns: '',
        },
        selected: [],
        pagination: {},
        alertVisible: false,
        alertUpdatedVisible: false,
        alertDeletedVisible: false,
      }
    },
    mounted () {
      this.loadItems()
    },
    methods: {
      loadItems (page = 1) {
        this.loading = true
        // echo   ('test');
        axios
          .get('http://localhost/crud-php/api/satuan/data.php?', {
            params: {
              page: page,
              per_page: this.itemsPerPage,
              search: this.search,
            },
          })
          .then((response) => {
            this.items = response.data
            this.pagination = response.data.pagination
            this.totalItems = response.data.total_items
            this.loading = false
          })
          .catch((error) => {
            console.error(error)
            this.loading = false
          })
      },
      openDialog () {
        this.addItem.ns = ''
        this.dialog = true
      },
      saveData () {
        axios.post('http://localhost/crud-php/api/satuan/createdata.php', {
          ns: this.addItem.ns,
        })
          .then(response => {
            console.log(response)
            this.dialog = false
            this.loadItems() // load data after success
            this.addItem = {
              ns: '',
            }
            this.alertVisible = true // add success alert
            this.$refs.successAlert.$refs.alert.open()
            this.hideSuccessAlert()
          })
          .catch(error => {
            console.log(error)
          })
      },
      hideSuccessAlert () {
        if (this.alertVisible) {
          this.alertVisible = false
          this.$refs.successAlert.$refs.alert.close()
        }
      },
      closeDialog () {
        this.dialog = false
      },
      editItem (item) {
        this.editedItem = Object.assign({}, item)
        this.editedIndex = this.items.indexOf(item)
        this.editDialog = true
      },
      closeDialogEdit () {
        if (this.$refs.form) {
          this.$refs.form.reset()
        }
        this.editDialog = false
      },
      // edit item
      updateItem () {
        axios.post('http://localhost/crud-php/api/satuan/updatedata.php', this.editedItem)
          .then(response => {
            // handle success
            console.log(response.data)
            this.editDialog = false
            this.loadItems()
            this.alertUpdatedVisible = true // add success alert
            this.$refs.updatedAlert.$refs.alert.open()
            this.hideUpdatedAlert()
          })
          .catch(error => {
            // handle error
            console.log(error)
          })
      },
      hideUpdatedAlert () {
        if (this.alertUpdatedVisible) {
          this.alertUpdatedVisible = false
          this.$refs.updatedAlert.$refs.alert.close()
        }
      },
      deleteItem (item) {
        if (confirm('Anda yakin ingin menghapus item ini?')) {
          axios
            .delete('http://localhost/crud-php/api/satuan/deletedata.php', { data: { pk: item.pk } })
            .then(() => {
              // menghapus item dari array items
              const index = this.items.indexOf(item)
              if (index > -1) {
                this.items.splice(index, 1)
              }
              // update the totalItems property
              this.totalItems -= 1
              this.alertDeletedVisible = true // add success alert
              this.$refs.deletedAlert.$refs.alert.open()
              this.hideDeletedAlert()
            })
            .catch((err) => {
              console.error(err)
            })
        }
      },
      hideDeletedAlert () {
        if (this.alertDeletedVisible) {
          this.alertDeletedVisible = false
          this.$refs.deletedAlert.$refs.alert.close()
        }
      },
    },
  }
</script>
