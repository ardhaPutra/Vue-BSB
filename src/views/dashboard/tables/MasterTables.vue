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
        <v-btn
          color="primary"
          @click="dialog = true"
        >
          Tambah Barang
        </v-btn>

        <v-dialog
          v-model="dialog"
          width="auto"
        >
          <v-card>
            <v-card-title>
              <span class="text-h5">Tambah Barang</span>
            </v-card-title>
            <v-card-text>
              <v-form @submit.prevent="submitForm">
                <v-container>
                  <v-row>
                    <v-col
                      cols="12"
                      sm="6"
                      md="4"
                    >
                      <v-text-field
                        v-model="newItem.nama"
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
                        v-model="newItem.jumlah"
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
                        v-model="newItem.harga"
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
                @click="addItem"
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
  </v-container>
</template>

<script>
  import axios from 'axios'
  export default {
    data () {
      return {
        dialog: false,
        selected: null,
        headers: [
          { text: 'ID', value: 'id_barang' },
          { text: 'Nama', value: 'nama' },
          { text: 'Jumlah', value: 'jumlah' },
          { text: 'Harga', value: 'harga' },
          { text: 'Barcode', value: 'barcode' },
          { text: 'Tanggal', value: 'tanggal' },
          { text: 'Action', value: 'actions', sortable: false },
        ],
        items: [],
        newItem: {
          nama: '',
          jumlah: '',
          harga: '',
        },
        search: '',
        loading: false,
        pagination: {
          rowsPerPage: 5,
          itemsPerPageOptions: [5, 10, 15, 20],
        },
        totalItems: 0,
        itemsPerPage: 10,
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
      submit () {
        // Panggil fungsi untuk mengirim data ke API PHP menggunakan Axios
        this.submitData()
        // Tutup dialog setelah mengirim data
        this.dialog = false
      },
      closeDialog () {
        this.dialog = false
        this.nama = ''
        this.jumlah = ''
        this.harga = ''
      },
      // submitForm () {
      //   // Mengirim data menggunakan Axios
      //   axios.post('http://localhost/crud-php/api/create.php', {
      //     nama: this.nama,
      //     jumlah: this.jumlah,
      //     harga: this.harga,
      //   })
      //     .then(response => {
      //       // Tampilkan pesan sukses jika data berhasil disimpan ke API PHP
      //       alert('Data berhasil disimpan')
      //       console.log(response)
      //       this.closeDialog()
      //       this.$emit('user-created')
      //     })
      //     .catch(error => {
      //       // Tampilkan pesan error jika terjadi kesalahan dalam mengirim data ke API PHP
      //       console.log(error)
      //       alert('Terjadi kesalahan dalam menyimpan data')
      //     })
      // },
      async addItem() {
        try {
          const response = await axios.post('http://localhost/crud-php/api/create.php', this.newItem);
          this.items.push(response.data);
          this.showAddDialog = false;
          this.newItem = {
            nama: '',
            jumlah: '',
            harga: '',
          };
        } catch (error) {
          console.error(error);
        }
      },
    },
  }
</script>
