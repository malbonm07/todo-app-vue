<template>
  <div id="box-item">
    <div v-if="!editing" @dblclick="editTodo" id="item-inpuTitle">
      <input type="checkbox" v-model="completed" @change="doneEdit" id="box-1">
      {{ todo.title }}
    </div>
    <div v-else>
      <input
        type="text"
        v-model="title"
        @keyup.enter="doneEdit"
        @blur="doneEdit"
        v-focus
        @keyup.esc="cancelEdit(todo);"
        id="item-editTitle"
      >
    </div>
    <div>
      <button type="button" id="item-button" @click="deleteTodo(index);">Remove</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "Todoitem",
  data() {
    return {
      title: this.todo.title,
      editing: this.todo.editing,
      completed: this.todo.completed,
      beforeEdit: ""
    };
  },
  props: {
    todo: {
      type: Object,
      required: true
    },
    index: {
      type: Number,
      required: true
    },
    checkAll: {
      type: Boolean,
      required: true
    }
  },
  directives: {
    focus: {
      // directive definition
      inserted: function(el) {
        el.focus();
      }
    }
  },
  watch: {
    checkAll() {
      if (this.checkAll) {
        this.completed = true;
      } else {
        this.completed = this.todo.completed;
      }
    },
    todo() {
      this.title = this.todo.title;
      this.completed = this.todo.completed;
    }
  },
  methods: {
    deleteTodo(index) {
      eventBus.$emit("deleTodo", index);
    },
    editTodo() {
      this.beforeEdit = this.title;
      this.editing = true;
    },
    doneEdit() {
      let regexSpaces = /^\S+(?: \S+)*$/;

      if (this.title && this.title.match(regexSpaces)) {
        this.editing = false;
      } else {
        this.cancelEdit();
      }
      eventBus.$emit("finishedEdit", {
        index: this.index,
        todo: {
          title: this.title,
          completed: this.completed,
          editing: this.editing
        }
      });
    },
    cancelEdit() {
      this.title = this.beforeEdit;
      this.editing = false;
    }
  }
};
</script>

<style scoped>
#box-item {
  width: 100%;
  margin: 10px 0;
  display: flex;
  justify-content: space-between;
}
#item-inputTitle {
  margin-left: 5px;
}
#item-editTitle {
  margin-left: 20px;
}
#item-button {
  padding: 4px 5px 4px 6px;
  margin-right: 5px;
  transition: .1s;
  outline: none;
  border-radius: 2px;
  font-size: 0.9em;
  color: #ffffff;
  border: none;
  box-shadow: 0 2px 4px 0 rgba(0,0,0,0.15);
  background: #f52f0c;
  border-radius: 3px;
}
#item-button:hover {
  background: #bb3018;
  cursor: pointer;
}
</style>

