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
          ADD TICKETS
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Tickets</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
              <v-col cols="12" sm="6" md="4">
                <v-text-field label="Title*" v-model="addTicketSeller.title"  required></v-text-field>
              </v-col>
            
              <v-col cols="12" sm="6" md="4">
                <v-text-field
                v-model="addTicketSeller.shortDescription"
                  label="shortDescription*"
                  
                  required
                ></v-text-field>
                <v-select
          :items="getCities"
          label="Select ticket type"
          item-text="title"
          v-model="addTicketSeller.ticketType"
          item-key="_id"
          item-value="_id"
          return-object
          @change="selectChange(addTicketSeller.ticketType._id)"

        ></v-select>
        
              </v-col>
           <v-col cols="12" sm="6" md="4">
                <v-text-field label="Inclusion*" v-model="addTicketSeller.inclusion" required></v-text-field>
              </v-col>
            
             <v-col cols="12" sm="6" md="4">
                <v-text-field label="Exclusion*" v-model="addTicketSeller.exclusion" required></v-text-field>
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
                
                
            <h1>Tickets</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
   <th class="text-left">TITLE</th>
   <th class="text-left">SHORTDESCRIPTION</th>
   <th class="text-left">INCLUSION</th>
   <th class="text-left">EXCLUSION</th>

   <th class="text-left">EDIT DATA</th>
   <th class="text-left">ADD PRICE</th>
   <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="datas in allTicketsForsellers" v-bind:key="datas._id">
  <td>{{datas.title}}</td>
  <td>{{datas.shortDescription}}</td>
  <td>{{datas.inclusion}}</td>
   <td>{{datas.exclusion}}</td>
   
  
 
   
   <td><button class="btn btn-info" v-on:click="editTask(datas),showAddModal=true">Edit</button></td>
    <td> <router-link :to="`/price/${$route.params.id}/${datas._id}`"><button class="btn btn-info">ADD PRICE</button></router-link>
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
  name: "Ticket",
  data(){
    return{
      search:"",
      allTicketsForsellers:[],
      allTicketTypes:[],
      isEdit: false,
      showAddModal: false,
     addTicketSeller:{title:"",shortDescription:"",inclusion:"",exclusion:"",seller:this.$route.params.id,ticketType:""},
      
       headers: {
       
        headers:{
         
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      
    
    }
    },
   mounted() {
     this.getTicketsForsellers()
     this.getAllTicketTypes()
  
 
  },
   methods: {
  getTicketsForsellers()
      {
      this.$axios.post("/api/tickets/ticketsOfSeller",{seller:this.addTicketSeller.seller},this.headers)
      .then((response) => {
      console.log(response.data);
      this.allTicketsForsellers=response.data;
    })
    },
     getAllTicketTypes()
      {
      this.$axios.get("/api/ticketTypes/")
      .then((response) => {
      console.log(response.data);
      this.allTicketTypes=response.data;
    })
    },
     selectChangeTicket(event){
      this.addTicketSeller.ticketType=event.target.value;
      
  },
      
    add() {
   
      
      this.$axios.post("/api/tickets/",this.addTicketSeller,this.headers)
      .then((response) => {
        this.addTicketSeller = {title:"",shortDescription:"",inclusion:"",exclusion:"",seller:this.$route.params.id,ticketType:""}
        this.allTicketsForsellers.push(response.data)
        
      })
      .catch((error) => {
        console.log(error);
      });
    },
     deleteTask(id) {
     
           this.$axios.delete("/api/tickets/"+id,this.headers)
           .then((response) => {
             this.allTicketsForsellers =_.reject(this.allTicketsForsellers,function(n){
               return n._id === id;
             })
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
   editTask(data) 
    {
      this.addTicketSeller.id =data._id
      this.addTicketSeller.title=data.title
      this.addTicketSeller.shortDescription=data.shortDescription
      this.addTicketSeller.inclusion=data.inclusion
      this.addTicketSeller.exclusion=data.exclusion
       this.addTicketSeller.seller=data.seller
      this.addTicketSeller.ticketType=data.ticketType
      
      
      this.isEdit=true
    },
    updateTask(){
        
      this.$axios.put("/api/tickets/"+this.addTicketSeller.id,this.addTicketSeller,this.headers)
      .then((response) => {
        this.getTicketsForsellers()
        this.clear()
        this.showAddModal=false
        
        this.isEdit = false
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
      })
        },
clear(){
   this.addTicketSeller = {title:"",inclusion:"",exclusion:"",shortDescription:"",seller:this.$route.params.id}
}
  }
  
};
</script>