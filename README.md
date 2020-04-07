# vue_todolist_project

## Project setup
```
npm install

npm run serve

npm install axios
```

![vue_todolist_project](https://user-images.githubusercontent.com/33843231/78655949-7fcb5800-78e8-11ea-8a1a-859133918904.png)


## views/Home.vue
```
<template>
  <div id="app">
    <AddTodo v-on:add-todo="addTodo" />
    <Todos :todos="todos" v-on:del-todo="deleteTodo" />
  </div>
</template>

<script>
import Todos from '../components/Todos';
import AddTodo from '../components/AddTodo';
import axios from 'axios';

export default {
  name: 'Home',
  components: {
     Todos,
     AddTodo
  },

  data: function () {
      return {
        todos: []
      }
  },

  methods: {

    addTodo: function(newTodo) {

      const {title, completed} = newTodo;

      axios.post('https://jsonplaceholder.typicode.com/todos', {
        title,
        completed
        })
          .then(res => this.todos = [...this.todos, res.data])
          .catch(err => console.log(err));
          
    },

    deleteTodo: function (id) {

      axios.delete(`https://jsonplaceholder.typicode.com/todos/${id}`)
        .then(this.todos = this.todos.filter(todo => todo.id !== id))
        .catch(err => console.log(err));
    }
  },

  created () {
    axios.get('https://jsonplaceholder.typicode.com/todos?_limit=5')
         .then(res => this.todos = res.data)
         .catch(err => console.log(err));
  }


}

</script>

<style>

   * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
   }

   body{
     font-family: Arial, Helvetica, sans-serif;
     line-height: 1.4;
   }

</style>

```

## AddTodo.vue
```
<template>
    <div>
        <form @submit="addTodo">
            <input type="text" v-model="title" name="title" placeholder="Add Todo...">
            <input type="submit" value="Submit" class="btn">
        </form>
    </div>
</template>

<script>
//import uuid from 'uuid';
export default {

    data: function () {
        return {
            title: ''
        }
    },

    methods: {
        
        addTodo: function (e) {

            e.preventDefault();

            const newTodo = {
                title: this.title,
                completed: false
            };

            // Send up to parent

            this.$emit('add-todo', newTodo);

            this.title = '';
        }
    }
}
</script>

<style scoped>
    form{
        display: flex;
    }

    input[type="text"] {
        flex: 10;
        padding: 5px;
    }

    input[type="submit"] {
        flex: 2;
    }

    .btn{
        display: inline-block;
        border: none;
        background: #555;
        color: #fff;
        padding: 7px 20px;
        cursor: pointer;
    }

    .btn:hover{
        background: #666;
    }
</style>
```

## Todos.vue
```
<template>
    <div>
        <div v-for="todo in todos" :key="todo.id">
            <TodoItem :todo="todo" v-on:del-todo="$emit('del-todo', todo.id)" />
        </div>
    </div>
</template>

<script>
import TodoItem from './TodoItem';
export default {
    name: "Todos",
    props: ["todos"],
    components: {
        TodoItem
    },
}
</script>

<style scoped>

</style>


```

## TodoItem.vue
```
<template>
    <div class="todo-item" :class="{'is-complete' : todo.completed}">
        <p>
            <input type="checkbox" @change="markComplete">
            {{ todo.title }}
            <button @click="$emit('del-todo', todo.id)" class="del">X</button>
        </p>
    </div>
</template>

<script>
export default {
    name: "TodoItem",
    props: ["todo"],
    methods: {
        markComplete: function () {
            this.todo.completed = !this.todo.completed;
        }
    }
}
</script>

<style scoped>
    .todo-item {
        background: f4f4f4;
        padding: 10px;
        border-bottom: 1px #ccc dotted;
    }
    .is-complete {
        text-decoration: line-through;
    }
    .del {
        background: #ff0000;
        color: #fff;
        border: none;
        padding: 5px 9px;
        border-radius: 50%;
        cursor: pointer;
        float: right;
    }
</style>
```



