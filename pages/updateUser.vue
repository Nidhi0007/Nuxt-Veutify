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
          Update User
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Update user</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
          <v-col cols="12" sm="6" md="4">
                 <v-text-field label="Username*" v-model="update.username"  required></v-text-field>
               
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field label="Email*" v-model="update.email"  required></v-text-field>
               
              </v-col>
                 <v-col cols="12" sm="6" md="4">
                <v-text-field label="DOB*" v-model="update.dob"  required></v-text-field>
               
              </v-col>
                    <v-col cols="12" sm="6" md="4">
                <v-text-field label="services*" v-model="update.services"  required></v-text-field>

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
                
                
            <h1>Update User</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
                            <th class="text-left">USERNAME</th>
                            <th class="text-left">EMAIL</th>
                            <th class="text-left">DOB</th>
                      
                            <th class="text-left">SERVICES</th>
                            <th class="text-left">CITY</th>
                            <th class="text-left">PHONE</th>
                            <th class="text-left">PROFILE PIC</th>
                            <th class="text-left">MERCHANT</th>
                            <th class="text-left">ADMIN</th>
                            <th class="text-left">USER</th>
                            <th class="text-left">CART</th>
                            <th class="text-left">BUCKETLIST</th>
                            <th class="text-left">UPDATE</th>
        </tr>
      </thead>
      <tbody>
 <tr>
                            <td>{{allMe.username}}</td>
                            <td>{{allMe.email}}</td>
                            <td>{{allMe.dob}}</td>
                            
                            <td>{{allMe.services}}</td>
                            <td>{{allMe.city}}</td>
                            <td>{{allMe.phone}}</td>
                            <td>{{allMe.profilePic}}</td>
                            <td>{{allMe.isMerchant}}</td>
                            <td>{{allMe.isAdmin}}</td>
                            <td>{{allMe.isUser}}</td>
                            <td>{{allMe.cart}}</td>
                            <td>{{allMe.bucketList}}</td>
                            <td><button class="btn btn-info" v-on:click="editTask(allMe), showAddModal=true">Edit</button></td>
                            </tr>
      
      </tbody>
    </template>
  </v-simple-table>
        </v-container>
    </div>
</template>
<script>
import axios from 'axios';
export default {
 
 
data(){
    return{
   
         allMe:[],
      showAddModal: false,
       update:{username:"",profileLink:"",profilePic:"",password:"",phone:"",isMerchant:false,isAdmin:false,isUser:false,about:"",dob:"",email:"",token:""},
         headers: {
               
        headers:{
         
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}}
    }
},
  mounted() {
        this.getMe()
    },
    methods: {
        getMe(){
      this.$axios.get("/api/users/me",this.headers)
      .then((response) => {
      console.log(response.data);
      this.allMe=response.data;
    })
 },
        editTask(allMe){
            this.update.username =allMe.username
            this.update.profileLink=allMe.profileLink
            this.update.profilePic=allMe.profilePic
            this.update.password=allMe.password
            this.update.phone=allMe.phone
            this.update.about=allMe.about
            this.update.dob=allMe.dob
            this.update.email=allMe.email
            this.update.token=allMe.token
            
        },
    

        updateTask(){
            this.$axios.put("/api/users/"+this.allMe._id,this.update,this.headers)
      .then((response) => {

        this.showAddModal=false
        this.getMe()
    
        
        
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
       
      })
        }

    }


}

</script>