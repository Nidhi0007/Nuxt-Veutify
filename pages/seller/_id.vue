<template>
    <div>
 
        <v-container>


            <v-flex xs12>
                
                
            <h1>Sellers</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>

  <th class="text-left">ACTIVITY</th>
   <th class="text-left">VENUE</th>
  <th class="text-left">DURATION</th>
   <th class="text-left">FREQUENCY</th>
    <th class="text-left">START DATE</th>
      <th class="text-left">END DATE</th>
        <th class="text-left">START TIME</th>
          <th class="text-left">ENDTIME</th>
          <th class="text-left">SLOTS</th>
          <th class="text-left">TICKETS</th>
        <th class="text-left">ACTIVATE</th>
<th class="text-left">MARK FEATURED</th>
   <th class="text-left">EDIT DATA</th>
   <th class="text-left">VIEW DATA</th>
   <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="sellers in allSellers" v-bind:key="sellers._id" v-bind:value="sellers._id">
<td>{{sellers.activity.title}}</td>
 <td>{{sellers.venue.name}}</td>
 <td>{{sellers.duration.type}}</td>
 <td>{{sellers.frequency}}</td>
 <td>{{sellers.startDate}}</td>
 <td>{{sellers.endDate}}</td>
 <td>{{sellers.startTime}}</td>
 <td>{{sellers.endTime}}</td>
 <td>{{sellers.slots}}</td>
  <td>{{sellers.tickets.length}}</td>
 
  
   <td><a v-if="sellers.active==false" class="btn btn-info" v-on:click="activate(sellers._id)">Activate</a>
<a v-else class="btn btn-info"  v-on:click="activate(sellers._id)">Deactivate</a></td>
<td><a v-if="sellers.featured==false" class="btn btn-info" v-on:click="featured(sellers._id)">Mark featured</a>
<a v-else class="btn btn-info"  v-on:click="featured(sellers._id)">Unmark featured</a></td>
   
   <td><button class="btn btn-info" v-on:click="editTask(sellers) ,showAddModal=true">Edit</button></td>
    <td> <router-link :to="`/tickets/${sellers._id}`"><button class="btn btn-info">ADD TICKET</button></router-link>
</td>
  <td><button class="btn btn-danger"  @click="deleteData(sellers._id)">Delete</button></td>

         
        </tr>
      
      </tbody>
    </template>
  </v-simple-table>
        </v-container>
    </div>
</template>




<script>
import Vue from "vue";
import _ from 'lodash'
Vue.component('input-tag', InputTag)
import InputTag from 'vue-input-tag'
import axios from 'axios';
export default {
  name: "Seller",
  data(){
    return{
      search:"",
      isEdit: false,
      showAddModal: false,
      allSellers:[],
      activityType:[],
      allDuration:[],
      allVenue:[],
      
      
      allTimeTypes:[],
      
      allFrequency:[],
      frequency:"",
      
      addSeller:{id:"",activity:this.$route.params.id,venue:"",duration:"",timeType:"",frequency:"",whichDay:"",startDate:"",endDate:"",startTime:"",endTime:"",slots:[]},
      //addSeller:{activity:this.$route.params.id,venue:"",duration:"",timeType:"",frequency:"",whichDay:"",startTime:"",endTime:"",slots:[]},
      headers: {
       
        headers:{
         
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      
    
    
    }
  },
   mounted() {
     this.getAllposts()
     this.getDuration()
     this.getVenue()
     this.getTimeTypes()
     this.getFrequency()
     
     this.getSellersActivity()
  
 
  },
  methods: {
   getAllposts(){
      this.$axios.get("/api/posts/"+ this.addSeller.activity)
    .then((response)=>{
      console.log(response.data);
      this.activityType=response.data;
    })
  },
   getDuration()
      {
      this.$axios.get("/api/durations/")
      .then((response) => {
      console.log(response.data);
      this.allDuration=response.data;
    })
    },
    getVenue(){
      this.$axios.get("/api/venues/")
    .then((response)=>{
      console.log(response.data);
      this.allVenue=response.data;
    })
  },
   getTimeTypes()
      {
      this.$axios.get("/api/timeTypes/")
      .then((response) => {
      console.log(response.data);
      this.allTimeTypes=response.data;
    })
    },
    getFrequency()
      {
      this.$axios.get("/api/frequencies/")
      .then((response) => {
      console.log(response.data);
      this.allFrequency=response.data;
    })
    },
    selectChangeDuration(event){
      this.addSeller.duration=event.target.value;
      
  },
  selectChangeVenue(event){
   this.addSeller.venue=event.target.value;
  
  },
  selectChangetimeTypes(event){
   this.addSeller.timeType=event.target.value;
  
  },
  selectChangeFrequency(event){
   this.addSeller.frequency=event.target.value;
  
  },
    
     getSellersActivity()
      {
      this.$axios.post("/api/sellers/getActivitySeller/",{activity:this.addSeller.activity},this.headers)
      .then((response) => {
      console.log(response.data);
      this.allSellers=response.data;
    })
    },
     add() {
    if (this.allDuration[0]._id==this.addSeller.duration){
       this.$axios.post("/api/sellers/",{activity:this.$route.params.id,venue:this.addSeller.venue,duration:this.addSeller.duration,timeType:this.addSeller.timeType,startDate:this.addSeller.startDate,endDate:this.addSeller.endDate,startTime:this.addSeller.startTime,endTime:this.addSeller.endTime,slots:this.addSeller.slots},this.headers)
      .then((response) => {
       
        this.allSellers.push(response.data)
        this.showAddModal=false
        this.clear()
         console.log(response.data)
         
      })
      .catch((error) => {
        console.log(error);
      });
    }
    else if(this.allDuration[1]._id==this.addSeller.duration){
          this.$axios.post("/api/sellers/",{activity:this.$route.params.id,venue:this.addSeller.venue,duration:this.addSeller.duration,timeType:this.addSeller.timeType,frequency:this.addSeller.frequency,whichDay:this.addSeller.whichDay,startTime:this.addSeller.startTime,endTime:this.addSeller.endTime,slots:this.addSeller.slots},this.headers)
      .then((response) => {
       
        this.allSellers.push(response.data)
        this.clear()
        this.showAddModal=false
         console.log(response.data)
         
      })
      .catch((error) => {
        console.log(error);
      });
    }
      else if(this.allDuration[2]._id==this.addSeller.duration){
          this.$axios.post("/api/sellers/",{activity:this.$route.params.id,venue:this.addSeller.venue,duration:this.addSeller.duration,timeType:this.addSeller.timeType,startDate:this.addSeller.startDate,startTime:this.addSeller.startTime,endTime:this.addSeller.endTime,slots:this.addSeller.slots},this.headers)
      .then((response) => {
       
        this.allSellers.push(response.data)
        this.clear()
        this.showAddModal=false
         console.log(response.data)
         
      })
      .catch((error) => {
        console.log(error);
      });
    }
      else if(this.allDuration[3]._id==this.addSeller.duration){
          this.$axios.post("/api/sellers/",{activity:this.$route.params.id,venue:this.addSeller.venue,duration:this.addSeller.duration},this.headers)
      .then((response) => {
       
        this.allSellers.push(response.data)
        this.showAddModal=false
        this.clear()
         console.log(response.data)
         
      })
      .catch((error) => {
        console.log(error);
      });
    }
    },
    editTask(seller) 
    {
      
      this.addSeller.activity=seller.activity._id
      this.addSeller.id=seller._id
      this.addSeller.venue=seller.venue._id
      this.addSeller.duration=seller.duration._id
       if(this.allDuration[0,1,2]._id==this.addSeller.duration){
      this.addSeller.timeType=seller.timeType._id}
       this.addSeller.startTime=seller.startTime
      this.addSeller.endTime=seller.endTime
      this.addSeller.startDate=seller.startDate
      if(this.allDuration[0]._id==this.addSeller.duration){
      this.addSeller.endDate=seller.endDate}
      this.addSeller.startTime=seller.startTime
      this.addSeller.endTime=seller.endTime
       if(this.allDuration[1]._id==this.addSeller.duration){
      this.addSeller.frequency=seller.frequency._id}
      
      this.isEdit=true
    },
    updateTask(){
        
      this.$axios.put("/api/sellers/"+this.addSeller.id,{venue:this.addSeller.venue,startDate:this.addSeller.startDate,startTime:this.addSeller.startTime,endTime:this.addSeller.endTime},this.headers)
      .then((response) => {
        this.getSellersActivity()
        this.showAddModal=false
        this.clear()
        
        this.isEdit = false
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
      })
        },
        deleteData(id) {
     
           this.$axios.delete("/api/sellers/"+id,this.headers)
           .then((response) => {
             this.allSellers=_.reject(this.allSellers,function(n){
               return n._id === id;
             })
             
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
   activate(id){
    this.$axios.get("/api/sellers/activate/"+id,this.headers)
    .then((response)=>{
      this.getSellersActivity()
      console.log(response.data);
     
    })

  },
  featured(id){
        this.$axios.get("/api/sellers/feature/"+id,this.headers)
    .then((response)=>{
      this.getSellersActivity()
      console.log(response.data);
     
    })
  },
    
  
   clear(){
     
      
      this.addSeller={id:"",activity:"",duration:"",timeType:"",frequency:"",whichDay:"",startDate:"",endDate:"",startTime:"",endTime:"",slots:[]}
      
  
  
  }
  }
  
};
</script>