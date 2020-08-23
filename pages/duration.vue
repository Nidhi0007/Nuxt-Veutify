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
          ADD DURATION
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">DURATION</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                 <v-text-field label="Type*" v-model="addDuration.type"  required></v-text-field>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
                 <v-checkbox label="isFrequent" v-model="addDuration.isFrequent" ></v-checkbox>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
           <v-checkbox label="isMultiple" v-model="addDuration.isMultiple" ></v-checkbox>
              </v-col>
              <v-col cols="12">
             <v-checkbox label="isSingle" v-model="addDuration.isSingle" ></v-checkbox>
              </v-col>
             
            
              
            </v-row>
          </v-container>
         
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="showAddModal = false">Close</v-btn>
          <v-btn v-if="this.isEdit == false" color="blue darken-1" text @click="add()">ADD</v-btn>
          <v-btn v-else color="blue darken-1" text @click="updateTask()">UPDATE</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-row>


            <v-flex xs12>
                
                
            <h1>Duration</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
 <th class="text-left">Type</th>
   <th class="text-left">isFrequent</th>
 
  <th class="text-left">isSingle</th>
   <th class="text-left">isMultiple</th>
   <th class="text-left">EDIT DATA</th>
   <th class="text-left">VIEW DATA</th>
   <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="datas in filteredPosts" v-bind:key="datas._id">
       
   <td>{{datas.type}}</td>
  <td>{{datas.isFrequent}}</td>
  <td>{{datas.isSingle}}</td>
  <td>{{datas.isMultiple}}</td>
 
  
 
   
   <td><button class="btn btn-info" v-on:click="editTask(datas.type,datas._id,datas.isFrequent,datas.isSingle,isMultiple),showAddModal=true">Edit</button></td>
   <td> <button class="btn btn-info">View</button>
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
  name: "Duration",
  data(){
    return{
      showAddModal: false,
      search:"",
      duration:[],
      addDuration : {type:"",isFrequent:false,isSingle:false,isMultiple:false},
       headers: {
       
        headers:{
         
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      isEdit: false,
      
    
    }
  },
   mounted() {
     this.getDuration()
     
  
 
  },
   computed: {
    filteredPosts() {
      return this.duration.filter(datas => datas.type.toLowerCase().includes(this.search.toLowerCase()))
    }
  },
  methods: {
      getDuration()
      {
      this.$axios.get("/api/durations/")
      .then((response) => {
      console.log(response.data);
      this.duration=response.data;
    })
    },
    add() {
   
      
      this.$axios.post("/api/durations/",this.addDuration,this.headers)
      .then((response) => {
       
        this.duration.push(response.data)
        this.clear()
        this.showAddModal=false
        
      })
      .catch((error) => {
        console.log(error);
      });
    },
     editTask(type,id,isFrequent,isSingle,isMultiple) 
    {
      this.addDuration.id =id
      this.addDuration.type=type
      this.addDuration.isFrequent=isFrequent
      this.addDuration.isSingle=isSingle
      this.addDuration.isMultiple=isMultiple
      
      this.isEdit=true
    },
    updateTask(){
        
      this.$axios.put("/api/durations/"+this.addDuration.id,this.addDuration,this.headers)
      .then((response) => {
        
        this.getDuration()
        this.clear()
        this.isEdit = false
        this.showAddModal=false
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
      })
        },
       deleteTask(id) {
     
           this.$axios.delete("/api/durations/"+id,this.headers)
           .then((response) => {
             this.duration =_.reject(this.duration,function(n){
               return n._id === id;
             })
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
  clear(){
       this.addDuration={type:"",isFrequent:false,isSingle:false,isMultiple:false}
  }
  },
  
  
};
</script>