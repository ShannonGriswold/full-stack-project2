<script setup>
import { ref, shallowRef } from 'vue'
import { VDateInput } from 'vuetify/labs/VDateInput';
import { useTemplateRef, onMounted } from 'vue';
import { useToast } from "vue-toastification";

const toast = useToast();

const task = ref({});

onMounted(() => {
  task.value.title = props.task.title;
  task.value.description = props.task.description;
  task.value.deadline = props.task.deadline;
  task.value.priority = props.task.priority;
  task.value.index = props.task.index;
});

const emit = defineEmits(['close-dialog', 'add-task', 'update-task']);

const props = defineProps({
  add: Boolean,
  task: {
    type: Object,
    default: {
      title: "",
      description: "",
      deadline: "",
      priority: "",
      index: -1,
    }
  },
  titleValidation: Function,
});

const errorMessages = ref({
  titleError: "",
  descriptionError: "",
  deadlineError: "",
  priorityError: "",
});

function closeDialog () {
  emit('close-dialog');        
}

function validateTask(adding) {
  let filledOut = true;
  if(adding) {
    if(!task.value.title) {
      errorMessages.value.titleError = "Title is Required!";
      filledOut = false;
    } else if (!props.titleValidation(task.value.title)) {
      errorMessages.value.titleError = "Title needs to be unique!";
      filledOut = false;
    } else {
      errorMessages.value.titleError = "";
    }
  }
  if(!task.value.description) {
    errorMessages.value.descriptionError = "Description is Required!";
    filledOut = false;
  } else {
    errorMessages.value.descriptionError = "";
  }
  if(!task.value.deadline) {
    errorMessages.value.deadlineError = "Deadline is Required!";
    filledOut = false;
  } else {
    errorMessages.value.deadlineError = "";
  }
  if(!task.value.priority) {
    errorMessages.value.priorityError = "Priority is Required!";
    filledOut = false;
  } else {
    errorMessages.value.priorityError = "";
  }
  
  console.log(filledOut);
  if(filledOut) {
    if(adding) {
      emit('add-task', task);
    } else {
      emit('update-task', task);
    }
  }
};

</script>

<template>
  <v-card>
    <v-toolbar color="primary">
      <v-toolbar-title v-if="add">
        <i class="fa fa-plus-circle mr-1" aria-hidden="true"></i>
        Add Task
      </v-toolbar-title>
      <v-toolbar-title v-else>
        <i class="fa fa-edit mr-1" aria-hidden="true"></i>
        Edit Task
      </v-toolbar-title>
    </v-toolbar>
    <v-form id = "task-form" ref="form" validate-on="submit">
      <v-card-text>
        <v-text-field 
          label="Title" 
          required 
          variant="outlined" 
          id = "title"
          v-if="add"
          v-model="task.title"
          :error-messages="errorMessages.titleError"
          class = "mb-2"
        ></v-text-field>
        <v-text-field 
          label="Description" 
          required 
          variant="outlined" 
          id = "description"
          v-model="task.description"
          :error-messages="errorMessages.descriptionError"
          class = "mb-2"
        ></v-text-field>
        <!-- <v-date-input 
          label="Deadline" 
          prepend-icon="" 
          variant="outlined" 
          append-inner-icon="mdi:mdi-calendar" persistent-placeholder 
          v-model="task.deadline"
          :error-messages="errorMessages.deadlineError"
          @blur="updateDeadline"
        ></v-date-input> -->
        <v-text-field 
          label="Deadline"
          required
          variant="outlined"
          id = "deadline"
          type="date"
          append-inner-icon="mdi:mdi-calendar" 
          persistent-placeholder
          v-model="task.deadline"
          :error-messages="errorMessages.deadlineError"
          class = "mb-2"
        ></v-text-field>
        <v-radio-group
          inline
          color="primary"
          label="Priority"
          id="priority"
          v-model="task.priority"
          :error-messages="errorMessages.priorityError"
          class = "mb-2"
        >
          <v-radio
            label="Low"
            value="Low"
          ></v-radio>
          <v-radio
            label="Med"
            value="Med"
          ></v-radio>
          <v-radio
            label="High"
            value="High"
          ></v-radio>
        </v-radio-group>
      </v-card-text>  
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn v-if="add" variant="elevated" color="primary" class="w-33" @click="validateTask(true)">
          <i class="fa fa-plus-circle mr-1" aria-hidden="true"></i>ADD
        </v-btn>
        <v-btn v-else variant="elevated" color="primary" class="w-33" @click="validateTask(false)">
          <i class="fa fa-pencil-square-o mr-1" aria-hidden="true"></i>EDIT
        </v-btn>

        <v-btn
          color="red"
          variant="elevated"
          v-on:click="closeDialog"
          class = "w-33"
        >
          <i class="fa fa-ban mr-1" aria-hidden="true"></i>CANCEL
        </v-btn>
      </v-card-actions>
    </v-form>
  </v-card>

</template>
