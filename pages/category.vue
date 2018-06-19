<template>
  <div>
    <v-dialog v-model="createD" persistent max-width="600px">
        <v-card>
          <v-card-title>
              <span class="headline">Categorie toevoegen</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field v-model="item.name" label="Naam" required></v-text-field>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-select :items="days" v-model="item.beginDay" label="Start beschikbaarheid"></v-select>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-dialog ref="cModalBeginTime" v-model="cModalBeginTime" :return-value.sync="item.beginTime" persistent lazy full-width width="290px">
                    <v-text-field slot="activator" v-model="item.beginTime" label="Starttijd beschikbaarheid" readonly></v-text-field>
                    <v-time-picker format="24hr" v-model="item.beginTime" actions>
                      <v-spacer></v-spacer>
                      <v-btn flat color="primary" @click="cModalBeginTime = false">Annuleren</v-btn>
                      <v-btn flat color="primary" @click="$refs.cModalBeginTime.save(item.beginTime)">Opslaan</v-btn>
                    </v-time-picker>
                  </v-dialog>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-select :items="days" v-model="item.endDay" label="Einde beschikbaarheid"></v-select>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-dialog ref="cModalEndTime" v-model="cModalEndTime" :return-value.sync="item.endTime" persistent lazy full-width width="290px">
                    <v-text-field slot="activator" v-model="item.endTime" label="Einde beschikbaarheid" readonly></v-text-field>
                    <v-time-picker format="24hr" v-model="item.endTime" actions>
                      <v-spacer></v-spacer>
                      <v-btn flat color="primary" @click="cModalEndTime = false">Annuleren</v-btn>
                      <v-btn flat color="primary" @click="$refs.cModalEndTime.save(item.endTime)">Opslaan</v-btn>
                    </v-time-picker>
                  </v-dialog>
                </v-flex>
                <v-flex xs12>
                  <v-text-field style="display: none;" v-model="item.imageURL" label="Afbeelding" required></v-text-field>
                </v-flex>
                <v-flex xs6>
                  <v-text-field label="Selecteer afbeelding" @click='pickFile' v-model='imageName' required> </v-text-field>
                  <input type="file" style="display: none" ref="image" accept="image/*" @change="onFilePicked" required>
                </v-flex>
                <v-flex class="text-xs-center" xs6>
                  <img :src="imageUrl" height="150" v-if="imageUrl"/>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-flex xs12 sm6 md6>
              <v-btn color="blue darken-1" flat @click="removeAvailability(item)">Beschikbaarheid verwijderen</v-btn>
            </v-flex>
            <v-flex xs12 sm6 md6>
              <v-btn color="blue darken-1" flat @click.native="createD = false">Annuleren</v-btn>
              <v-btn color="blue darken-1" flat @click="createP(item)">Opslaan</v-btn>
            </v-flex>
          </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="editD" persistent max-width="600px">
        <v-card>
          <v-card-title>
              <span class="headline">Categorie bewerken</span>
          </v-card-title>
          <v-card-text>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field v-model="item.name" label="Naam"></v-text-field>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-select :items="days" v-model="item.beginDay" label="Start beschikbaarheid"></v-select>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-dialog ref="eModalBeginTime" v-model="eModalBeginTime" :return-value.sync="item.beginTime" persistent lazy full-width width="290px">
                    <v-text-field slot="activator" v-model="item.beginTime" label="Starttijd beschikbaarheid"></v-text-field>
                    <v-time-picker format="24hr" v-model="item.beginTime" actions>
                      <v-spacer></v-spacer>
                      <v-btn flat color="primary" @click="eModalBeginTime = false">Annuleren</v-btn>
                      <v-btn flat color="primary" @click="$refs.eModalBeginTime.save(item.beginTime)">Opslaan</v-btn>
                    </v-time-picker>
                  </v-dialog>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-select :items="days" v-model="item.endDay" label="Einde beschikbaarheid"></v-select>
                </v-flex>
                <v-flex xs12 sm6 md6>
                  <v-dialog ref="eModalEndTime" v-model="eModalEndTime" :return-value.sync="item.endTime" persistent lazy full-width width="290px">
                    <v-text-field slot="activator" v-model="item.endTime" label="Einde beschikbaarheid"></v-text-field>
                    <v-time-picker format="24hr" v-model="item.endTime" actions>
                      <v-spacer></v-spacer>
                      <v-btn flat color="primary" @click="eModalEndTime = false">Annuleren</v-btn>
                      <v-btn flat color="primary" @click="$refs.eModalEndTime.save(item.endTime)">Opslaan</v-btn>
                    </v-time-picker>
                  </v-dialog>
                </v-flex>
                <v-flex xs12>
                  <v-text-field style="display: none;" v-model="item.imageURL" label="Afbeelding"></v-text-field>
                </v-flex>
                <v-flex xs6>
                  <v-text-field label="Selecteer afbeelding" @click='pickFile' v-model='imageName'> </v-text-field>
                  <input type="file" style="display: none" ref="image" accept="image/*" @change="onFilePicked">
                </v-flex>
                <v-flex class="text-xs-center" xs6>
                  <img :src="imageUrl" height="150" v-if="imageUrl"/>
                </v-flex>
              </v-layout>
            </v-container>
          </v-card-text>
          <v-card-actions>
            <v-spacer></v-spacer>
            <v-flex xs12 sm6 md6>
              <v-btn color="blue darken-1" flat @click="removeAvailability(item)">Beschikbaarheid verwijderen</v-btn>
            </v-flex>
            <v-flex xs12 sm6 md6>
              <v-btn color="blue darken-1" flat @click.native="editD = false">Annuleren</v-btn>
              <v-btn color="blue darken-1" flat @click="updateP(item)">Opslaan</v-btn>
            </v-flex>
          </v-card-actions>
        </v-card>
    </v-dialog>

    <v-dialog v-model="deleteD" persistent max-width="290px">
      <v-card>
        <v-card-title class="headline">Categorie verwijderen</v-card-title>
        <v-card-text>Weet u zeker dat u de geselecteerde categorie wilt verwijderen?</v-card-text>
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
        <v-btn color="primary" @click="createItem(item)">Nieuwe categorie <v-icon dark> add</v-icon></v-btn>
      </v-flex>
      <v-spacer></v-spacer>
      <v-text-field v-model="search" append-icon="search" label="Zoeken..." single-line hide-details></v-text-field>
    </v-card-title>   
    <v-data-table :loading="loading" :headers="headers" :items="categories" :search="search" hide-actions class="elevation-1">
      <v-progress-linear slot="progress" color="blue" indeterminate></v-progress-linear>
        <template slot="items" slot-scope="props" >
          <td class="text-xs-left"></td>
          <td class="text-xs-left">{{ props.item.categoryID }}</td>
          <td class="text-xs-left">{{ props.item.name }}</td>
          <td class="text-xs-left">{{ formatTimeOrDay(formatNumberToDay(props.item.beginDay), formatNumberToDay(props.item.endDay)) }}</td>
          <td class="text-xs-left">{{ formatTimeOrDay(props.item.beginTime, props.item.endTime) }}</td>
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
          <div style="display: none;" class="text-md-center" :value="refreshBtn">
            <v-btn round color="primary" @click="getCategories" ><v-icon>refresh</v-icon> Vernieuwen</v-btn>
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
      search: '',
      loading: true,
      refreshBtn: false,
      saveBtn: true,
      imageName: '',
      imageUrl: '',
      imageFile: '',
      cModalBeginTime: false,
      cModalEndTime: false,
      eModalBeginTime: false,
      eModalEndTime: false,
      headers: [
        {
          align: 'center',
          sortable: false,
          value: 'name'
        },
        { text: 'Categorie ID', value: 'categoryID' },
        { text: 'Naam', value: 'name' },
        { text: 'Beschikbaarheidsdagen', value: 'beginDay & endDay' },
        { text: 'Beschikbaarheidstijden', value: 'beginTime & endTime' },
        { text: 'Afbeelding', value: 'imageURL' },
        { text: 'Acties', value: 'name', sortable: false }
      ],
      days: [
        { text: 'Selecteer dag', value: null },
        { text: 'Maandag', value: 0 },
        { text: 'Dinsdag', value: 1 },
        { text: 'Woensdag', value: 2 },
        { text: 'Donderdag', value: 3 },
        { text: 'Vrijdag', value: 4 },
        { text: 'Zaterdag', value: 5 },
        { text: 'Zondag', value: 6 }
      ],
      categories: [],
      itemIndex: -1,
      item: {
        categoryID: 0,
        name: '',
        imageURL: '',
        beginDay: null,
        endDay: null,
        beginTime: null,
        endTime: null
      },
      defaultItem: {
        categoryID: 0,
        name: '',
        imageURL: '',
        beginDay: null,
        endDay: null,
        beginTime: null,
        endTime: null
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
      createItem (item) {
        this.createD = true
        this.getCategories()
      },

      editItem (item) {
        this.itemIndex = this.categories.indexOf(item)
        this.item = Object.assign({}, item)
        this.editD = true
        this.imageUrl = this.item.imageURL
        this.getCategories()
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
        this.imageName = null
        this.imageUrl = null
        this.saveBtn = true
        this.getCategories()
        setTimeout(() => {
          this.item = Object.assign({}, this.defaultItem)
          this.itemIndex = -1
        }, 300)
      },

      createP (item) {
        this.postCategory(item)
      },

      updateP (item) {
        this.updateCategory(item)
      },

      deleteP (item) {
        this.deleteCategory(item)
      },

      removeAvailability (item) {
        item.beginDay = null
        item.endDay = null
        item.beginTime = null
        item.endTime = null
      },

      /* API Logic for CRUD Functions. */
      getCategories () {
        this.loading = true
        this.refreshBtn = false
        axios.get('https://handpicked-refreshments.herokuapp.com/api/category/all')
          .then(response => {
            this.categories = response.data
            this.loading = false
          })
          .catch(error => {
            console.log(error)
            this.refreshBtn = false
          })
      },

      formatNumberToDay (item) {
        let dayParse = item
        switch (dayParse) {
          case 0:
            dayParse = 'Maandag'
            break
          case 1:
            dayParse = 'Dinsdag'
            break
          case 2:
            dayParse = 'Woensdag'
            break
          case 3:
            dayParse = 'Donderdag'
            break
          case 4:
            dayParse = 'Vrijdag'
            break
          case 5:
            dayParse = 'Zaterdag'
            break
          case 6:
            dayParse = 'Zondag'
            break
        }
        return dayParse
      },

      formatTimeOrDay (begin, end) {
        let format = `${begin} / ${end}`
        if (begin === null && end === null) {
          format = ''
        } else {
          format = `${begin} t/m ${end}`
        }
        return format
      },

      postCategory (item) {
        const data = {
          name: item.name,
          imageURL: item.imageURL,
          beginDay: item.beginDay,
          beginTime: item.beginTime,
          endDay: item.endDay,
          endTime: item.endTime
        }
        const header = {
          ContentType: 'application/x-www-form-urlencoded',
          Accept: 'application/json'
        }
        axios.post('https://handpicked-refreshments.herokuapp.com/api/category/', data, header)
          .then(response => {
            this.getCategories()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      updateCategory (item) {
        const data = {
          name: item.name,
          imageURL: item.imageURL,
          beginDay: item.beginDay,
          beginTime: item.beginTime,
          endDay: item.endDay,
          endTime: item.endTime
        }
        const header = {
          ContentType: 'application/x-www-form-urlencoded',
          Accept: 'application/json'
        }
        axios.put('https://handpicked-refreshments.herokuapp.com/api/category/' + item.categoryID, data, header)
          .then(response => {
            this.getCategories()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      deleteCategory (item) {
        axios.delete('https://handpicked-refreshments.herokuapp.com/api/category/' + item.categoryID)
          .then(response => {
            this.getCategories()
            this.close()
          })
          .catch(error => {
            console.log(error)
          })
      },

      pickFile () {
        this.$refs.image.click()
      },

      onFilePicked (e) {
        const files = e.target.files
        if (files[0] !== undefined) {
          this.imageName = files[0].name
          if (this.imageName.lastIndexOf('.') <= 0) {
            return
          }
          const fr = new FileReader()
          fr.readAsDataURL(files[0])
          fr.addEventListener('load', () => {
            this.imageUrl = fr.result
            this.imageFile = files[0]
            /* Betere image afhandeling */
            this.fileUpload()
          })
        } else {
          this.imageName = ''
          this.imageFile = ''
          this.imageUrl = ''
        }
      },

      fileUpload () {
        const fd = new FormData()
        fd.append('fileUpload', this.imageFile, this.imageFile.name)
        axios.post('https://handpicked-refreshments.herokuapp.com/api/upload/', fd)
          .then(response => {
            this.item.imageURL = response.data
            this.saveBtn = false
          })
          .catch(error => {
            console.log(error)
          })
      }
    }
  }
</script>