<template>
  <div class="todofilter__main">
    <div class="todofilter__filter" v-if="!isCreate">
      <button @click="changeView">Добавить</button>
      <button
        v-if="isDemo"
        class="todofilter__demo"
        @click="addDemo"
      >Добавить демо данные</button>
      <input
        class="todofilter__checkbox"
        type="checkbox"
        v-model="checked"
        @click="filterPriority"
      > по приоритету
      <select
        v-model="project"
        class="todofilter__select"
        @change="changeProject"
      >
        <option>Все</option>
        <option v-for="project of projects" :key="project">{{project}}</option>
      </select>
    </div>
    <div class="todofilter__create" v-else>
      <form class="todofilter__form">
        <fieldset>
          <label for="todo-title">Заголовок задачи</label>
          <input type="text" id="todo-title" v-model="task.title">
        </fieldset>
        <fieldset>
          <label for="todo-project">Название проекта</label>
          <input type="text" id="todo-project" v-model="task.nameProject">
        </fieldset>
        <fieldset>
          <label for="todo-priority">Приоритет</label>
          <select id="todo-priority" v-model="task.priority">
            <option v-for="priority of priorities" :key="priority">{{priority}}</option>
          </select>
        </fieldset>
        <fieldset>
          <label for="todo-description">Описание</label>
          <textarea v-model="task.description" id="todo-description"></textarea>
        </fieldset>
        <div class="form__action">
          <button type="submit" @click.prevent="save" class="form__submit">Сохранить</button>
          <button @click="changeView">Отмена</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
  import {EventBus} from '@/eventBus';
  import {demoData} from '@/testData';

  export default {
    name: "TodoListFilter",
    data() {
      return {
        task: {
          title: '',
          nameProject: '',
          description: '',
          priority: 1,
        },
        id: 0,
        priorities: [1, 2, 3, 4],
        isCreate: false,
        isChange: false,
        isDemo: true,
        checked: false,
        projects: [],
        project: 'Все',
        oldProject: '',
        count: 0,
        demo: demoData,
      }
    },
    mounted() {
      EventBus.$on('changeItem', (value) => {
        this.task = {
          id: value.id,
          title: value.title,
          nameProject: value.nameProject,
          description: value.description,
          priority: value.priority,
        };
        this.changeView();
        this.isChange = true;
      });
      EventBus.$on('deleteProject', (value) => {
        if (!value.maybe) {
          this.projects.splice(this.projects.indexOf(value), 1)
        } else {
          this.oldProject = value.value;
        }
      });
      EventBus.$on('initProjects', (value) => {
        this.projects = value;
      });
    },
    methods: {
      changeView() {
        this.isCreate = !this.isCreate;
      },
      save() {
        if (this.isChange) {
          this.changeTask();
        } else {
          this.addTask();
        }
      },
      addTask() {
        this.task.id = this.id;
        if (this.projects.indexOf(this.task.nameProject) === -1) {
          this.projects.push(this.task.nameProject);
        }
        EventBus.$emit('addTask', this.task);
        this.changeView();
        this.$nextTick(() => {
          this.task = {
            title: '',
            nameProject: '',
            description: '',
            priority: 1,
          };
          this.id++;
        });
      },
      changeTask() {
        if (this.task.nameProject !== this.oldProject) {
          this.projects.splice(this.projects.indexOf(this.oldProject), 1)
        }
        if (this.projects.indexOf(this.task.nameProject) === -1) {
          this.projects.push(this.task.nameProject);
        }
        EventBus.$emit('changeTask', this.task);
        this.changeView();
        this.$nextTick(() => {
          this.task = {
            title: '',
            nameProject: '',
            description: '',
            priority: 1,
          };
        });
      },
      filterPriority() {
        this.checked = !this.checked;
        EventBus.$emit('sortPriority', this.checked);
      },
      changeProject() {
        EventBus.$emit('filterProject', this.project);
      },
      addDemo() {
        this.isDemo = !this.isDemo;
        EventBus.$emit('addDemo', this.demo);
      }
    }
  }
</script>

<style scoped>
.todofilter__main {
  display: flex;
  align-items: center;
  padding: 15px;
  border: 1px solid #000;
  margin-top: 20px;
}

.todofilter__filter {
  display: flex;
  align-items: center;
}

.todofilter__form {
  display: flex;
  flex-direction: column;
}

.todofilter__checkbox {
  margin-left: 15px;
}

.todofilter__select {
  margin-left: 15px;
}

fieldset {
  border: none;
  display: flex;
  align-items: center;
}

fieldset select, fieldset textarea, fieldset input {
  margin-left: 10px;
}

.form__submit, .form__action {
  margin-right: 15px;
}

.todofilter__demo {
  margin-left: 15px;
}

</style>
