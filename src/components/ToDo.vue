<script setup>
import { ref, computed, onMounted } from "vue";

const tasks = ref([]);
const newTask = ref("");

onMounted(() => {
  const savedTasks = localStorage.getItem("tasks");
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  } else {
    tasks.value = "";
  }
});

const totalTasks = computed(() => tasks.value.length);
const doneTasksCount = computed(
  () => tasks.value.filter((task) => task.done).length
);

function addTask() {
  if (newTask.value.trim()) {
    tasks.value.push({ text: newTask.value, done: false });
    newTask.value = "";
    saveTasks();
  }
}

function removeTask(index) {
  tasks.value.splice(index, 1);
  saveTasks();
}

const deleteTasksDone = () => {
  tasks.value = tasks.value.filter((task) => !task.done); 
  saveTasks();
};

function removeTasks(index) {
  tasks.value.splice(index);
  savedTasks();
}

function saveTasks() {
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
}
</script>
<template>
  <section class="container py-24">
    <h1 class="text-center text-5xl font-bold text-white pb-3">Vue ToDo List</h1>
    <div class="bg-gray-50 p-5  h-[500px] max-w-xl mx-auto">
      <!-- buttons -->
      <div class="p-5 flex gap-5 text-white">
        <div class="bg-blue-600 p-2 rounded-lg">
          <button>
            Tasks <span class="bg-white text-blue-600 p-1 rounded-full">{{ totalTasks }}</span>
          </button>
        </div>
        <div class="bg-blue-600 p-2 rounded-lg">
          <button>
            Tasks Done <span class="bg-white text-blue-600 p-1 rounded-full"> {{  doneTasksCount }}</span>
          </button>
        </div>
        <div class="bg-red-600 p-2 rounded">
          <button @click="deleteTasksDone()">
            <i class="pi pi-trash pr-1"></i> Tasks Done
          </button>
        </div>
        <div class="bg-red-600 p-2 rounded">
          <button @click="removeTasks()">
            <i class="pi pi-trash pr-1"></i>Tasks
          </button>
        </div>
      </div>

      <!-- tasks list -->
      <div class="task-list mb-4">
        <div
          v-for="(task, index) in tasks"
          :key="index"
          class="flex items-center justify-between p-2 border-gray-200"
        >
          <div class="flex items-center">
            <input
              type="checkbox"
              v-model="task.done"
              @change="saveTasks"
              class="mr-2 h-4 w-4 text-blue-500 rounded border-1 focus:ring-blue-500"
            />
            <span :class="{ 'line-through text-gray-500': task.done }">{{
              task.text
            }}</span>
          </div>
          <!-- <button
            @click="removeTask(index)"
            class="text-red-500 hover:text-red-700 focus:outline-none focus:ring-2 focus:ring-red-500"
          >
            Remove
          </button> -->
        </div>
      </div>

      <!-- task input -->
      <div class="rounded-full border-blue-500 bg-white p-3 border-2">
        <div class="flex gap-3">
          <input
            v-model="newTask"
            @keyup.enter="addTask"
            placeholder="New task"
            class="flex-1 p-2 focus:outline-none"
          />
          <button
            @click="addTask"
            class="px-4 bg-blue-500 text-white rounded-full hover:bg-blue-600 focus:outline-none"
          >
            <i @click="addTask" class="pi pi-plus"></i>
          </button>
        </div>
      </div>
    </div>
  </section>
</template>
