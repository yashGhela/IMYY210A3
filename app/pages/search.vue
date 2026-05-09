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
    const response  = await fetch(`http://localhost:1337/api/posts/${title}`)

    if(!response.ok) throw new Error('Failed to fetch posts')
    
    const data =await response.json()
    console.log("fetching posts")

    posts.value=data

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

onMounted(()=>{

  fetchPosts()

})


</script>

<template>
<LoadingComponent v-if="loading" />
<p v-if="error">{{ error }}</p>

<div v-if="searchedPost">
   <h1>{{searchedPost.title}}</h1>
   
</div>
</template>