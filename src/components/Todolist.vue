<template>
  <div class="main-container">
    <h1>{{ msg }}</h1>
    <div id="search-box-container">
      <input
        type="text"
        class="todo-input"
        v-model="newTodo"
        @keyup.enter="addTodo"
        id="search-input"
        required
        @click="showBtnSearch = true"
        v-focus
      >
      <label id="search-label" for="search-input">To be done</label>
    </div>
    <div id="search-button-container">
      <a
        v-show="showBtnSearch"
        type="button"
        @click="addTodo"
        id="search-button"
        class="btn btn-primary"
      >Add new tarea</a>
    </div>
    <div id="box-items">
      <Todoitem
        v-for="(todo, index) in todosFilter"
        :key="index"
        :todo="todo"
        :index="index"
        :checkAll="!anyRemaining"
      ></Todoitem>
    </div>
    <div id="box-checks">
      <div class="checks-label">
        <label>
          <input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos();">
          Check All
        </label>
      </div>
      <div class="checks-remaining">
        <small>{{ remaining }} items left</small>
      </div>
    </div>
    <div id="box-filters">
      <button @click="filter = 'all';" :class="{ currentFilterAll: filter === 'all' }">all</button>
      <button
        @click="filter = 'active';"
        :class="{ currentFilterActive: filter === 'active' }"
      >active</button>
      <button
        @click="filter = 'completed';"
        :class="{ currentFilterCompleted: filter === 'completed' }"
      >completed</button>
    </div>
    <div id="clearAll-box">
      <button v-if="remaining < todos.length" @click="clearAllCompleted">Clear all completed</button>
    </div>
  </div>
</template>

<script>
import Todoitem from "./Todoitem";

export default {
  name: "TodoList",
  components: { Todoitem },
  data() {
    return {
      msg: "TODO LIST",
      newTodo: "",
      beforeEdit: "",
      filter: "all",
      todos: [
        { title: "Finish Vue Screencast", completed: false, editing: false },
        { title: "Take over world", completed: false, editing: false },
        { title: "Do Homework", completed: false, editing: false },
        { title: "Take care of brother", completed: false, editing: false },
        { title: "Practice vue examples", completed: false, editing: false },
        { title: "Coding time", completed: false, editing: false }
      ],
      showBtnSearch: false
    };
  },
  created() {
    eventBus.$on("deleTodo", index => this.deleteTodo(index));
    eventBus.$on("finishedEdit", data => this.doneEdit(data));
  },
  directives: {
    focus: {
      // directive definition
      inserted: function(el) {
        el.focus();
      }
    }
  },
  computed: {
    todosFilter() {
      if (this.filter === "all") {
        return this.todos;
      } else if (this.filter === "active") {
        return this.todos.filter(todo => {
          return !todo.completed;
        });
      } else if (this.filter === "completed") {
        return this.todos.filter(todo => {
          return todo.completed === true;
        });
      }
    },
    remaining() {
      return this.todos.filter(e => {
        return e.completed === false;
      }).length;
    },
    anyRemaining() {
      return this.remaining;
    }
  },
  methods: {
    addTodo() {
      let regexSpaces = /^\S+(?: \S+)*$/;

      if (this.newTodo && this.newTodo.match(regexSpaces)) {
        let newTodoUpperCase = this.newTodo.replace(
          this.newTodo[0],
          this.newTodo.split("")[0].toUpperCase()
        );
        this.todos.push({
          title: newTodoUpperCase,
          completed: false,
          editing: false
        });
        this.newTodo = "";
      }
      this.newTodo = "";
    },
    editTodo(data) {
      this.beforeEdit = data.todo.title;
      this.todos[data.index].editing = true;
    },
    doneEdit(data) {
      this.todos.splice(data.index, 1, data.todo);
    },
    deleteTodo(index) {
      this.todos.splice(index, 1);
    },
    checkAllTodos() {
      this.todos.forEach(todo => {
        todo.completed = event.target.checked;
      });
    },
    clearAllCompleted() {
      this.todos = this.todos.filter(todo => {
        return !todo.completed;
      });
    }
  }
};
</script>

<style scoped>
.main-container {
  min-height: 500px;
  display: flex;
  align-items: center;
  flex-direction: column;
}
.todo-items ul {
  list-style-type: none;
}
.completed {
  text-decoration: line-through;
  color: grey;
}
button {
  padding: 5px 20px;
  font-size: 0.9em;
  border: none;
  outline: none;
  border-radius: 2px;
  transition: 0.2s;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.15);
}
button:hover {
  cursor: pointer;
}
.currentFilterAll {
  background: #348dc9;
  outline: none;
  color: white;
}
.currentFilterActive {
  background: #69d472;
  outline: none;
  color: white;
}
.currentFilterCompleted {
  background: #e7391a;
  outline: none;
  color: white;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.15);
}
#clearAll-box {
  width: 229.438px;
  box-sizing: border-box;
}
#clearAll-box > button {
  width: 100%;
  height: 30px;
  margin: 10px 0;
  padding: 0;
  background: #f52f0c;
  transition: 0.3s;
  border-radius: 2px;
  color: #ffffff;
  font-weight: bold;
}
#clearAll-box:hover > button {
  background: #bb3018;
}
#search-input {
  border: 0;
  border-bottom: 2px solid #9e9e9e;
  outline: none;
  transition: 0.2s;
  box-sizing: border-box;
}

#search-label {
  top: 0;
  left: 0;
  right: 0;
  color: #616161;
  display: flex;
  align-items: center;
  position: absolute;
  font-size: 1rem;
  cursor: text;
  transition: 0.2s;
  box-sizing: border-box;
}

#search-input,
#search-label {
  width: 100%;
  height: 3rem;
  font-size: 1rem;
}

/* Interation */
#search-input:valid,
#search-input:focus {
  border-bottom: 2px solid #26a69a;
}

#search-input:valid + #search-label,
#search-input:focus + #search-label {
  color: #26a69a;
  font-size: 0.8rem;
  top: -30px;
  pointer-events: none;
}
#search-box-container {
  width: 80%;
  position: relative;
  display: flex;
  margin-bottom: 10px;
}
#search-button-container {
  width: 80%;
  display: flex;
  align-items: center;
  justify-content: center;
}
#search-button {
  width: 100%;
  height: 30px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #41b883;
  color: white;
  transition: 0.3s;
  border-radius: 2px;
  margin-bottom: 10px;
  font-weight: bold;
  border: none;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.15);
}
#search-button:hover {
  background: rgb(62, 170, 121);
  color: white;
  transition: 0.3s;
  cursor: pointer;
}
#box-items {
  border: 1px solid transparent;
  width: 80%;
  min-height: 100px;
  display: flex;
  flex-direction: column;
  background: white;
  color: #113654;
}
button {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
}
#box-checks {
  width: 80%;
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin: 10px 0;
}
#box-checks > .checks-label {
  margin-left: 2px;
}
#box-checks > .checks-remaining {
  margin-right: 9px;
}
</style>
