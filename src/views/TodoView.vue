<template>
  <!-- input -->
  <div class="bg-white d-flex align-items-center rounded-2 position-relative mb-2">
    <input
      type="text"
      class="form-control py-2"
      style="height: 48px;"
      placeholder="請輸入待辦事項"
      v-model="text"
      @keyup.enter="addTodo"
    >
    <button
      type="button"
      class="btn btn-dark position-absolute lh-1 p-1"
      style="right: 4px; top: 4px;"
      @click="addTodo"
    >
      <i class="bi bi-plus-lg d-block text-white fw-bold fs-2"></i>
    </button>
  </div>
  <!-- todo tab & items -->
  <div class="bg-white rounded-2">
    <!-- tab -->
    <ul class="nav nav-underline flex-nowrap gap-0 text-center">
      <li class="nav-item w-100">
        <button
          type="button"
          class="nav-link w-100"
          :class="{ active: visibility === 'all'}"
          @click="visibility = 'all'"
          :disabled="visibility === 'all'"
        >全部</button>
      </li>
      <li class="nav-item w-100">
        <button
          type="button"
          class="nav-link w-100"
          :class="{ active: visibility === 'active'}"
          @click="visibility = 'active'"
          :disabled="visibility === 'active'"
        >待完成</button>
      </li>
      <li class="nav-item w-100">
        <button
          type="button"
          class="nav-link w-100"
          :class="{ active: visibility === 'completed'}"
          @click="visibility = 'completed'"
          :disabled="visibility === 'completed'"
        >已完成</button>
      </li>
    </ul>
    <!-- todo items -->
    <div class="px-4">
      <template v-if="filterData.length">
        <ul class="list-unstyled mb-0">
          <li v-for="item in filterData" :key="item.id" class="py-3 border-bottom d-flex justify-content-between">
            <template v-if="editTodoId === item.id">
              <input
                type="text"
                class="form-control border"
                v-model="editTodoText"
                @keyup.enter="completeEdit(item)"
              />
            </template>
            <template v-else>
              <div class="form-check">
                <input
                  class="form-check-input"
                  type="checkbox"
                  :id="item.id"
                  v-model="item.done"
                />
                <label
                  class="form-check-label"
                  :for="item.id"
                  :class="{ 'text-primary text-decoration-line-through': item.done }"
                  @dblclick="editTodo(item)"
                >
                  {{ item.text }}
                </label>
              </div>
              <button type="button" class="btn p-0" @click="removeTodo(item.id)">
                <i class="bi bi-x-circle"></i>
              </button>
            </template>
          </li>
        </ul>
      </template>
      <template v-else>
        <p class="text-center py-5">這邊還沒有項目喔</p>
      </template>
      <div class="py-3 d-flex justify-content-between">
        <p>{{ doneNum }} 個已完成項目</p>
        <button type="button" class="btn p-0" @click="deleteAllCompleted">清除已完成項目</button>
      </div>
    </div>
  </div>
</template>
<script setup>
import { ref, watchEffect, computed } from 'vue'

const localData = JSON.parse(localStorage.getItem('todoLocalData')) || []

const text = ref('')
const todos = ref(localData)

const addTodo = () => {
  if(text.value) {
    todos.value.push({
      id: Date.now(),
      text: text.value,
      done: false
    })
  }
  text.value = ''
}

// 移除
const removeTodo = (id) => {
  todos.value = todos.value.filter(e => e.id !== id)
}

// 編輯中
const editTodoId = ref(null)
const editTodoText = ref('')
const editTodo = (todo) => {
  editTodoId.value = todo.id
  editTodoText.value = todo.text
}

// 編輯完成
const completeEdit = (todo) => {
  const index = todos.value.findIndex(e => e.id === todo.id)
  todos.value[index].text = editTodoText.value

  editTodoText.value = ''
  editTodoId.value = ''
}

// 自動儲存
watchEffect(() => {
  localStorage.setItem('todoLocalData', JSON.stringify(todos.value))
})

// 切換 tab
const visibility = ref('all')
// all 全部 / active 待完成 / completed 已完成
const filterData = computed(() => {
  if(visibility.value === 'active') return todos.value.filter(e => !e.done)
  if(visibility.value === 'completed') return todos.value.filter(e => e.done)
  return todos.value
})

// 目前已完成多少數量
const doneNum = computed(() => {
  return todos.value.filter(e => e.done).length
})

// 刪除所有已完成項目
const deleteAllCompleted = () => {
  todos.value = todos.value.filter(e => !e.done)
}

</script>