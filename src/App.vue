<template>
  <div class="app__main">
    <div class="app__block">
      <h1 class="app__title">Todo: </h1>
      <todo-list
        :items="filtredItems.length ? filtredItems : items"
      />
      <todo-list-filter />
    </div>
  </div>
</template>

<script>
import TodoList from '@/components/TodoList';
import TodoListFilter from '@/components/TodoListFilter';
import {EventBus} from '@/eventBus';

export default {
  name: 'App',
  components: {
    TodoList,
    TodoListFilter,
  },
  data() {
    return {
      items: [],
      filtredItems: [],
      sought: {},
      soughtIndex: null,
      projects: [],
      count: 0,
    }
  },
  mounted() {
    EventBus.$on('addTask', (value) => {
      this.items.push(value);
    });
    EventBus.$on('deleteItem', (value) => {
      this.items.forEach((el, i) => {
        if (el.id === value.id) {
          this.soughtIndex = i;
        }
        if (el.nameProject === value.nameProject) {
          this.count++;
        }
      });
      this.items.splice(this.soughtIndex, 1);
      if (this.count === 1) {
        EventBus.$emit('deleteProject', { value: value.nameProject, maybe: false } );
      }
      this.count = 0;
    });
    EventBus.$on('changeTask', (value) => {
      this.items.forEach((el, i) => {
        if (el.id === value.id) {
          this.sought = i;
        }
        if (el.nameProject === value.nameProject) {
          this.count++;
        }
      });
      this.$set(this.items, this.sought, value);
      if (this.count === 1) {
        EventBus.$emit('deleteProject', { value: value.nameProject, maybe: true } );
      }
      this.count = 0;
    });
    EventBus.$on('sortPriority', (value) => {
      if (value) {
        this.sort(this.filtredItems.length ? this.filtredItems : this.items, 'priority');
      } else {
        this.sort(this.filtredItems.length ? this.filtredItems : this.items, 'title');
      }
    });
    EventBus.$on('filterProject', (value) => {
      if (value !== 'Все') {
        this.filtredItems = this.items.filter((el) => el.nameProject === value);
      } else {
        this.filtredItems = [];
      }
    });
    EventBus.$on('addDemo', (value) => {
      this.items = value;
      this.items.forEach((el) => {
        if(!this.projects.includes(el.nameProject)) {
          this.projects.push(el.nameProject);
        }
      });
      EventBus.$emit('initProjects', this.projects);
    });
  },
  methods: {
    sort(arr, field) {
      if (field === 'title') {
        return arr.sort((a, b) => {
          let aTitle = a.title.toLowerCase();
          let bTitle = b.title.toLowerCase();
          if (aTitle < bTitle) return -1;
          if (aTitle > bTitle) return 1;
          return 0;
        })
      }
      if (field === 'priority') {
        return arr.sort((a, b) => {
          return a.priority - b.priority;
        })
      }
    }
  }
}
</script>

<style scoped>
  .app__main {
    display: flex;
    justify-content: center;
  }

  .app__block {
    display: flex;
    flex-direction: column;
    width: 500px;
  }

  .app__title {
    text-align: center;
  }
</style>
