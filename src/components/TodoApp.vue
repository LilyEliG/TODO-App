<template>
  <div :class="{ 'dark-bg': isDarkMode }">
    <main class="main mx-5 my-10">
      <header class="main__header flex justify-between items-center">
        <h1 class="main__title">Todo</h1>
        <label for="sun-moon">
          <img :src="isDarkMode ? sunIcon : moonIcon" alt="" class="main__mode-icon w-[20px] cursor-pointer" @click="toggleDarkMode">
          <input type="checkbox" name="sun-moon" id="sun-moon" class="hidden" v-model="isDarkMode">
        </label>
      </header>

      <div class="main__container grid gap-5 my-6">
        <form class="main__form relative flex items-center gap-2" @submit.prevent="addTask">
          <input type="checkbox" name="todo-checkbox" class="main__circle absolute">
          <input v-model="newTask" type="text" name="new-todo" id="new-todo" placeholder="Create new todo..."
                 class="main__input w-full rounded p-4" :class="{ 'grey-container': isDarkMode }">
        </form>

        <ul class="main__todo-list shadow-md rounded" :class="{ 'grey-container': isDarkMode }">
          <li v-for="(task, index) in filteredTasks" :key="index"
              class="main__todo-list-item flex items-center justify-between p-4"
              :class="{ 'grey-container': isDarkMode }"
              draggable="true">
            <label class="main__todo-list-item-label mr-auto flex items-center gap-4">
              <input type="checkbox" name="todo-checkbox" class="main__todo-list-checkbox" v-model="task.checked" @change="updateTask(task)">
              <p class="main__todo-list-item-description" :class="{ 'line-through': task.checked }">{{ task.description }}</p>
            </label>
            <img src='@/assets/images/icon-cross.svg' alt="" class="main__icon-cross cursor-pointer" @click="removeTask(index)">
          </li>

          <div class="main__todo-left-n-clear flex justify-between p-4" :class="{ 'grey-container': isDarkMode }">
            <p class="main__todo-left"><span class="main__todo-left-number">{{ activeTasks.length }}</span> items left</p>
            <p class="main__todo-clear cursor-pointer" @click="clearCompleted">Clear Completed</p>
          </div>
        </ul>
      </div>

      <div class="main__all-active-completed flex justify-center gap-4 p-4 rounded shadow-md" :class="{ 'grey-container': isDarkMode }">
        <span :class="{ 'active-tasks': filter === 'all' }" class="main__all cursor-pointer" @click="filter = 'all'">All</span>
        <span :class="{ 'active-tasks': filter === 'active' }" class="main__active cursor-pointer" @click="filter = 'active'">Active</span>
        <span :class="{ 'active-tasks': filter === 'completed' }" class="main__completed cursor-pointer" @click="filter = 'completed'">Completed</span>
      </div>

      <p class="main__drag-n-drop text-center mt-4">Drag and drop to reorder list</p>
    </main>

    <div class="attribution text-center mb-10 mt-3">
      Challenge by <a href="https://www.frontendmentor.io?ref=challenge" target="_blank">Frontend Mentor</a>.
      Coded by <a href="https://github.com/LilyEliG" target="_blank">Lily Eli Goloh</a>.
    </div>
  </div>
</template>

<script>

export default {
  data() {
    return {
      newTask: '',
      tasks: [],
      filter: 'all',
      isDarkMode: false,
      sunIcon: require('@/assets/images/icon-sun.svg'),
      moonIcon: require('@/assets/images/icon-moon.svg'),
    }
  },
  computed: {
    filteredTasks() {
      if (this.filter === 'active') {
        return this.tasks.filter(task => !task.checked)
      } else if (this.filter === 'completed') {
        return this.tasks.filter(task => task.checked)
      }
      return this.tasks
    },
    activeTasks() {
      return this.tasks.filter(task => !task.checked)
    }
  },
  methods: {
    addTask() {
      if (this.newTask.trim()) {
        this.tasks.push({ description: this.newTask, checked: false })
        this.newTask = ''
        this.updateLocalStorage()
      } else {
        alert("Please write a valid task!")
      }
    },
    removeTask(index) {
      this.tasks.splice(index, 1)
      this.updateLocalStorage()
    },
    updateTask(task) {
      console.log('Updating task:', task.description);
      this.updateLocalStorage()
    },
    clearCompleted() {
      this.tasks = this.tasks.filter(task => !task.checked)
      this.updateLocalStorage()
    },
    toggleDarkMode() {
      this.isDarkMode = !this.isDarkMode
    },
    updateLocalStorage() {
      localStorage.setItem('tasks', JSON.stringify(this.tasks))
    },
    loadTasks() {
      const savedTasks = JSON.parse(localStorage.getItem('tasks'))
      if (savedTasks) {
        this.tasks = savedTasks
      }
    }
  },
  mounted() {
    this.loadTasks()
    this.$nextTick(() => {
      document.querySelector('.main__input').focus()
    })
  }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Josefin+Sans:ital,wght@0,100;0,200;0,300;0,400;0,500;0,600;0,700;1,100;1,200;1,300;1,400;1,500;1,600;1,700&display=swap');

:root {
  --Bright-Blue: #3a7bfd;
  --Check-Background: linear-gradient(to bottom, #57ddff, #c058f3);
  --Very-Light-Gray: #fafafa;
  --Very-Light-Grayish-Blue: #e4e5f1;
  --Light-Grayish-Blue: #d2d3db;
  --Dark-Grayish-Blue: #9394a5;
  --Very-Dark-Grayish-Blue: #484b6a;
  --White: #fff;
  --Grey: #adadad;
  --Very-Dark-Blue: #161722;
  --Very-Dark-Desaturated-Blue: #25273c;
  --Light-Grayish-Blue-hover: #e4e5f1;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: 'Josefin Sans', sans-serif;
}

body {
  background: var(--Very-Light-Gray) url('@/assets/images/bg-mobile-light.jpg') no-repeat;
  background-size: contain;
}

.main__title {
  text-transform: uppercase;
  letter-spacing: 10px;
  font-size: 1.75rem;
  font-weight: 700;
  color: var(--Very-Light-Gray);
}

.main__circle {
  width: 20px;
  height: 20px;
  border-radius: 100%;
  vertical-align: middle;
  border: 1px solid #ddd;
  appearance: none;
  outline: none;
  cursor: pointer;
  left: 15px;
}

.main__input {
  font-size: 0.8rem;
  padding-left: 2.75rem;
}

.main__todo-list {
  overflow: hidden;
  background-color: var(--White);
}

.main__todo-list-item {
  font-size: 0.8rem;
  border-bottom: 1px solid var(--Light-Grayish-Blue-hover);
}

.grey-container {
  background-color: #2c2f51;
  color: var(--Very-Light-Grayish-Blue);
  font-weight: 400;
  border-bottom: 1px solid rgba(100, 100, 100, 0.436);
}

.main__todo-list-checkbox {
  width: 20px;
  height: 20px;
  border-radius: 100%;
  vertical-align: middle;
  border: 1px solid #ddd;
  appearance: none;
  outline: none;
  cursor: pointer;
  transform: translateY(-1px);
  position: relative;
}

.main__todo-list-checkbox:checked {
  background-image: url('@/assets/images/icon-check.svg'), var(--Check-Background);
  background-size: 10px, contain;
  background-repeat: no-repeat, no-repeat;
  background-position: center, center;
}

.main__todo-list-checkbox:checked + .main__todo-list-item-description {
  text-decoration: line-through;
  color: var(--Grey);
}

.main__todo-list-item-label:hover {
  cursor: pointer;
}

.line-through {
  text-decoration: line-through;
  color: var(--Grey);
}

.new-container {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 1.25rem 1.25rem 1.25rem 1rem;
}

.new-label {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 1.25rem;
}

.new-description {
  margin-right: auto;
}

.main__todo-left-n-clear {
  background-color: var(--White);
  color: var(--Grey);
  font-size: 0.9rem;
}

.main__all-active-completed {
  background-color: var(--White);
  color: var(--Grey);
  font-size: 0.9rem;
  font-weight: 700;
}

.active-tasks {
  color: var(--Bright-Blue);
  font-weight: 900;
}

.main__drag-n-drop {
  color: var(--Grey);
  font-size: 0.8rem;
  font-weight: 600;
}

.attribution {
  color: var(--Dark-Grayish-Blue);
}

.attribution a {
  color: var(--Bright-Blue);
}

.attribution a:hover {
  text-decoration: underline;
}

.dark-bg {
  background: var(--Very-Dark-Desaturated-Blue) url('@/assets/images/bg-mobile-dark.jpg') no-repeat;
  background-size: contain;
}

@media screen and (min-width: 560px) {
  .dark-bg {
    background: var(--Very-Dark-Desaturated-Blue) url('@/assets/images/bg-desktop-dark.jpg') no-repeat;
    background-size: contain;
  }

  body {
    background: var(--Very-Light-Gray) url('@/assets/images/bg-desktop-light.jpg') no-repeat;
    background-size: contain;
  }

  .main {
    min-width: 530px;
    margin: 4.7rem auto 0;
    position: relative;
    display: grid;
  }

  .main__container {
    gap: 1.5rem;
    margin-bottom: 0;
  }

  .main__title {
    font-size: 2.5rem;
    letter-spacing: 15px;
  }

  .main__mode-icon {
    width: auto;
  }

  .main__circle {
    width: 25px;
    height: 25px;
    left: 15px;
  }

  .main__input {
    font-size: 1.1rem;
    padding: 1.25rem 3.5rem;
  }

  .new-container {
    padding: 1.25rem;
  }

  .main__add-new-task {
    padding: 1.31rem;
  }

  .main__todo-list-item {
    padding: 1.25rem;
    font-size: 1.1rem;
  }

  .main__todo-list-item:hover > .main__icon-cross {
    display: block;
  }

  .main__icon-cross {
    cursor: pointer;
    display: none;
  }

  .main__todo-list-checkbox {
    width: 25px;
    height: 25px;
  }

  .main__todo-left-n-clear {
    background-color: var(--White);
    color: var(--Grey);
    font-size: 0.9rem;
    padding: 1rem 1.3rem;
  }

  .main__all-active-completed {
    position: absolute;
    justify-self: center;
    bottom: 1.2rem;
    font-size: 0.9rem;
    background-color: transparent;
    box-shadow: none;
  }
}
</style>
