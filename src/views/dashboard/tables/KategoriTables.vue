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

    <v-card>
      <v-card-title>
        Data Kategori
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
          width="auto"
        >
          <template v-slot:activator="{ on }">
            <v-btn
              dark color="primary"
              v-on="on"
            >
              Tambah Kategori
            </v-btn>
          </template>

          <v-card>
            <v-card-title>
              <span class="text-h5">Tambah Kategori</span>
            </v-card-title>
            <v-card-text>
              <v-form ref="form">
                <v-container>
                  <v-row>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="addItem.nm"
                        outlined label="Nama Kategori*"
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
          'items-per-page-text': 'Items per page:',
          'show-current-page': true,
          'show-first-last-page': true,
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
          Edit Kategori
        </v-card-title>

        <v-card-text>
          <v-form ref="form">
            <v-text-field v-if="hideIdField" :value="editedItem.pk" label="kategori" readonly disabled />
            <v-text-field v-model="editedItem.nm" label="Nama" />
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
          { text: 'Nama', value: 'nm' },
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
          nm: '',
        },
        defaultItem: {
          pk: '',
          nm: '',
        },
        addItem: {
          nm: '',
        },
        categories: [],
      }
    },
    mounted () {
      this.loadKategori()
      this.loadItems()
    },
    methods: {
      loadItems (page = 1) {
        this.loading = true
        // echo   ('test');
        axios
          .get('http://localhost/crud-php/api/kategori/data.php', {
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
        this.addItem.nm = ''
        this.dialog = true
      },
      saveData () {
        axios.post('http://localhost/crud-php/api/kategori/createdata.php', {
          nm: this.addItem.nm,
        })
          .then(response => {
            console.log(response)
            this.dialog = false
            this.loadItems() // load data after success
            this.addItem = {
              nm: '',
            }
          })
          .catch(error => {
            console.log(error)
          })
      },
      closeDialog () {
        this.dialog = false
      },
      editItem (item) {
        this.editedItem = Object.assign({}, item)
        this.editedIndex = this.items.indexOf(item)
        this.editDialog = true

        if (this.editedItem.kategorifk) {
          this.hideIdField = true
        }
      },
      closeDialogEdit () {
        if (this.$refs.form) {
          this.$refs.form.reset()
        }
        this.editDialog = false
      },
      // edit item
      updateItem () {
        axios.post('http://localhost/crud-php/api/barang/updatedata.php', this.editedItem)
          .then(response => {
            // handle success
            console.log(response.data)
            this.editDialog = false
            this.loadItems()
            this.$toast.success('Data barang berhasil diupdate')
          })
          .catch(error => {
            // handle error
            console.log(error)
            this.$toast.error('Data barang gagal diupdate')
          })
      },
      deleteItem (item) {
        if (confirm('Anda yakin ingin menghapus item ini?')) {
          axios
            .delete('http://localhost/crud-php/api/kategori/deletedata.php', { data: { pk: item.pk } })
            .then(() => {
              // menghapus item dari array items
              const index = this.items.indexOf(item)
              if (index > -1) {
                this.items.splice(index, 1)
              }
              // update the totalItems property
              this.totalItems -= 1
              // show success message
              this.snackbar = true
              this.snackbarText = 'Data berhasil dihapus'
            })
            .catch((err) => {
              console.error(err)
              // show error message
              this.snackbar = true
              this.snackbarText = 'Gagal menghapus data'
            })
        }
      },
    },
  }
</script>
