<template>
  <div class="container">
    <h1>To Do List</h1>
    <div class="add-task">
      <input type="text" v-model="newTask" placeholder="Add new task..." />
      <input type="text" v-model="newCategory" placeholder="Category..." />
      <button @click="addTask">Add</button>
    </div>

    <table class="task-table">
      <thead>
        <tr>
          <th></th>
          <th>Task</th>
          <th>Category</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="(task, index) in filteredTasks"
          :key="index"
          :class="{ completed: task.completed }"
        >
          <td><input type="checkbox" v-model="task.completed" class="checkbox" /></td>
          <td>
            <div v-if="!task.editing" class="task-text">{{ task.title }}</div>
            <div v-else class="edit-mode">
              <input v-model="task.title" class="edit-input" />
            </div>
          </td>
          <td>
            <div v-if="!task.editing" class="task-text">{{ task.category }}</div>
            <div v-else class="edit-mode">
              <input v-model="task.category" class="edit-input" />
            </div>
          </td>
          <td>
            <div class="button-group">
              <button class="edit-btn" @click="editTask(index)">Edit</button>
              <button class="delete-btn" @click="deleteTask(index)">Delete</button>
            </div>
          </td>
        </tr>
      </tbody>
    </table>

    <div class="filters">
      <select v-model="selectedCategory" @change="filterByCategory">
        <option value="">All Categories</option>
        <option v-for="category in categories" :key="category">{{ category }}</option>
      </select>
      <input type="text" v-model="searchQuery" placeholder="Search tasks..." />
    </div>

    <button class="filter-btn" @click="toggleShowIncomplete">
      {{ showIncomplete ? 'Show All Tasks' : 'Show Incomplete Tasks Only' }}
    </button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      tasks: [],
      newTask: '',
      newCategory: '',
      showIncomplete: true,
      selectedCategory: '',
      searchQuery: ''
    }
  },
  computed: {
    filteredTasks() {
      let filtered = this.tasks.filter((task) => {
        if (this.showIncomplete && task.completed) return false
        if (this.selectedCategory && task.category !== this.selectedCategory) return false
        if (this.searchQuery && !task.title.toLowerCase().includes(this.searchQuery.toLowerCase()))
          return false
        return true
      })
      return filtered
    },
    categories() {
      let categories = new Set(this.tasks.map((task) => task.category))
      return Array.from(categories)
    }
  },
  methods: {
    addTask() {
      if (this.newTask.trim() !== '') {
        this.tasks.push({
          title: this.newTask,
          completed: false,
          category: this.newCategory || 'Uncategorized'
        })
        this.newTask = ''
        this.newCategory = ''
      }
    },
    editTask(index) {
      this.tasks[index].editing = true
    },
    deleteTask(index) {
      this.tasks.splice(index, 1)
    },
    toggleShowIncomplete() {
      this.showIncomplete = !this.showIncomplete
    },
    filterByCategory() {}
  }
}
</script>

<style scoped>
.container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

h1 {
  text-align: center;
  margin-bottom: 20px;
}

.add-task {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.add-task input {
  flex: 1;
  margin-right: 10px;
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.add-task button {
  padding: 10px 20px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.task-table {
  width: 100%;
  border-collapse: collapse;
}

.task-table th,
.task-table td {
  padding: 10px;
  border-bottom: 1px solid #ccc;
  text-align: left;
}

.completed {
  text-decoration: line-through;
  color: #999;
}

.filters {
  margin-bottom: 20px;
}

.filters select,
.filters input {
  padding: 10px;
  margin-right: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.filter-btn {
  padding: 10px 20px;
  background-color: #28a745;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.button-group button {
  padding: 8px 16px;
  margin-right: 5px;
  background-color: #007bff;
  color: #fff;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.button-group button:last-child {
  margin-right: 0;
}
</style>
