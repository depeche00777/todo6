<template>
  <main class="app">
    <section class="greeting">
      <h1 class="title">
        TODO LIST
        <input type="text" placeholder="name here" v-model.lazy="name">
      </h1>
    </section>

    <section class="create-todo">
      <h2>CREATE A TODO</h2>
      <h4>What's on your todo list?</h4>
      <form action="" v-on:submit.prevent="addTodo">

        <input 
          type="text" 
          placeholder="할일을 입력해주세요"
          v-model="input_content"
        >

        <div class="options">
          <label>
            <input type="radio" 
              name="category" 
              id="category1" 
              value="business"
              v-model="input_category">
            <span class="bubble business"></span>
            <div>business</div>
          </label>
          <label>
            <input type="radio" 
              name="category" 
              id="category2" 
              value="personal"
              v-model="input_category">
            <span class="bubble personal"></span>
            <div>personal</div>
          </label>
        </div>
        {{ input_content }} {{ input_category }}
        <input type="submit" value="Add Todo">
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        
        <div v-bind:class="`todo-item ${todo.done && 'done'}`"
          v-for="todo in todos_sort">
          <label>
            <input type="checkbox" v-model="todo.done">
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

<script setup>
  import { ref, watch, onMounted, computed } from 'vue';
  

  const name = ref('')  //이름
  const todos = ref([])

  const input_content = ref('')   //입력 내용
  const input_category = ref(null)   //business,personal선택

  // 입력값을 todos 배열에 들어가게 하는 함수 
  const addTodo = () => {
    if(input_content.value.trim() === '' ||  input_category.value === null){
      return
    }

    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done:false,
      createAt:new Date().getTime()   //UTC시간부터 현재까의 시간을 알아옴
    })
    console.log(todos)

    // 입력 후 초기화
    input_content.value = '';
    input_category.value = null
  }

  //시간 순으로 정리  배열.sort((a,b)=>b-a)  -내림차순(새로운 todo가 위에보임)
  const todos_sort = computed(()=>{
    return todos.value.sort((a,b)=>{
        return b.createAt - a.createAt
      })
  }) 
  

  // 삭제 함수
  const removeTodo = (todo) => {
    todos.value = todos.value.filter(t=> t != todo)
  }


  //이름 입력한 것을 인지하고 로컬스토리지에 저장
  watch(name,(newValue)=>{
    if(newValue != ''){
      localStorage.setItem("name", newValue); //(키, 밸류)
    }
  })

  //todos 배열이 바뀐것을 인지하고 로컬스토리지에 저장, 업데이트
  watch(todos, (newVal) => {
    localStorage.setItem('todos',JSON.stringify(newVal))
  },{ deep:true } )
  //JSON.stringify(newValue) - js문서를 json파일로 변환


  //새로열었을때 로컬스토리지에서 불러오기(없을때는 비워놈)
  onMounted(() => {
    name.value = localStorage.getItem("name") || '';
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  })
</script>
