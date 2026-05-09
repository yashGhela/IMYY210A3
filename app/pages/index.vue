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


const getContentSnippet = (content, maxLength = 100) => {
  if (!content) return 'No content available'
  

  if (typeof content === 'string') {
    return content.length > maxLength ? content.substring(0, maxLength) + '...' : content
  }
  

  if (Array.isArray(content)) {
    let plainText = ''
    

    const extractText = (blocks) => {
      for (const block of blocks) {
        if (block.text) {
          plainText += block.text + ' '
        }
        if (block.children && Array.isArray(block.children)) {
          extractText(block.children)
        }
      }
    }
    
    extractText(content)
    plainText = plainText.trim()
    
    return plainText.length > maxLength ? plainText.substring(0, maxLength) + '...' : plainText
  }
  
  
  if (content.text) {
    return content.text.length > maxLength ? content.text.substring(0, maxLength) + '...' : content.text
  }
  
  return 'Content unavailable'
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
              <NuxtLink :to="`/blogs?id=${post.documentId}`">
                <div class="p-5 m-5 bg-gray-100 rounded-md cursor-pointer ">
                <p class="text-lg font-bold">{{ post.Title }}</p>
                <p class="font-normal text-lg">{{ post.Author }}</p>
                 <p class="text-gray-600 text-sm mt-2">{{ getContentSnippet(post.Content, 100) }}</p>
              </div>
              </NuxtLink>
              
            </li>
          </ul>
  </h1>
</template>