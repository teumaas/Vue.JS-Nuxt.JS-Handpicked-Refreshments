<template>
  <div>
    <v-dialog v-model="createD" persistent max-width="500px">
        <v-card>
          <v-card-title>
              <span class="headline">Attribuut toevoegen</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field v-model="item.name" label="Naam" required></v-text-field>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" flat @click.native="createD = false">Annuleren</v-btn>
            <v-btn color="blue darken-1" flat @click="createP(item)">Opslaan</v-btn>
          </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="editD" persistent max-width="500px">
        <v-card>
          <v-card-title>
              <span class="headline">Attribuut Bewerken</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field v-model="item.name" label="Naam" required></v-text-field>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" flat @click.native="editD = false">Annuleren</v-btn>
            <v-btn color="blue darken-1" flat @click="updateP(item)">Opslaan</v-btn>
          </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="deleteD" persistent max-width="290px">
      <v-card>
        <v-card-title class="headline">Attribuut verwijderen</v-card-title>
        <v-card-text>Weet u zeker dat het geselecteerde attribuut wilt verwijderen?</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="deleteD = false">Annuleren</v-btn>
          <v-btn color="blue darken-1" flat @click="deleteP(item)">Verwijderen</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-card-title>
      <v-flex style="margin-top: 15px;" xs12 sm6 text-xs-left>
        <v-btn color="primary" @click="createItem()">Nieuw attribuut <v-icon dark> add</v-icon></v-btn>
      </v-flex>
      <v-spacer></v-spacer>
      <v-text-field v-model="search" append-icon="search" label="Zoeken..." single-line hide-details></v-text-field>
    </v-card-title>   
    <v-data-table :loading="loading" :headers="headers" :items="attributes" :search="search" hide-actions class="elevation-1">
      <v-progress-linear slot="progress" color="blue" indeterminate></v-progress-linear>
        <template slot="items" slot-scope="props" >
          <td class="text-xs-left"></td>
          <td class="text-xs-left">{{ props.item.attributeID }}</td>
          <td class="text-xs-left">{{ props.item.name }}</td>
          <td class="justify-left layout px-0">
            <v-btn icon class="mx-0" @click="editItem(props.item)">
              <v-icon color="teal">edit</v-icon>
            </v-btn>
            <v-btn icon class="mx-0" dark @click="deleteItem(props.item)">
              <v-icon color="red">delete</v-icon>
            </v-btn>
          </td>
        </template>
        <template slot="no-data">
          <div style="display: none;" class="text-md-center" :value="refreshBtn">
            <v-btn round color="primary" @click="getAttributes" ><v-icon>refresh</v-icon> Vernieuwen</v-btn>
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
      createD: false,
      editD: false,
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
        { text: 'Attribuut ID', value: 'attributeID' },
        { text: 'Naam', value: 'name' },
        { text: 'Acties', value: 'name', sortable: false }
      ],
      attributes: [],
      itemIndex: -1,
      item: {
        attributeID: 0,
        name: ''
      },
      defaultItem: {
        attributeID: 0,
        name: ''
      }
    }),

    computed: {
    },

    /* Add here new function for new dialog */
    watch: {
      createD (val) {
        val || this.close()
      },

      editD (val) {
        val || this.close()
      },

      deleteD (val) {
        val || this.close()
      }
    },

    /* Here the DataTable will be loaded */
    created () {
      this.getAttributes()
    },

    methods: {
      /* UI Logic for CRUD Functions. */
      createItem () {
        this.createD = true
      },

      editItem (item) {
        this.itemIndex = this.attributes.indexOf(item)
        this.item = Object.assign({}, item)
        this.editD = true
      },

      deleteItem (item) {
        this.itemIndex = this.attributes.indexOf(item)
        this.item = Object.assign({}, item)
        this.deleteD = true
      },

      close () {
        this.createD = false
        this.editD = false
        this.deleteD = false
        setTimeout(() => {
          this.item = Object.assign({}, this.defaultItem)
          this.itemIndex = -1
        }, 300)
      },

      createP (item) {
        this.postAttribute(item)
      },

      updateP (item) {
        this.updateAttribute(item)
      },

      deleteP (item) {
        this.deleteAttribute(item)
      },

      /* API Logic for CRUD Functions. */
      getAttributes () {
        this.loading = true
        this.refreshBtn = false
        axios.get('https://handpicked-refreshments.herokuapp.com/api/product/attribute/all')
          .then(response => {
            this.attributes = response.data
            this.loading = false
          })
          .catch(error => {
            console.log(error)
            this.refreshBtn = false
          })
      },

      postAttribute (item) {
        const querystring = require('querystring')
        const data = {
          name: item.name
        }
        const header = {
          ContentType: 'application/x-www-form-urlencoded',
          Accept: 'application/json'
        }
        axios.post('https://handpicked-refreshments.herokuapp.com/api/product/attribute/', querystring.stringify(data), header)
          .then(response => {
            this.getAttributes()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      updateAttribute (item) {
        const data = {
          name: item.name
        }
        const header = {
          ContentType: 'application/x-www-form-urlencoded',
          Accept: 'application/json'
        }
        axios.put('https://handpicked-refreshments.herokuapp.com/api/product/attribute/' + item.attributeID, data, header)
          .then(response => {
            this.getAttributes()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      deleteAttribute (item) {
        axios.delete('https://handpicked-refreshments.herokuapp.com/api/product/attribute/' + item.attributeID)
          .then(response => {
            this.getAttributes()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      }
    }
  }
</script>