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
            <v-flex xs12>
                
                
            <h1>Price</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
  <th class="text-left">DATE</th>
   <th class="text-left">SLOT</th>

  

   <th class="text-left">QUANTITY</th>
  <th class="text-left">PRICE</th>
  <th class="text-left">STRIKE PRICE</th>
  <th class="text-left">SELLER PRICE</th>
     <th class="text-left">EDIT DATA</th>
      <th class="text-left">VIEW DATA</th>
            <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="data in allPrice" v-bind:key="data._id">
 <td>{{data.date}}</td>
 <td>{{data.slot}}</td>


 <td>{{data.quantity}}</td>
  <td>{{data.price}}</td>
  <td>{{data.strikePrice}}</td>
  <td>{{data.sellerPrice}}</td>
  <td><a  class="text-success" v-on:click="editTask(data),showAddModal=true,enumerateDaysBetweenDates(addPrice.startDate,addPrice.endDate)">Edit</a></td>
   <td><a  class="text-success"   >View</a></td>
    <td><a class="text-danger"  @click="deleteTask(data._id)">Delete</a></td>
         
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
var moment =require('moment');
export default {
  name: "Price",
    data(){
   return{
      search:"",
      allSellers:[],
      moment:moment,
      isEdit: false,
      showAddModal: false,
     seller:this.$route.params.id,
     frequency:"",
     date:[],
      addPrice:{id:"",ticket:"",startDate:"",endDate:"",startTime:"",endTime:"",quantity:"",price:"",slots:"",strikePrice:"",sellerPrice:""},
      allPrice:[],
      headers: {
       
        headers:{
        
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      
    
    }
    },
      mounted() {
     this.getTicketsForsellers()
     this.getFrequency()
    
     const url = window.location.href;
     this.addPrice.ticket =url.split("/").slice(-1)[0];
      this.getSlotPrice()
     
     
    
  
 
  },
   methods: {
      enumerateDaysBetweenDates(startDate,endDate){
      this.date=[]
        while(moment(startDate)<=moment(endDate)){
         this.date.push(startDate);
          startDate=moment(startDate).add(1,'days').format("YYYY-MM-DD");
          
        }
        
        
        
      
      },
  getTicketsForsellers()
       {
      this.$axios.get("/api/sellers/"+this.seller)
      .then((response) => {
      console.log(response.data);
      this.allSellers=response.data;
      this.addPrice.startDate=this.allSellers.startDate.slice(0,10)
       this.addPrice.endDate=this.allSellers.endDate.slice(0,10)
    })
    },
     getFrequency()
      {
      this.$axios.get("/api/frequencies/")
      .then((response) => {
      console.log(response.data);
      this.frequency=response.data;
    })
    },
   getSlotPrice(){
        this.$axios.post("/api/slotPrices/getSlotPriceOfTicket/",{ticket:this.addPrice.ticket},this.headers)
      .then((response) => {
      console.log(response.data);
       this.allPrice=(response.data)
      
    })
    },
       add() {
       this.$axios.post("/api/slotPrices/",{ticket:this.addPrice.ticket,date:this.addPrice.startDate,quantity:this.addPrice.quantity,price:this.addPrice.price,slot:this.addPrice.slots,strikePrice:this.addPrice.strikePrice,sellerPrice:this.addPrice.sellerPrice},this.headers)
      .then((response) => {
       
       
        this.allPrice.push(response.data)
        
        
         console.log(response.data)
        
      })
      .catch((error) => {
        console.log(error);
      });
    
    
    },
    clear(){
      this.addPrice={quantity:"",price:"",slots:""}
    },
     editTask(data) 
    {
      this.addPrice.id =data._id
      
    
      this.addPrice.slots=data.slot
      this.addPrice.quantity=data.quantity
      this.addPrice.price=data.price
      
      
      this.isEdit=true
    },
    updateTask(){
        
      this.$axios.put("/api/slotPrices/"+this.addPrice.id,{quantity:this.addPrice.quantity,price:this.addPrice.price,slot:this.addPrice.slots,date:this.addPrice.startDate,strikePrice:this.addPrice.strikePrice,sellerPrice:this.addPrice.sellerPrice},this.headers)
      .then((response) => {
        this.getSlotPrice()
        
        this.showAddModal=false
        
        this.isEdit = false
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
      })
        },
     deleteTask(id) {
     
           this.$axios.delete("/api/slotPrices/"+id,this.headers)
           .then((response) => {
             this.allPrice =_.reject(this.allPrice,function(n){
               return n._id === id;
             })
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
   }
 
};
</script>
