<script setup lang="ts">
const techStack = [
  { name: 'Vue 3', description: '渐进式 JavaScript 框架' },
  { name: 'TypeScript', description: '类型安全的 JavaScript 超集' },
  { name: 'Element Plus', description: '基于 Vue 3 的组件库' },
  { name: 'Pinia', description: 'Vue 状态管理' },
  { name: 'Vue Router', description: 'Vue 官方路由' },
  { name: 'Vite', description: '下一代前端构建工具' },
  { name: 'Sass', description: 'CSS 预处理器' },
]
</script>

<template>
  <div class="about">
    <el-card>
      <template #header>
        <h2>关于项目</h2>
      </template>
      <p>这是一个使用以下技术栈搭建的项目模板：</p>
      <el-table :data="techStack" stripe style="width: 100%">
        <el-table-column prop="name" label="技术" width="150" />
        <el-table-column prop="description" label="说明" />
      </el-table>
    </el-card>
  </div>
</template>

<style scoped lang="scss">
.about {
  max-width: 600px;
  margin: 0 auto;
}
</style>
