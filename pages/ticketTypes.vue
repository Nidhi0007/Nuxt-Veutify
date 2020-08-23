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
          ADD Ticket Type
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Ticket Type</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
          <v-col cols="12" sm="6" md="4">
                 <v-text-field label="Type*" v-model="addTicketType.type"  required></v-text-field>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
                 <v-checkbox label="isActivity" v-model="addTicketType.isActivity" ></v-checkbox>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
           <v-checkbox label="isService" v-model="addTicketType.isService" ></v-checkbox>
              </v-col>
              <v-col cols="12">
             <v-checkbox label="isProduct" v-model="addTicketType.isProduct" ></v-checkbox>
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
  <th class="text-left">Title</th>
   <th class="text-left">isActivity</th>
 
  <th class="text-left">isService</th>
   <th class="text-left">isProduct</th>
   <th class="text-left">EDIT DATA</th>
   <th class="text-left">VIEW DATA</th>
   <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
 <tr v-for="datas in allTicketTypes" v-bind:key="datas._id">
 <td>{{datas.title}}</td>
  <td>{{datas.isActivity}}</td>
  <td>{{datas.isService}}</td>
  <td>{{datas.isProduct}}</td>
 
  
 
   
   <td><button class="btn btn-info" v-on:click="editTask(datas),showAddModal=true">Edit</button></td>
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
import _ from 'lodash'
import axios from 'axios';
export default {
  name: "Ticket",
   data(){
    return{
        
      showAddModal: false,
      search:"",
      allTicketTypes:[],
      addTicketType : {id:"",title:"",isActivity:false,isService:false,isProduct:false},
       headers: {
       
        headers:{
          
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      isEdit: false,
      
    
    }
  },
  mounted() {
     this.getAllTicketTypes()
     
  
 
  },
  methods: {
       getAllTicketTypes()
      {
      this.$axios.get("/api/ticketTypes/")
      .then((response) => {
      console.log(response.data);
      this.allTicketTypes=response.data;
    })
    },
   add() {
   
      
      this.$axios.post("/api/ticketTypes/",this.addTicketType,this.headers)
      .then((response) => {
       
        this.allTicketTypes.push(response.data)
        
        this.showAddModal=false
        
      })
      .catch((error) => {
        console.log(error);
      });
    },
      editTask(data) 
    {
      this.addTicketType.id =data._id
      this.addTicketType.title=data.title
      this.addTicketType.isActivity=data.isActivity
      this.addTicketType.isService=data.isService
      this.addTicketType.isProduct=data.isProduct
      
      this.isEdit=true
    },
    updateTask(){
        
      this.$axios.put("/api/ticketTypes/"+this.addTicketType.id,this.addTicketType,this.headers)
      .then((response) => {
        
        this.getAllTicketTypes()
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
     
           this.$axios.delete("/api/ticketTypes/"+id,this.headers)
           .then((response) => {
             this.allTicketTypes =_.reject(this.allTicketTypes,function(n){
               return n._id === id;
             })
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
  clear(){
       this.addTicketType={id:"",title:"",isActivity:false,isService:false,isProduct:false}
  }
  
  }
 
};
</script>
