<template>
    <div>
 
        <v-container>
         <form>
    <v-text-field
      v-model="search"
      :error-messages="nameErrors"
      
      label="Search"
      
    ></v-text-field>
     
    
  </form>
                  <v-row justify="center">
    <v-dialog v-model="showAddModal" persistent max-width="600px">
      <template v-slot:activator="{ on, attrs }">
        <v-btn
          color="primary"
          dark
          v-bind="attrs"
          v-on="on"
        >
          ADD CITIES
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">CITIES</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field label="Name*" v-model="newCities.name" @change="ConvertToSlug" required></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field label="Slug" hint="example of helper text only on focus" v-model="newCities.slug"></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                v-model="newCities.shortDescription"
                  label="shortDescription*"
                  
                  required
                ></v-text-field>
           
              </v-col>
              <v-col cols="12">
                <input class="text-black" type="file" @change="onFileSelected" multiple>
              </v-col>
             
               <v-col cols="12" sm="6" md="4">
                <v-text-field label="lattitude" hint="example of helper text only on focus" v-model="newCities.latitude"></v-text-field>
              </v-col>
                <v-col cols="12" sm="6" md="4">
                <v-text-field label="Longitude" hint="example of helper text only on focus" v-model="newCities.longitude"></v-text-field>
              </v-col>
                  <v-col cols="12" sm="6" md="4">
                <v-text-field label="Pincode" hint="example of helper text only on focus" v-model="newCities.pincode"></v-text-field>
              </v-col>
              
              
            </v-row>
          </v-container>
         
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="showAddModal = false">Close</v-btn>
          <v-btn v-if="this.isEdit == false" color="blue darken-1" text @click="add()">ADD</v-btn>
          <v-btn v-else color="blue darken-1" text @click="updateData()">UPDATE</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>
            <v-flex xs12>
                
                
            <h1>Cities</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
     <th class="text-left">CITIES</th>
   <th class="text-left">SLUG</th>
   <th class="text-left">GALLERY</th>
   <th class="text-left">PINCODE</th>
   <th class="text-left">LATITUDE</th>
   <th class="text-left">LONGITUDE</th>
<th class="text-left">SHORT DESCRIPTION</th>
<th class="text-left">ACTIVATE</th>
<th class="text-left">MARK FEATURED</th>
     <th class="text-left">EDIT DATA</th>
      <th class="text-left">VIEW DATA</th>
            <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="data in filteredPosts" v-bind:key="data._id">
          <td>{{data.name}}</td>
 <td>{{data.slug}}</td>
 <td>{{data.gallery.length}}</td>
  <td>{{data.pincode}}</td>
 <td>{{data.latitude}}</td>
  <td>{{data.longitude}}</td>
  <td>{{data.shortDescription}}</td>
<td><a v-if="data.active==false" class="btn btn-info" v-on:click="activate(data._id)">Activate</a>
<a v-else class="btn btn-info"  v-on:click="activate(data._id)">Deactivate</a></td>
<td><a v-if="data.featured==false" class="btn btn-info" v-on:click="featured(data._id)">Mark featured</a>
<a v-else class="btn btn-info"  v-on:click="featured(data._id)">Unmark featured</a></td>
  <td><a  class="text-success" v-on:click="editData(data),showAddModal=true">Edit</a></td>
   <td><a  class="text-success"   >View</a></td>
    <td><a class="text-danger"  @click="deleteData(data._id)">Delete</a></td>
        </tr>
      </tbody>
    </template>
  </v-simple-table>
        </v-container>
    </div>
</template>
<script>
import axios from 'axios'
import _ from 'lodash'
export default {
  name: 'Cities',
  data() {
    return {
      showAddModal: false,
      showEditModal: false,

      search: [],
      allCities: [],
      newCities: {
        gallery: [],
        name: '',
        slug: '',
        shortDescription: '',
        pincode: '',
        latitude: '',
        longitude: '',
      },
      selectedFile: null,
      headers: {
        headers: {
          'Content-Type': 'multipart/form-data',
          Authorization: `bearer ${
            JSON.parse(localStorage.getItem('token') || '{}')[0].token
          }`,
        },
      },
      isEdit: false,
    }
  },
  mounted() {
    this.getCities()
  },

  computed: {
    filteredPosts() {
      return this.allCities.filter((data) =>
        data.name.toLowerCase().includes(this.search)
      )
    },
  },
  methods: {
    getCities() {
      this.$axios.get('/api/cities/').then((response) => {
        console.log(response.data)
        this.allCities = response.data
      })
    },

    onFileSelected(event) {
      this.selectedFile = event.target.files[0]
    },

    add() {
      //upload file
      const fd = new FormData()
      fd.append('name', this.newCities.name)
      fd.append('slug', this.newCities.slug)
      fd.append('latitude', this.newCities.latitude)
      fd.append('pincode', this.newCities.pincode)
      fd.append('longitude', this.newCities.longitude)
      fd.append('shortDescription', this.newCities.shortDescription)
      if (this.selectedFile) {
        fd.append('gallery', this.selectedFile, this.selectedFile.name)
      }

      this.$axios
        .post('/api/cities/', fd, this.headers)
        .then((response) => {
          this.getCities()
          this.clear()
          console.log(response)
        })
        .catch((error) => {
          console.log(error)
        })
    },

    ConvertToSlug() {
      var theSlug = this.newCities.name
      theSlug = theSlug.toLowerCase()
      theSlug = theSlug.replace(/\s+/g, '-')
      this.newCities.slug = theSlug
    },
    deleteData(id) {
      this.$axios.delete('/api/cities/' + id, this.headers).then((response) => {
        console.log(response.data)
        this.allCities = _.reject(this.allCities, function (n) {
          return n._id === id
        })
      })
    },
    editData(data) {
      this.newCities.id = data._id
      this.newCities.name = data.name
      this.newCities.slug = data.slug
      this.newCities.shortDescription = data.shortDescription
      this.newCities.gallery = data.gallery
      this.newCities.pincode = data.pincode
      this.newCities.latitude = data.latitude
      this.newCities.longitude = data.longitude
      this.isEdit = true
    },
    updateData() {
      const fd = new FormData()
      fd.append('name', this.newCities.name)
      fd.append('slug', this.newCities.slug)
      fd.append('shortDescription', this.newCities.shortDescription)
      fd.append('pincode', this.newCities.pincode)
      fd.append('latitude', this.newCities.latitude)
      fd.append('longitude', this.newCities.longitude)

      if (this.selectedFile) {
        fd.append('gallery', this.selectedFile, this.selectedFile.name)
      }
      this.$axios
        .put('/api/cities/' + this.newCities.id, fd, this.headers)
        .then((response) => {
          this.isEdit = false
          this.getCities()
          this.clear()

          console.log(response)
        })
        .catch((error) => {
          console.log(error)
        })
    },
    activate(id) {
      this.$axios
        .get('/api/cities/activate/' + id, this.headers)
        .then((response) => {
          this.getCities()
          console.log(response.data)
        })
    },
    featured(id) {
      this.$axios
        .get('/api/cities/feature/' + id, this.headers)
        .then((response) => {
          this.getCities()
          console.log(response.data)
        })
    },
    clear() {
      this.newCities = {
        gallery: '',
        name: '',
        slug: '',
        shortDescription: '',
        pincode: '',
        latitude: '',
        longitude: '',
      }
      this.showAddModal = false
    },
  },
}
</script>