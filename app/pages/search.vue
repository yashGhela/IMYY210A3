<script setup>


import {ref, onMounted} from 'vue'
import Navbar from '~/components/Navbar.vue'

const posts=ref([])
const loading = ref(false)
const error = ref(null)
const searchTerm=ref('')
const searchedPost= ref(null)

const fetchPosts= async() =>{
  loading.value =true;
  error.value= null;

  try{
    const response  = await fetch(`http://localhost:1337/api/posts/`)

    if(!response.ok) throw new Error('Failed to fetch posts')
    
    const data =await response.json()
    console.log("fetching posts")

    if (data && data.data) {
      posts.value = data.data
    } 

  
     console.log("Posts loaded:", )
     console.log(posts.value[0])
  }catch(err){
    error.value= err.message;
    console.log("Posts API error:", err)
  }finally{
    loading.value=false;
  }
}

const search=()=>{
  searchedPost.value = null
  error.value = null
  
  console.log(searchTerm.value)
  
  // if (!searchTerm.value.trim()) {
  //   error.value = 'Please enter a title to search'
  //   return
  // }
  
  
  for (let i = 0; i < posts.value.length; i++) {
    
    console.log(posts.value[i].Title)
    
    if (posts.value[i].Title==searchTerm.value) {
      
      searchedPost.value = posts.value[i]
      break
    }
  }
  
  console.log("Found search term:"+searchedPost.value)

  if (!searchedPost.value) {
    error.value = `No post found with title: "${searchTerm.value}"`
  }
}

onMounted(()=>{
  fetchPosts()
})

</script>

<template>
  <Navbar/>
<LoadingComponent v-if="loading" />

<input class="border-black border m-5 p-2 rounded-md" 
v-model="searchTerm"
        type="text" 
        placeholder="Enter post title..."
        @keyup.enter="search"/>
<button class="bg-blue-400 p-2 cursor-pointer text-white hover:bg-blue-600 rounded-md" @click="search">Search</button>
<p v-if="error">{{ error }}</p>

<div v-if="searchedPost">
   <NuxtLink :to="`/blogs?id=${searchedPost.documentId}`">{{ searchedPost.Title }}</NuxtLink>
   
</div>
</template>