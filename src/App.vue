<script setup>
import TaskDialog from './components/TaskDialog.vue';
import { ref } from 'vue'
import { useToast } from "vue-toastification";

defineEmits(['close-dialog', 'add-task', 'update-task']);

const toast = useToast();

const dialog = ref(false);
const taskList = ref([]);
const emptyTask = {
  title: "",
  description: "",
  deadline: "",
  priority: "",
  index: -1,
};  

const taskForDialog = ref(emptyTask);
const addOrUpdate = ref(true);

function closeDialog(){
  dialog.value = false;
} 
function addTask(task) {
  let newTask = {
    title: task.value.title,
    description: task.value.description,
    deadline: task.value.deadline,
    priority: task.value.priority,
    complete: ref(false),
  }
  taskList.value.push(newTask);
  taskForDialog.value = emptyTask;
  closeDialog();
  toast.success("Task added successfully!");
}

function deleteTask(index) {
  taskList.value.splice(index, 1);
  toast.success("Task deleted successfully!")
}

function openUpdateDialog(index) {
  taskForDialog.value = {
    title: taskList.value[index].title,
    description: taskList.value[index].description,
    deadline: taskList.value[index].deadline,
    priority: taskList.value[index].priority,
    index: index
  };
  addOrUpdate.value = false;
  dialog.value = true;
}

function updateTask(task) {
  taskList.value[task.value.index].description = task.value.description;
  taskList.value[task.value.index].deadline = task.value.deadline;
  taskList.value[task.value.index].priority = task.value.priority;
  taskForDialog.value = emptyTask;
  addOrUpdate.value=true;
  closeDialog();
  toast.success("Task updated successfully!");
}

function validateUniqueTitle(title) {
  for(let i = 0; i < taskList.value.length; i++) {
    if(taskList.value[i].title == title) {
      return false;
    }
  }
  return true;
}
</script>

<template>
    <v-toolbar color="primary">
        <v-toolbar-title class="text-center">
          <i class="fa fa-bars" aria-hidden="true"></i>
          FRAMEWORKS
        </v-toolbar-title>
        
        <v-dialog max-width="600" v-model="dialog">
        <template v-slot:activator="{ props: activatorProps }">
          <v-btn variant="elevated" color="blue-darken-1" class="ma-3" v-bind="activatorProps" @click="addOrUpdate=true">
          <i class="fa fa-plus-circle mr-1" aria-hidden="true"></i>ADD
          </v-btn>
        </template>

        <template v-slot:default="{ isActive }">
          <TaskDialog msg="Hello" @add-task="addTask" @close-dialog="closeDialog" @update-task="updateTask" :add="addOrUpdate" :task="taskForDialog" :titleValidation="validateUniqueTitle"/>
        </template>
      </v-dialog>
      
    </v-toolbar>
  <v-table color="primary">
  <thead color="primary">
    <tr>
      <th class="text-center">
        Title
      </th>
      <th class="text-center">
        Description
      </th>
      <th class="text-center">
        Deadline
      </th>
      <th class="text-center">
        Priority
      </th>
      <th class="text-center">
        Is Complete
      </th>
      <th class="text-center">
        Action
      </th>
    </tr>
  </thead>
  <tbody>
    <tr
      v-for="(task, index) in taskList"
      :key="task.title"
    >
      <td class="text-center">{{ task.title }}</td>
      <td class="text-center">{{ task.description }}</td>
      <td class="text-center">{{ task.deadline.substring(5, 7) + '/' + task.deadline.substring(8) + '/' + (task.deadline.substring(0, 4) % 100) }}</td>
      <td class="text-center">{{ task.priority }}</td>
      <td class="text-center">
        <v-checkbox-btn :id = "'checkbox' + index" v-model="task.complete" class = "d-inline-flex align-center"></v-checkbox-btn>
      </td>
      <td class="text-center">
        <template v-if="!task.complete">
        <v-btn 
          color="blue-darken-1"
          v-if="!task.complete"
          @click="openUpdateDialog(index)"
          class="w-50"
          >
          <i class="fa fa-pencil-square-o mr-1" aria-hidden="true"></i>UPDATE
        </v-btn>
        <br>
      </template>
        <v-btn
            color="red"
            @click="deleteTask(index)"
            class="w-50"
          >
          <i class="fa fa-times-circle mr-1" aria-hidden="true"></i>DELETE
        </v-btn>
      </td>
    </tr>
  </tbody>
</v-table>
</template>