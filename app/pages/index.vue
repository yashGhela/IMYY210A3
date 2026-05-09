<script setup>

import {ref, onMounted} from 'vue'

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

    posts.value=data


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
     <LoadingComponent v-if="loading" />
        <p v-if="error">{{ error }}</p>
        <ul v-if="!loading && !error">
            <li v-for="post in posts" :key="post.id">
            </li>
          </ul>
  </h1>
</template>