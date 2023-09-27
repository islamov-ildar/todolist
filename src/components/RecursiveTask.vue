<script>
import {ref} from "vue";

export default {
  name: "recursive-comment",
  props: {
    taskData: {
      type: Object,
      required: true,
    },
  },
  emits: ['saveData'],
  setup(props, { emit }) {
    const task = ref(props.taskData);

    const saveData = () => emit('saveData', task);
    return {
      task,
      saveData,
    }
  }
};
</script>

<template>
  <div class="task border-regular flex justify-between flex-column my-1">
    <div class="flex justify-between my-1">
      <div class="flex gap-10px">
        <input
            type="checkbox"
            v-model="task.isDone"
            class="doneCheckBox"
            @input="saveData()"
        />
        <span :class="{ 'striked-text': task.isDone }">
           <span v-if="!task.isEditMode">{{ task.taskName }}</span>
           <input v-else type="text" v-model="task.taskName"/>
        </span>
      </div>
      <div>
        <button v-if="!task.isEditMode" @click="task.isEditMode = true">Редактировать</button>
        <button v-if="task.isEditMode" @click="task.isEditMode = false; saveData();">
          Ок
        </button>
        <button v-if="!task.isEditMode" @click="deleteTask(idx)">
          Удалить
        </button>
        <button v-if="!task.isEditMode" @click="addSubTask(idx)">
          Добавить подзадачу
        </button>
      </div>
    </div>

    <div v-if="taskData.subTasks.length">
      <div v-for="(item, index) in taskData.subTasks" :key="index">
        <recursive-comment
            :taskData="item"
        />
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
