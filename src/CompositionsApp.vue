<template>
  <Layout>
    <div class="composer">
      <Form v-model="newTodo" @submitTodo="addTodo" :isError="isError"/>
      <Filter 
        :selectedFilter="selectedFilter" 
        @filterAll="selectedFilter = 'all'" 
        @filterPending="selectedFilter = 'pending'" 
        @filterDone="selectedFilter = 'done'"
        :total="totalTodos" 
        :pending="pendingTodos" 
        :done="doneTodos"
        @clear="clearDoneTodo"
      />
    </div>

    <Todo v-for="todo in filteredTodos" :key="todo.id" :todo="todo" @toggleTodo="toggleTodo(todo)"/>
  </Layout>
</template>

<script setup>
    import {v4 as uid} from "uuid";
    import Layout from './components/Layout.vue';
    import Todo from './components/Todo.vue';
    import Form from './components/Form.vue';
    import Filter from './components/Filter.vue';
    import { ref, onMounted, computed, watch } from "vue";

    // reactive state
    const todos = ref([]);
    const selectedFilter = ref("all");
    const newTodo = ref("");
    const isError = ref(false);

    // lifecycle hooks
    onMounted(()=>{
        todos.value = JSON.parse(localStorage.getItem("todos")) || [];
    });

    //Computed filtered todos
    const filteredTodos = computed(() => {
      if(selectedFilter.value == "all"){
        return todos.value;
      }else if(selectedFilter.value == "pending"){
        return todos.value.filter(todo => !todo.status);
      }else if(selectedFilter.value == "done"){
        return todos.value.filter(todo => todo.status);
      }
    });

    const totalTodos = computed(()=> todos.value.length );
    const pendingTodos = computed(()=> todos.value.filter(todo => !todo.status).length );
    const doneTodos = computed(()=> todos.value.filter(todo => todo.status).length );


    //add todo
    const addTodo = () => {
      if(newTodo.value == ''){
        isError.value = true;
      }else{
        let todo = {
          id: uid,
          title: newTodo.value,
          date: new Date().toString(),
          status: false,
        }
        todos.value.unshift(todo);
        newTodo.value = '';
        isError.value = false;
      }
    }

    const clearDoneTodo = () => {
      todos.value = todos.value.filter(todo => !todo.status);
    }

    const toggleTodo = (todo) => {
      todo.status = !todo.status;
    }

    const showDoneTodos = () => {
      filtered_todos = todos.value.filter(todo => todo.status);
    }

    const showPendingTodos = () => {
      filtered_todos = todos.value.filter(todo => !todo.status);
    }

    //watch
    watch(todos, (newTodo)=> {
        localStorage.setItem("todos", JSON.stringify(todos.value))
    }, {deep: true})
    
</script>
