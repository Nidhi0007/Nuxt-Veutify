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
          ADD VENUE
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Venue</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
                  <v-col cols="12" sm="6" md="4">
                <v-text-field label="Name*" v-model="addVenue.name"  required></v-text-field>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">

                        <v-select
          :items="getCities"
          label="Select city"
          item-text="name"
          v-model="city"
          item-key="_id"
          item-value="_id"
          return-object
          @change="selectChange(city._id)"

        ></v-select>
        
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field label="Phone*" v-model="addVenue.phone"  required></v-text-field>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
                 <v-text-field label="Address*" v-model="addVenue.address"  required></v-text-field>
              </v-col>
              <v-col cols="12">
             <v-text-field label="Lattitude*" v-model="addVenue.latitude"  required></v-text-field>
              </v-col>
                 <v-col cols="12">
             <v-text-field label="Longitude*" v-model="addVenue.longitude"  required></v-text-field>
              </v-col>
                <v-col cols="12">
             <v-text-field label="Landmark*" v-model="addVenue.landmark"  required></v-text-field>
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
                
                
            <h1>Venue</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
        <th class="text-left">NAME</th>  
     <th class="text-left">CITIES</th>
                  <th class="text-left">PHONE</th>
              <th class="text-left">LATTITUDE</th>

                <th class="text-left">LONGITUDE</th>
                <th class="text-left">ADDRESS</th>
                  <th class="text-left">LANDMARK</th>

                <th class="text-left">EDIT DATA</th>
                  <th class="text-left">VIEW DATA</th>
                        <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="data in filteredPosts" v-bind:key="data._id">
 <td>{{data.name}}</td>
 <td>{{data.city}}</td>
                <td>{{data.phone}}</td>
                <td>{{data.latitude}}</td>
                  <td>{{data.longitude}}</td>
                  <td>{{data.address}}</td>
                  <td>{{data.landmark}}</td>
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
import axios from 'axios';
import _ from 'lodash'
export default {
  name: "venue",
  data (){
    return{
showAddModal: false,
isEdit:false,
      getCities:[],
      city:"",
      search:"",
        addVenue:{name:"",phone:"",latitude:"",longitude:"",address:"",landmark:""},
        allVenue:[],
        headers: {
       
        headers:{
          
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}}
    }
  },
   mounted(){
    this.getVenue()
    this.getAllCities()
  },
     computed: {
    filteredPosts() {
      return this.allVenue.filter(data => data.name.toLowerCase().includes(this.search.toLowerCase()))
    }
  },
   methods: {
       getAllCities(){
      this.$axios.get("/api/cities/")
    .then((response)=>{
      console.log(response.data);
      this.getCities=response.data;
    })
  },
  getVenue(){
      this.$axios.get("/api/venues/")
    .then((response)=>{
      console.log(response.data)
      this.allVenue=response.data;
    })
  },
  selectChange(_id){
    this.city=_id
;
  },
    add() {
   
      
      this.$axios.post("/api/venues/",this.addVenue,this.headers)
      .then((response) => {
        this.allVenue.push(response.data)
        this.clear()
        this.showAddModal=false
        this.getVenue();
        this.addVenue = {name:"",phone:"",latitude:"",longitude:"",address:"",landmark:""}
           console.log(response);
        
      })
      .catch((error) => {
        console.log(error);
      });
    },
     deleteData(id) {
     
           this.$axios.delete("/api/venues/"+id,this.headers)
           .then((response) => {
             this.allVenue=_.reject(this.allVenue,function(n){
               return n._id === id;
             })
             
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
 editData(data) 
    {
      this.addVenue.id =data._id
      this.addVenue.name=data.name
      this.addVenue.phone=data.phone
         this.addVenue.latitude=data.latitude
            this.addVenue.longitude=data.longitude
               this.addVenue.address=data.address
               this.addVenue.landmark=data.landmark
      
      this.isEdit=true
    },
    updateData(){
        
      this.$axios.put("/api/venues/"+this.addVenue.id,this.addVenue,this.headers)
      .then((response) => {
        this.clear()
        this.getVenue()
        this.showAddModal=false
        this.isEdit = false
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
      })
        },
            clear(){
          this.addVenue={name:"",phone:"",latitude:"",longitude:"",address:"",landmark:""}   
  },
  
       
}
}
</script>