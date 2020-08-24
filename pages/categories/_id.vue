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
          ADD Category
        </v-btn>
      </template>
      <v-card>
        <v-card-title>
          <span class="headline">Posts</span>
        </v-card-title>
        <v-card-text>
          <v-container>
            <v-row>
               <v-col cols="12" sm="6" md="4">
                <v-text-field label="Name*"  v-model="addCategories.name" @change="convertToSlug" required></v-text-field>
              </v-col>
              <v-col cols="12" sm="6" md="4">
                <v-text-field label="Slug" hint="example of helper text only on focus" v-model="addCategories.slug"></v-text-field>
              </v-col>
               <v-col cols="12" sm="6" md="4">
                <v-text-field label="Short Description" hint="example of helper text only on focus" v-model="addCategories.shortDescription"></v-text-field>
              </v-col>
               <v-col cols="12">
               <input type="file" @change="onFileSelected">
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
                
                
            <h1>Categories</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
  <th class="text-left">CATEGORY</th>
   <th class="text-left">SLUG</th>
   <th class="text-left">GALLERY</th>
   <th class="text-left">ACTIVITIES</th>
   <th class="text-left">SHORT DESCRIPTION</th>
      <th class="text-left">ACTIVATE</th>
<th class="text-left">MARK FEATURED</th>
   <th class="text-left">EDIT DATA</th>
   <th class="text-left">VIEW DATA</th>
   <th class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="datas in filteredPosts" v-bind:key="datas._id">
  <td>{{datas.name}}</td>
  <td>{{datas.slug}}</td>
  <td>{{datas.gallery.length}}</td>
   <td>{{datas.activities.length}}</td>
  <td>{{datas.shortDescription}}</td>
   <td><a v-if="datas.active==false" class="btn btn-info" v-on:click="activate(datas._id)">Activate</a>
<a v-else class="btn btn-info"  v-on:click="activate(datas._id)">Deactivate</a></td>
<td><a v-if="datas.featured==false" class="btn btn-info" v-on:click="featured(datas._id)">Mark featured</a>
<a v-else class="btn btn-info"  v-on:click="featured(datas._id)">Unmark featured</a></td>
  <td><button class="btn btn-info" v-on:click="editTask(datas.name,datas._id,datas.slug, datas.shortDescription,datas.gallery),showAddModal=true">Edit</button></td>
  <td> <nuxt-link :to="`/post/${activityTypeCategories._id}/${datas._id}`"><button class="btn btn-info">View</button></nuxt-link>
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
import _ from 'lodash';
import axios from 'axios';
export default {
  name: "Categories",
  data(){
    return{
      showAddModal: false,
      search:"",
      selectedFile:null,
      activityTypeCategories:{categories:[]},
      typeId:this.$route.params.id,
      addCategories:{gallery:"",name:"",slug:"", shortDescription:""},
      isEdit: false,
       headers: {
       
        headers:{
          'Content-Type': 'multipart/form-data',
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
      
    
    
    }
  },
    mounted() {
     this.getTypes()
     
  
 
  },
  computed: {
    filteredPosts() {
      return this.activityTypeCategories.categories.filter(datas => datas.name.toLowerCase().includes(this.search.toLowerCase()))
    }
  },
   
  methods: {
       getTypes()
    {
      axios.get("https://goa-app.herokuapp.com/api/activityTypes/"+ this.typeId)
      .then((response) => {
      console.log(response.data);
      this.activityTypeCategories=response.data;
    })
    },
    
      onFileSelected(event) {
       this.selectedFile=event.target.files[0]
     },
          
      add() {
        //upload file
     const fd= new FormData();
     fd.append('name', this.addCategories.name)
      fd.append('slug', this.addCategories.slug)
       fd.append('shortDescription', this.addCategories.shortDescription)
       
       fd.append('typeId',this.typeId)
       if (this.selectedFile) {
          fd.append('gallery',this.selectedFile,this.selectedFile.name)
       }
  
      
     
       axios.post("https://goa-app.herokuapp.com/api/activityCategories/",fd,this.headers)
      .then((response) => {
       this.addCategories = {gallery:"",name:"",slug:"", shortDescription:"",typeId:this.$route.params.id}
        this.activityTypeCategories.categories.push(response.data)
        console.log(response);
       
      })
      .catch((error) => {
        console.log(error);
      });
    },
      editTask(name,id,slug,shortDescription,gallery){
   this.addCategories.id =id
      this.addCategories.name=name
      this.addCategories.slug=slug
      this.addCategories.shortDescription=shortDescription
      this.addCategories.gallery=gallery
      
      this.isEdit=true
},
  updateTask(){
    const fd= new FormData();
     fd.append('name', this.addCategories.name)
      fd.append('slug', this.addCategories.slug)
       fd.append('shortDescription', this.addCategories.shortDescription)
        fd.append('typeId',this.typeId)
      
       if (this.selectedFile) {
          fd.append('gallery',this.selectedFile,this.selectedFile.name)
       }
axios.put('https://goa-app.herokuapp.com/api/activityCategories/'+ this.addCategories.id,fd,this.headers)
 .then((response) => {
        this.getTypes()
        this.isEdit = false
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
         })
  
  
  },
      deleteTask(id) {
     
           axios.delete("https://goa-app.herokuapp.com/api/activityCategories/"+id,this.headers)
           .then((response) => {
             this.activityTypeCategories.categories=_.reject(this.activityTypeCategories.categories,function(n){
               return n._id === id;
             })
             
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
      
      
  convertToSlug(){
    var theSlug=this.addCategories.name;
    theSlug=theSlug.toLowerCase();
    theSlug=theSlug.replace(/\s+/g,"-");
    this.addCategories.slug =theSlug;
  },
     activate(id){
    this.$axios.get("/api/activityCategories/activate/"+id,this.headers)
    .then((response)=>{
      this.getTypes()
      console.log(response.data);
     
    })

  },
  featured(id){
        this.$axios.get("/api/activityCategories/feature/"+id,this.headers)
    .then((response)=>{
      this.getTypes()
      console.log(response.data);
     
    })
  },
   
  }
  
};
</script>