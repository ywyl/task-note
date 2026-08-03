<script setup lang="ts">
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { HomeFilled, InfoFilled } from '@element-plus/icons-vue'

const router = useRouter()
const activeIndex = ref('/')

function handleSelect(index: string) {
  router.push(index)
}
</script>

<template>
  <div id="app-container">
    <el-container>
      <el-header class="app-header">
        <div class="logo">Task Note</div>
        <el-menu :default-active="activeIndex" mode="horizontal" @select="handleSelect">
          <el-menu-item index="/">
            <el-icon><HomeFilled /></el-icon>
            首页
          </el-menu-item>
          <el-menu-item index="/about">
            <el-icon><InfoFilled /></el-icon>
            关于
          </el-menu-item>
        </el-menu>
      </el-header>
      <el-main class="app-main">
        <router-view />
      </el-main>
    </el-container>
  </div>
</template>

<style scoped lang="scss">
.app-header {
  display: flex;
  align-items: center;
  background: #fff;
  border-bottom: 1px solid #e4e7ed;
  padding: 0 20px;

  .logo {
    font-size: 20px;
    font-weight: bold;
    color: #409eff;
    margin-right: 40px;
    white-space: nowrap;
  }
}

.app-main {
  min-height: calc(100vh - 60px);
  background-color: #f5f7fa;
  padding: 24px;
}
</style>
