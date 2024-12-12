<template>
  <h1>Task Manager</h1>
  <div class="task-manager">
    <!-- Button to toggle the form -->
    <button @click="toggleForm" class="toggle-button">
      {{ showForm ? "Hide Form" : "Add New Task" }}
    </button>

    <!-- Form for adding new tasks -->
    <div v-if="showForm" class="task-form">
      <input
        type="text"
        v-model="newTaskName"
        placeholder="Enter task name"
        @keydown.enter="addTask"
      />
      <button @click="addTask">Add Task</button>
    </div>

    <!-- All Tasks -->
    <h2>All Tasks</h2>
    <p v-if="tasks.length === 0">No tasks available</p>
    <ul v-else>
      <li
        v-for="(task, index) in tasks"
        :key="task.id"
        :class="{ completed: task.completed }"
      >
        <span @click="toggleCompletion(task)">{{ task.name }}</span>
        <button @click="deleteTask(index)" class="delete-button">Delete</button>
      </li>
    </ul>

    <!-- Completed Tasks -->
    <button @click="toggleCompletedTasks" class="toggle-button">
      {{ showCompleted ? "Hide Completed Tasks" : "Show Completed Tasks" }}
    </button>
    <ul v-if="showCompleted" class="completed-tasks">
      <li v-for="task in completedTasks" :key="task.id">{{ task.name }}</li>
    </ul>
  </div>
</template>

<script>
import { reactive, ref, computed } from "vue";

export default {
  name: "TaskList",
  setup() {
    const tasks = reactive(JSON.parse(localStorage.getItem("tasks")) || []);
    const newTaskName = ref("");
    const showForm = ref(false);
    const showCompleted = ref(false);

    // Computed property for completed tasks
    const completedTasks = computed(() =>
      tasks.filter((task) => task.completed)
    );

    // Save tasks to localStorage
    const saveTasks = () => {
      localStorage.setItem("tasks", JSON.stringify(tasks));
    };

    // Add new task
    const addTask = () => {
      if (newTaskName.value.trim().length < 3) {
        alert("Task name must be at least 3 characters.");
        return;
      }
      tasks.push({ id: Date.now(), name: newTaskName.value, completed: false });
      newTaskName.value = "";
      saveTasks();
    };

    // Delete task
    const deleteTask = (index) => {
      tasks.splice(index, 1);
      saveTasks();
    };

    // Toggle task completion
    const toggleCompletion = (task) => {
      task.completed = !task.completed;
      saveTasks();
    };

    // Toggle form visibility
    const toggleForm = () => {
      showForm.value = !showForm.value;
    };

    // Toggle visibility of completed tasks
    const toggleCompletedTasks = () => {
      showCompleted.value = !showCompleted.value;
    };

    return {
      tasks,
      newTaskName,
      showForm,
      showCompleted,
      completedTasks,
      addTask,
      deleteTask,
      toggleCompletion,
      toggleForm,
      toggleCompletedTasks,
    };
  },
};
</script>

<style scoped>
/* Task Manager Container */
.task-manager {
  max-width: 500px;
  margin: 0 auto;
  padding: 20px;
  text-align: center;
}

/* Form Styles */
.task-form {
  margin: 20px 0;
}

.task-form input {
  padding: 8px;
  margin-right: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
}

/* List Styles */
ul {
  list-style: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 5px;
  margin-bottom: 5px;
  border: 1px solid #ddd;
  border-radius: 5px;
  background-color: #f9f9f9;
}

li.completed {
  background-color: lightgreen;
  border: 2px solid red;
  text-decoration: line-through;
}

/* Button Styles */
.toggle-button {
  margin-bottom: 20px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.delete-button {
  background-color: red;
  color: white;
  border: none;
  border-radius: 5px;
  padding: 3px;
  cursor: pointer;
}
</style>
