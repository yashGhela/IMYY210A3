<script setup>



import {ref, onMounted} from 'vue'
import { StrapiBlocks } from 'vue-strapi-blocks-renderer'
import Navbar from '~/components/Navbar.vue'

const post=ref(null)
const loading = ref(false)
const error = ref(null)

const route = useRoute()

const fetchPosts= async() =>{
  loading.value =true;
  error.value= null;
  const query = route.query.id
  console.log(query)

  try{
    const response  = await fetch(`http://localhost:1337/api/posts/${query}`)

    if(!response.ok) throw new Error('Failed to fetch post')
    
    const data =await response.json()
    console.log("fetching post")

    post.value=data.data
    console.log(post.value)

    



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
   <Navbar/>
<LoadingComponent v-if="loading" />
<p v-if="error">{{ error }}</p>

<div v-if="post">
   <h1>{{post.Title}}</h1>
   <p>{{ post.Author }}</p>

   <StrapiBlocks :content="post.Content"/>
   
</div>
</template>