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
          ADD Seller
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Sellers</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
        <v-col class="d-flex" cols="12" sm="6">
                <v-select 
    outline 
    label="Select Venue" 
    :items="allVenue" 
    item-text="name" 
    v-model="addSeller.venue"
     item-key="_id"
    item-value="_id"
    return-object
    @change="selectChangeVenue(addSeller.venue._id)"
>

</v-select>
    </v-col>
  
 <v-col class="d-flex" cols="12" sm="6">
        <v-select 
    outline 
    label="Select Duration" 
    :items="allDuration" 
    item-text="type" 
    v-model="addSeller.duration"
     item-key="_id"
    item-value="_id"
    return-object
    @change="selectChangeDuration(addSeller.duration._id)"
>

</v-select>
      </v-col>
     
       <v-col class="d-flex" cols="12" sm="6">
        <v-select 
    outline 
    label="Select Timetype" 
    :items="allTimeTypes" 
    item-text="type" 
    v-model="addSeller.timeType"
     item-key="_id"
    item-value="_id"
    return-object
    @change="selectChangetimeTypes(addSeller.timeType._id)"
>

</v-select>
      </v-col>
             
             
            
              
            </v-row>
          </v-container>
         
        </v-card-text>
       <!-- singleday -->
          <v-row justify="center">
             <div class="centered" v-if="this.addSeller.duration=='5f2158a2279bce0017e02b68'">
     <form>
    <v-row justify="center">
    <v-date-picker v-model="addSeller.startDate">Start Date</v-date-picker>
  </v-row>
   <!-- From To time -->
  <div class="centered" v-if=" '5f21414dddc8f60017961536'==this.addSeller.timeType">
   <v-row justify="center">
    <v-time-picker v-model="addSeller.startTime">Start Time</v-time-picker>
  </v-row>
  <v-row justify="center">
    <v-time-picker v-model="addSeller.endTime">End Time</v-time-picker>
  </v-row>
    </div>
    <!-- Time slot-->
       <div class="centered" v-if=" '5f2141cbddc8f60017961537'==this.addSeller.timeType">
           <div class="col">
            <label>Time Slots
  <input-tag placeholder="Time slots" v-model="addSeller.slots" :limit="limit"></input-tag></label>
</div>

      
</div>
 <!-- From To time and time slot -->
   <div class="centered" v-if=" '5f2141fcddc8f60017961538'==this.addSeller.timeType">
           

 <v-row justify="center">
    <v-time-picker v-model="addSeller.startTime">Start Time</v-time-picker>
  </v-row>
  <v-row justify="center">
    <v-time-picker v-model="addSeller.endTime">End Time</v-time-picker>
  </v-row>


     
           <div class="col">
            <label>Time Slots
  <input-tag placeholder="Time slots" v-model="addSeller.slots" :limit="limit"></input-tag></label>
</div>

      
</div>
 <v-spacer></v-spacer>
 
     <v-btn color="blue darken-1" text @click="showAddModal = false">Close</v-btn>
          <v-btn v-if="this.isEdit == false" color="blue darken-1" text @click="add()">ADD</v-btn>
          <v-btn v-else color="blue darken-1" text @click="updateTask()">UPDATE</v-btn>
  </form>
  </div>
   </v-row>

   <!-- multipleday -->
             <v-row justify="center">
             <div class="centered" v-if="this.addSeller.duration=='5f21407dddc8f60017961533'">
     <form>
    <v-row justify="center">
    <v-date-picker v-model="addSeller.startDate">Start Date</v-date-picker>
  </v-row>
  <v-row justify="center">
    <v-date-picker v-model="addSeller.endDate">End Date</v-date-picker>
  </v-row>
   <!-- From To time -->
  <div class="centered" v-if=" '5f21414dddc8f60017961536'==this.addSeller.timeType">
   <v-row justify="center">
    <v-time-picker v-model="addSeller.startTime">Start Time</v-time-picker>
  </v-row>
  <v-row justify="center">
    <v-time-picker v-model="addSeller.endTime">End Time</v-time-picker>
  </v-row>
    </div>
    <!-- Time slot-->
       <div class="centered" v-if=" '5f2141cbddc8f60017961537'==this.addSeller.timeType">
           <div class="col">
            <label>Time Slots
  <input-tag placeholder="Time slots" v-model="addSeller.slots" :limit="limit"></input-tag></label>
</div>

      
</div>
 <!-- From To time and time slot -->
   <div class="centered" v-if=" '5f2141fcddc8f60017961538'==this.addSeller.timeType">
           

 <v-row justify="center">
    <v-time-picker v-model="addSeller.startTime">Start Time</v-time-picker>
  </v-row>
  <v-row justify="center">
    <v-time-picker v-model="addSeller.endTime">End Time</v-time-picker>
  </v-row>


     
           <div class="col">
            <label>Time Slots
  <input-tag placeholder="Time slots" v-model="addSeller.slots" :limit="limit"></input-tag></label>
</div>

      
</div>
 <v-spacer></v-spacer>
 
     <v-btn color="blue darken-1" text @click="showAddModal = false">Close</v-btn>
          <v-btn v-if="this.isEdit == false" color="blue darken-1" text @click="add()">ADD</v-btn>
          <v-btn v-else color="blue darken-1" text @click="updateTask()">UPDATE</v-btn>
  </form>
  </div>
   </v-row>

    <!-- Repititive -->
             <v-row justify="center">
              <div class="centered" v-if="'5f2140a9ddc8f60017961534'==this.addSeller.duration">
     <form>

     <v-col class="d-flex" cols="12" sm="6">
        <v-select 
    outline 
    label="Select Timetype" 
    :items="allFrequency" 
    item-text="type" 
    v-model="addSeller.frequency"
     item-key="_id"
    item-value="_id"
    return-object
    @change="selectChangeFrequency(addSeller.frequency._id)"
>

</v-select>
      </v-col>
      
      <div class="col" v-if="'5f1ffca845bc000017c0a3aa'==this.addSeller.frequency" >
       <v-col class="d-flex" cols="12" sm="6">
        <v-select 
    outline 
    label="Select Day" 
    :items="whichDay" 
    item-text="name" 
    v-model="addSeller.whichDay"
  
    item-value="whichDay"
    return-object
    @change="selectChangeWhichDay(addSeller.whichDay.value)"
>

</v-select>
      </v-col>
      </div>
      <div class="right" v-if="'5f20497dbe795400172ed18b'==this.addSeller.frequency">
      <v-row justify="center">
    <v-date-picker >Date Of Month</v-date-picker>
  </v-row>

    </div>
    
   <!-- From To time -->
  <div class="centered" v-if=" '5f21414dddc8f60017961536'==this.addSeller.timeType">
   <v-row justify="center">
    <v-time-picker v-model="addSeller.startTime">Start Time</v-time-picker>
  </v-row>
  <v-row justify="center">
    <v-time-picker v-model="addSeller.endTime">End Time</v-time-picker>
  </v-row>
    </div>
    <!-- Time slot-->
       <div class="centered" v-if=" '5f2141cbddc8f60017961537'==this.addSeller.timeType">
           <div class="col">
            <label>Time Slots
  <input-tag placeholder="Time slots" v-model="addSeller.slots" :limit="limit"></input-tag></label>
</div>

      
</div>
 <!-- From To time and time slot -->
   <div class="centered" v-if=" '5f2141fcddc8f60017961538'==this.addSeller.timeType">
           

 <v-row justify="center">
    <v-time-picker v-model="addSeller.startTime">Start Time</v-time-picker>
  </v-row>
  <v-row justify="center">
    <v-time-picker v-model="addSeller.endTime">End Time</v-time-picker>
  </v-row>


     
           <div class="col">
            <label>Time Slots
  <input-tag placeholder="Time slots" v-model="addSeller.slots" :limit="limit"></input-tag></label>
</div>

      
</div>
 <v-spacer></v-spacer>
 
     <v-btn color="blue darken-1" text @click="showAddModal = false">Close</v-btn>
          <v-btn v-if="this.isEdit == false" color="blue darken-1" text @click="add()">ADD</v-btn>
          <v-btn v-else color="blue darken-1" text @click="updateTask()">UPDATE</v-btn>
  </form>
  </div>
   </v-row>
      </v-card>
    </v-dialog>
  </v-row>
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
      whichDay:[{name:'Monday',value:'0'},{name:'Tuesday',value:'1'},{name:'Wednesday',value:'2'},{name:'Thursday',value:'3'},{name:'Friday',value:'4'},{name:'Saturday',value:'5'},{name:'Sunday',value:'6'}],
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
    selectChangeDuration(_id){
      this.addSeller.duration=_id;
      
  },
  selectChangeVenue(_id){
   this.addSeller.venue=_id;
  
  },
  selectChangetimeTypes(_id){
   this.addSeller.timeType=_id;
  
  },
  selectChangeFrequency(_id){
   this.addSeller.frequency=_id;
  
  },
  selectChangeWhichDay(_id){
    console.log(_id);
   this.addSeller.whichDay=_id;
  
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