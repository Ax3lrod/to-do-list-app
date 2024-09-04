<!-- eslint-disable vue/html-self-closing -->
<template>
  <div>
    <div>
      <button>
        <NuxtImg
          src="/tuneicon.svg"
          height="30"
          width="30"
          class="fixed left-[70px] mt-[10px] transform"
        />
      </button>
      <button @click="openSearchBar">
        <NuxtImg
          src="/searchicon.svg"
          width="30"
          height="30"
          class="fixed right-[70px] mt-[10px] transform"
        />
      </button>
    </div>

    <!-- search bar -->
    <div
      v-if="searchBar"
      class="fixed top-[23px] left-0 right-0 ml-auto mr-auto mt-0 w-[80%]"
    >
      <input
        v-model="searchQuery"
        type="text"
        placeholder="Search Tasks"
        class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 w-full focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
      />
      <button class="absolute top-[5px] right-[15px]" @click="closeSearchBar">
        X
      </button>
    </div>

    <!-- Task List -->
    <div class="task-list mt-[70px] mx-[20px]">
      <div v-if="tasks.length === 0" class="text-center text-gray-500">
        No tasks found. Add a new task to get started!
      </div>
      <div v-else>
        <div
          v-for="task in filteredTasks"
          :key="task.taskId"
          class="relative bg-gray-100 p-4 mb-4 rounded-lg shadow-lg"
        >
          <button @click="openTaskMenu(task)">
            <NuxtImg
              src="/menuicon.png"
              class="absolute -mt-[20px] right-[20px]"
            />
          </button>
          <div class="font-bold text-lg inline-flex">
            {{ task.taskName }}
          </div>
          <div class="text-sm text-gray-700 font-semibold">
            {{ task.dueDate }}
          </div>
          <div class="text-sm text-gray-700">Type: {{ task.taskType }}</div>
          <div class="text-sm text-gray-700">
            Priority: {{ task.taskPriority }}
          </div>
          <div class="text-sm text-gray-700">{{ task.taskDescription }}</div>
        </div>
      </div>
    </div>

    <!-- Task Menu -->
    <div
      v-if="taskMenu"
      class="fixed inset-0 flex justify-center items-center z-50 bg-black bg-opacity-50"
    >
      <div class="bg-white p-6 shadow-lg w-[300px] rounded-md relative">
        <button
          class="absolute -top-[17px] -right-[15px] text-xl font-extrabold text-teal1 border-solid bg-white w-[30px] h-[30px] rounded-full focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150 hover:outline-none hover:ring-1 hover:ring-teal1 shadow-md"
          @click="closeTaskMenu"
        >
          X
        </button>
        <div class="flex flex-col">
          <button
            class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
            @click="editTask"
          >
            Edit Task
          </button>
          <button
            class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
            @click="deleteTask"
          >
            Delete Task
          </button>
        </div>
      </div>
    </div>

    <!-- Modal -->
    <div
      v-if="showModal"
      class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50"
    >
      <div class="bg-white p-6 rounded-lg shadow-lg w-[300px]">
        <div class="text-xl font-semibold mb-4 justify-center text-center">
          Edit Task
        </div>
        <form class="flex flex-col" @submit.prevent="saveTask">
          <input
            v-model="taskName"
            placeholder="Task Name"
            class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
            type="text"
            required
          />
          <input
            v-model="dueDate"
            placeholder="Due Date"
            class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
            type="date"
            required
          />
          <input
            v-model="taskType"
            placeholder="Task Type"
            class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
            type="text"
            required
          />
          <input
            v-model="taskPriority"
            placeholder="Task Priority (1 - 10)"
            class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
            type="number"
            min="1"
            max="10"
            step="1"
            required
            @input="validatePriority"
          />
          <textarea
            v-model="taskDescription"
            placeholder="Task Description"
            class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
            name="taskDescription"
            required
          />
          <div class="flex justify-between">
            <button
              class="bg-[#e00d0d] text-white px-4 py-2 rounded h-[40px] w-[80px]"
              @click="cancelEdit"
            >
              Cancel
            </button>
            <button
              class="bg-teal1 text-white px-4 py-2 rounded h-[40px] w-[80px]"
              type="submit"
            >
              Save
            </button>
          </div>
        </form>
      </div>

      <!-- Pop Up Notice -->
      <div
        v-if="showNotice"
        role="alert"
        class="absolute rounded-lg z-50 bg-teal1 shadow-lg w-[300px] p-6 top-0 mt-5 border-solid border-white"
      >
        <div class="font-semibold text-xl text-center mb-3">Hey Listen!</div>
        <div class="mb-3">{{ noticeContent }}</div>
        <div class="items-center justify-center text-center">
          <button
            class="rounded-full bg-red-600 w-[24px] h-[24px] text-white mt-[10px]"
            @click="showNotice = false"
          >
            X
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";

const tasks = ref([]);
const taskMenu = ref(false);
const currentTaskId = ref(null); // Store the ID of the current task being edited
const searchBar = ref(false);
const searchQuery = ref("");
const priorityError = ref("");

// show/hide notice
const showNotice = ref(false);

// notice content
const noticeContent = ref("");

const validatePriority = () => {
  if (taskPriority.value < 1 || taskPriority.value > 10) {
    showNotice.value = true;
    priorityError.value = "Priority must be between 1 and 10";
    noticeContent.value = priorityError.value;
  } else {
    priorityError.value = "";
  }
};

// Form data
const taskName = ref("");
const dueDate = ref("");
const taskType = ref("");
const taskPriority = ref(1);
const taskDescription = ref("");
const showModal = ref(false);

// Load tasks from localStorage when the component is mounted
onMounted(() => {
  const storedTasks = JSON.parse(localStorage.getItem("tasks")) || [];
  tasks.value = storedTasks;
});

const openTaskMenu = (task) => {
  currentTaskId.value = task.taskId;
  taskMenu.value = !taskMenu.value;
};

const closeTaskMenu = () => {
  taskMenu.value = false;
};

const openSearchBar = () => {
  searchBar.value = true;
};

const closeSearchBar = () => {
  searchBar.value = false;
};

const editTask = () => {
  const taskToEdit = tasks.value.find(
    (task) => task.taskId === currentTaskId.value,
  );
  taskName.value = taskToEdit.taskName;
  dueDate.value = taskToEdit.dueDate;
  taskType.value = taskToEdit.taskType;
  taskPriority.value = taskToEdit.taskPriority;
  taskDescription.value = taskToEdit.taskDescription;
  showModal.value = true;
  taskMenu.value = false;
};

const saveTask = () => {
  if (taskPriority.value < 1 || taskPriority.value > 10) {
    showNotice.value = true;
    noticeContent.value = "Task priority must be between 1 and 10";
    return;
  }

  // Validate that all fields are filled
  if (
    !taskName.value ||
    !dueDate.value ||
    !taskType.value ||
    !taskPriority.value ||
    !taskDescription.value
  ) {
    const errorMessage = ref("");
    showNotice.value = true;
    errorMessage.value = "Please fill in all fields before saving.";
    noticeContent.value = errorMessage.value;
    disappearNotice();
    return;
  }

  const taskIndex = tasks.value.findIndex(
    (task) => task.taskId === currentTaskId.value,
  );
  if (taskIndex !== -1) {
    tasks.value[taskIndex].taskName = taskName.value;
    tasks.value[taskIndex].dueDate = dueDate.value;
    tasks.value[taskIndex].taskType = taskType.value;
    tasks.value[taskIndex].taskPriority = taskPriority.value;
    tasks.value[taskIndex].taskDescription = taskDescription.value;
    localStorage.setItem("tasks", JSON.stringify(tasks.value));
  }
  showModal.value = false;

  // Show success message
  showNotice.value = true;
  noticeContent.value = "Task saved successfully!";
  disappearNotice();

  window.location.reload();
};

const deleteTask = () => {
  tasks.value = tasks.value.filter(
    (task) => task.taskId !== currentTaskId.value,
  );
  localStorage.setItem("tasks", JSON.stringify(tasks.value));
  taskMenu.value = false;
};

const cancelEdit = () => {
  showModal.value = false;
  currentTaskId.value = null;
};

const filteredTasks = computed(() => {
  if (!searchQuery.value) return tasks.value;
  return tasks.value.filter(
    (task) =>
      task.taskName.toLowerCase().includes(searchQuery.value.toLowerCase()) ||
      task.taskDescription
        .toLowerCase()
        .includes(searchQuery.value.toLowerCase()) ||
      task.taskType.toLowerCase().includes(searchQuery.value.toLowerCase()),
  );
});

const disappearNotice = () => {
  setTimeout(() => {
    showNotice.value = false;
  }, 3000);
};
</script>
