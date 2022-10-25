<template >
  <body class="h-screen bg-gray-100">
    <div class="mx-4 ">
        <h1 class="font-bold text-lg text-left py-6">What's up, 
            <input placeholder="Name" type="text" v-model="name" class="no-border focus:outline-none bg-gray-100" />
        </h1>
        <h5 class="text-left text-xs text-gray-700">CREATE A TODOS</h5>
  <h4 class="text-left font-bold text-sm text-gray-500">What's on your todos list?</h4>
  <form @submit.prevent="addtodos">
  <input type="text" v-model="input_content" placeholder="   e.g: Make an Assignment" class=" shadow-lg my-2 rounded-md w-full h-11 bg-white" />
  <br />
  <div class="text-left my-2">
      <label class=" font-bold text-xs text-gray-500 "> Pick a Category </label>
      <div class="flex flex-row space-x-4 justify-center my-2">
        <div class="bg-white w-1/2 text-center h-16 rounded-lg shadow-lg shadow-gray-300 no-border">
      <input type="radio" id="Business" value="Business" v-model="type" class=" mx-12 my-3 lg:my-6 md:my-6  " />
      <label for="Business"  >  Business </label>
    </div>
    <div class="bg-white w-1/2 text-center h-16 rounded-lg shadow-lg shadow-gray-300 no-border">
      <input type="radio" id="Personal" value="Personal" v-model="type" class=" mx-12 my-3 lg:my-6 md:my-6 focus:accent-purple-500 " />
      <label for="Personal" >Personal</label>
      </div>
    </div>
    </div>
  <button typ="submit" class="bg-blue-600 w-full text-white rounded-md h-12 my-2 shadow-lg shadow-blue-200"> ADD TODOS </button>
  <div class="text-gray-400 text-left py-2 font-bold">TODO LIST</div>
  <div v-for="todo in todos_asc" :key="todo" class="my-2 text-sm text-left space-x-2 ">
    <div class=" text-xs bg-white rounded-sm h-9 flex flex-row  shadow-lg "> 
        <h2 class="mx-1 my-[3%] lg:my-3 md:my-3 lg:mx-[15%] md:mx-[10%]">  {{todo.category}} : </h2>
    <input v-model="todo.content" class="bg-white my-2 mx-1 lg:mx-[5%] md:mx-[10%] font-bold no-border focus:outline-none" />
    <button @click="remove" class="bg-orange-500 mx-6 my-2 lg:mx-[10%] md:mx-[10%] rounded-sm w-1/6 lg:w-1/4 h-5 text-white text-xs font-bold justify-right">Remove</button>
    </div>

  </div>
  </form>
    </div>
  </body>

 
</template>

<script>

import {ref, watch, onMounted, computed} from 'vue'
    export default {
        // eslint-disable-next-line vue/multi-word-component-names
        name:'App',
        setup() {
            const todos= ref([])
            const input_content= ref('')
            const type= ref(null)
            const name= ref('')
            const todos_asc = computed(() => todos.value.slice().sort((a,b) => {
                 return b.createdAt - a.createdAt 
                }))
            
            watch (name, (newValue) => {
                localStorage.setItem('name',newValue)
                console.log('name', newValue)
            })
            watch (todos, (newValue) => {
                localStorage.setItem('todos', JSON.stringify(newValue))
                console.log ('todos',newValue)
             },{
                deep: true
             }
             )
            
            function addtodos() {
                if (input_content.value.trim() === '' || type.value === null) {
                    return
                }
                todos.value.push({
                    content: input_content.value,
                    category: type.value,
                    done: false,
                    createdAt: new Date().getTime()
                })
                
            }
            function remove() {
                todos.value.pop()
            }
            onMounted(() => {
                name.value= localStorage.getItem('name') || '' 
                todos.value= JSON.parse( localStorage.getItem('todos') ) || []
            })

            return {
            todos,
            type,
            remove,
            name,
            input_content,
            addtodos,
            todos_asc
            }
  }
    }
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;

  text-align: center;
  color: #2c3e50;

}
</style>
