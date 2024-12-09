<template>
  <div class="max-w-lg mx-auto p-6 bg-gradient-to-br from-blue-50 to-white shadow-lg rounded-lg">
    <h1 class="text-4xl text-blue-600 font-extrabold text-center mb-8">
      Todo List
    </h1>

    <div class="text-center mb-6">
      <p class="text-xl font-semibold text-blue-700">{{ day }}</p>
      <p class="text-3xl font-extrabold text-blue-800">{{ formattedDate }}</p>
    </div>

    <div class="flex mb-6">
      <input v-model="newTask" type="text" placeholder="What do you want to accomplish?"
        class="flex-1 px-4 py-3 border border-gray-300 rounded-l-lg focus:ring-2 focus:ring-blue-500 focus:outline-none transition-all duration-300"
        @keyup.enter="addTask" />
      <button @click="addTask"
        class="px-5 py-3 bg-blue-600 text-white rounded-r-lg hover:bg-blue-700 focus:ring-2 focus:ring-blue-500 focus:outline-none transition duration-300">
        Add
      </button>
    </div>

    <div>
      <div v-if="filteredTasks.length > 0">
        <draggable class="space-y-4" :list="tasks" group="tasks" @start="drag = true" @end="handleDragEnd">
          <li v-for="task in filteredTasks" :key="task.id"
            class="flex justify-between items-center p-4 bg-white shadow-md rounded-lg hover:shadow-lg transition duration-300">
            <span class="text-lg font-medium" :class="{ 'line-through text-gray-400': task.completed }">
              {{ task.title }}
            </span>
            <div class="flex space-x-2">
              <button @click="markAsCompleted(task.id)" :class="{ 'opacity-50 pointer-events-none': task.completed }"
                class="px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition duration-200"
                title="Mark as completed">
                ✓
              </button>
              <button @click="markAsUncompleted(task.id)"
                class="px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition duration-200"
                title="Mark as uncompleted">
                ✕
              </button>

              <button @click="deleteTask(task.id)"
                class="px-4 py-2 bg-red-600 text-white rounded-lg hover:bg-red-700 transition duration-200"
                title="Delete task">
                🗑️
              </button>
            </div>
          </li>
        </draggable>
      </div>

      <div v-else class="p-6 text-center bg-white shadow-md rounded-lg">
        <p class="text-gray-600">
          No tasks available. Add a task to get started!
        </p>
      </div>
    </div>

    <div class="flex items-center justify-between mt-6 p-4 bg-blue-500 text-white rounded-lg">
      <span>Hide Completed Tasks</span>
      <input type="checkbox" v-model="hideCompleted"
        class="ml-2 w-5 h-5 text-blue-500 border-gray-300 rounded focus:ring focus:ring-offset-0 focus:ring-blue-400" />
    </div>
  </div>
</template>

<script>
import draggable from "vuedraggable";

export default {
  name: "App",
  components: {
    draggable,
  },
  data() {
    return {
      newTask: "",
      hideCompleted: false,
      currentDate: new Date(),
      tasks: [],
      drag: false,
    };
  },
  computed: {
    day() {
      const daysOfWeek = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];
      return daysOfWeek[this.currentDate.getDay()];
    },
    formattedDate() {
      const options = { year: "numeric", month: "short", day: "numeric" };
      return this.currentDate.toLocaleDateString("en-US", options);
    },
    filteredTasks() {
      return this.hideCompleted
        ? this.tasks.filter((task) => !task.completed)
        : this.tasks;
    },
  },

  methods: {
    addTask() {
      if (!this.newTask.trim()) return;
      this.tasks.push({
        id: Date.now(),
        title: this.newTask.trim(),
        completed: false,
      });
      this.storeData();
      this.newTask = "";
    },

    markAsCompleted(id) {
      const task = this.tasks.find((task) => task.id === id);
      if (task) task.completed = true;
      this.storeData();
    },

    markAsUncompleted(id) {
      const task = this.tasks.find((task) => task.id === id);
      if (task) task.completed = false;
      this.storeData();
    },

    deleteTask(id) {
      this.tasks = this.tasks.filter((task) => task.id !== id);
      this.storeData();
    },
    storeData() {
      localStorage.setItem("myLocalTasks", JSON.stringify(this.tasks));
    },
    handleDragEnd() {
      this.storeData();
    },
  },

  mounted() {
    let data = localStorage.getItem("myLocalTasks");
    if (data) {
      this.tasks = JSON.parse(data);
    }
  },
};
</script>

<style scoped></style>
