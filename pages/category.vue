<template>
  <div>
    <v-dialog v-model="createD" persistent max-width="500px">
        <v-card>
          <v-card-title>
              <span class="headline">Categorie Toevoegen</span>
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
              <span class="headline">Categorie Bewerken</span>
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
        <v-card-title class="headline">Categorie Verwijderen</v-card-title>
        <v-card-text>Weet u zeker dat de geselecteerde categorie wilt verwijderen?</v-card-text>
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

    <v-card-title>
      <v-flex style="margin-top: 15px;" xs12 sm6 text-xs-left>
        <v-btn color="primary" @click="createItem()">Nieuwe categorie <v-icon dark> add</v-icon></v-btn>
      </v-flex>
      <v-spacer></v-spacer>
      <v-text-field v-model="search" append-icon="search" label="Zoeken..." single-line hide-details></v-text-field>
    </v-card-title>   
    <v-data-table :headers="headers" :items="categories" :search="search" hide-actions class="elevation-1">
      <template slot="items" slot-scope="props">
        <td class="text-xs-left"></td>
        <td class="text-xs-left">{{ props.item.categoryID }}</td>
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
        </td>
      </template>
      <template slot="no-data">
        <v-btn color="primary" @click="getCategories">Reset</v-btn>
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
      search: '',
      hidden: false,
      headers: [
        {
          align: 'left',
          sortable: false,
          value: 'name'
        },
        { text: 'Category ID', value: 'categoryID' },
        { text: 'Naam', value: 'name' },
        { text: 'Afbeelding', value: 'imageURL' },
        { text: 'Acties', value: 'name', sortable: false }
      ],
      categories: [],
      itemIndex: -1,
      item: {
        categoryID: 0,
        name: '',
        imageURL: ''
      },
      defaultItem: {
        categoryID: 0,
        name: '',
        imageURL: ''
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
      }
    },

    /* Here the DataTable will be loaded */
    created () {
      this.getCategories()
    },

    methods: {
      /* UI Logic for CRUD Functions. */
      createItem () {
        this.createD = true
      },

      editItem (item) {
        this.itemIndex = this.categories.indexOf(item)
        this.item = Object.assign({}, item)
        this.editD = true
      },

      deleteItem (item) {
        this.itemIndex = this.categories.indexOf(item)
        this.item = Object.assign({}, item)
        this.deleteD = true
      },

      imageItem (item) {
        this.itemIndex = this.categories.indexOf(item)
        this.item = Object.assign({}, item)
        this.imageD = true
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
        this.postCategories(item)
      },

      updateP (item) {
        this.updateCategory(item)
      },

      deleteP (item) {
        this.deleteCategory(item)
      },

      /* API Logic for CRUD Functions. */
      getCategories () {
        axios.get(`https://handpicked-refreshments.herokuapp.com/api/category/all`).then(response => {
          this.categories = response.data
        })
      },

      postCategories (item) {
        axios.post(`https://handpicked-refreshments.herokuapp.com/api/category/`, {
          name: item.name,
          imageURL: item.imageURL
        },
        { headers: { 'Content-Type': 'application/json; charset=utf-8' } }
        ).then(function (response) {
          console.log(response)
        }).catch(function (error) {
          console.log(error)
        })
        this.close()
      },

      updateCategory (item) {
        axios.put(`https://handpicked-refreshments.herokuapp.com/api/category/` + item.categoryID, {
          name: item.name,
          imageURL: item.imageURL
        },
        { headers: { 'Content-Type': 'application/json; charset=utf-8' } }
        ).then(function (response) {
          console.log(response)
        }).catch(function (error) {
          console.log(error)
        })
        this.close()
      },

      deleteCategory (item) {
        console.log(item)
        axios.delete(`https://handpicked-refreshments.herokuapp.com/api/category/` + item.categoryID)
          .then(function (response) {
            console.log(response)
          }).catch(function (error) {
            console.log(error)
          })
        this.close()
      }
    }
  }
</script>