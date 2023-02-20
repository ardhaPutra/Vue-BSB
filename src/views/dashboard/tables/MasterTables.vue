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
          dark
          class="mb-2"
          v-bind="props"
        >
          Tambah Barang
        </v-btn>
      </v-card-title>
      <v-data-table
        :headers="headers"
        :items="barang"
        v-model="selected"
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
