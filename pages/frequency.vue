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
          ADD FREQUENCY
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Frequency</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                 <v-text-field label="Type*" v-model="addFrequency.type"  required></v-text-field>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
                 <v-checkbox label="isDaily" v-model="addFrequency.isDaily" ></v-checkbox>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
           <v-checkbox label="isWeekly" v-model="addFrequency.isWeekly" ></v-checkbox>
              </v-col>
              <v-col cols="12">
             <v-checkbox label="isMonthly" v-model="addFrequency.isMonthly" ></v-checkbox>
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
                
                
            <h1>Frequency</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
  <th class="text-left">Type</th>
   <th class="text-left">isDaily</th>
  <th class="text-left">isWeekly</th>
  <th class="text-left">isMonthly</th>
   <th class="text-left">EDIT DATA</th>
   <th class="text-left">VIEW DATA</th>
   <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="datas in frequency" v-bind:key="datas._id">
 <td>{{datas.type}}</td>
  <td>{{datas.isDaily}}</td>
  <td>{{datas.isWeekly}}</td>
   <td>{{datas.isMonthly}}</td>
    
  
 
  
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
  name: "Frequency",
  data(){
    return{
      showAddModal: false,
      search:"",
      frequency:[],
      addFrequency : {type:"",isDaily:false,isWeekly:false,isMonthly:false},
       headers: {
       
        headers:{
          
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      isEdit: false,
      
    
    }
  },
   mounted() {
     this.getFrequency()
     
  
 
  },
  methods: {
      getFrequency()
      {
      this.$axios.get("/api/frequencies/")
      .then((response) => {
      console.log(response.data);
      this.frequency=response.data;
    })
    },
      add() {
   
      
      this.$axios.post("/api/frequencies/",this.addFrequency,this.headers)
      .then((response) => {
        this.clear()
        this.showAddModal=false
        
        this.frequency.push(response.data)
        
      })
      .catch((error) => {
        console.log(error);
      });
    },
    editTask(datas) 
    {
      this.addFrequency.id =datas._id
      this.addFrequency.type=datas.type
      this.addFrequency.isDaily=datas.isDaily
      this.addFrequency.isWeekly=datas.isWeekly
      this.addFrequency.isMonthly=datas.isMonthly
      this.isEdit=true
    },
    updateTask(){
        
      this.$axios.put("/api/frequencies/"+this.addFrequency.id,this.addFrequency,this.headers)
      .then((response) => {
        this.clear()
        this.showAddModal=false
        this.getFrequency()
        this.isEdit = false
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
      })
        },
     deleteTask(id) {
     
           this.$axios.delete("/api/frequencies/"+id,this.headers)
           .then((response) => {
             this.frequency =_.reject(this.frequency,function(n){
               return n._id === id;
             })
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
  clear(){
    this.addFrequency = {type:"",isDaily:false,isWeekly:false,isMonthly:false}
  }
  }
 
};
</script>