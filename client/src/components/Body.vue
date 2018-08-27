<template>
<div>
  <div class="form-row align-items-center" id="formAddTodo">
    <input type="text" v-model="newTodo" @keyup.enter="submitTodo(newTodo)" class="form-control" id="inputTodo" placeholder="Create new todo">
    <button type="button" @click="submitTodo" class="btn btn-info">Add Todo</button>
  </div>
  <div class="row" id="board">
    <div class="col sm-3">
      <div class="card" style="width: 18rem;">
        <button type="button" class="btn btn-danger">Backlog</button>
        <div class="card-body" v-for="(todo, index) in todos" v-bind:key="index">
          <h5 class="card-title">{{todo.todo}}</h5>
          <a href="#" class="btn btn-danger" @click="deleteTodo(todo.todo)">Delete</a>
          <a href="#" class="btn btn-warning" @click="addTodo(todo.todo)">To-Do</a>
          <hr>
        </div>
      </div>
    </div>
    <div class="col sm-3">
      <div class="card" style="width: 18rem;">
        <button type="button" class="btn btn-warning">Todo</button>
        <div class="card-body" v-for="(todo, index) in todoFields" v-bind:key="index">
          <h5 class="card-title">{{todo.todo}}</h5>
          <a href="#" class="btn btn-danger" @click="backStart(todo.todo)">Back</a>
          <a href="#" class="btn btn-primary" @click="addToDoing(todo.todo)">Doing</a>
          <hr>
        </div>
      </div>
    </div>
    <div class="col sm-3">
      <div class="card" style="width: 18rem;">
        <button type="button" class="btn btn-primary">Doing</button>
        <div class="card-body" v-for="(todo, index) in doingTodos" v-bind:key="index">
          <h5 class="card-title">{{todo.todo}}</h5>
          <a href="#" class="btn btn-warning" @click="backTodo(todo.todo)">Back</a>
          <a href="#" class="btn btn-success" @click="addToDone(todo.todo)">Done</a>
          <hr>
        </div>
      </div>
    </div>
    <div class="col sm-3">
      <div class="card" style="width: 18rem;">
        <button type="button" class="btn btn-success">Done</button>
        <div class="card-body" v-for="(todo, index) in doneTodos" v-bind:key="index">
          <h5 class="card-title">{{todo.todo}}</h5>
          <a href="#" class="btn btn-primary" @click="backToDoing(todo.todo)">Back</a>
          <a href="#" class="btn btn-danger" @click="deleteTodo(todo.todo)">Delete</a>
          <hr>
        </div>
      </div>
    </div>
  </div>
</div>
</template>

<script>
import firebase from 'firebase'
var config = {
  apiKey: 'AIzaSyD7eLv1tlPBAfxX3dCTgwAXwbD2uGvpr-E',
  authDomain: 'amazing-kanban-4a08a.firebaseapp.com',
  databaseURL: 'https://amazing-kanban-4a08a.firebaseio.com',
  storageBucket: ''
}
firebase.initializeApp(config)
let firebasetodos = firebase.database().ref('todos')
export default {
  name: 'forBody',
  props: [],
  data () {
    return {
      newTodo: '',
      todos: [],
      status: 'start',
      rawData: [],
      doingTodos: [],
      doneTodos: [],
      todoFields: []
    }
  },
  mounted () {
    this.gotData()
  },
  firebase () {
    return {
      gotodos: firebasetodos
    }
  },
  watch: {
    gotodos () {
      this.gotData()
    }
  },
  methods: {
    clear () {
      this.todos = []
      this.doingTodos = []
      this.doneTodos = []
      this.todoFields = []
    },
    submitTodo () {
      this.clear()
      firebase.database().ref('todos/' + this.newTodo).set({
        todo: this.newTodo,
        status: this.status
      })
      this.newTodo = ''
    },
    gotData () {
      var starCountRef = firebase.database().ref('todos/')
      starCountRef.on('value', snapshot => {
        var dataTodos = snapshot.val()
        var keys = Object.values(dataTodos)
        this.rawData = keys
        this.rawData.forEach(element => {
          if (element.status === 'start') {
            this.todos.push(element)
          } else if (element.status === 'todo') {
            this.todoFields.push(element)
          } else if (element.status === 'doing') {
            this.doingTodos.push(element)
          } else if (element.status === 'done') {
            this.doneTodos.push(element)
          }
        })
      })
    },
    deleteTodo (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set(null)
        }
      })
    },
    addTodo (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'todo' })
        }
      })
    },
    addToDoing (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'doing' })
        }
      })
    },
    backTodo (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'todo' })
        }
      })
    },
    backStart (key) {
      this.clear()
      this.gotodos.forEach(element => {
        console.log(element.todo)
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'start' })
        }
      })
    },
    addToDone (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'done' })
        }
      })
    },
    backToDoing (key) {
      this.clear()
      this.gotodos.forEach(element => {
        if (element.todo === key) {
          firebase.database().ref('todos/' + key).set({ todo: key, status: 'doing' })
        }
      })
    }
  }
}
</script>

<style scoped>
  #inputTodo {
    width: 40%;
    height: 40px;
    border-radius: 5px;
  }
  #formAddTodo{
    justify-content: center;
    margin: 30px;
  }
  #board{
    justify-content: center;
    margin-left: 10px;
  }
</style>
