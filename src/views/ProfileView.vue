<template>
  <div class="profile">
    <!-- 系统标题栏 -->
    <div class="desktop-header">
      <div class="system-title glass-button">
        <el-icon :size="24" color="#409EFF">
          <Monitor />
        </el-icon>
        <span class="title-text">As you've seen</span>
      </div>
      <div class="header-actions">
        <!-- 功能按钮组 -->
        <div class="button-container">
          <ThemeSwitcher :fixed="false" button-type="text" :button-circle="false" button-class="glass-button" :use-theme-color="false" />
          <el-button type="text" @click="goHome" class="glass-button">
            <el-icon><House /></el-icon>
          </el-button>
          <el-button type="text" @click="openSettings" class="glass-button">
            <el-icon><Setting /></el-icon>
          </el-button>
        </div>
        <!-- 用户按钮组 -->
        <div class="button-container">
          <div class="glass-button user-info-button">
            <el-avatar
              :size="24"
              src="https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png"
            />
            <span class="username">Manager</span>
          </div>
          <el-dropdown>
            <el-button type="text" class="glass-button">
              <el-icon><ArrowDown /></el-icon>
            </el-button>
            <template #dropdown>
              <el-dropdown-menu>
                <el-dropdown-item @click="openProfile">Profile</el-dropdown-item>
                <el-dropdown-item divided>Log out</el-dropdown-item>
              </el-dropdown-menu>
            </template>
          </el-dropdown>
        </div>
      </div>
    </div>

    <div class="content-container">
      <el-card class="profile-card">
        <template #header>
          <h2>User Profile</h2>
        </template>

        <div style="padding: 20px">
          <div class="profile-header">
            <el-avatar
              :size="80"
              src="https://cube.elemecdn.com/0/88/03b0d39583f48206768a7534e55bcpng.png"
            />
            <div class="profile-info">
              <h3>Manager</h3>
              <p>System Administrator</p>
            </div>
          </div>

          <el-descriptions :column="1" border style="margin-top: 20px">
            <el-descriptions-item label="Username">Manager</el-descriptions-item>
            <el-descriptions-item label="Role">Administrator</el-descriptions-item>
            <el-descriptions-item label="Email">manager@example.com</el-descriptions-item>
          </el-descriptions>

          <div style="margin-top: 20px; text-align: center">
            <el-button @click="$router.push('/')">Back to Desktop</el-button>
          </div>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { House, ArrowDown, Setting } from '@element-plus/icons-vue'
import ThemeSwitcher from '@/components/ThemeSwitcher.vue'

const router = useRouter()

// 打开个人资料
const openProfile = () => {
  router.push('/profile')
}

// 返回首页
const goHome = () => {
  router.push('/')
}

// 打开设置
const openSettings = () => {
  router.push('/settings')
}
</script>

<style scoped>

.profile-header {
  display: flex;
  align-items: center;
  gap: 20px;
  margin-bottom: 20px;
}

.profile-info h3 {
  margin: 0 0 8px 0;
  color: var(--text-color);
  font-size: 24px;
  font-weight: bold;
}

.profile-info p {
  margin: 0;
  color: var(--secondary-color);
  font-size: 16px;
}
</style>
