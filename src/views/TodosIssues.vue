<template>
  <div>
    <h1>Lista de tareas e incidencias</h1>
    <form @submit.prevent="addTodo()">
      <el-input placeholder="todo" v-model="todo"></el-input>
    </form>
    <el-row :gutter="12">
      <TodoItem v-for="(todo, index) in todos" :key="'todo-' + index" :todo="todo" :index="index" @done="removeTodo" />
      <TodoItem v-for="(issue, index) in issues" :key="'issue-' + index" :todo="issue.title" :index="index" @done="closeIssue" />
    </el-row>
  </div>
</template>

<script>
import axios from 'axios';
import TodoItem from '@/components/TodoItem';

const client = axios.create({
  baseURL: process.env.VUE_APP_GITHUB_ENDPOINT,
  headers: {
    'Authorization': `token ${process.env.VUE_APP_GITHUB_TOKEN}`,
    'Accept': 'application/vnd.github.v3+json',
    'Content-Type': 'application/json',
  },
});

export default {
  name: 'TodosIssues',
  components: {
    TodoItem
  },
  data() {
    return {
      todo: '',
      todos: [],
      issues: []
    };
  },
  created() {
    this.getIssues();
  },
  methods: {
    addTodo() {
      this.todos.push(this.todo);
      this.todo = '';
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
    },
    closeIssue(index) {
      const issue = this.issues[index];
      client.patch(`/issues/${issue.number}`, { state: 'closed' })
        .then(() => {
          this.issues.splice(index, 1);
        })
        .catch((error) => {
          console.error('Error closing issue:', error);
        });
    },
    getIssues() {
      client.get('/issues')
        .then((res) => {
          this.issues = res.data;
        })
        .catch((error) => {
          console.error('Error fetching issues:', error);
        });
    }
  }
};
</script>
