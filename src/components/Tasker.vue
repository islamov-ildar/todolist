<template>
  <div class="hello">
    <h1>Tasker</h1>
    <div class="flex justify-center flex-column align-items gap-30px">
      <div class="flex justify-center flex-column gap-10px width300px border-regular p-1">
        <label>Введите название проекта</label>
        <input type="text" v-model="projectName"/>
        <button @click="setNewProjectName()" :disabled="!projectName.length">
          Применить
        </button>
      </div>
      <div class="flex justify-center flex-column gap-10px width300px border-regular p-1">
        <label>Введите название новой задачи</label>
        <input type="text" v-model="newTaskName"/>
        <button @click="addNewTask()" :disabled="!newTaskName.length">
          Добавить
        </button>
      </div>
      <div class="flex justify-center flex-column gap-10px width300px border-regular p-1">
        <label>Введите запрос для поиска</label>
        <input type="text" v-model="search" @input="runSearch"/>
      </div>
      <div class="width600px flex justify-center flex-column gap-10px">
        <div v-for="(task, idx) in taskArrayForRender" :key="idx" class="task-card">
          <div class="task border-regular flex justify-between flex-column">
            <div class="flex justify-between my-1">
              <div class="flex gap-10px">
                <input
                    type="checkbox"
                    v-model="task.isDone"
                    class="doneCheckBox"
                    @input="saveDataToSessionStorage()"
                />
                <span :class="{ 'striked-text': task.isDone }">
                  <span v-if="!task.isEditMode">
                    {{ task.taskName }}
                  </span>
                  <input
                      v-else
                      type="text"
                      v-model="task.taskName"
                  />
                </span>
              </div>
              <div>
                <button v-if="!task.isEditMode" @click="task.isEditMode = true">
                  Редактировать
                </button>
                <button
                    v-if="task.isEditMode"
                    @click="
                    task.isEditMode = false;
                    saveDataToSessionStorage();
                  "
                >
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
            <div>
              <div
                  v-for="(sTask, sIdx) in task.subTasks"
                  :key="idx.toString() + sIdx.toString()"
                  class="task subtask border-regular"
              >
                <div class="flex justify-between">
                  <div class="flex gap-10px">
                    <input
                        type="checkbox"
                        v-model="sTask.isDone"
                        class="doneCheckBox"
                        @input="saveDataToSessionStorage()"
                    />
                    <div :class="{ 'striked-text': sTask.isDone }" class="subtask-name">
                      <div v-if="!sTask.isEditMode">
                        {{ sTask.taskName }}
                      </div>
                      <input
                          v-else
                          type="text"
                          v-model="sTask.taskName"
                      />
                    </div>
                  </div>
                  <div>
                    <button
                        v-if="!sTask.isEditMode"
                        @click="sTask.isEditMode = true"
                    >
                      Редактировать
                    </button>
                    <button
                        v-if="sTask.isEditMode"
                        @click="
                        sTask.isEditMode = false;
                        saveDataToSessionStorage();
                      "
                    >
                      Ок
                    </button>
                    <button
                        v-if="!sTask.isEditMode"
                        @click="deleteSubTask(idx, sIdx)"
                    >
                      Удалить подзадачу
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-for="(item, index) in tasks" :key="index" class="width600px flex justify-center flex-column gap-10px">
        <RecursiveTask
            :taskData="item"
            class="task-card"
            @save-data="saveDataToSessionStorage"
        />
      </div>
    </div>

  </div>
</template>

<script>
import {ref, onMounted} from "vue";
import RecursiveTask from './RecursiveTask.vue'

export default {
  name: "TaskerComponent",
  components: {RecursiveTask},

  setup() {

    const tasks = [
      {
        id: '1',
        taskName: "Новый пункт-1",
        isDone: false,
        isEditMode: false,
        subTasks: [
          {
            id: '11',
            taskName: "Новый пункт-11",
            isDone: false,
            isEditMode: false,
            subTasks: [{
              id: '111',
              taskName: "Новый пункт-111",
              isDone: false,
              isEditMode: false,
              subTasks: [],
            },
              {
                id: '112',
                taskName: "Новый пункт-112",
                isDone: false,
                isEditMode: false,
                subTasks: [],
              }],
          },
          {
            id: '12',
            taskName: "Новый пункт-12",
            isDone: false,
            isEditMode: false,
            subTasks: [{
              id: '121',
              taskName: "Новый пункт-121",
              isDone: false,
              isEditMode: false,
              subTasks: [],
            },
              {
                id: '112',
                taskName: "Новый пункт-122",
                isDone: false,
                isEditMode: false,
                subTasks: [],
              }],
          },
        ],
      },
      {
        id: '2',
        taskName: "Новый пункт-2",
        isDone: false,
        isEditMode: false,
        subTasks: [
          {
            id: '21',
            taskName: "Новый пункт-21",
            isDone: false,
            isEditMode: false,
            subTasks: [{
              id: '211',
              taskName: "Новый пункт-211",
              isDone: false,
              isEditMode: false,
              subTasks: [],
            },
              {
                id: '212',
                taskName: "Новый пункт-212",
                isDone: false,
                isEditMode: false,
                subTasks: [],
              }],
          },
          {
            id: '22',
            taskName: "Новый пункт-22",
            isDone: false,
            isEditMode: false,
            subTasks: [{
              id: '221',
              taskName: "Новый пункт-221",
              isDone: false,
              isEditMode: false,
              subTasks: [],
            },
              {
                id: '222',
                taskName: "Новый пункт-222",
                isDone: false,
                isEditMode: false,
                subTasks: [],
              }],
          },
        ],
      },
    ];


    const projectName = ref("");

    const setNewProjectName = () => {
      document.title = projectName.value;
      projectName.value = "";
      saveDataToSessionStorage();
    };

    const defaultTaskObj = {
      id: '',
      taskName: "Новый пункт",
      isDone: false,
      isEditMode: false,
      subTasks: [],
    };

    const taskArray = ref([]);

    const taskArrayForRender = ref();

    const newTaskName = ref("");

    const search = ref("");

    const saveArrForRender = () => {
      taskArrayForRender.value = taskArray.value;
    }
    const addNewTask = () => {
      const newTask = JSON.parse(JSON.stringify(defaultTaskObj));
      newTask.id = new Date();
      newTask.taskName = newTaskName.value;
      newTaskName.value = "";
      taskArray.value.unshift(newTask);
      saveArrForRender();
      saveDataToSessionStorage();
    };

    const deleteTask = (id) => {
      taskArray.value.splice(id, 1);
      saveArrForRender();
      saveDataToSessionStorage();
    };

    const deleteSubTask = (mainArrId, subTaskId) => {
      taskArray.value[mainArrId].subTasks.splice(subTaskId, 1);
      saveArrForRender();
      saveDataToSessionStorage();
    };

    onMounted(() => {
      let taskArrayFromSessionStorage = sessionStorage.getItem("taskArray");
      document.title = sessionStorage.getItem("projectName");
      !sessionStorage.getItem("projectName") ?
          document.title = 'Unnamed project' : document.title = sessionStorage.getItem("projectName");
      search.value = sessionStorage.getItem("searchValue");
      if (!search.value) search.value = '';
      if (JSON.parse(taskArrayFromSessionStorage)) {
        taskArray.value = JSON.parse(taskArrayFromSessionStorage);
        saveArrForRender();
      }
      runSearch();
    });

    const saveDataToSessionStorage = (tasks = null) => {
      console.log(taskArray.value);
      if(tasks) {
        taskArray.value.
        taskArray.value = tasks.value;
      }
      setTimeout(() => {
        const taskArrayJSON = JSON.stringify(taskArray.value);
        sessionStorage.setItem("taskArray", taskArrayJSON);
        sessionStorage.setItem("projectName", document.title);
        sessionStorage.setItem("searchValue", search.value);
        console.log("Saved");
      }, 100);
    };

    const newSubTaskName = ref("");

    const addSubTask = (mainTaskIdx) => {
      const newSubTask = JSON.parse(JSON.stringify(defaultTaskObj));
      newSubTask.id = new Date();
      taskArray.value[mainTaskIdx].subTasks.unshift(newSubTask);
      console.log("addSubTask", mainTaskIdx, newSubTaskName);
      saveArrForRender();
      saveDataToSessionStorage();
    };

    const runSearch = () => {
      saveDataToSessionStorage();
      taskArrayForRender.value = [];
      taskArray.value.forEach(oneTask => {
        if (oneTask.taskName.includes(search.value)) {
          taskArrayForRender.value.push(oneTask);
        } else {
          oneTask.subTasks.forEach(oneSubtask => {
            if (oneSubtask.taskName.includes(search.value)) {
              taskArrayForRender.value.push(oneTask);
            }
          })
        }
      })
    };

    return {
      addSubTask,
      projectName,
      setNewProjectName,
      taskArray,
      taskArrayForRender,
      addNewTask,
      newTaskName,
      deleteTask,
      saveDataToSessionStorage,
      deleteSubTask,
      search,
      runSearch,
      tasks,
    };
  },
};
</script>

<style>
.is-hidden {
  display: none;
}

h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.flex {
  display: flex;
}

.justify-center {
  justify-content: center;
}

.justify-between {
  justify-content: space-between;
}

.align-items {
  align-items: center;
}

.flex-column {
  flex-direction: column;
}

.gap-10px {
  gap: 10px;
}

.gap-30px {
  gap: 30px;
}

.task {
  padding: 15px;
  cursor: pointer;
}

.task:hover {
  box-shadow: 0 0 23px 17px rgba(167, 212, 255, 0.4);
}

.border-regular {
  border: 1px solid grey;
  border-radius: 5px;
}

.width300px {
  width: 300px;
}

.width600px {
  width: 600px;
}

.striked-text {
  text-decoration: line-through;
  text-decoration-color: orange;
}

.doneCheckBox {
  cursor: pointer;
}

.subtask {
  margin: 8px 0;
}

.my-1 {
  margin-top: 8px;
  margin-bottom: 8px;
}

button {
  margin: 0 3px;
  cursor: pointer;
}

.p-1 {
  padding: 10px;
}
</style>
