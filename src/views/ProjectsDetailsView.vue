<template>
  <div class="projects-details">
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
      <el-card class="project-card">
        <template #header>
          <h2>Project Details</h2>
        </template>

        <!-- 加载状态 -->
        <div v-if="loading" style="text-align: center; padding: 20px">
          <el-icon class="is-loading"><Loading /></el-icon>
          <p>Loading project data...</p>
        </div>

        <!-- 错误状态 -->
        <div v-else-if="error" style="text-align: center; padding: 20px; color: #f56c6c">
          <p>{{ error }}</p>
          <el-button @click="retryLoad">Retry</el-button>
        </div>

        <!-- 项目详情 -->
        <div v-else-if="currentProject" style="padding: 20px">
          <div class="project-header">
            <h3>{{ currentProject.name }}</h3>
            <el-tag :type="getStatusType(currentProject.status)" style="margin-left: 16px">
              {{ currentProject.status }}
            </el-tag>
          </div>
          <el-descriptions :column="2" style="margin-top: 20px">
            <el-descriptions-item label="Project ID">{{ currentProject.id }}</el-descriptions-item>
            <el-descriptions-item label="Status">{{ currentProject.status }}</el-descriptions-item>
            <el-descriptions-item label="Created At">{{
              currentProject.created_at || 'N/A'
            }}</el-descriptions-item>
            <el-descriptions-item label="Updated At">{{
              currentProject.updated_at || 'N/A'
            }}</el-descriptions-item>
            <el-descriptions-item label="Description" :span="2">{{
              currentProject.description
            }}</el-descriptions-item>
            <el-descriptions-item label="Project URL" :span="2">
              <a
                :href="currentProject.URL"
                target="_blank"
                style="color: var(--primary-color); text-decoration: none"
              >
                {{ currentProject.URL }}
              </a>
            </el-descriptions-item>
          </el-descriptions>

          <div style="margin-top: 20px; text-align: center">
            <el-button type="primary" @click="openProject" style="margin-right: 16px">
              Open Project
            </el-button>
            <el-button @click="$router.push('/')"> Back to Desktop </el-button>
          </div>
        </div>

        <!-- 未找到项目 -->
        <div v-else style="text-align: center; padding: 20px; color: #909399">
          <p>Project not found</p>
          <el-button @click="$router.push('/')">Back to Desktop</el-button>
        </div>
      </el-card>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import projectsDetailsData from '@/assets/data/projectsdetails.json'
import { Monitor, ArrowDown, Setting, Loading } from '@element-plus/icons-vue'
import ThemeSwitcher from '@/components/ThemeSwitcher.vue'

const route = useRoute()
const router = useRouter()
const loading = ref(true)
const error = ref(null)
const projectsDetails = ref([])

// 获取当前项目ID
const projectId = computed(() => route.params.projectId)

// 获取当前项目信息
const currentProject = computed(() => {
  if (!projectId.value || !projectsDetails.value.length) return null
  return projectsDetails.value.find((project) => project.id === projectId.value)
})

// 获取状态类型
const getStatusType = (status) => {
  switch (status?.toLowerCase()) {
    case 'done':
    case 'completed':
      return 'success'
    case 'in progress':
    case 'working':
      return 'warning'
    case 'pending':
      return 'info'
    default:
      return 'info'
  }
}

// 打开项目
const openProject = () => {
  if (currentProject.value?.URL) {
    window.open(currentProject.value.URL, '_blank')
  }
}

// 打开个人资料
const openProfile = () => {
  router.push('/profile')
}

const goHome = () => {
  router.push('/')
}

// 打开设置
const openSettings = () => {
  router.push('/settings')
}

// 重试加载
const retryLoad = () => {
  loadData()
}

// 加载数据
const loadData = async () => {
  try {
    loading.value = true
    error.value = null

    console.log('Loading projects details data...')
    console.log('Project ID:', projectId.value)

    if (projectsDetailsData && projectsDetailsData.projectsdetails) {
      projectsDetails.value = projectsDetailsData.projectsdetails
      console.log('Projects details loaded:', projectsDetails.value)
      console.log('Current project:', currentProject.value)
    } else {
      error.value = 'No projects data found'
      console.error('No projects data found in:', projectsDetailsData)
    }
  } catch (err) {
    error.value = 'Failed to load projects data: ' + err.message
    console.error('Error loading projects data:', err)
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  loadData()
})
</script>

<style scoped>

.project-header {
  display: flex;
  align-items: center;
  margin-bottom: 16px;
}

.project-header h3 {
  margin: 0;
  color: var(--text-color);
  font-size: 24px;
  font-weight: bold;
}
</style>
