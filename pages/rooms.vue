<template>
  <div>
    <v-dialog v-model="createD" persistent max-width="500px">
        <v-card>
          <v-card-title>
              <span class="headline">Vergaderruimte toevoegen</span>
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
              <span class="headline">Vergaderruimte bewerken</span>
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
            <v-flex xs12 sm9 md9>
              <v-btn color="blue darken-1" :style="removeBtnDisplay" :disabled="removeBtn" flat @click="deleteTabletItem(item)">Tablet verwijderen</v-btn>
            </v-flex>
            <v-flex xs12 sm9 md9>
              <v-btn color="blue darken-1" flat @click.native="editD = false">Annuleren</v-btn>
              <v-btn color="blue darken-1" flat @click="updateP(item)">Opslaan</v-btn>
            </v-flex>
          </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="deleteTabletD" persistent max-width="450px">
      <v-card>
        <v-card-title class="headline">Tablet {{ item.tabletName }} uit {{ item.name }} verwijderen</v-card-title>
        <v-card-text>Weet u zeker dat u de tablet {{ item.tabletName }} uit de vergaderruimte {{ item.name }} wilt verwijderen?</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="deleteTabletD = false">Annuleren</v-btn>
          <v-btn color="blue darken-1" flat @click="deleteTabletP(item)">Verwijderen</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="deleteD" persistent max-width="290px">
      <v-card>
        <v-card-title class="headline">Vergaderruimte Verwijderen</v-card-title>
        <v-card-text>Weet u zeker dat de geselecteerde vergaderruimte wilt verwijderen?</v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="deleteD = false">Annuleren</v-btn>
          <v-btn color="blue darken-1" flat @click="deleteP(item)">Verwijderen</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-card-title>
      <v-flex style="margin-top: 15px;" xs12 sm6 text-xs-left>
        <v-btn color="primary" @click="createItem()">Nieuwe vergaderruimte <v-icon dark> add</v-icon></v-btn>
      </v-flex>
      <v-spacer></v-spacer>
      <v-text-field v-model="search" append-icon="search" label="Zoeken..." single-line hide-details></v-text-field>
    </v-card-title>   
    <v-data-table :loading="loading" :headers="headers" :items="rooms" :search="search" hide-actions class="elevation-1">
      <v-progress-linear slot="progress" color="blue" indeterminate></v-progress-linear>
        <template slot="items" slot-scope="props" >
          <td class="text-xs-left"></td>
          <td class="text-xs-left">{{ props.item.meetingRoomID }}</td>
          <td class="text-xs-left">{{ props.item.name }}</td>
          <td class="text-xs-left">{{ props.item.tabletName }}</td>
          <td class="justify-left layout px-0">
            <v-btn icon class="mx-0" @click="editItem(props.item)">
              <v-icon color="teal">edit</v-icon>
            </v-btn>
            <v-btn icon class="mx-0" @click="deleteItem(props.item)">
              <v-icon color="red">delete</v-icon>
            </v-btn>
          </td>
        </template>
        <template slot="no-data">
          <div style="display: none;" class="text-md-center" :value="refreshBtn">
            <v-btn round color="primary" @click="getRooms" ><v-icon>refresh</v-icon> Vernieuwen</v-btn>
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
      deleteTabletD: false,
      search: ``,
      loading: true,
      refreshBtn: false,
      removeBtn: false,
      removeBtnDisplay: null,
      headers: [
        {
          align: `center`,
          sortable: false,
          value: `name`
        },
        { text: `Vergaderruimte ID`, value: `meetingRoomID` },
        { text: `Naam`, value: `name` },
        { text: `Tablet`, value: `tabletName` },
        { text: `Acties`, value: `name`, sortable: false }
      ],
      rooms: [],
      tablets: [],
      itemIndex: -1,
      item: {
        meetingRoomID: 0,
        name: ``,
        tabletName: ``,
        hardwareID: ``
      },
      defaultItem: {
        meetingRoomID: 0,
        name: ``,
        tabletName: ``,
        hardwareID: ``
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
      this.getRooms()
    },

    methods: {
      /* UI Logic for CRUD Functions. */
      createItem () {
        this.createD = true
      },

      editItem (item) {
        this.itemIndex = this.rooms.indexOf(item)
        this.item = Object.assign({}, item)
        this.editD = true
        if (item.hardwareID === null) {
          this.removeBtn = true
          this.removeBtnDisplay = `display: none;`
        }
      },

      deleteItem (item) {
        this.itemIndex = this.rooms.indexOf(item)
        this.item = Object.assign({}, item)
        this.deleteD = true
      },

      deleteTabletItem (item) {
        this.itemIndex = this.rooms.indexOf(item)
        this.item = Object.assign({}, item)
        this.deleteTabletD = true
      },

      close () {
        this.createD = false
        this.editD = false
        this.deleteD = false
        this.deleteTabletD = false
        this.getRooms()
        setTimeout(() => {
          this.item = Object.assign({}, this.defaultItem)
          this.itemIndex = -1
          this.removeBtn = false
          this.removeBtnDisplay = null
        }, 300)
      },

      createP (item) {
        this.postRoom(item)
      },

      updateP (item) {
        this.updateRoom(item)
      },

      deleteP (item) {
        this.deleteRoom(item)
      },

      deleteTabletP (item) {
        this.deleteTabletInRoom(item)
      },

      /* API Logic for CRUD Functions. */
      getRooms () {
        this.loading = true
        this.refreshBtn = false
        axios.get(`https://handpicked-refreshments.herokuapp.com/api/room/`)
          .then(response => {
            this.rooms = response.data
            this.loading = false
          })
          .catch(error => {
            console.log(error)
            this.refreshBtn = false
          })
      },

      getTablets () {
        axios.get(`https://handpicked-refreshments.herokuapp.com/api/tablet`)
          .then(response => {
            for (let i = 0; i < response.data.length; i++) {
              this.tablets.push({ value: response.data[i].tabletID, text: response.data[i].tabletName })
            }
          })
          .catch(error => {
            console.log(error)
          })
      },

      postRoom (item) {
        const data = {
          name: item.name
        }
        const header = {
          ContentType: `application/x-www-form-urlencoded`,
          Accept: `application/json`
        }
        axios.post(`https://handpicked-refreshments.herokuapp.com/api/room/`, data, header)
          .then(response => {
            this.getRooms()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      updateRoom (item) {
        const data = {
          name: item.name
        }
        const header = {
          ContentType: `application/x-www-form-urlencoded`,
          Accept: `application/json`
        }
        axios.put(`https://handpicked-refreshments.herokuapp.com/api/room/${item.meetingRoomID}`, data, header)
          .then(response => {
            this.getRooms()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      deleteRoom (item) {
        axios.delete(`https://handpicked-refreshments.herokuapp.com/api/room/${item.meetingRoomID}`)
          .then(response => {
            this.getRooms()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      deleteTabletInRoom (item) {
        const data = { data: { hardwareID: item.hardwareID } }
        const header = {
          ContentType: `application/x-www-form-urlencoded`,
          Accept: `application/json`
        }
        axios.delete(`https://handpicked-refreshments.herokuapp.com/api/room/tablet/delete`, data, header)
          .then(response => {
            this.getRooms()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      }
    }
  }
</script>