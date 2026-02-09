<script setup lang="ts">
import {ref,computed} from 'vue'

interface ToDoItem {
  id: number;
  title: string;
  completed: boolean;
  deadline: string;
}
const toDoList = ref<ToDoItem[]>([]);
const newTaskTitle = ref("");
const newTaskDeadline = ref("");

const AddTask = (title: string,deadline: string) => {
  const newTask: ToDoItem = {
    id: toDoList.value.length,
    title: title,
    completed: false,
    deadline: deadline
  };
  toDoList.value.push(newTask);
  SortByDeadline();
};

const RemoveTask = (id: number) => {
  toDoList.value = toDoList.value.filter(task => task.id !== id);
};

const ResetList = () => {
  toDoList.value = [];
};

const SortByDeadline = () => {
  toDoList.value.sort((a, b) => a.deadline.localeCompare(b.deadline));
};

const activeTasks = computed(() => {
  return toDoList.value.filter(task => !task.completed)
})

const completedTasks = computed(() => {
  return toDoList.value.filter(task => task.completed)
})

// ... (既存のimportや変数定義の下あたりに追加)

// ★追加: 期限に応じたクラス名を返す関数
const getDeadlineClass = (deadline: string): string => {
  if (!deadline) return ''; // 締め切りなしなら何もしない

  const today = new Date();
  const targetDate = new Date(deadline);

  // 時間を0時0分0秒にリセット（これをしないと「今日の朝」が「過去」判定されたりする）
  today.setHours(0, 0, 0, 0);
  targetDate.setHours(0, 0, 0, 0);

  // 差分（ミリ秒）を計算
  const diffTime = targetDate.getTime() - today.getTime();
  // 日数に変換 (1000ミリ秒 * 60秒 * 60分 * 24時間)
  const diffDays = diffTime / (1000 * 60 * 60 * 24);

  if (diffDays < 0) {
    return 'overdue'; // 期限切れクラス
  } else if (diffDays <= 3) {
    return 'warning'; // 3日以内クラス
  }
  return '';
};

const getDone = (completed: boolean): string => {
  return completed ? 'done' : 'undone';
};

</script>

<template>
  <div>ToDoList Component</div>
  <h2>ToDoList</h2>
  <input type="text" v-model="newTaskTitle" placeholder="名前" />
  <input type="date" v-model="newTaskDeadline" placeholder="締め切り" />
  <button @click="AddTask(newTaskTitle, newTaskDeadline); newTaskTitle = ''; newTaskDeadline = ''">Add Task</button>
  <button @click="ResetList">Reset List</button>

  <div class="container">
    
    <div class="column left-column">
      <h3>TODO ({{ activeTasks.length }})</h3>
      <ul>
        <li 
          v-for="todo in activeTasks" 
          :key="todo.id" 
          :class="getDeadlineClass(todo.deadline)"
        >
          <input type="checkbox" v-model="todo.completed" />
          {{ todo.title }}
          <span class="date">({{ todo.deadline }})</span>
          <button @click="RemoveTask(todo.id)">×</button>
        </li>
      </ul>
    </div>

    <div class="column right-column">
      <h3>DONE ({{ completedTasks.length }})</h3>
      <ul>
        <li 
          v-for="todo in completedTasks" 
          :key="todo.id"
          class="done-item"
        >
          <input type="checkbox" v-model="todo.completed" />
          {{ todo.title }}
          <span class="date">({{ todo.deadline }})</span>
          <button @click="RemoveTask(todo.id)">×</button>
        </li>
      </ul>
    </div>

  </div>
</template>

<style scoped>
/* ★追加 */
.overdue {
  color: red;
  font-weight: bold;
}
.warning {
  color: #d6af0d; /* 暗めの黄色 */
  font-weight: bold;
}
.done {
  text-align: right;
}
.undone {
  text-align: left;
}
.container {
  display: flex;       /* 横並びにする魔法 */
  width: 100%;
  gap: 20px;           /* カラム間の隙間 */
}

/* Unity: Layout Element (Flexible Width) */
.column {
  flex: 1;             /* 幅を1:1で分け合う */
  border: 1px solid #ccc;
  padding: 10px;
  border-radius: 8px;
}
.done-item {
  text-decoration: line-through;
  color: green;
  font-weight: bold;
}
</style>