<!-- eslint-disable vue/html-self-closing -->
<template>
  <div class="relative">
    <button>
      <NuxtImg
        src="/listsymbol.svg"
        height="35"
        width="35"
        class="fixed left-1/2 transform -translate-x-[120px] translate-y-[35px] md:-translate-x-[225px] lg:-translate-x-[300px] xl:-translate-x-[380px]"
      />
    </button>
    <button @click="goToComingSoon">
      <NuxtImg
        src="/calendarsymbol.svg"
        height="40"
        width="40"
        class="fixed left-1/2 transform translate-x-[85px] translate-y-[30px] md:translate-x-[195px] lg:translate-x-[275px] xl:translate-x-[340px]"
      />
    </button>
    <div class="w-screen bg-teal1 h-[90px] rounded-t-3xl bottom-0 z-0" />
  </div>
  <button @click="showModal = true">
    <NuxtImg
      src="/addbutton2.svg"
      width="75"
      height="75"
      class="fixed left-1/2 transform -translate-x-1/2 bottom-[50px]"
    />
  </button>

  <!-- Modal -->
  <div
    v-if="showModal"
    class="fixed inset-0 bg-black bg-opacity-50 flex justify-center items-center z-50"
  >
    <div class="bg-white p-6 rounded-lg shadow-lg w-[300px]">
      <div class="text-xl font-semibold mb-4 justify-center text-center">
        New Task
      </div>
      <form class="flex flex-col">
        <input
          v-model="taskName"
          placeholder="Task Name"
          class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
          type="text"
        />
        <input
          v-model="dueDate"
          placeholder="Due Date"
          class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
          type="date"
        />
        <!--<select
          id="product"
          class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
        >
          <option value="product-1">Product 1</option>
          <option value="product-2">Product 2</option>
          <option value="product-3">Product 3</option>
        </select>
      -->
        <input
          v-model="taskType"
          placeholder="Task Type"
          class="bg-gray-100 text-gray-800 border-0 rounded-md p-2 mb-4 focus:bg-gray-200 focus:outline-none focus:ring-1 focus:ring-teal1 transition ease-in-out duration-150"
          type="text"
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
        />
      </form>
      <button
        class="bg-[#e00d0d] text-white px-4 py-2 rounded h-[40px] w-[80px]"
        @click="
          showModal = false;
          showNotice = false;
        "
      >
        Cancel
      </button>
      <button
        class="bg-teal1 text-white px-4 py-2 rounded ml-[90px] h-[40px] w-[80px]"
        @click="saveTask"
      >
        Save
      </button>
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
</template>
<script setup>
import { ref } from "vue";
import { useRouter } from "vue-router";

const router = useRouter();

const goToComingSoon = () => {
  router.push("/comingsoon");
};

// Form data
const taskName = ref("");
const dueDate = ref("");
const taskType = ref("");
const taskPriority = ref(1);
const taskDescription = ref("");
const priorityError = ref("");

const validatePriority = () => {
  if (taskPriority.value < 1 || taskPriority.value > 10) {
    showNotice.value = true;
    priorityError.value = "Priority must be between 1 and 10";
    noticeContent.value = priorityError.value;
  } else {
    priorityError.value = "";
  }
};

// show/hide modal
const showModal = ref(false);

// show/hide notice
const showNotice = ref(false);

// notice content
const noticeContent = ref("");

// Function to generate a unique ID
const generateUniqueId = () => {
  const date = new Date();
  const dateString = date.toISOString(); // e.g., "2024-08-28T12:34:56.789Z"
  const randomPart = Math.floor(Math.random() * 100000); // Random number to ensure uniqueness
  return `${dateString}-${randomPart}`;
};

// Save task
const saveTask = () => {
  if (taskPriority.value < 1 || taskPriority.value > 10) {
    showNotice.value = true;
    priorityError.value = "Priority must be between 1 and 10";
    noticeContent.value = priorityError.value;
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

  const newTask = {
    taskId: generateUniqueId(),
    taskName: taskName.value,
    dueDate: dueDate.value,
    taskType: taskType.value,
    taskPriority: taskPriority.value,
    taskDescription: taskDescription.value,
  };

  // Get existing tasks from local storage
  const tasks = JSON.parse(localStorage.getItem("tasks")) || [];

  // Add the new task to the existing tasks
  tasks.push(newTask);

  // Save the updated tasks to local storage
  localStorage.setItem("tasks", JSON.stringify(tasks));

  // Clear form fields
  taskName.value = "";
  dueDate.value = "";
  taskType.value = "";
  taskPriority.value = "";
  taskDescription.value = "";
  showModal.value = false;

  // Show success message
  showNotice.value = true;
  noticeContent.value = "Task saved successfully!";
  disappearNotice();

  window.location.reload();
};

const disappearNotice = () => {
  setTimeout(() => {
    showNotice.value = false;
  }, 3000);
};
</script>
