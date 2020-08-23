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
          ADD Time Type
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Time Type</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
          <v-col cols="12" sm="6" md="4">
                 <v-text-field label="Type*" v-model="addTimeTypes.type"  required></v-text-field>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
                 <v-checkbox label="isToFrom" v-model="addTimeTypes.isToFrom" ></v-checkbox>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
           <v-checkbox label="isSlot" v-model="addTimeTypes.isSlot" ></v-checkbox>
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
                
                
            <h1>Time Types</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
  <th class="text-left">Type</th>
   <th class="text-left">isToFrom</th>
  <th class="text-left">isSlot</th>

   <th class="text-left">EDIT DATA</th>
   <th class="text-left">VIEW DATA</th>
   <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
     <tr v-for="datas in filteredPosts" v-bind:key="datas._id">
 <td>{{datas.type}}</td>
  <td>{{datas.isToFrom}}</td>
  <td>{{datas.isSlot}}</td>

    
  
 
   
   <td><button class="btn btn-info" v-on:click="editTask(datas),showAddModal=true">Edit</button></td>
  <td><button class="btn btn-info">View</button>
</td>
  <td><button class="btn btn-info" v-on:click="deleteTask(datas._id)">Delete</button></td>
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
  name: "Time",
  data(){
    return{
      showAddModal: false,
      search:"",
      timeTypes:[],
      addTimeTypes : {type:"",isToFrom:false,isSlot:false},
       headers: {
       
        headers:{
          
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      isEdit: false,
      
    
    }
  },
   mounted() {
     this.getTimeTypes()
     
  
 
  },
   computed: {
    filteredPosts() {
      return this.timeTypes.filter(datas => datas.type.toLowerCase().includes(this.search.toLowerCase()))
    }
  },
  methods: {
      getTimeTypes()
      {
      this.$axios.get("/api/timeTypes/")
      .then((response) => {
      console.log(response.data);
      this.timeTypes=response.data;
    })
    },
      add() {
   
      
      this.$axios.post("/api/timeTypes/",this.addTimeTypes,this.headers)
      .then((response) => {
        
        this.timeTypes.push(response.data)
        this.showAddModal=false
        this.addTimeTypes = {type:"",isToFrom:false,isSlot:false}
        
      })
      .catch((error) => {
        console.log(error);
      });
    },
    editTask(datas) 
    {
      this.addTimeTypes.id =datas._id
      this.addTimeTypes.type=datas.type
      this.addTimeTypes.isToFrom=datas.isToFrom
      this.addTimeTypes.isSlot=datas.isSlot
      this.isEdit=true
    },
    updateTask(){
        
      this.$axios.put("/api/timeTypes/"+this.addTimeTypes.id,this.addTimeTypes,this.headers)
      .then((response) => {
        
        this.getTimeTypes()
        this.isEdit = false
        this.showAddModal=false
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
      })
        },
     deleteTask(id) {
     
           this.$axios.delete("/api/timeTypes/"+id,this.headers)
           .then((response) => {
             this.timeTypes =_.reject(this.timeTypes,function(n){
               return n._id === id;
             })
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
     clear(){
       this.addTimeTypes ={type:"",isSlot :false, isToFrom:false}
  }
  }
 
};
</script>
