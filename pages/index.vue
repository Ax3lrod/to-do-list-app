<template>
  <div>
    <div class="">
      <button>
        <NuxtImg
          src="/tuneicon.svg"
          height="30"
          width="30"
          class="fixed left-[70px] mt-[10px] transform"
        />
      </button>
      <button>
        <NuxtImg
          src="/searchicon.svg"
          width="30"
          height="30"
          class="fixed right-[70px] mt-[10px] transform"
        />
      </button>
    </div>

    <!-- Task List -->
    <div class="task-list mt-[70px] mx-[20px]">
      <div v-if="tasks.length === 0" class="text-center text-gray-500">
        No tasks found. Add a new task to get started!
      </div>
      <div v-else>
        <div
          v-for="task in tasks"
          :key="task.taskId"
          class="bg-gray-100 p-4 mb-4 rounded-lg shadow-lg"
        >
          <div class="font-semibold text-lg">{{ task.taskName }}</div>
          <div class="text-sm text-gray-700">{{ task.dueDate }}</div>
          <div class="text-sm text-gray-700">{{ task.taskType }}</div>
          <div class="text-sm text-gray-700">
            Priority: {{ task.taskPriority }}
          </div>
          <div class="text-sm text-gray-700">{{ task.taskDescription }}</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, onMounted } from "vue";

const tasks = ref([]);

// Load tasks from localStorage when the component is mounted
onMounted(() => {
  const storedTasks = JSON.parse(localStorage.getItem("tasks")) || [];
  tasks.value = storedTasks;
});
</script>
