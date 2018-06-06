<template>
  <div>
    <v-dialog v-model="createDeleteD" persistent max-width="500px">
      <v-btn slot="activator" color="primary" dark class="mb-2 right">Nieuwe categorie <v-icon right dark>add</v-icon></v-btn>
        <v-card>
          <v-card-title>
              <span class="headline">{{ formTitle }}</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                <v-text-field v-model="editedItem.name" label="Category"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field v-model="editedItem.pictureURL" label="Afbeelding"></v-text-field>          
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-btn color="blue darken-1" flat @click.native="createDeleteD = false">Annuleren</v-btn>
            <v-btn color="blue darken-1" flat @click="saveC(editedItem)">Opslaan</v-btn>
          </v-card-actions>
        </v-card> 
    </v-dialog>


    <v-dialog v-model="deleteD" persistent max-width="290px">
      <v-card>
        <v-card-title class="headline">Verwijder categorie</v-card-title>
        <v-card-text>Weet u zeker dat u deze categorie wil verwijderen?</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="deleteD = false">Annuleren</v-btn>
          <v-btn color="blue darken-1" flat @click="deleteC(deletedItem)">Verwijderen</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-card-title>
      <v-spacer></v-spacer> 
      <v-text-field v-model="search" append-icon="search" label="Zoeken..." single-line hide-details></v-text-field>
    </v-card-title>   
    <v-data-table :headers="headers" :items="categories" :search="search" hide-actions class="elevation-1">
      <template slot="items" slot-scope="props">
        <td class="text-xs-left"></td>
        <td class="text-xs-left">{{ props.item.categoryID }}</td>
        <td class="text-xs-left">{{ props.item.name }}</td>
        <td class="text-xs-left">{{ props.item.pictureURL }}</td>
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
      createDeleteD: false,
      deleteD: false,
      search: '',
      headers: [
        {
          align: 'left',
          sortable: false,
          value: 'name'
        },
        { text: 'Categorie ID', value: 'categoryID' },
        { text: 'Naam', value: 'name' },
        { text: 'Afbeelding', value: 'pictureURL' },
        { text: 'Acties', value: 'name', sortable: false }
      ],
      categories: [],
      editedIndex: -1,
      editedItem: {
        categoryID: 0,
        name: '',
        pictureURL: ''
      },
      defaultItem: {
        categoryID: 0,
        name: '',
        pictureURL: ''
      }
    }),

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'Nieuwe Categorie' : 'Bewerk Categorieën'
      }
    },

    /* Add here new function for new dialog */
    watch: {
      createDeleteD (val) {
        val || this.close()
      },

      deleteD (val) {
        val || this.close()
      }
    },

    /* Here the DataTable will be loaded */
    created () {
      this.getCategories()
    },

    methods: {
      /* UI Logic for CRUD Functions. */
      editItem (item) {
        this.editedIndex = this.categories.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.createDeleteD = true
      },

      deleteItem (item) {
        this.deletedIndex = this.categories.indexOf(item)
        this.deletedItem = Object.assign({}, item)
        this.deleteD = true
      },

      close () {
        this.createDeleteD = false
        this.deleteD = false
        setTimeout(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.deleteItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
          this.deletedIndex = -1
        }, 300)
      },

      saveC (item) {
        this.editCategory(item)
      },

      deleteC (item) {
        this.deleteCategory(item)
      },

      /* API Logic for CRUD Functions. */
      getCategories () {
        axios.get(`https://handpicked-refreshments.herokuapp.com/api/category/all`).then(response => {
          this.categories = response.data
        })
      },

      postCategory (item) {
        axios.post(`https://handpicked-refreshments.herokuapp.com/api/category/`, {
          name: item.name,
          imageURL: item.pictureURL
        },
        { headers: { 'Content-Type': 'application/x-www-form-urlencoded' } }
        ).then(function (response) {
          console.log(response)
        }).catch(function (error) {
          console.log(error)
        })
        this.close()
      },

      editCategory (item) {
        axios.put(`https://handpicked-refreshments.herokuapp.com/api/category` + item.categoryID, {
          name: item.name,
          pictureURL: item.pictureURL
        },
        { headers: { 'Content-Type': 'application/x-www-form-urlencoded' } }
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