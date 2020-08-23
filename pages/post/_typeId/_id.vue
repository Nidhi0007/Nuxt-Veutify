<template>
    <div>
 
        <v-container>


            <v-flex xs12>
                
                
            <h1>POSTS</h1></v-flex>
              <v-simple-table dark>
    <template v-slot:default>
      <thead>
        <tr>
  <th class="text-left">TITLE</th>
  <th class="text-left">SLUG</th>
  <th class="text-left">GUIDELINES</th>
  <th class="text-left">TAGS</th>
  <th class="text-left">CITY</th>

  <th class="text-left">SHORT DESCRIPTION</th>
  <th class="text-left">GALLERY</th>
  <th class="text-left">LOCATION</th>
 <th class="text-left">WAYS</th>
 
  <th class="text-left">RATINGS</th>
  <th class="text-left">VIEWS</th>
  <th class="text-left">LIKES</th>
 <th class="text-left">ACTIVATE</th>
<th class="text-left">MARK FEATURED</th>
  <th  class="text-left">UPDATE DATA</th>
  <th  class="text-left">VIEW DATA</th>
  <th  class="text-left">DELETE DATA</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="posts in allPosts" v-bind:key="posts._id">
 <td>{{posts.title}}</td>
 <td>{{posts.slug}}</td>
 <td>{{posts.guidelines}}</td>
 <td>{{posts.tags}}</td>
 <td>{{posts.city.name}}</td>
 <td>{{posts.shortDescription}}</td>
 <td>{{posts.gallery.length}}</td>
 <td>{{posts.location}}</td>
<td>{{posts.ways}}</td>
 <td>{{posts.rating.averageRating}}</td>
 <td>{{posts.views.count}}</td>
 <td>{{posts.likes.count}}</td>
   
   <td><a v-if="posts.active==false" class="btn btn-info" v-on:click="activate(posts._id)">Activate</a>
<a v-else class="btn btn-info"  v-on:click="activate(posts._id)">Deactivate</a></td>
<td><a v-if="posts.featured==false" class="btn btn-info" v-on:click="featured(posts._id)">Mark featured</a>
<a v-else class="btn btn-info"  v-on:click="featured(posts._id)">Unmark featured</a></td>
   <td><button class="btn btn-info" v-on:click="editTask(posts),showAddModal=true">Update</button></td>
     <td> <router-link :to="`/seller/${posts._id}`"><button class="btn btn-info">ADD SELLER</button></router-link>
</td>
  <td><button class="btn btn-danger" v-on:click="deleteTask(posts._id)">Delete</button></td>
         
        </tr>
      
      </tbody>
    </template>
  </v-simple-table>
        </v-container>
    </div>
</template>
<script>
import Vue from "vue";
import _ from 'lodash';
Vue.component('input-tag', InputTag)
import InputTag from 'vue-input-tag'
import axios from 'axios';
export default {
    
    
  name: "post-id-type",
  data (){
    return{
      showAddModal: false,
      isEdit: false,
      city:"",
      typeId:this.$route.params.id,
      category:{categoryId:""},
      ways:{walkingOnly:false,byBoat:false,byCar:false,byPublicTransport:false},
      selectedFile:null,
      getCities:[],
      allPosts:[],
      a:[],
      location:{latitude:"",longitude:"",address:""},
      addPosts:{gallery:"",title:"",slug:"", shortDescription:"",guidelines:"",text:"",tags:[]},
     headers: {
       
        headers:{
          'Content-Type': 'multipart/form-data',
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
       headersUrl: {
       
        headers:{
          
          Authorization:`bearer ${JSON.parse(localStorage.getItem('token') || "{}")[0].token}`}},
    }
  },
  mounted(){
    this.getAllCities()
    const url = window.location.href;
    this.category.categoryId =url.split("/").slice(-1)[0];
    this.getPosts()
   
    
   
  },
    methods: {
  getAllCities(){
      this.$axios.get("/api/cities/")
    .then((response)=>{
      console.log(response.data);
      this.getCities=response.data;
    })
  },
    getPosts()
    {
      this.$axios.post("/api/posts/postOfCategory/",this.category,this.headersUrl)
      .then((response) => {
   
        this.allPosts=(response.data)
        console.log(response);
       
      })
      .catch((error) => {
        console.log(error);
      });
    },
   
    onFileSelected(event) {
       this.selectedFile=event.target.files[0]
     },
          
      add() {
        console.log(JSON.stringify(this.addPosts))
        //upload file
     const fd= new FormData();
     fd.append('title', this.addPosts.title)
      fd.append('slug', this.addPosts.slug)
       fd.append('shortDescription', this.addPosts.shortDescription)
       fd.append('text',this.addPosts.text)
      if (this.selectedFile) {
          fd.append('gallery',this.selectedFile,this.selectedFile.name)
        }
       fd.append('guidelines',this.addPosts.guidelines)
       fd.append('location.latitude',this.location.latitude)
       fd.append('location.longitude',this.location.longitude)
       fd.append('location.address',this.location.address)
      this.addPosts.tags.forEach(tag=>{
            fd.append('tags',tag)
      })
       fd.append('ways.byBoat',this.ways.byBoat)
       fd.append('ways.byPublicTransport',this.ways.byPublicTransport)
       fd.append('ways.byCar',this.ways.byCar)
       fd.append('ways.walkingOnly',this.ways.walkingOnly)
       fd.append('city',this.city)
       fd.append('typeId',this.typeId)
       fd.append('categories',this.category.categoryId)
       this.$axios.post("/api/posts/",fd,this.headers)
      .then((response) => {
   
        this.allPosts.push(response.data)
         this.location = {latitude:"",longitude:"",address:""}
      this.addPosts = {gallery:"",title:"",slug:"", shortDescription:"",guidelines:"",tags:[]}
      this.ways={walkingOnly:false,byBoat:false,byCar:false,byPublicTransport:false}
      this.showAddModal=false
         
      })
      .catch((error) => {
        console.log(error);
      });
  
    },
 editTask(post){
   this.addPosts.id = post._id
      this.addPosts.title=post.title
      this.addPosts.slug=post.slug
      this.addPosts.shortDescription=post.shortDescription
      this.addPosts.guidelines=post.guidelines
      this.addPosts.gallery=post.gallery
      this.addPosts.text = post.text
      //  this.location.latitude=post.location.latitude
      // this.location.longitude=post.location.longitude
      //  this.location.address=post.location.address
      //  this.ways.byCar=post.ways.byCar
      //  this.ways.walkingOnly=post.ways.walkingOnly
      //  this.ways.Byboat=post.ways.Byboat
      //  this.ways.byPublicTransport=post.ways.byPublicTransport
      //  this.addPosts.tags=post.tags
       this.city=post.city._id
        
      this.isEdit=true
},
  updateTask(){
    const fd= new FormData();
     fd.append('title', this.addPosts.title)
      fd.append('slug', this.addPosts.slug)
       fd.append('shortDescription', this.addPosts.shortDescription)
      
        fd.append('guidelines',this.addPosts.guidelines)
        if (this.selectedFile) {
        fd.append('gallery',this.selectedFile,this.selectedFile.name)
        }
       fd.append('location.latitude',this.location.latitude)
       fd.append('location.longitude',this.location.longitude)
       fd.append('location.address',this.location.address)
       fd.append('ways.byCar',this.ways.byCar)
       fd.append('ways.walkingOnly',this.ways.walkingOnly)
       fd.append('ways.byBoat',this.ways.byBoat)
       fd.append('ways.byPublicTransport',this.ways.byPublicTransport)
       fd.append('city',this.city)
      
       
    
this.$axios.put('/api/posts/'+ this.addPosts.id,fd,this.headers)
 .then((response) => {
        this.getPosts()
        this.clear()
        this.showAddModal=false
        this.isEdit = false;
        console.log(response);
      })
       .catch((error) => {
        console.log(error);
         })
  
  
  },
 deleteTask(id) {
     
           this.$axios.delete("/api/posts/"+id,this.headers)
           .then((response) => {
             this.allPosts=_.reject(this.allPosts,function(n){
               return n._id === id;
             })
             
            
             
             console.log(response)
           })
           .catch((error) => {
        console.log(error);
      })
        
  },
      
    convertToSlug(){
    var theSlug=this.addPosts.title;
    theSlug=theSlug.toLowerCase();
    theSlug=theSlug.replace(/\s+/g,"-");
    this.addPosts.slug =theSlug;
  },
  selectChange(event){
      this.city=event.target.value;
      
  },
     activate(id){
    this.$axios.get("/api/posts/activate/"+id,this.headers)
    .then((response)=>{
      this.getPosts()
      console.log(response.data);
     
    })

  },
  featured(id){
        this.$axios.get("/api/posts/feature/"+id,this.headers)
    .then((response)=>{
      this.getPosts()
      console.log(response.data);
     
    })
  },
  
  clear(){
      this.location = {latitude:"",longitude:"",address:""}
      this.addPosts = {gallery:"",title:"",slug:"", shortDescription:"",guidelines:"",tags:[]}
      this.ways={walkingOnly:false,byBoat:false,byCar:false,byPublicTransport:false}
      this.city=""
      
  }
 
  
   
   
  },
   
};
</script>