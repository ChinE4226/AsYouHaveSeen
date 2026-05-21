<template>
  <div class="users">
    <!-- 系统标题栏 -->
    <div class="desktop-header">
      <div class="system-title glass-button">
        <el-icon :size="24" color="#409EFF">
          <Monitor />
        </el-icon>
        <span class="title-text">As you've seen</span>
      </div>
      <div class="header-actions">
        <div class="button-container">
          <ThemeSwitcher :fixed="false" button-type="text" :button-circle="false" button-class="glass-button" :use-theme-color="false" />
          <el-button type="text" @click="goHome" class="glass-button">
            <el-icon><House /></el-icon>
          </el-button>
          <el-button type="text" @click="openSettings" class="glass-button">
            <el-icon><Setting /></el-icon>
          </el-button>
        </div>
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
      <el-card class="users-card">
        <template #header>
          <h2>User Management</h2>
        </template>

        <div style="padding: 20px; text-align: center; color: var(--secondary-color)">
          <p>Users page is under construction.</p>
          <el-button @click="$router.push('/')">Back to Desktop</el-button>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'
import ThemeSwitcher from '@/components/ThemeSwitcher.vue'

const router = useRouter()

const openProfile = () => {
  router.push('/profile')
}

const goHome = () => {
  router.push('/')
}

const openSettings = () => {
  router.push('/settings')
}
</script>

<style scoped>

</style>
