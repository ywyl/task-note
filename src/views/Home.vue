<script setup lang="ts">
import { computed, ref } from 'vue'
import { Calendar, Clock, Delete, Histogram, Plus } from '@element-plus/icons-vue'
import { ElMessage, ElMessageBox } from 'element-plus'
import CreateTask, { type CreateTaskPayload } from './CreateTask.vue'

type TabName = 'day' | 'week' | 'month'

interface TaskItem {
  id: number
  title: string
  tag?: string
  tagType?: 'primary' | 'success' | 'warning' | 'danger' | 'info'
  permanent: boolean
  done: boolean
}

const activeTab = ref<TabName>('day')

// 当前日期（月/日/年，启动时获取一次）
const now = new Date()
const monthDay = `${now.getMonth() + 1}/${now.getDate()}`
const year = String(now.getFullYear())

// 各时间维度的任务数据（演示用）
const dayTasks = ref<TaskItem[]>([
  { id: 1, title: '完成周报填写', tag: '工作', tagType: 'primary', permanent: false, done: false },
  { id: 2, title: '参加项目评审会议', tag: '会议', tagType: 'warning', permanent: false, done: false },
  { id: 3, title: '阅读技术文档 30 分钟', tag: '学习', tagType: 'success', permanent: false, done: true },
])

const weekTasks = ref<TaskItem[]>([
  { id: 1, title: '周一：制定本周目标', tag: '规划', tagType: 'primary', permanent: false, done: false },
  { id: 2, title: '周三：提交代码评审', tag: '开发', tagType: 'success', permanent: false, done: false },
  { id: 3, title: '周五：整理本周笔记', tag: '总结', tagType: 'info', permanent: false, done: true },
])

const monthTasks = ref<TaskItem[]>([
  { id: 1, title: '完成月度项目里程碑', tag: '项目', tagType: 'danger', permanent: false, done: false },
  { id: 2, title: '月度学习计划打卡', tag: '学习', tagType: 'success', permanent: false, done: false },
  { id: 3, title: '输出月度复盘文档', tag: '总结', tagType: 'warning', permanent: false, done: false },
])

// 当前 tab 对应的任务列表
const currentTasks = computed(() => {
  const map: Record<TabName, { tasks: TaskItem[]; title: string; subtitle: string }> = {
    day: { tasks: dayTasks.value, title: '今日任务', subtitle: '今天要做的事情' },
    week: { tasks: weekTasks.value, title: '本周任务', subtitle: '这一周要完成的事情' },
    month: { tasks: monthTasks.value, title: '本月任务', subtitle: '这个月的整体规划' },
  }
  return map[activeTab.value]
})

const remainingCount = computed(() => currentTasks.value.tasks.filter((t) => !t.done).length)

// 新建任务
const createTaskRef = ref<InstanceType<typeof CreateTask>>()
let nextTaskId = 100

function openAddDialog() {
  createTaskRef.value?.open()
}

function handleCreated(payload: CreateTaskPayload) {
  currentTasks.value.tasks.push({
    id: nextTaskId++,
    ...payload,
    done: false,
  })
}

function doRemove(task: TaskItem) {
  const list = currentTasks.value.tasks
  const index = list.findIndex((t) => t.id === task.id)
  if (index > -1) {
    list.splice(index, 1)
    ElMessage.success('已删除任务')
  }
}

// 临时任务：小确认框删除
function removeTask(task: TaskItem) {
  doRemove(task)
}

// 永久任务：警告弹出框确认
function removePermanentTask(task: TaskItem) {
  ElMessageBox.confirm('当前删除的任务是永久任务，确定删除吗？', '警告', {
    confirmButtonText: '删除',
    cancelButtonText: '取消',
    type: 'warning',
    confirmButtonType: 'danger',
  })
    .then(() => doRemove(task))
    .catch(() => {})
}

function toggleTask(task: TaskItem) {
  task.done = !task.done
}
</script>

<template>
  <div class="home">
    <!-- 页头 -->
    <header class="page-header">
      <h1 class="title">任务笔记</h1>
      <div class="header-bottom">
        <p class="subtitle">按时间维度管理你的任务</p>
        <span class="header-date">
          <span class="date-md">{{ monthDay }}</span>
          <span class="date-year">{{ year }}</span>
        </span>
      </div>
    </header>

    <!-- Tab 切换 -->
    <el-tabs v-model="activeTab" class="view-tabs" stretch>
      <el-tab-pane name="day">
        <template #label>
          <span class="tab-label"
            ><el-icon><Clock /></el-icon>天</span
          >
        </template>
      </el-tab-pane>
      <el-tab-pane name="week">
        <template #label>
          <span class="tab-label"
            ><el-icon><Calendar /></el-icon>周</span
          >
        </template>
      </el-tab-pane>
      <el-tab-pane name="month">
        <template #label>
          <span class="tab-label"
            ><el-icon><Histogram /></el-icon>月</span
          >
        </template>
      </el-tab-pane>
    </el-tabs>

    <!-- 当前维度内容 -->
    <section class="task-panel">
      <div class="panel-head">
        <div>
          <h2 class="panel-title">{{ currentTasks.title }}</h2>
          <p class="panel-subtitle">{{ currentTasks.subtitle }}</p>
        </div>
        <div class="panel-actions">
          <el-tag type="info" round>剩余 {{ remainingCount }} 项</el-tag>
          <el-button type="primary" :icon="Plus" @click="openAddDialog">新建任务</el-button>
        </div>
      </div>

      <el-empty v-if="currentTasks.tasks.length === 0" description="暂无任务" :image-size="80" />

      <transition-group v-else name="list" tag="ul" class="task-list">
        <li
          v-for="task in currentTasks.tasks"
          :key="task.id"
          class="task-item"
          :class="{ done: task.done }"
        >
          <el-checkbox :model-value="task.done" @change="toggleTask(task)" />
          <span class="task-title">{{ task.title }}</span>
          <el-tag v-if="task.tag" :type="task.tagType" size="small" effect="light">{{
            task.tag
          }}</el-tag>
          <el-popconfirm
            v-if="!task.permanent"
            title="确定删除该任务吗？"
            confirm-button-text="删除"
            cancel-button-text="取消"
            confirm-button-type="danger"
            width="180"
            @confirm="removeTask(task)"
          >
            <template #reference>
              <el-button class="delete-btn" :icon="Delete" circle text size="small" />
            </template>
          </el-popconfirm>
          <el-button
            v-else
            class="delete-btn"
            :icon="Delete"
            circle
            text
            size="small"
            @click="removePermanentTask(task)"
          />
        </li>
      </transition-group>
    </section>

    <!-- 新建任务弹窗 -->
    <CreateTask ref="createTaskRef" @created="handleCreated" />
  </div>
</template>

<style scoped lang="scss">
.home {
  max-width: 720px;
  margin: 0 auto;
  padding: 24px 16px 40px;
}

.page-header {
  margin-bottom: 20px;

  .title {
    margin: 0;
    font-size: 26px;
    color: $text-color;
  }

  .header-bottom {
    display: flex;
    align-items: center;
    margin-top: 6px;
  }

  .subtitle {
    margin: 0;
    font-size: 14px;
    color: #909399;
  }

  .header-date {
    margin-left: auto;
    margin-right: 20px;
    font-size: 20px;
    font-weight: 600;
    color: #606266;
    white-space: nowrap;

    .date-md {
      color: $primary-color;
    }

    .date-year {
      margin-left: 4px;
      font-size: 14px;
      font-weight: 400;
    }
  }
}

.view-tabs {
  :deep(.el-tabs__nav-wrap::after) {
    height: 1px;
  }

  .tab-label {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    font-weight: 600;
  }
}

.task-panel {
  margin-top: 16px;
  padding: 20px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 2px 12px rgba(0, 0, 0, 0.06);
}

.panel-head {
  display: flex;
  align-items: flex-start;
  justify-content: space-between;
  gap: 12px;
  margin-bottom: 16px;

  .panel-title {
    margin: 0;
    font-size: 18px;
    color: $text-color;
  }

  .panel-subtitle {
    margin: 4px 0 0;
    font-size: 13px;
    color: #909399;
  }

  .panel-actions {
    display: flex;
    align-items: center;
    gap: 8px;
    flex-shrink: 0;
  }
}

.task-list {
  list-style: none;
  margin: 0;
  padding: 0;
}

.task-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 4px;
  border-bottom: 1px solid #f0f2f5;

  &:last-child {
    border-bottom: none;
  }

  .task-title {
    flex: 1;
    font-size: 14px;
    color: $text-color;
  }

  .delete-btn {
    color: #c0c4cc;
    transition: color 0.2s;

    &:hover {
      color: $primary-color;
    }
  }

  &.done {
    .task-title {
      color: #c0c4cc;
      text-decoration: line-through;
    }
  }
}

.list-enter-active,
.list-leave-active {
  transition: all 0.3s ease;
}

.list-enter-from,
.list-leave-to {
  opacity: 0;
  transform: translateX(16px);
}
</style>
