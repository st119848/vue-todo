<script setup>
import { ref, computed } from 'vue'

// ==========================================
// 1. STATE (ข้อมูลในระบบ Todo)
// ==========================================
const todos = ref([
  { id: 1, text: 'อ่านเอกสาร Vue.js 3', done: true },
  { id: 2, text: 'เขียน Todo List แบบรวมไฟล์เดียว', done: false },
  { id: 3, text: 'เตรียมสอนการแยก Component ในบทเรียนถัดไป', done: false }
])

const newTodoText = ref('')
const currentFilter = ref('all') // 'all' | 'active' | 'completed'
const editingId = ref(null)
const editingText = ref('')

// ==========================================
// 2. COMPUTED PROPERTIES (การคำนวณสถิติและตัวกรอง)
// ==========================================
const totalCount = computed(() => todos.value.length)
const completedCount = computed(() => todos.value.filter(todo => todo.done).length)
const remainingCount = computed(() => todos.value.filter(todo => !todo.done).length)

// [VIEW ALL / FILTER] แสดงรายการตาม Filter ที่เลือก (All / Active / Completed)
const filteredTodos = computed(() => {
  if (currentFilter.value === 'active') {
    return todos.value.filter(todo => !todo.done)
  }
  if (currentFilter.value === 'completed') {
    return todos.value.filter(todo => todo.done)
  }
  return todos.value
})

// ==========================================
// 3. METHODS (ฟังก์ชันการทำงาน CRUD)
// ==========================================

// [CREATE / ADD] เพิ่มรายการใหม่
function addTodo() {
  const trimmed = newTodoText.value.trim()
  if (!trimmed) return

  const nextId = todos.value.length > 0
    ? Math.max(...todos.value.map(t => t.id)) + 1
    : 1

  todos.value.push({
    id: nextId,
    text: trimmed,
    done: false
  })

  newTodoText.value = ''
}

// [UPDATE] อัปเดตสถานะ เสร็จ / ยังไม่เสร็จ (Toggle Done)
function toggleTodo(todo) {
  todo.done = !todo.done
}

// [UPDATE] เริ่มแก้ไขข้อความ Todo
function startEdit(todo) {
  editingId.value = todo.id
  editingText.value = todo.text
}

// [UPDATE] บันทึกข้อความที่แก้ไข
function saveEdit(todo) {
  const trimmed = editingText.value.trim()
  if (trimmed) {
    todo.text = trimmed
  }
  editingId.value = null
  editingText.value = ''
}

// [UPDATE] ยกเลิกการแก้ไข
function cancelEdit() {
  editingId.value = null
  editingText.value = ''
}

// [DELETE] ลบรายการเดี่ยวตาม ID
function deleteTodo(id) {
  todos.value = todos.value.filter(todo => todo.id !== id)
}

// [DELETE] ล้างรายการที่ทำเสร็จแล้วทั้งหมด
function clearCompleted() {
  todos.value = todos.value.filter(todo => !todo.done)
}
</script>

<template>
  <div class="todo-app">
    <!-- ส่วนหัวข้อ (Header) -->
    <header class="app-header">
      <h1>Todo List App</h1>
      <p class="subtitle">จัดการรายการสิ่งที่ต้องทำ (All-in-One Component)</p>
    </header>

    <!-- แบบฟอร์มเพิ่ม Todo (Add Todo Form) -->
    <form class="todo-form" @submit.prevent="addTodo">
      <input
        v-model="newTodoText"
        type="text"
        placeholder="เพิ่มรายการใหม่ที่ต้องทำ..."
        class="todo-input"
      />
      <button type="submit" class="btn btn-primary" :disabled="!newTodoText.trim()">
        เพิ่มรายการ
      </button>
    </form>

    <!-- แถบสถิติและตัวกรอง (Statistics & Filter) -->
    <div class="filter-stats-bar">
      <div class="stats">
        <span>ทั้งหมด: <strong>{{ totalCount }}</strong></span>
        <span>เสร็จแล้ว: <strong class="text-success">{{ completedCount }}</strong></span>
        <span>คงเหลือ: <strong class="text-warning">{{ remainingCount }}</strong></span>
      </div>

      <div class="filter-buttons">
        <button
          type="button"
          :class="['btn-filter', { active: currentFilter === 'all' }]"
          @click="currentFilter = 'all'"
        >
          ทั้งหมด
        </button>
        <button
          type="button"
          :class="['btn-filter', { active: currentFilter === 'active' }]"
          @click="currentFilter = 'active'"
        >
          ยังไม่เสร็จ
        </button>
        <button
          type="button"
          :class="['btn-filter', { active: currentFilter === 'completed' }]"
          @click="currentFilter = 'completed'"
        >
          เสร็จแล้ว
        </button>
      </div>
    </div>

    <!-- รายการ Todos (View All Todos / List) -->
    <ul v-if="filteredTodos.length > 0" class="todo-list">
      <li
        v-for="todo in filteredTodos"
        :key="todo.id"
        :class="['todo-item', { completed: todo.done }]"
      >
        <!-- โหมดปกติ (View Mode) -->
        <div v-if="editingId !== todo.id" class="todo-content">
          <label class="checkbox-container">
            <input
              type="checkbox"
              :checked="todo.done"
              @change="toggleTodo(todo)"
            />
            <span class="checkmark"></span>
          </label>

          <span class="todo-text" :class="{ strikethrough: todo.done }">
            {{ todo.text }}
          </span>

          <div class="item-actions">
            <button
              type="button"
              class="btn-icon btn-edit"
              title="แก้ไข"
              @click="startEdit(todo)"
            >
              แก้ไข
            </button>
            <button
              type="button"
              class="btn-icon btn-delete"
              title="ลบ"
              @click="deleteTodo(todo.id)"
            >
              ลบ
            </button>
          </div>
        </div>

        <!-- โหมดแก้ไขข้อความ (Edit / Update Mode) -->
        <div v-else class="edit-mode">
          <input
            v-model="editingText"
            type="text"
            class="edit-input"
            @keyup.enter="saveEdit(todo)"
            @keyup.esc="cancelEdit"
            autofocus
          />
          <div class="edit-actions">
            <button
              type="button"
              class="btn btn-save"
              @click="saveEdit(todo)"
            >
              บันทึก
            </button>
            <button
              type="button"
              class="btn btn-cancel"
              @click="cancelEdit"
            >
              ยกเลิก
            </button>
          </div>
        </div>
      </li>
    </ul>

    <!-- กล่องแจ้งเตือนเมื่อไม่มีรายการ (Empty State) -->
    <div v-else class="empty-state">
      <p>ไม่มีรายการที่ต้องทำในหมวดนี้ 🎉</p>
    </div>

    <!-- ส่วนท้าย (Footer Actions) -->
    <footer v-if="completedCount > 0" class="app-footer">
      <button
        type="button"
        class="btn-clear"
        @click="clearCompleted"
      >
        ล้างรายการที่ทำเสร็จแล้ว ({{ completedCount }})
      </button>
    </footer>
  </div>
</template>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 0 auto;
  padding: 1.75rem;
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
  font-family: inherit;
  color: #333333;
}

@media (prefers-color-scheme: dark) {
  .todo-app {
    background-color: #1e1e24;
    color: #e0e0e0;
    box-shadow: 0 4px 20px rgba(0, 0, 0, 0.3);
  }
}

.app-header {
  text-align: center;
  margin-bottom: 1.5rem;
}

.app-header h1 {
  font-size: 1.85rem;
  font-weight: 700;
  color: #42b883;
  margin-bottom: 0.25rem;
}

.subtitle {
  font-size: 0.9rem;
  color: #888888;
}

.todo-form {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.25rem;
}

.todo-input {
  flex: 1;
  padding: 0.7rem 1rem;
  border: 2px solid #e2e8f0;
  border-radius: 8px;
  font-size: 0.95rem;
  outline: none;
  background-color: transparent;
  color: inherit;
  transition: border-color 0.2s;
}

.todo-input:focus {
  border-color: #42b883;
}

.btn {
  padding: 0.7rem 1.2rem;
  border: none;
  border-radius: 8px;
  font-size: 0.95rem;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.2s, opacity 0.2s;
}

.btn-primary {
  background-color: #42b883;
  color: #ffffff;
}

.btn-primary:hover:not(:disabled) {
  background-color: #33a06f;
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.filter-stats-bar {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #e2e8f0;
  margin-bottom: 1rem;
}

@media (min-width: 480px) {
  .filter-stats-bar {
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }
}

.stats {
  display: flex;
  gap: 0.75rem;
  font-size: 0.85rem;
}

.text-success {
  color: #42b883;
}

.text-warning {
  color: #e67e22;
}

.filter-buttons {
  display: flex;
  gap: 0.35rem;
}

.btn-filter {
  padding: 0.35rem 0.75rem;
  border: 1px solid #e2e8f0;
  background: transparent;
  border-radius: 6px;
  font-size: 0.85rem;
  cursor: pointer;
  color: inherit;
  transition: all 0.2s;
}

.btn-filter.active {
  background-color: #42b883;
  color: #ffffff;
  border-color: #42b883;
}

.btn-filter:hover:not(.active) {
  background-color: rgba(66, 184, 131, 0.1);
}

.todo-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
}

.todo-item {
  padding: 0.75rem 1rem;
  border-radius: 8px;
  background-color: rgba(0, 0, 0, 0.02);
  border: 1px solid #edf2f7;
  transition: all 0.2s;
}

@media (prefers-color-scheme: dark) {
  .todo-item {
    background-color: rgba(255, 255, 255, 0.04);
    border-color: rgba(255, 255, 255, 0.1);
  }
}

.todo-item:hover {
  border-color: #cbd5e0;
}

.todo-content {
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.checkbox-container input {
  cursor: pointer;
  width: 1.15rem;
  height: 1.15rem;
  accent-color: #42b883;
}

.todo-text {
  flex: 1;
  font-size: 0.95rem;
  word-break: break-word;
  user-select: none;
}

.strikethrough {
  text-decoration: line-through;
  opacity: 0.55;
}

.item-actions {
  display: flex;
  gap: 0.4rem;
}

.btn-icon {
  padding: 0.3rem 0.6rem;
  border: none;
  border-radius: 4px;
  font-size: 0.8rem;
  cursor: pointer;
  transition: background-color 0.2s;
}

.btn-edit {
  background-color: #e2e8f0;
  color: #4a5568;
}

.btn-edit:hover {
  background-color: #cbd5e0;
}

.btn-delete {
  background-color: #fed7d7;
  color: #c53030;
}

.btn-delete:hover {
  background-color: #feb2b2;
}

.edit-mode {
  display: flex;
  gap: 0.5rem;
  align-items: center;
}

.edit-input {
  flex: 1;
  padding: 0.4rem 0.6rem;
  border: 2px solid #42b883;
  border-radius: 6px;
  font-size: 0.95rem;
  background-color: transparent;
  color: inherit;
  outline: none;
}

.edit-actions {
  display: flex;
  gap: 0.35rem;
}

.btn-save {
  padding: 0.4rem 0.75rem;
  background-color: #42b883;
  color: #ffffff;
}

.btn-save:hover {
  background-color: #33a06f;
}

.btn-cancel {
  padding: 0.4rem 0.75rem;
  background-color: #a0aec0;
  color: #ffffff;
}

.btn-cancel:hover {
  background-color: #718096;
}

.empty-state {
  text-align: center;
  padding: 2rem 1rem;
  color: #a0aec0;
  font-size: 0.95rem;
}

.app-footer {
  margin-top: 1.25rem;
  text-align: right;
  border-top: 1px solid #e2e8f0;
  padding-top: 0.75rem;
}

.btn-clear {
  background: none;
  border: none;
  color: #e53e3e;
  font-size: 0.85rem;
  cursor: pointer;
  text-decoration: underline;
  padding: 0;
}

.btn-clear:hover {
  color: #c53030;
}
</style>
