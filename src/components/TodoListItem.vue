<template>
  <div class="todoitem__main">
    <div class="todoitem__block">
      <p class="todoitem__title">{{item.title}}</p>
      <div class="todoitem__description">
        <p>Проект: {{item.nameProject}}</p>
        <p>Приоритет: {{item.priority}}</p>
      </div>
      <div class="todoitem__text" v-show="isOpen">{{item.description}}</div>
      <div class="todoitem__action">
        <button @click="changeItem">Изменить</button>
        <button @click="deleteItem">Закрыть</button>
        <button @click="isOpen = !isOpen">{{isOpen ? 'свернуть' : 'развернуть'}}</button>
      </div>
    </div>
  </div>
</template>

<script>
  import {EventBus} from '@/eventBus';

  export default {
    name: "TodoListItem",
    props: {
      item: {
        type: Object,
        default: () => ({}),
      },
    },
    data() {
      return {
        isOpen: false,
      };
    },
    mounted() {
      // console.log(this.item);
    },
    methods: {
      deleteItem() {
        EventBus.$emit('deleteItem', this.item)
      },
      changeItem() {
        EventBus.$emit('changeItem', this.item)
      }
    }
  }
</script>

<style scoped>
.todoitem__main {
  padding: 15px;
  border: 1px solid;
  margin-top: 20px;
}


.todoitem__description {
  display: flex;
  justify-content: space-between;
}

.todoitem__action {
  display: flex;
  justify-content: space-between;
}

.todoitem__title {
  margin-top: 0;
}
</style>
