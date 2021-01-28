<template>
  <div class="todo__list">
    <div class="todo__block" v-if="items.length">
      <todo-list-item
        v-for="(item, index) of items"
        :key="index"
        :item="item"
      />
    </div>
    <div class="todo__block" v-else>
      <p>Нажмите на кнопку Добавить демо данные, для добавления демо задач </p>
    </div>
  </div>
</template>

<script>
  import TodoListItem from '@/components/TodoListItem';

  export default {
    name: "TodoList",
    components: {
      TodoListItem,
    },
    props: {
      items: {
        type: Array,
        default: () => [],
        required: false,
      }
    },
  }
</script>

<style scoped>
  .todo__list {
    max-height: 500px;
    border: 1px solid #000;
    padding: 15px;
    overflow: auto;
  }

</style>
