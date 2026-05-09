<script setup>

import {ref, onMounted} from 'vue'
import Navbar from '~/components/Navbar.vue'

const posts=ref([])
const loading = ref(false)
const error = ref(null)

const fetchPosts= async() =>{
  loading.value =true;
  error.value= null;

  try{
    const response  = await fetch("http://localhost:1337/api/posts")

    if(!response.ok) throw new Error('Failed to fetch posts')
    
    const data =await response.json()
    console.log("fetching posts")

     if (data && data.data) {
      posts.value = data.data
    } else if (Array.isArray(data)) {
      posts.value = data
    } else if (data && data.posts) {
      posts.value = data.posts
    } else {
      posts.value = data
    }
    
    console.log("Processed posts:", posts.value)


  }catch(err){
    error.value= err.message;
    console.log("Posts API error:", err)
  }finally{
    loading.value=false;
  }
}

onMounted(()=>{

  fetchPosts()

})


</script>

<template>
  <h1 class="text-3xl font-bold ">
     <Navbar/>
     <LoadingComponent v-if="loading" />
        <p v-if="error">{{ error }}</p>
        <ul v-if="!loading && !error">
            <li v-for="post in posts" :key="post.id">
              <p>{{ post.Title }}</p>
            </li>
          </ul>
  </h1>
</template>