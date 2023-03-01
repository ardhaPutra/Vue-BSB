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
              <v-form>
                <v-container>
                  <v-row>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="addItem.nama"
                        label="Nama Barang*"
                        required
                      />
                    </v-col>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="addItem.jumlah"
                        label="Jumlah*"
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
                        label="Harga*"
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
                color="error"
                variant="text"
                @click="closeDialog"
              >
                Batal
              </v-btn>
              <v-btn
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

    <v-dialog v-model="editDialog" max-width="500px">
      <v-card>
        <v-card-title>
          Edit Barang
        </v-card-title>

        <v-card-text>
          <v-form ref="form">
            <v-text-field v-model="editedItem.id_barang" label="id_barang" />
            <v-text-field v-model="editedItem.nama" label="Nama" />
            <v-text-field v-model="editedItem.jumlah" label="Jumlah" type="number" />
            <v-text-field v-model="editedItem.harga" label="Harga" type="number" />
          </v-form>
        </v-card-text>

        <v-card-actions>
          <v-spacer />
          <v-btn color="blue darken-1" text @click="closeDialogEdit">
            Cancel
          </v-btn>
          <v-btn color="blue darken-1" text @click="updateItem">
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
          { text: 'Action', value: 'actions', sortable: false },
        ],
        totalItems: 0,
        loading: false,
        itemsPerPage: 10,
        currentPage: 1,
        search: '',
        editedIndex: -1,
        editedItem: {
          id_barang: '',
          nama: '',
          jumlah: 0,
          harga: 0,
          barcode: '',
          tanggal: '',
        },
        defaultItem: {
          id_barang: '',
          nama: '',
          jumlah: 0,
          harga: 0,
          barcode: '',
          tanggal: '',
        },
        addItem: {
          nama: '',
          jumlah: 0,
          harga: 0,
        },
        selected: [],
        // pagination: {
        //   rowsPerPage: 5,
        //   itemsPerPageOptions: [5, 10, 15, 20],
        // },
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
          .get('http://localhost/crud-php/api/data.php', {
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
        this.addItem.jumlah = ''
        this.addItem.harga = ''
        this.dialog = true
      },
      saveData () {
        axios.post('http://localhost/crud-php/api/createdata.php', {
          nama: this.addItem.nama,
          jumlah: this.addItem.jumlah,
          harga: this.addItem.harga,
        })
          .then(response => {
            console.log(response)
            this.dialog = false
          })
          .catch(error => {
            console.log(error)
          })
      },
      submit () {
        // Panggil fungsi untuk mengirim data ke API PHP menggunakan Axios
        this.submitData()
        // Tutup dialog setelah mengirim data
        this.dialog = false
      },
      closeDialog () {
        this.dialog = false
      },
      editItem (item) {
        this.editedItem = Object.assign({}, item)
        this.editDialog = true
      },
      closeDialogEdit () {
        this.editDialog = false
        this.$refs.form.reset()
      },
      // edit item
      updateItem () {
        axios.post('http://localhost/crud-php/api/update.php' + this.editedItem.id_barang, this.editedItem)
          .then(response => {
            // handle success
            console.log(response.data)
            this.editDialog = false
            // this.$refs.form.reset()
            // this.loadData()
            this.loadItems()
            this.$toast.success('Data barang berhasil diupdate')
          })
          .catch(error => {
            // handle error
            console.log(error)
          })
      },
      deleteItem (item) {
        const index = this.items.indexOf(item)
        if (confirm('Anda yakin ingin menghapus item ini?') && this.items.splice(index, 1)) {
          axios.delete('http://localhost/crud-php/api/delete.php/' + item.id_barang)
            .then(() => {
              // this.loadItems()
              this.items.splice(index, 1)
              this.snackbar = true
              this.snackbarText = 'Data berhasil dihapus'
              // this.$toast.success('Data barang berhasil dihapus')
            })
            .catch((err) => {
              console.error(err)
              this.snackbar = true
              this.snackbarText = 'Gagal menghapus data'
            })
        }
      },
    },
  }
</script>
