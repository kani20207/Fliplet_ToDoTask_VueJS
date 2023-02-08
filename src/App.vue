<template>
  <div class="main d-grid">
    <div class="mt-2 h-100">
      <h5 class="createTask">Create Task</h5>
      <div class="row">
        <div class="col form-inline">
          <input
            id="input-2"
            class="input-style"
            style="padding: 10px; outline: auto"
            v-model="newTask"
            name="newTask"
            required
            placeholder="Enter New Task"
            @keyup.enter="add"
          />
          <button
            class="ml-3 add primary-btn"
            variant="primary"
            type="submit"
            @click="add"
          >
            Add
          </button>
        </div>
      </div>
      <h5 class="tasks">Task List</h5>
      <div class="row column-split">
        <div class="col-6">
          <div class="p-4 card bg-transparent h-100 position-relative">
            <h3>
              Tasks
              <span class="mdi mdi-swap-vertical" @click="sort"></span>
            </h3>
            <!-- New Task draggable component. Pass arrNewTask to list prop -->
            <p
              align="center"
              class="text-absolute"
              v-if="isSort && arrNewTask.length === 0"
            >
              No task
            </p>
            <draggable
              v-if="isSort"
              class="list-group doto-column border-0 scroll-list"
              :list="arrNewTask"
              group="tasks"
            >
              <div
                class="list-group-item listItems added-task"
                style="
                  display: flex;
                  justify-content: space-between;
                  align-items: center;
                  margin-bottom: 10px;
                "
                v-for="element in arrNewTask"
                :key="element.name"
              >
                <input
                  type="checkbox"
                  value="element.status"
                  @click="statusChange(element.name)"
                  v-model="element.status"
                />
                {{ element.name }}
                <button
                  type="button"
                  align="end"
                  @click="remove(element.name)"
                  class="btn-rm"
                >
                  Remove
                </button>
              </div>
            </draggable>
            <h6 v-if="!isSort">
              Done Task
              <span
                class="mdi mdi-swap-vertical"
                @click="sortBy('Done Task')"
              ></span>
            </h6>
            <draggable
              v-if="!isSort"
              class="list-group border-0 scroll-done-progress"
              :list="arrDone"
              group="tasks"
            >
              <div
                class="list-group-item listItems added-task done"
                style="
                  display: flex;
                  justify-content: space-between;
                  align-items: center;
                  margin-bottom: 10px;
                "
                v-for="element in arrDone"
                :key="element.name"
              >
                <input
                  type="checkbox"
                  @click="statusChange(element.name)"
                  value="element.status"
                  v-model="element.status"
                />
                {{ element.name }}
                <button
                  type="button"
                  align="end"
                  @click="remove(element.name)"
                  class="btn-rm"
                >
                  Remove
                </button>
              </div>
            </draggable>
            <p align="center" v-if="!isSort && arrDone.length === 0">No task</p>
            <h6 v-if="!isSort">
              In Progress
              <span
                class="mdi mdi-swap-vertical"
                @click="sortBy('In Progress')"
              ></span>
            </h6>
            <draggable
              v-if="!isSort"
              class="list-group border-0 scroll-done-progress"
              :list="arrInProgress"
              group="tasks"
            >
              <div
                class="list-group-item listItems added-task"
                style="
                  display: flex;
                  justify-content: space-between;
                  align-items: center;
                  margin-bottom: 10px;
                "
                v-for="element in arrInProgress"
                :key="element.name"
              >
                <input
                  type="checkbox"
                  @click="statusChange(element.name)"
                  value="element.status"
                  v-model="element.status"
                />
                {{ element.name }}
                <button
                  type="button"
                  align="end"
                  @click="remove(element.name)"
                  class="btn-rm"
                >
                  Remove
                </button>
              </div>
            </draggable>
            <p align="center" v-if="!isSort && arrInProgress.length === 0">
              No task
            </p>
          </div>
        </div>
        <div class="col-6">
          <div class="p-4 card bg-transparent h-100 position-relative">
            <h3>
              Done<span
                class="mdi mdi-swap-vertical"
                @click="sortBy('Done')"
              ></span>
            </h3>
            <!-- Done draggable component. Pass arrDone to list prop -->
            <p align="center" class="text-absolute" v-if="arrDone.length === 0">
              No task
            </p>
            <draggable
              class="list-group doto-column border-0 scroll-list"
              :list="arrDone"
              group="tasks"
            >
              <div
                class="list-group-item listItems done-list"
                style="
                  display: flex;
                  justify-content: space-between;
                  align-items: center;
                  margin-bottom: 10px;
                "
                v-for="element in arrDone"
                :key="element.name"
              >
                {{ element.name }}
              </div>
            </draggable>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
//import draggable
import draggable from "vuedraggable";

export default {
  name: "doto-board",
  components: {
    //import draggable as a component
    draggable,
  },
  data() {
    return {
      doneSort: "DESC",
      inProgressSort: "DESC",
      // for new tasks
      newTask: "",
      arrNewTask: [],
      arrInProgress: [],
      arrDone: [],
      isSort: true,
    };
  },
  methods: {
    //add new tasks method
    add: function () {
      if (
        this.newTask &&
        !this.arrNewTask.find((ele) => ele.name === this.newTask)
      ) {
        this.arrNewTask.push({ name: this.newTask, status: false });
        this.newTask = "";
        this.arrDone = this.arrNewTask.filter((ele) => ele.status === true);
        this.arrInProgress = this.arrNewTask.filter(
          (ele) => ele.status === false
        );
      }
      else
      {
        this.newTask = "";
      }
    },
    // remove tasks method
    remove: function (name) {
      this.arrNewTask = this.arrNewTask.filter((ele) => ele.name !== name);
      this.arrDone = this.arrNewTask.filter((ele) => ele.status === true);
      this.arrInProgress = this.arrNewTask.filter(
        (ele) => ele.status === false
      );
    },
    // status change in tasks method
    statusChange: function (name) {
      if (name) {
        this.arrNewTask.map((ele) => {
          if (ele.name == name) {
            ele.status = ele.status ? false : true;
          }
        });
        this.arrDone = this.arrNewTask.filter((ele) => ele.status === true);
        this.arrInProgress = this.arrNewTask.filter(
          (ele) => ele.status === false
        );
      }
    },
    // sort in tasks method
    sort: function () {
      this.isSort = this.isSort ? false : true;
      this.arrDone = this.arrNewTask.filter((ele) => ele.status === true);
      this.arrInProgress = this.arrNewTask.filter(
        (ele) => ele.status === false
      );
    },
    // sortBy in ASC and DESC
    sortBy: function (type) {
      if (type === "In Progress") {
        this.arrInProgress =
          this.inProgressSort == "ASC"
            ? this.sorting("DESC", this.arrInProgress)
            : this.sorting("ASC", this.arrInProgress);
        this.inProgressSort = this.inProgressSort == "ASC" ? "DESC" : "ASC";
      } else {
        this.arrDone =
          this.doneSort == "ASC"
            ? this.sorting("DESC", this.arrDone)
            : this.sorting("ASC", this.arrDone);
        this.doneSort = this.doneSort == "ASC" ? "DESC" : "ASC";
      }
    },
    sorting(type, data) {
      const op =
        type === "ASC"
          ? data.sort((a, b) => {
              let fa = a.name.toLowerCase(),
                fb = b.name.toLowerCase();
              if (fa < fb) {
                return -1;
              }
              if (fa > fb) {
                return 1;
              }
              return 0;
            })
          : data.sort((a, b) => {
              let fa = a.name.toLowerCase(),
                fb = b.name.toLowerCase();
              if (fa < fb) {
                return 1;
              }
              if (fa > fb) {
                return -1;
              }
              return 0;
            });
      return op;
    },
  },
};
</script>

<style>
/* light stylings for the doto columns */
.doto-column {
  min-height: 300px;
}
.add {
  padding: 8px 30px;
  outline: auto;
  background-color: #8cb2e4;
}
.btn-rm {
  background: #8cb2e4;
  padding: 5px 10px;
  border-radius: 6px;
}
.content {
  background-color: #ffeded;
}
.addtask {
  display: flex;
}
.main {
  border: 1px solid #ccc;
  border-radius: 10px;
  padding: 40px;
  min-height: 100%;
  background-color: #f4f5f6;
}
body {
  height: 100vh;
  padding: 30px;
}
.input-style {
  padding: 8px 14.5% !important;
  border: 1px solid #ccc;
  border-radius: 6px;
  outline: none !important;
}
.primary-btn {
  background-color: #03045e;
  color: #fff;
  border-radius: 6px;
}
.scroll-list {
  max-height: calc(100vh - 375px);
  overflow: auto;
}
.text-absolute {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
}
.done-list {
  border-left: 5px solid rgb(10, 126, 10) !important;
}
.added-task {
  gap: 15px;
  justify-content: flex-start;
  border-radius: 6px;
}
.added-task:has(input:checked) {
  border: 1px solid #03045e55;
}
.added-task.done:has(input:checked) {
  border: 1px solid rgba(0, 128, 0, 0.623);
}
.added-task input {
  accent-color: #03045e;
  width: 16px;
  height: 16px;
}
.added-task.done input {
  accent-color: rgb(10, 126, 10);
}
.added-task button {
  background-color: transparent;
  color: red;
  border: none;
  outline: none;
  margin-left: auto;
  font-size: 14px;
}
.scroll-done-progress {
  max-height: calc(100vh - 400px);
  overflow: auto;
}
::-webkit-scrollbar {
  width: 6px;
  height: 6px;
}
::-webkit-scrollbar-thumb {
  background-color: #03045e;
  border-radius: 6px;
}
.column-split {
  min-height: calc(100vh - 280px);
}
.h5 {
  font-size: 2.5rem;
}
.h6 {
  font-size: 1.5rem;
}
.tasks
{
  margin: 15px 0px; font-size: 30px;
}
.createTask{
  font-size: 30px;
}
</style>