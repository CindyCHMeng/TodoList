<template>
  <div class="content">
    <div class="title">
      <font-awesome-icon class="sidebar-btn icon-btn" :icon="['fas', 'bars']" @click="showSidebar" />
      <font-awesome-icon class="icon-btn" :icon="['fas', 'trash']" @click="deleteTodo" />
    </div>

    <div class="data-row">
      <div class="data-item">
        <div class="item-title">Title</div>
        <input type="text" v-model="todoData.title"  @change="onDataChange" />
      </div>

      <div class="data-item">
        <div class="item-title">Date</div>
        <div class="date-block">
          <input type="date" v-model="todoData.dateFrom" @change="onStartDateChange" />
          <div class="cust-txt"> ~ </div>
          <input type="date" v-model="todoData.dateTo" :min="minEndDate" @change="onDataChange" />
        </div>
      </div>
    </div>

    <div class="data-row image-row">
      <div class="data-item">
        <div class="item-title">Image</div>
        <div class="img-input-block">
          <div class="file-block">
            <input class="cust-btn" type="button" value="Upload Image" />
            <input class="cust-flie-upload" type="file" accept="image/*" ref="inputFile" @change="onFileChange" />
          </div>
          <div class="cust-txt"> or </div>
          <input type="text" v-model="todoData.inputUrl" placeholder="請輸入圖片網址" @input="onUrlInput" />
        </div>
      </div>

      <div class="data-item">
        <div class="preview-img" >
          <img v-if="todoData.imageUrl" :src="todoData.imageUrl" />
        </div>
      </div>
    </div>

    <div class="data-row">
      <div class="data-item full">
        <div class="item-title">Content</div>
        <textarea
          class="cust-textarea"
          v-model="todoData.content"
          rows="5"
          :maxlength="contentMaxCount"
          @change="onDataChange"
        ></textarea>
        <div class="char-count"> {{ contentMaxCount }}/{{ todoData.content?.length || 0 }} </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'TodoItem',
  props: {
    todoDataProp: { required: true,  default: null, type: Object },
    itemIdx: { required: true, default: 0, type: Number },
  },
  watch: {
    todoDataProp: {
      deep: true,
      handler(newVal) {
        this.todoData = newVal || {};
      }
    }
  },
  data () {
    return {
      minEndDate: null,
      uploadFileUrl: null,
      contentMaxCount: 200,
      todoData: {
        title: "new item title",
        inputUrl: null,
        imageUrl: null,
        dateFrom: null,
        dateTo: null,
        content: "",
      },
    }
  },
  methods: {
    initTodoData() {
      this.todoData = this.todoDataProp || {};
    },
    onStartDateChange() {
      if (this.todoData.dateFrom) {
        const start = new Date(this.todoData.dateFrom);
        let minDate = new Date(this.todoData.dateFrom);

        minDate = minDate.setDate(start.getDate() + 1);
        this.minEndDate = new Date(minDate).toISOString().split('T')[0];

        if (this.todoData.dateTo) {          
          const end = new Date(this.todoData.dateTo);

          if (start >= end) {
            this.todoData.dateTo = null;
          }
        }
      }

      this.onDataChange();
    },
    onFileChange(event) {
      const file = event.target.files[0];

      if (file && file.type.startsWith('image/')) {
        if (this.uploadFileUrl) {
          URL.revokeObjectURL(this.uploadFileUrl);
        }

        this.todoData.inputUrl = null;
        this.uploadFileUrl = URL.createObjectURL(file);
        this.todoData.imageUrl = this.uploadFileUrl;        
        this.onDataChange();
      }
    },
    onUrlInput() {
      const url = this.todoData.inputUrl?.trim() || '';

      if (url === '') {
        this.todoData.imageUrl = null;
        this.onDataChange();
      } else {
        const pattern = /\.(jpeg|jpg|png|gif|bmp|webp|svg)(\?.*)?$/i;

        if (pattern.test(url)) {
          if (this.uploadFileUrl) {
            URL.revokeObjectURL(this.uploadFileUrl);
            this.$refs.inputFile.value = "";
            this.uploadFileUrl = null;
          }

          this.todoData.imageUrl = url;
          this.onDataChange();
        }
      }
    },
    onDataChange() {
      this.$emit("data-update", this.todoData, this.itemIdx);
    },
    deleteTodo() {
      this.$emit("delete-todo", this.itemIdx);
    },
    showSidebar() {
      this.$emit("show-sidebar");
    }
  },
  mounted () {
    this.initTodoData();
  }
}
</script>

<style scoped>
.content {
  box-sizing: border-box;
  width: 100%;
  height: 100%;
  padding: 0 1rem 1rem 1rem;
  overflow: auto;
  background-color: #FFFFFF;
}

.title {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  height: 50px;

  .icon-btn {
    cursor: pointer;
  }

  .sidebar-btn {
    display: none;
  }
}

.data-row {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  box-sizing: border-box;
  width: 100%;

  &.image-row {
    align-items: end;
  }

  .data-item {
    position: relative;
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    box-sizing: border-box;
    width: calc(50% - 0.5rem);
    margin-bottom: 1rem;

    &.full {
      width: 100%;
    }

    input, textarea {
      box-sizing: border-box;
      width: 100%;
      border: 0;
      border-radius: 5px;
      padding: 0.5rem;
      background: #EBEBEB;

      &:focus-visible {
        outline: 0;
      }
    }

    input {
      box-sizing: border-box;
      height: 38px;
    }

    .item-title {
      box-sizing: border-box;
      height: 24px;
    }

    .date-block {
      display: flex;
      justify-content: space-between;
      width: 100%;

      input {
        width: calc((100% - 26px) / 2);
      }
    }

    .cust-txt {
      display: flex;
      align-items: center;
      justify-content: center;
      padding: 0.5rem;
    }

    .img-input-block {
      display: flex;
      flex-direction: column;
      box-sizing: border-box;
      width: 100%;  
    }

    .file-block {
      position: relative;
      display: flex;
      width: 100%;      

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
          color: #EBEBEBE;
        }
      }

      .cust-flie-upload {
        position: absolute;        
        height: 100%;
        opacity: 0;
        cursor: pointer;
      }
    }
    
    .preview-img {
      box-sizing: border-box;
      width: 100%;
      height: 110px;
      background-color: #EBEBEB;
      border-radius: 5px;
      overflow: hidden;

      img {
        object-fit: contain;
        width: 100%;
        height: 100%;
      }
    }

    .char-count {
      position: absolute;
      bottom: 0;
      right: 0;
      display: inline-block;
      height: 20px;
      box-sizing: border-box;
      padding: 0 5px;
      border-bottom-right-radius: 5px;
      background: #E7FFE9;
      line-height: 22px;
      text-align: center;
      font-size: 14px;

      &::before {
        content: '';
        position: absolute;
        top: 0;
        right: 100%;        
        display: block;
        width: 0;
        height: 0;
        border-left: 20px solid transparent;
        border-bottom: 20px solid #E7FFE9;
      }
    }
  }
}

@media screen and (max-width: 767px) {
  .title {
    justify-content: space-between;

    .sidebar-btn {
      display: block;
    }
  }

  .data-row {
    .data-item {
      width: 100%;
    }
  }	
}
</style>
