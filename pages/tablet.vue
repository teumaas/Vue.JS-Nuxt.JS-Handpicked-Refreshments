<template>
  <div>
    <v-dialog v-model="deleteD" persistent max-width="290px">
      <v-card>
        <v-card-title class="headline">Tablet Verwijderen</v-card-title>
        <v-card-text>Weet u zeker dat de geselecteerde tablet wilt verwijderen?</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="deleteD = false">Annuleren</v-btn>
          <v-btn color="blue darken-1" flat @click="deleteP(item)">Verwijderen</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
// 
    <v-card-title>
      <v-spacer></v-spacer><v-spacer></v-spacer>
      <v-text-field v-model="search" append-icon="search" label="Zoeken..." single-line hide-details></v-text-field>
    </v-card-title>   
    <v-data-table :loading="loading" :headers="headers" :items="tablets" :search="search" hide-actions class="elevation-1">
      <v-progress-linear slot="progress" color="blue" indeterminate></v-progress-linear>
        <template slot="items" slot-scope="props" >
          <td class="text-xs-left"></td>
          <td class="text-xs-left">{{ props.item.tabletID }}</td>
          <td class="text-xs-left">{{ props.item.tabletName }}</td>
          <td class="text-xs-left">{{ props.item.hardwareID }}</td>
          <td class="justify-left layout px-0">
            <v-btn icon class="mx-0" dark @click="deleteItem(props.item)">
              <v-icon color="red">delete</v-icon>
            </v-btn>
          </td>
        </template>
        <template slot="no-data">
          <div style="display: none;" class="text-md-center" :value="refreshBtn">
            <v-btn round color="primary" @click="getTablets" ><v-icon>refresh</v-icon> Vernieuwen</v-btn>
          </div>
        </template>
        <div class="text-md-center" slot="no-results" :value="true">
          Geen resultaten gevonden voor "{{ search }}".
        </div>
    </v-data-table>
  </div>
</template>

<script>
  import axios from 'axios'

  /* DataTable settings */
  export default {
    data: () => ({
      deleteD: false,
      search: '',
      loading: true,
      refreshBtn: false,
      headers: [
        {
          align: 'center',
          sortable: false,
          value: 'name'
        },
        { text: 'Tablet ID', value: 'tabletID' },
        { text: 'Naam', value: 'tabletName' },
        { text: 'Hardware ID', value: 'hardwareID' },
        { text: 'Acties', value: 'name', sortable: false }
      ],
      tablets: [],
      itemIndex: -1,
      item: {
        tabletID: 0,
        tabletName: '',
        hardwareID: ''
      },
      defaultItem: {
        tabletID: 0,
        tabletName: '',
        hardwareID: ''
      }
    }),

    computed: {
    },

    /* Add here new function for new dialog */
    watch: {
      deleteD (val) {
        val || this.close()
      }
    },

    /* Here the DataTable will be loaded */
    created () {
      this.getTablets()
    },

    methods: {
      /* UI Logic for RD Functions. */
      deleteItem (item) {
        this.itemIndex = this.tablets.indexOf(item)
        this.item = Object.assign({}, item)
        this.deleteD = true
      },

      close () {
        this.deleteD = false
        setTimeout(() => {
          this.item = Object.assign({}, this.defaultItem)
          this.itemIndex = -1
        }, 300)
      },

      deleteP (item) {
        this.deleteTablet(item)
      },

      /* API Logic for CRD Functions. */
      getTablets () {
        this.loading = true
        this.refreshBtn = false
        axios.get('https://handpicked-refreshments.herokuapp.com/api/tablet')
          .then(response => {
            this.tablets = response.data
            this.loading = false
          })
          .catch(error => {
            console.log(error)
            this.refreshBtn = false
          })
      },

      deleteTablet (item) {
        axios.delete('https://handpicked-refreshments.herokuapp.com/api/tablet/' + item.tabletID)
          .then(response => {
            this.getTablets()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      }
    }
  }
</script>