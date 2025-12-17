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
        <!-- 返回按钮组 -->
        <div class="button-container">
          <el-button type="text" @click="goHome" class="glass-button">
            <el-icon><House /></el-icon>
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
import { House, ArrowDown } from '@element-plus/icons-vue'

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
.profile {
  min-height: 100vh;
  width: 100vw;
  background: linear-gradient(135deg, var(--background-left-color), var(--background-right-color));
  padding: 0;
  overflow-y: auto;
}

.desktop-header {
  padding: 12px 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
}

.system-title {
  display: flex;
  align-items: center;
  gap: 12px;
}

.system-title.glass-button {
  width: auto;
  height: 40px;
  padding: 0 16px;
}

.title-text {
  font-size: 20px;
  font-weight: bold;
  color: var(--text-color);
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 12px;
}

.button-container {
  display: flex;
  align-items: center;
  gap: 8px;
}

.glass-button {
  width: 40px;
  height: 40px;
  padding: 0;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.6) !important;
  backdrop-filter: blur(10px) saturate(180%);
  border: 1px solid rgba(255, 255, 255, 0.3) !important;
  transition: all 0.3s ease;
  color: var(--text-color);
}

.glass-button:hover {
  background: rgba(255, 255, 255, 0.8) !important;
  border-color: rgba(255, 255, 255, 0.5) !important;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
}

.user-info-button {
  padding: 0 12px !important;
  width: auto;
  gap: 8px;
}

.username {
  font-weight: 500;
  color: var(--text-color);
  font-size: 13px;
  white-space: nowrap;
}

.desktop-header {
  background: var(--header-color);
  padding: 16px 24px;
  box-shadow: 0 2px 8px var(--shadow-color);
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 24px;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 1000;
}

.system-title {
  display: flex;
  align-items: center;
  gap: 12px;
}

.title-text {
  font-size: 20px;
  font-weight: bold;
  color: var(--text-color);
}

.user-info {
  display: flex;
  align-items: center;
  gap: 12px;
}

.username {
  font-weight: 500;
  color: var(--secondary-color);
}

.content-container {
  margin: 80px 24px 32px;
  padding: 20px;
}

.profile-card {
  background: var(--header-color);
  border-radius: 8px;
  box-shadow: 0 4px 16px var(--shadow-color);
  border: 1px solid var(--border-color);
}

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
