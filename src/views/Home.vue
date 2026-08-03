<script setup lang="ts">
import { useCounterStore } from '@/stores/counter'

const counter = useCounterStore()
</script>

<template>
  <div class="home">
    <el-card class="welcome-card">
      <template #header>
        <h2>欢迎使用 Task Note</h2>
      </template>
      <p>这是一个基于 Vue 3 + TypeScript 的任务笔记应用。</p>
      <el-divider />
      <div class="counter-demo">
        <p>计数器示例 (Pinia):</p>
        <el-button-group>
          <el-button type="danger" @click="counter.decrement">-</el-button>
          <el-button disabled>{{ counter.count }}</el-button>
          <el-button type="primary" @click="counter.increment">+</el-button>
        </el-button-group>
        <el-button class="reset-btn" size="small" @click="counter.reset">重置</el-button>
      </div>
    </el-card>
  </div>
</template>

<style scoped lang="scss">
.home {
  display: flex;
  justify-content: center;
  align-items: center;
  min-height: 60vh;

  .welcome-card {
    max-width: 500px;
    width: 100%;
  }

  .counter-demo {
    .reset-btn {
      margin-left: 12px;
    }
  }
}
</style>
