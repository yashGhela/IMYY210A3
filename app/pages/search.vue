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
  <Navbar/>
<LoadingComponent v-if="loading" />

<input class="border-gray-400 active:border-gray-800 border m-5 p-2 rounded-md" 
v-model="searchTerm"
        type="text" 
        placeholder="Enter post title..."
        @keyup.enter="search"/>
<button class="bg-blue-400 p-2 cursor-pointer text-white hover:bg-blue-600 rounded-md" @click="search">Search</button>
<p v-if="error">{{ error }}</p>

<div v-if="searchedPost">
   <NuxtLink :to="`/blogs?id=${searchedPost.documentId}`">
    <div class="p-5 m-5 bg-gray-100 rounded-md cursor-pointer ">
                <p class="text-lg font-bold">{{ searchedPost.Title }}</p>
                <p class="font-normal text-lg">{{ searchedPost.Author }}</p>
                 <p class="text-gray-600 text-sm mt-2">{{ getContentSnippet(searchedPost.Content, 100) }}</p>
              </div>
   </NuxtLink>
   
</div>
</template>