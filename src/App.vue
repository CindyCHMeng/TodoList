<template>
  <div class="side-bar-content">
    <SideBar :todoListData="todoList" :currentSel="curIdx" @add-todo="addTodo" @change-sel-idx="changeSelIdx" />
  </div>

  <div class="body-content">
    <TodoItem :todoDataProp="todoList[curIdx]" :itemIdx="curIdx" @data-update="updateTodo" @delete-todo="deleteTodo" @show-sidebar="showDrawer" />
  </div>

  <div v-if="openDrawer" class="drawer-backdrop" @click.self="hideDrawer">
    <div class="drawer-panel">
      <SideBar :todoListData="todoList" :currentSel="curIdx" @add-todo="addTodo" @change-sel-idx="changeSelIdx" />
    </div>
  </div>
</template>

<script>
import TodoItem from './components/TodoItem.vue';
import SideBar from './components/SideBar.vue';
import * as Utils from '@/utils/utils';

export default {
  name: 'App',
  components: {
    TodoItem,
    SideBar
  },
  data() {
    return {
      todoList: [],
      curIdx: 0,
      openDrawer: false
    }
  },
  methods: {
    initTodoList() {
      let todoArr = Utils.getItem('todoList');
      if (todoArr) {
        this.todoList = todoArr;
        this.changeSelIdx(0);
      }
    },
    changeSelIdx(index) {
      this.curIdx = index;
    },
    addTodo() {
      let todoObj = {
        title: "new item title",
        image: null,
        dateFrom: null,
        dateTo: null,
        content: "",
      };

      let tempList = Utils.getItem('todoList');

      if (tempList) {
        tempList.push(todoObj);
        Utils.setItem('todoList', tempList);
      } else {
        let tempData = [];
        tempData.push(todoObj);
        Utils.setItem('todoList', tempData);
      }

      this.todoList.push(todoObj);
      this.changeSelIdx(this.todoList.length - 1);
    },
    deleteTodo(index) {
      this.todoList.splice(index, 1);
      Utils.setItem('todoList', this.todoList);

      if (this.todoList.length === 0) {
        this.changeSelIdx(0);
      } else if (this.curIdx > this.todoList.length - 1) {
        this.changeSelIdx(this.todoList.length - 1);
      }
    },    
    updateTodo(data, index) {
      if (index < this.todoList.length) {
        this.todoList[index] = data;
        Utils.setItem('todoList', this.todoList);
      }      
    },
    showDrawer() {
      this.openDrawer = true;
    },
    hideDrawer() {
      this.openDrawer = false;
    },
    clearData() {
      localStorage.clear();
      this.todoList = [];
      this.changeSelIdx(0);
    }
  },
  mounted() {
    this.initTodoList();
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  box-sizing: border-box;
  height: 100vh;
  width: 100vw;
}

.side-bar-content {
  box-sizing: border-box;
  width: 200px;
  min-width: 200px;
}

.body-content {
  box-sizing: border-box;
  width: calc(100% - 200px);
}

.drawer-backdrop {
  position: fixed;
  display: flex;
  width: 100%;
  height: 100%;
  z-index: 999;
  background: rgba(0, 0, 0, 0.4);

  .drawer-panel {
    position: relative;
    box-sizing: border-box;
    width: 200px;
    height: 100%;
  }
}

@media screen and (max-width: 767px) {
	.side-bar-content {
		display: none;
	}

	.body-content {
		box-sizing: border-box;
		width: 100%;
	}
}
</style>
