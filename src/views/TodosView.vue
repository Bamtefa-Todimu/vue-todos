<script setup>
import TodoCreator from '@/components/TodoCreator.vue';
import { Icon } from "@iconify/vue";
import { computed, ref, watch } from 'vue';
import {uid} from "uid"
import TodoItem from '@/components/TodoItem.vue';

  const todoList = ref([])

   const todosCompleted = computed( () => {
    return todoList.value.every((todo) => todo.isCompleted)
  })

  watch(todoList,() => {
    setTodosToLocalStorage()
  },{
    deep:true
  })

  const setTodosToLocalStorage = () => {
    localStorage.setItem('todoList',JSON.stringify(todoList.value))
  }

  const getTodosFromLocalStorage = () => {
    const savedTodoList = JSON.parse(localStorage.getItem("todoList"));
    if (savedTodoList) {
      todoList.value = savedTodoList;
    }
  }

  getTodosFromLocalStorage()

 

 const createTodo = (todo) => {
  todoList.value.push({
    id:uid(),
    todo,
    isCompleted:null,
    isEditing:null
  })
 }

 const toggleEditing = (index) => {
  todoList.value[index].isEditing = !todoList.value[index].isEditing
 }

 const toggleComplete = (index) => {
  todoList.value[index].isCompleted = !todoList.value[index].isCompleted
 }
 const changeTodo = (todo,index) => {
  todoList.value[index].todo = todo
 }
 const deleteTodo = (id) => {
  todoList.value = todoList.value.filter((todo) => todo.id !== id)
 }

</script>

<template>
  <main>
    <h1>Create Todo</h1>
    <TodoCreator @create-todo="createTodo"/>
    <ul class="todo-list" v-if="todoList.length > 0">
      <TodoItem 
        @toggle-complete="toggleComplete" 
        @toggle-editing="toggleEditing" 
        @change-todo="changeTodo" 
        @delete-todo="deleteTodo" 
        v-for="(todo,index) in todoList" 
        :todo="todo" 
        :index="index" 
        :key="todo.id" 
      />
    </ul>
    <p class="todos-msg" v-else>
      <Icon icon="noto-v1:sad-but-relieved-face" />
      <span>You have no todo's to complete! Add one!</span>
    </p>
    <p v-if="todosCompleted && todoList.length > 0" class="todos-msg">
      <Icon icon="noto-v1:party-popper" />
      <span>You have completed all your todos!</span>
    </p>
  </main>
</template>


<style scoped lang="scss">
  main {
  display: flex;
  flex-direction: column;
  max-width: 500px;
  width: 100%;
  margin: 0 auto;
  padding: 40px 16px;

  h1 {
    margin-bottom: 16px;
    text-align: center;
  }

  .todo-list {
    display: flex;
    flex-direction: column;
    list-style: none;
    margin-top: 24px;
    gap: 20px;
  }

  .todos-msg {
    display: flex;
    align-items: center;
    justify-content: center;
    gap: 8px;
    margin-top: 24px;
  }
}
</style>