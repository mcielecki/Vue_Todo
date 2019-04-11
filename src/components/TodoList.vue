<template>
  <div>
      <input type="text" class="todo-list" placeholder="Things to do" v-model="newTodo" @keyup.enter="addTodo">
      <div v-for="(todo, index) in todosFiltered" :key="todo.id" class="todo-item">
        <div class="todo-item-left">
            <input type="checkbox" v-model="todo.completed">
            <div v-if="!todo.editing" @dblclick="editTodo(todo)" class="todo-item-label" :class="{ completed : todo.completed}">{{todo.title}}</div>
            <input v-else class="todo-item-edit" type="text" v-model="todo.title" @blur="doneEdit(todo)" @keyup.enter="doneEdit(todo)" @keyup.esc="cancelEdit(todo)" v-focus>
        </div>
        <div class="close" @click="removeTodo(index)">&times;</div>
      </div>
    <div class="extra-container">
        <div><label><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Check all </label></div>
        <div>{{remaining}} items left </div>
    </div>
       <div class="extra-container">
           <div>
               <button :class="{active: filter == 'all' }" @click="filter = 'all'">All</button>
               <button :class="{active: filter == 'active' }" @click="filter = 'active'">Active</button>
               <button :class="{active: filter == 'completed' }" @click="filter = 'completed'">completed</button>
           </div>
            <div>
                <transition name="fade">
                    <button v-if="showClearCompletedButton" @click="clearCompleted">clearCompleted</button>
                </transition>
            </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'todo-list',
  data () {
    return {
        newTodo: '',
        idForTodo: 3,
        beforeEditCache: '',
        filter: 'all',
 
        todos: [
            {
                'id': 1,
                'title': 'Rzecz do zrobienia nr 1',
                'completed': false,
                'editing': false,
            },
            {
                'id': 2,
                'title': 'Rzecz do zrobienia nr 2',
                'completed': false,
                'editing': false,
            },
        ]
    }
  },
  computed: {
      remaining() {
          return this.todos.filter(todo => !todo.completed).length
      },
      anyRemaining() {
          return this.remaining != 0
      },
      todosFiltered() {
          if(this.filter == 'all') {
              return this.todos
          }
          else if (this.filter == 'active') {
              return this.todos.filter(todo => !todo.completed)
          }
            else if (this.filter == 'completed') {
              return this.todos.filter(todo => todo.completed)
          }
          return this.todos
      },
      showClearCompletedButton() {
          return this.todos.filter(todo => todo.completed).length > 0
      }
  },
  directives: {
  focus: {
    // directive definition
    inserted: function (el) {
      el.focus()
    }
  }
},
  methods: {
      addTodo() {
          if (this.newTodo != "")
          {
          this.todos.push({
              id: this.idForTodo,
              title: this.newTodo,
              completed: false,
              editing: false,
          })
          this.newTodo = '',
          this.idForTodo++
          }

        },
        editTodo(todo) {
          this.beforeEditCache = todo.title
          todo.editing = true
        },
        doneEdit(todo) {
            if(todo.title.trim() == '') {
                todo.title = this.beforeEditCache
            }
          todo.editing = false
        },
        removeTodo(index) {
          this.todos.splice(index, 1);
        },
        cancelEdit(todo) {
            todo.title = this.beforeEditCache
            todo.editing = false
        },
        checkAllTodos() {
      this.todos.forEach((todo) => todo.completed = event.target.checked)
        },
        clearCompleted() {
            this.todos = this.todos.filter(todo => !todo.completed)
        }
  }
}
</script>


<style lang="scss">
.todo-list {
    width: 100%;
    padding: 10px 18px;
    font-size: 18px;
    margin-bottom: 16px;
    &:focus {
        outline: 0;
    }
}
.todo-item {
    display: flex;
    justify-content: space-between;
    align-items: center;
    padding: 10px;
}
.close {
    width: 20px;
    height: 20px;
    background:red;
    color: white;
    line-height: 20px;
    text-align: center;
    cursor: pointer;
    font-weight: bold;
    font-size: 16px;
}
.todo-item-left {
    display: flex;
    align-items: center;
}
.todo-item-label {
    padding: 10px;
    border: 1px solid white;
    margin-left: 12px;
}
.todo-item-edit {
    font-size: 24px;
    color: #2c3e50;
    margin-left: 12px;
    width: 100%;
    padding: 10px;
    border: 1px solid #ccc;
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    &:focus {
        outline: none;
    }
}
.completed {
    text-decoration: line-through;
    color: grey;
}
.extra-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    font-size: 16px;
    border-top: 1px solid lightgrey;
    padding-top: 15px;
    margin-bottom: 15px;
}
button {
    font-size: 14px;
    background-color: #fff;
    appearance: none;
    &:hover {
        background: lightgreen;
    }
    &:focus {
        outline: none;
    }
}
.active {
    background: lightgreen;
}
.fade-enter-active, .fade-leave-to {
    transition: opacity .2s;
}
.fade-enter, .fade-leave-to {
    opacity: 0;
}
</style>
