<template>
  <div id="app">
    <h1>To-Do List</h1>
    <input v-model="newTodo" @keyup.enter="addTodo" placeholder="Add a new todo" />
    <ul>
      <li v-for="todo in todos" :key="todo.id">
        <input type="checkbox" v-model="todo.completed" @change="updateTodoStatus(todo)" />
        <span :class="{ completed: todo.completed }">{{ todo.task }}</span>
        <button @click="removeTodo(todo.id)">Remove</button>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  name: 'App',
  data() {
    return {
      newTodo: '',
      todos: []
    };
  },
  methods: {
    // Fetch todos from the backend
    async fetchTodos() {
      try {
        const response = await fetch('https://backend-todo-app-xwrh.onrender.com/todos');  // Replace with your backend API URL
        if (!response.ok) throw new Error('Failed to fetch todos');
        // const data = await response.json();
        this.todos = await response.json();
      } catch (error) {
        console.error('Error fetching todos:', error);
      }
    },

    // Add a new todo (send data to backend)
    async addTodo() {
      if (this.newTodo.trim() === '') return;
      try {
        const response = await fetch('https://backend-todo-app-xwrh.onrender.com/todos', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({ task: this.newTodo })
        });
        if (!response.ok) throw new Error('Failed to add todo');
        const data = await response.json();
        this.todos.push(data);  // Add the new todo to the list
        this.newTodo = '';  // Clear input field
      } catch (error) {
        console.error('Error adding todo:', error);
      }
    },

    // Remove a todo (send delete request to backend)
    async removeTodo(id) {
      try {
        const response = await fetch(`https://backend-todo-app-xwrh.onrender.com/todos/${id}`, {
          method: 'DELETE'
        });
        if (!response.ok) throw new Error('Failed to delete todo');
        this.todos = this.todos.filter(todo => todo.id !== id);  // Remove from the local list
      } catch (error) {
        console.error('Error removing todo:', error);
      }
    },

    // Update the completion status of a todo (send update request to backend)
    async updateTodoStatus(todo) {
      try {
        const response = await fetch(`https://backend-todo-app-xwrh.onrender.com/todos/${todo.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({ completed: todo.completed })
        });
        if (!response.ok) throw new Error('Failed to update todo');
      } catch (error) {
        console.error('Error updating todo status:', error);
      }
    }
  },

  // Fetch todos when the component is mounted
  mounted() {
    this.fetchTodos();
  }
};
</script>

<style>
.completed {
  text-decoration: line-through;
}
input[type="checkbox"] {
  margin-right: 10px;
}
button {
  margin-left: 10px;
  color: red;
  background: none;
  border: none;
  cursor: pointer;
}
</style>
