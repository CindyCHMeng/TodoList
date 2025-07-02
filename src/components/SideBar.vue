<template>
  <div class="side-bar">
    <div>
      <div class="title">
        Demo Todo List
      </div>

      <div class="list-block">
        <div
          class="list-item"
          v-for="(item, index) in todoListData"
          :key="index"
          @click="changeSelIdx(index)"
        >
          <div class="item-content">
            <div class="item-title" :class="{ 'sel-item': (index === currentSel) }">
              {{ index + 1 }}. {{ item.title }}
            </div>
          </div>
          <div v-show="(index === currentSel)" class="sel-icon"></div>
        </div>
      </div>

      <div class="btn-block">
        <button class="cust-btn" :disabled="todoListData.length >= maxCount" @click="addTodo()">Add Item</button>
      </div>
    </div>
    
    <div class="random-img" @click="refreshImg" >
      <img v-if="imageUrl" :src="imageUrl" />
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoList',
  props: {
    todoListData: { required: true, type: Array },
    currentSel: { default: 0, type: Number },
  },
  data() {
    return {
      maxCount: 10,
      imageUrl: null
    }
  },
  methods: {
    init() {
      this.refreshImg();
    },
    addTodo() {
      this.$emit("add-todo");
    },
    changeSelIdx(index) {
      this.$emit("change-sel-idx", index);
    },
    refreshImg() {
      this.imageUrl = `https://picsum.photos/600/200?random=${Date.now()}`;
    }
  },
  mounted() {
    this.init();
  }
}
</script>

<style scoped>
.side-bar {
  box-sizing: border-box;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: 100%;
  overflow: auto;
  background-color: #A1FFC7;
}

.title {
  display: flex;
  align-items: center;
  height: 50px;
  padding: 0 1rem;
  box-sizing: border-box;
  font-weight: bold;
}

.list-block {
  .list-item {
    display: flex;
    align-items: center;
    justify-content: space-between;
    height: 35px;
    background-color: #81F8B1;
    cursor: pointer;

    &:not(:last-child) {
      margin-bottom: 4px;
    }

    .item-content {
      display: flex;
      align-items: center;
      box-sizing: border-box;
      padding: 1rem;
      overflow: hidden;

      .item-title {        
        overflow: hidden;
        white-space: nowrap;
        text-overflow: ellipsis;
      }      

      .sel-item {
        font-weight: bold;
      }
    }

    .sel-icon {
      box-sizing: border-box;
      width: 0;
      height: 0;
      border-style: solid;
      border-width: 17px 14px 17px 0;
      border-color: transparent #fff transparent transparent;
    }
  }
}

.btn-block {
  box-sizing: border-box;
  margin: 1rem;

  .cust-btn {
    box-sizing: border-box;
    width: 100%;
    padding: 0.5rem;
    border: 0;
    border-radius: 5px;
    background-color: #E7FFE9;

    &:hover:not(:disabled) {
      background-color: #81F8B1;
    }

    &:disabled {
      color: #EBEBEB;
    }
  }
}

.random-img {
  box-sizing: border-box;
  min-height: 56px;
  margin: 1rem;
  border-radius: 5px;
  overflow: hidden;
  cursor: pointer;

  img {
    object-fit: contain;
    width: 100%;
    border-radius: 5px;
  }
}
</style>
