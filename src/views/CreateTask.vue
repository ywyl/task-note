<script setup lang="ts">
import { reactive, ref } from 'vue'
import { ElMessage } from 'element-plus'

export interface CreateTaskPayload {
  title: string
  tag?: string
  tagType?: 'primary' | 'success' | 'warning' | 'danger' | 'info'
  permanent: boolean
}

// 标签选项
const tagOptions = [
  { label: '工作', type: 'primary' },
  { label: '学习', type: 'success' },
  { label: '会议', type: 'warning' },
  { label: '开发', type: 'success' },
  { label: '规划', type: 'primary' },
  { label: '总结', type: 'info' },
  { label: '项目', type: 'danger' },
] as const

const dialogVisible = ref(false)
const form = reactive<{ title: string; tag: string; permanent: boolean }>({
  title: '',
  tag: '',
  permanent: false,
})

const emit = defineEmits<{
  (e: 'created', payload: CreateTaskPayload): void
}>()

// 打开弹窗并重置表单
function open() {
  form.title = ''
  form.tag = ''
  form.permanent = false
  dialogVisible.value = true
}

function confirmAdd() {
  if (!form.title.trim()) {
    ElMessage.warning('请输入任务内容')
    return
  }
  const tag = tagOptions.find((o) => o.label === form.tag)
  emit('created', {
    title: form.title.trim(),
    tag: form.tag || undefined,
    tagType: tag?.type,
    permanent: form.permanent,
  })
  dialogVisible.value = false
  ElMessage.success('已添加任务')
}

defineExpose({ open })
</script>

<template>
  <el-dialog v-model="dialogVisible" title="新建任务" width="460px" append-to-body>
    <el-form label-width="90px" @submit.prevent>
      <el-form-item label="任务内容" required>
        <el-input
          v-model="form.title"
          placeholder="请输入任务内容"
          maxlength="50"
          show-word-limit
          @keyup.enter="confirmAdd"
        />
      </el-form-item>
      <el-form-item label="标签">
        <el-select v-model="form.tag" placeholder="选择标签" clearable style="width: 100%">
          <el-option
            v-for="opt in tagOptions"
            :key="opt.label"
            :label="opt.label"
            :value="opt.label"
          />
        </el-select>
      </el-form-item>
      <el-form-item label="类型">
        <el-radio-group v-model="form.permanent">
          <el-radio :value="false">临时</el-radio>
          <el-radio :value="true">永久</el-radio>
        </el-radio-group>
      </el-form-item>
    </el-form>
    <template #footer>
      <el-button @click="dialogVisible = false">取消</el-button>
      <el-button type="primary" @click="confirmAdd">确定</el-button>
    </template>
  </el-dialog>
</template>
