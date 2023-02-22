<!-- eslint-disable vue/max-attributes-per-line -->
<!-- eslint-disable no-undef -->
<!-- eslint-disable vue/valid-v-model -->
<template>
  <v-container
    id="regular-tables"
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
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      label="Legal first name*"
                      required
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      label="Legal middle name"
                      hint="example of helper text only on focus"
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="4"
                  >
                    <v-text-field
                      label="Legal last name*"
                      hint="example of persistent helper text"
                      persistent-hint
                      required
                    />
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      label="Email*"
                      required
                    />
                  </v-col>
                  <v-col cols="12">
                    <v-text-field
                      label="Password*"
                      type="password"
                      required
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                  >
                    <v-select
                      :items="['0-17', '18-29', '30-54', '54+']"
                      label="Age*"
                      required
                    />
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                  >
                    <v-autocomplete
                      :items="['Skiing', 'Ice hockey', 'Soccer', 'Basketball', 'Hockey', 'Reading', 'Writing', 'Coding', 'Basejump']"
                      label="Interests"
                      multiple
                    />
                  </v-col>
                </v-row>
              </v-container>
              <small>*indicates required field</small>
            </v-card-text>
            <v-card-actions>
              <v-spacer />
              <v-btn
                color="blue-darken-1"
                variant="text"
                @click="dialog = false"
              >
                Close
              </v-btn>
              <v-btn
                color="blue-darken-1"
                variant="text"
                @click="dialog = false"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
      </v-card-title>
      <v-data-table
        v-model="selected"
        :headers="headers"
        :items="barang"
        item-value="nama"
        show-select
      />
    </v-card>
  </v-container>
</template>

<script>
  import axios from 'axios'
  export default {
    name: 'TestIndex',

    data () {
      return {
        search: '',
        dialog: false,
        headers: [
          { text: 'Id', value: 'id' },
          { text: 'Nama', value: 'nama' },
          { text: 'Jumlah', value: 'jumlah' },
          { text: 'Harga', value: 'harga' },
          { text: 'Barcode', value: 'barcode' },
          { text: 'Tanggal', value: 'tanggal' },
          { text: 'Action', value: '' },
        ],
        barang: [
          {
            id: '1',
            nama: 'Frozen Yogurt',
            jumlah: 159,
            harga: 6.0,
            barcode: 24,
            tanggal: 4.0,
            iron: '1',
          },
          {
            id: '2',
            nama: 'Jelly bean',
            jumlah: 375,
            harga: 0.0,
            barcode: 94,
            tanggal: 0.0,
            iron: '0',
          },
          {
            id: '3',
            nama: 'KitKat',
            jumlah: 518,
            harga: 26.0,
            barcode: 65,
            tanggal: 7,
            iron: '6',
          },
          {
            id: '4',
            nama: 'Eclair',
            jumlah: 262,
            harga: 16.0,
            barcode: 23,
            tanggal: 6.0,
            iron: '7',
          },
          {
            id: '5',
            nama: 'Gingerbread',
            jumlah: 356,
            harga: 16.0,
            barcode: 49,
            tanggal: 3.9,
            iron: '16',
          },
          {
            id: '6',
            nama: 'Ice cream sandwich',
            jumlah: 237,
            harga: 9.0,
            barcode: 37,
            tanggal: 4.3,
            iron: '1',
          },
          {
            id: '7',
            nama: 'Lollipop',
            jumlah: 392,
            harga: 0.2,
            barcode: 98,
            tanggal: 0,
            iron: '2',
          },
        ],
        mounted () {
          this.getBarang()
        },
        methods: {
          getBarang () {
            axios.get('http://localhost/crud-php/api/read.php').then(
              response => (
                this.products = response.data
              ),
            )
          },
        },

      }
    },
  }
</script>
