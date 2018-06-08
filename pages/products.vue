<template>
  <div>
    <v-dialog v-model="createD" persistent max-width="500px">
        <v-card>
          <v-card-title>
              <span class="headline">Product Toevoegen</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field v-model="item.name" label="Naam"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="item.imageURL" label="Afbeelding"></v-text-field>
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
              <span class="headline">Product Bewerken</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field v-model="item.name" label="Naam"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="item.imageURL" label="Afbeelding"></v-text-field>
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
        <v-card-title class="headline">Product Verwijderen</v-card-title>
        <v-card-text>Weet u zeker dat het geselecteerde product wilt verwijderen?</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="deleteD = false">Annuleren</v-btn>
          <v-btn color="blue darken-1" flat @click="deleteP(item)">Verwijderen</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="imageD" persistent max-width="290px">
      <v-card>
        <v-card-title class="headline">Afbeelding</v-card-title>
          <v-flex text-xs-center layout align-center justify-center>
            <v-avatar :tile="false" :size="256">
              <img :src="item.imageURL" :alt="item.name">
            </v-avatar>
          </v-flex>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="imageD = false">Sluiten</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

        <v-dialog v-model="addAt" persistent max-width="290px">
      <v-card>
        <v-card-title class="headline">Attributen toevoegen aan + {{ item.name }} </v-card-title>
        <v-card-text>Selecteer een attribuut die u wil toevoegen</v-card-text>
        <v-card-actions>
          <v-spacer>
            <v-flex xs12>
                  <v-select 
                    v-model="value"
                    track-by="name" 
                    label="Attribuut" 
                    placeholder="Voeg attributen toe" 
                    :itemsAt = "attributes"
                    :options="options" 
                    :searchable="true" 
                    :allow-empty="true"
                    :multiple="false"
                    :close-on-select="false"
                    :hide-selected="true">
                    <template slot="tag" slot-scope="props">{{ props.option.name }}</template>
                  </v-select>
                </v-flex>
          </v-spacer>
          <v-btn color="blue darken-1" flat @click.native="addAt = false">Annuleren</v-btn>
          <!--<v-btn color="blue darken-1" flat @click.native="addAttribute(item, props.selected)">Bevestigen</v-btn>-->
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-card-title>
      <v-flex style="margin-top: 15px;" xs12 sm6 text-xs-left>
        <v-btn color="primary" @click="createItem()">Nieuw product <v-icon dark> add</v-icon></v-btn>
      </v-flex>
      <v-spacer></v-spacer>
      <v-text-field v-model="search" append-icon="search" label="Zoeken..." single-line hide-details></v-text-field>
    </v-card-title>   
    <v-data-table :loading="loading" :headers="headers" :items="products" :search="search" hide-actions class="elevation-1">
      <v-progress-linear slot="progress" color="blue" indeterminate></v-progress-linear>
        <template slot="items" slot-scope="props" >
          <td class="text-xs-left"></td>
          <td class="text-xs-left">{{ props.item.productID }}</td>
          <td class="text-xs-left">{{ props.item.name }}</td>
          <td class="text-xs-left">
            <v-avatar class="justify-left">
              <img :src="props.item.imageURL" @click="imageItem(props.item)">    
            </v-avatar>
          </td>
          <td class="justify-left layout px-0">
            <v-btn icon class="mx-0" @click="editItem(props.item)">
              <v-icon color="teal">edit</v-icon>
            </v-btn>
            <v-btn icon class="mx-0" dark @click="deleteItem(props.item)">
              <v-icon color="red">delete</v-icon>
            </v-btn>
            <v-btn icon class="mx-0" @click="attributeItem(props.item)">
              <v-icon dark>add</v-icon>
            </v-btn>
          </td>
        </template>
        <template slot="no-data">
          <div style="display: none;" class="text-md-center" :value="refreshBtn">
            <v-btn round color="primary" @click="getProducts" ><v-icon>refresh</v-icon> Vernieuwen</v-btn>
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
      imageD: false,
      addAt: false,
      search: '',
      loading: true,
      refreshBtn: false,
      value: null,
      headers: [
        {
          align: 'center',
          sortable: false,
          value: 'name'
        },
        { text: 'Product ID', value: 'productID' },
        { text: 'Naam', value: 'name' },
        { text: 'Afbeelding', value: 'imageURL' },
        { text: 'Acties', value: 'name', sortable: false }
      ],
      products: [],
      itemIndex: -1,
      item: {
        productID: 0,
        name: '',
        imageURL: ''
      },
      defaultItem: {
        productID: 0,
        name: '',
        imageURL: ''
      },
      attributes: [],
      itemA: {
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
      },

      imageD (val) {
        val || this.close()
      },

      addAt (val) {
        val || this.close()
      }
    },

    /* Here the DataTable will be loaded */
    created () {
      this.getProducts()
      this.getAttributes()
    },

    methods: {
      /* UI Logic for CRUD Functions. */
      createItem () {
        this.createD = true
      },

      editItem (item) {
        this.itemIndex = this.products.indexOf(item)
        this.item = Object.assign({}, item)
        this.editD = true
      },

      deleteItem (item) {
        this.itemIndex = this.products.indexOf(item)
        this.item = Object.assign({}, item)
        this.deleteD = true
      },

      imageItem (item) {
        this.itemIndex = this.products.indexOf(item)
        this.item = Object.assign({}, item)
        this.imageD = true
      },

      attributeItem (item) {
        this.itemIndex = this.product.indexOf(item)
        this.item = Object.assign({}, item)
        this.addAt = true
      },

      close () {
        this.createD = false
        this.imageD = false
        this.editD = false
        this.deleteD = false
        setTimeout(() => {
          this.item = Object.assign({}, this.defaultItem)
          this.itemIndex = -1
        }, 300)
      },

      createP (item) {
        this.postProduct(item)
      },

      updateP (item) {
        this.updateProduct(item)
      },

      deleteP (item) {
        this.deleteProduct(item)
      },

      attributeP (item, attribute) {
        this.addAttribute(item, attribute)
      },

      /* API Logic for CRUD Functions. */
      getProducts () {
        this.loading = true
        this.refreshBtn = false
        axios.get('https://handpicked-refreshments.herokuapp.com/api/product/')
          .then(response => {
            this.products = response.data
            this.loading = false
          })
          .catch(error => {
            console.log(error)
            this.refreshBtn = false
          })
      },

      postProduct (item) {
        const querystring = require('querystring')
        const data = {
          name: item.name,
          imageURL: item.imageURL
        }
        const header = {
          ContentType: 'application/x-www-form-urlencoded',
          Accept: 'application/json'
        }
        axios.post('https://handpicked-refreshments.herokuapp.com/api/product/', querystring.stringify(data), header)
          .then(response => {
            this.getProducts()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      updateProduct (item) {
        const data = {
          name: item.name,
          imageURL: item.imageURL
        }
        const header = {
          ContentType: 'application/x-www-form-urlencoded',
          Accept: 'application/json'
        }
        axios.put('https://handpicked-refreshments.herokuapp.com/api/product/' + item.productID, data, header)
          .then(response => {
            this.getProducts()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      deleteProduct (item) {
        axios.delete('https://handpicked-refreshments.herokuapp.com/api/product/' + item.productID)
          .then(response => {
            this.getProducts()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

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
      }
      /*,
      addAttribute (item, attribute) {
        const header = {
          ContentType: 'application/x-www-form-urlencoded',
          Accept: 'application/json'
        }
        axios.post('https://handpicked-refreshments.herokuapp.com/api/product/' + item.productID + '/attribute/' + attribute.attributeID, header)
          .then(response => {
            this.getProducts()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      }
      */
    }
  }
</script>