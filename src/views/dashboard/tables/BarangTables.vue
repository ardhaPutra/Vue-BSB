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
              <v-icon class="white--text">mdi-close-circle</v-icon>
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
              <v-icon class="white--text">mdi-close-circle</v-icon>
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
              <v-icon class="white--text">mdi-close-circle</v-icon>
            </v-btn>
          </template>
          <span class="white--text">Data berhasil diubah.</span>
        </v-alert>
      </div>
    </template>

    <v-card>
      <v-card-title>
        Data Barang
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
              Tambah Barang
            </v-btn>
          </template>

          <v-card>
            <v-card-title>
              <span class="text-h5">Tambah Barang</span>
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
                        v-model="addItem.nama"
                        outlined label="Nama Barang*"
                        required
                      />
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-select
                        v-model="addItem.kategorifk"
                        :items="categories"
                        item-text="nm"
                        item-value="pk"
                        outlined label="Kategori"
                      />
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-select
                        v-model="addItem.satuanfk"
                        :items="satuan"
                        item-text="ns"
                        item-value="pk"
                        outlined label="satuan"
                      />
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="addItem.jumlah"
                        outlined label="Jumlah*"
                        type="number"
                        hint="masukkan dalam bentuk angka"
                      />
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="addItem.harga"
                        outlined label="Harga*"
                        type="number"
                        hint="masukkan dalam bentuk angka"
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

    <v-dialog :key="editedItem.id_barang" v-model="editDialog" max-width="500px">
      <v-card>
        <v-card-title>
          Edit Barang
        </v-card-title>

        <v-card-text>
          <v-form ref="form">
            <v-text-field v-if="hideIdField" :value="editedItem.id_barang" label="id_barang" readonly disabled />
            <v-text-field v-model="editedItem.nama" label="Nama" />
            <v-text-field v-model="editedItem.jumlah" label="Jumlah" type="number" />
            <v-col>
              <v-select
                v-model="editedItem.kategorifk"
                :items="categories"
                item-text="nm"
                item-value="pk"
                outlined label="Kategori"
                :value="editedItem.kategorifk"
              />
            </v-col>
            <v-col>
              <v-select
                v-model="editedItem.satuanfk"
                :items="satuan"
                item-text="ns"
                item-value="pk"
                outlined label="Satuan"
                :value="editedItem.satuanfk"
              />
            </v-col>
            <v-text-field v-model="editedItem.harga" label="Harga" type="number" />
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
          { text: 'ID', value: 'id_barang' },
          { text: 'Nama', value: 'nama' },
          { text: 'Jumlah', value: 'jumlah' },
          { text: 'Harga', value: 'harga' },
          { text: 'Barcode', value: 'barcode' },
          { text: 'Tanggal', value: 'tanggal' },
          { text: 'Kategori', value: 'nm' },
          { text: 'Satuan', value: 'ns' },
          { text: 'Action', value: 'actions', sortable: false },
        ],
        totalItems: 0,
        loading: false,
        itemsPerPage: 10,
        currentPage: 1,
        search: '',
        editedIndex: null,
        editedItem: {
          id_barang: '',
          nama: '',
          kategorifk: '',
          satuanfk: '',
          jumlah: 0,
          harga: 0,
          barcode: '',
          tanggal: '',
        },
        defaultItem: {
          id_barang: '',
          nama: '',
          kategorifk: '',
          satuanfk: '',
          jumlah: 0,
          harga: 0,
          barcode: '',
          tanggal: '',
        },
        addItem: {
          nama: '',
          jumlah: 0,
          harga: 0,
          kategorifk: 0,
          satuanfk: 0,
        },
        kategorifk: '',
        satuanfk: '',
        categories: [],
        selected: [],
        selectedCategory: null,
        alertVisible: false,
        alertUpdatedVisible: false,
        alertDeletedVisible: false,
      }
    },
    mounted () {
      this.loadKategori()
      this.loadSatuan()
      this.loadItems()
    },
    methods: {
      loadKategori () {
        axios
          .get('http://localhost/crud-php/api/kategori.php')
          .then((response) => {
            this.categories = response.data
          })
          .catch((error) => {
            console.error(error)
          })
      },
      loadSatuan () {
        axios
          .get('http://localhost/crud-php/api/satuan.php')
          .then((response) => {
            this.satuan = response.data
          })
          .catch((error) => {
            console.error(error)
          })
      },
      loadItems (page = 1) {
        this.loading = true
        // echo   ('test');
        axios
          .get('http://localhost/crud-php/api/barang/data.php', {
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
        this.addItem.nama = ''
        this.addItem.kategorifk = ''
        this.addItem.satuanfk = ''
        this.addItem.jumlah = ''
        this.addItem.harga = ''
        this.dialog = true
      },
      saveData () {
        axios.post('http://localhost/crud-php/api/barang/createdata.php', {
          nama: this.addItem.nama,
          jumlah: this.addItem.jumlah,
          harga: this.addItem.harga,
          kategorifk: this.addItem.kategorifk,
          satuanfk: this.addItem.satuanfk,
        })
          .then(response => {
            console.log(response)
            this.dialog = false
            this.loadItems() // load data after success
            this.addItem = {
              nama: '',
              kategorifk: null,
              satuanfk: null,
              jumlah: null,
              harga: null,
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

        // Cari data kategori yang sesuai
        this.selectedCategory = this.categories.find((category) => {
          return category.pk === item.kategorifk
        })

        // Set value kategorifk agar terpilih secara otomatis
        this.editedItem.kategorifk = this.selectedCategory.pk

        if (this.editedItem.kategorifk) {
          this.hideIdField = true
        }

        // Cari data satuan yang sesuai
        this.selectedSatuan = this.satuan.find((satuan) => {
          return satuan.pk === item.satuanfk
        })

        // Set value kategorifk agar terpilih secara otomatis
        this.editedItem.satuanfk = this.selectedSatuan.pk

        if (this.editedItem.satuanfk) {
          this.hideIdField = true
        }
      },
      closeDialogEdit () {
        // this.editDialog = false
        // this.$refs.form.reset()
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
            .delete('http://localhost/crud-php/api/barang/deletedata.php', { data: { id_barang: item.id_barang } })
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
