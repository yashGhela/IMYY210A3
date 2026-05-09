<script setup>


import {ref, onMounted} from 'vue'

const posts=ref([])
const loading = ref(false)
const error = ref(null)
const title=ref(null)
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
    } else if (Array.isArray(data)) {
      posts.value = data
    } else if (data && data.posts) {
      posts.value = data.posts
    } else {
      posts.value = data
    }

    for(var i=0; i<data.length;i++){

        if(data[i].title==title){
            searchedPost=data[i];
            break;
        }
    }

    if(searchedPost==null){
        error.value='Post not found'
    }


  }catch(err){
    error.value= err.message;
    console.log("Posts API error:", err)
  }finally{
    loading.value=false;
  }
}



</script>

<template>
<LoadingComponent v-if="loading" />

<input class="border-black border m-5 p-2 rounded-md" :value="searchedPost" type="text">
<button class="bg-blue-400 p-2 cursor-pointer text-white hover:bg-blue-600 rounded-md" @click="fetchPosts">Search</button>
<p v-if="error">{{ error }}</p>

<div v-if="searchedPost">
   <h1>{{searchedPost.title}}</h1>
   
</div>
</template>