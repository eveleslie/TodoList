<script setup>
import {ref,onMounted, computed, watch } from 'vue'

// 定义变量
// ref() ： 定义响应式变量，它可以定义任意类型
const todos = ref([])
const name = ref('')
const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b)=>{
  return b.createdAt - a.createdAt
}))
const addTodo = () => {
  // 输入为空则返回
  // trim() 函数移除字符串两侧的空白字符或其他预定义字符
  if(input_content.value.trim() === '' || input_category.value === null){
    return
  }
  todos.value.push({
    content:input_content.value,
    category:input_category.value,
    done:false,
    createdAt:new Date().getTime()
  })

}
const removeTodo = todo =>{
  todos.value = todos.value.filter(t => t!== todo)
  // (t) => t !== todo实际上就是
  // function(t){ return t !==todo}
  }

  // 在HTML5中，新加入了一个localStorage特性，这个特性主要是用来作为本地存储来使用的
  // 解决了cookie存储空间不足的问题(cookie中每条cookie的存储空间为4k)
  // localStorage中一般浏览器支持的是5M大小，这个在不同的浏览器中localStorage会有所不同。

watch(todos,newVal=>{
  // localStorage.getItem(key):获取指定key本地存储的值
  // JSON.stringify 方法将某个对象转换成 JSON 字符串形式
  localStorage.setItem('todos',JSON.stringify(newVal))
},{ deep:true })
watch(name,(newVal)=>{
  localStorage.setItem('name',newVal)
})

onMounted(()=>{
  name.value = localStorage.getItem('name') || ''
  todos.value= JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
<main class="app">
  <section class="greeting">
    <h2 class="title">
      What's up, <input type="text" placeholder="Name here" v-model="name" />
    </h2>
  </section>

  <section class="create-todo">
    <h3>CREATE A TODO</h3>

    <form @submit.prevent="addTodo">
      <!-- @submit.prevent
        .prevent 表示提交以后不刷新页面，prevent是preventDefault,阻止标签默认行为，有些标签有默认行为，例如a标签的跳转链接属性href等。
        submit点击默认行为是提交表单，这里并不需要它提交,只需要执行addMemo方法，故阻止为好。
      -->
      <h4>What's on your todo list?</h4>
      <input type="text" placeholder="e.g. make a video"
      v-model="input_content">

      <h4>Pick a category</h4>

      <div class="options">
        <label >
          <input type="radio" name="category" 
          value="business"
          v-model="input_category" />
          <span class="bubble business"></span>
          <div>Business</div>
        </label>
        <label >
          <input type="radio" name="category" 
          value="personal"
          v-model="input_category" />
          <span class="bubble personal"></span>
          <div>Personal</div>
        </label>
      </div>

      <input type="submit" value="Add Todo">
    
    </form>
  </section>


  <section class="todo-list">
    <h3>TODO LIST</h3>
    <div class="list">
      <div v-for="todo in todos_asc" :class="`todo-item ${todo.done &&'done'}`">

        <label>
          <input type="checkbox" v-model="todo.done" />
          <span :class="`bubble ${todo.category}`"></span>
        </label>
        <div class="todo-content">
          <input type="text" v-model="todo.content">
        </div>

        <div class="actions">
          <button class="delete" @click="removeTodo(todo)">Delete</button>
        </div>
      
      </div>
    </div>
  </section>


</main>
</template>

