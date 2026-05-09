<script setup>



import {ref, onMounted} from 'vue'

const post=ref(null)
const loading = ref(false)
const error = ref(null)

const route = useRoute()

const fetchPosts= async() =>{
  loading.value =true;
  error.value= null;

  try{
    const response  = await fetch(`http://localhost:1337/api/posts/${route.params.id}`)

    if(!response.ok) throw new Error('Failed to fetch post')
    
    const data =await response.json()
    console.log("fetching posts")

    post.value=data

    



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

<div v-if="post">
   <h1>{{post.title}}</h1>
   <p>{{ post.author }}</p>

   <p>{{ post.content }}</p>
   
</div>
</template>