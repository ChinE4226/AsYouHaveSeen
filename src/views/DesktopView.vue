<template>
  <div class="desktop">
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

    <!-- 建议 -->
    <section class="status-section">
      <h2 class="section-title">
        <el-icon><Monitor /></el-icon>
        Recommendations
      </h2>

      <div v-if="recommendations.length > 0" class="recommendations-container">
        <div
          v-for="(recommendation, index) in recommendations"
          :key="index"
          class="recommendation-group"
        >
          <!-- 推荐应用 -->
          <div
            v-if="recommendation.RecommendedApps && recommendation.RecommendedApps.length > 0"
            class="recommendation-section"
          >
            <div class="recommendation-grid">
              <div
                v-for="app in recommendation.RecommendedApps"
                :key="app.id"
                class="recommendation-card app-card"
                @click="openRecommendedApp(app)"
              >
                <div class="card-icon app-icon">
                  <el-icon><Monitor /></el-icon>
                </div>
                <div class="card-info">
                  <div class="card-name">{{ app.name }}</div>
                  <div class="card-subtitle">{{ app.location }}</div>
                </div>
              </div>
            </div>
          </div>

          <!-- 推荐工具 -->
          <div
            v-if="recommendation.RecommendedTools && recommendation.RecommendedTools.length > 0"
            class="recommendation-section"
          >
            <div class="recommendation-grid">
              <div
                v-for="tool in recommendation.RecommendedTools"
                :key="tool.id"
                class="recommendation-card tool-card"
                @click="openRecommendedTool(tool)"
              >
                <div class="card-icon tool-icon">
                  <el-icon><Tools /></el-icon>
                </div>
                <div class="card-info">
                  <div class="card-name">{{ tool.name }}</div>
                  <div class="card-subtitle">{{ tool.URL }}</div>
                </div>
              </div>
            </div>
          </div>

          <!-- 推荐项目 -->
          <div
            v-if="
              recommendation.RecommendedProjects && recommendation.RecommendedProjects.length > 0
            "
            class="recommendation-section"
          >
            <div class="recommendation-grid">
              <div
                v-for="project in recommendation.RecommendedProjects"
                :key="project.id"
                class="recommendation-card project-card"
                @click="openRecommendedProject(project)"
              >
                <div class="card-icon project-icon">
                  <el-icon><Document /></el-icon>
                </div>
                <div class="card-info">
                  <div class="card-name">{{ project.name }}</div>
                  <div class="card-subtitle">{{ project.details }}</div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <div v-else class="no-recommendations">
        <el-empty description="暂无推荐内容" />
      </div>
    </section>

    <!-- 快捷应用入口 -->
    <section class="apps-section">
      <div class="section-header">
        <h2 class="section-title">
          <el-icon><Grid /></el-icon>
          Projects
        </h2>
        <el-button
          type="text"
          size="small"
          @click="handleEditProjects"
          :class="{ 'edit-active': isEditing }"
        >
          {{ isEditing ? 'Finish' : 'Edit' }}
        </el-button>
      </div>

      <div class="apps-grid">
        <div v-for="app in systemApps" :key="app.id" class="app-item">
          <el-card
            class="app-card"
            shadow="hover"
            @click="isEditing ? null : openApp(app)"
            :class="{ editing: isEditing }"
          >
            <div v-if="isEditing" class="project-delete-button" @click.stop="deleteProject(app)">
              <el-icon><Close /></el-icon>
            </div>
            <div class="app-content">
              <div class="app-icon" :style="{ backgroundColor: app.color }">
                <el-icon :size="32">
                  <component :is="app.icon" />
                </el-icon>
              </div>
              <div class="app-info">
                <div class="app-name">{{ app.name }}</div>
                <div class="app-description">{{ app.description }}</div>
              </div>
            </div>
          </el-card>
        </div>

        <!-- 添加新项目的卡片 -->
        <div v-if="isEditing" class="app-item">
          <el-card class="app-card add-card" shadow="hover" @click="showAddProjectDialog">
            <div class="add-icon">
              <el-icon :size="32"><Plus /></el-icon>
            </div>
          </el-card>
        </div>
      </div>

      <!-- 添加/编辑项目的对话框 -->
      <el-dialog
        v-model="dialogVisible"
        :title="editingProject.id ? 'Edit Project' : 'Add Project'"
        width="500px"
      >
        <el-form :model="editingProject" label-width="100px">
          <el-form-item label="Name">
            <el-input v-model="editingProject.name" />
          </el-form-item>
          <el-form-item label="Description">
            <el-input v-model="editingProject.description" type="textarea" />
          </el-form-item>
          <el-form-item label="Icon">
            <el-select v-model="editingProject.icon">
              <el-option label="User" value="User" />
              <el-option label="Setting" value="Setting" />
              <el-option label="Document" value="Document" />
              <el-option label="Folder" value="Folder" />
              <el-option label="DataAnalysis" value="DataAnalysis" />
              <el-option label="Monitor" value="Monitor" />
            </el-select>
          </el-form-item>
          <el-form-item label="Color">
            <el-color-picker v-model="editingProject.color" />
          </el-form-item>
          <el-form-item label="Route">
            <el-input v-model="editingProject.route" />
          </el-form-item>
        </el-form>
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible = false">Cancel</el-button>
            <el-button type="primary" @click="saveProject">Save</el-button>
          </span>
        </template>
      </el-dialog>
    </section>

    <!-- 常用工具 -->
    <section class="tools-section">
      <div class="section-header">
        <h2 class="section-title">
          <el-icon><Tools /></el-icon>
          Tools
        </h2>
        <el-button
          type="text"
          size="small"
          @click="handleEditTools"
          :class="{ 'edit-active': isEditingTools }"
        >
          {{ isEditingTools ? 'Finish' : 'Edit' }}
        </el-button>
      </div>

      <div class="tools-grid">
        <div v-for="tool in commonTools" :key="tool.id" class="tool-item">
          <el-card
            class="tool-card"
            shadow="hover"
            @click="isEditingTools ? null : openTool(tool)"
            :class="{ editing: isEditingTools }"
          >
            <div class="tool-content">
              <div class="tool-icon" :style="{ backgroundColor: tool.color }">
                <span class="tool-emoji">{{ tool.icon }}</span>
              </div>
              <div class="tool-info">
                <div class="tool-name">{{ tool.name }}</div>
                <div class="tool-description">{{ tool.description }}</div>
              </div>
            </div>
          </el-card>
        </div>

        <!-- 添加新工具的卡片 -->
        <div v-if="isEditingTools" class="tool-item">
          <el-card class="tool-card add-card" shadow="hover" @click="showAddToolDialog">
            <div class="add-icon">
              <el-icon :size="32"><Plus /></el-icon>
            </div>
          </el-card>
        </div>
      </div>

      <!-- 添加/编辑工具的对话框 -->
      <el-dialog
        v-model="toolDialogVisible"
        :title="editingTool.id ? 'Edit Tool' : 'Add Tool'"
        width="500px"
      >
        <el-form :model="editingTool" label-width="100px">
          <el-form-item label="Name">
            <el-input v-model="editingTool.name" />
          </el-form-item>
          <el-form-item label="Description">
            <el-input v-model="editingTool.description" type="textarea" />
          </el-form-item>
          <el-form-item label="Icon">
            <el-input v-model="editingTool.icon" placeholder="输入表情符号" />
          </el-form-item>
          <el-form-item label="Color">
            <el-color-picker v-model="editingTool.color" />
          </el-form-item>
          <el-form-item label="URL">
            <el-input v-model="editingTool.url" />
          </el-form-item>
        </el-form>
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="toolDialogVisible = false">Cancel</el-button>
            <el-button type="primary" @click="saveTool">Save</el-button>
          </span>
        </template>
      </el-dialog>
    </section>

    <!-- 时间轴 -->
    <section class="timeline-section">
      <h2 class="section-title">
        <el-icon><Clock /></el-icon>
        Timeline
      </h2>
      <!-- 按钮剧中显示-->
      <div class="timeline-button">
        <el-button type="primary" @click="openTimeline"> Open Timeline </el-button>
      </div>
      <el-row :gutter="16"> </el-row>
    </section>

  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue'
import { useRouter } from 'vue-router'
import defaultProjectsData from '../assets/data/projects.json'
import defaultToolsData from '../assets/data/tools.json'
import recommendationsData from '../assets/data/recommendations.json'
import { Edit, Close, Plus } from '@element-plus/icons-vue'
import { ElMessage } from 'element-plus'
import ThemeSwitcher from '@/components/ThemeSwitcher.vue'

// 创建响应式数据存储
const projectsData = reactive({
  projects: [...defaultProjectsData.projects],
})
const toolsData = reactive({
  tools: [...defaultToolsData.tools],
})

export default {
  name: 'DesktopMain',
  components: {
    ThemeSwitcher,
  },
  setup() {
    const router = useRouter()
    const systemApps = ref([])
    const isEditing = ref(false)
    const isEditingTools = ref(false)
    const dialogVisible = ref(false)
    const toolDialogVisible = ref(false)
    const recommendations = ref([])
    const editingProject = ref({
      name: '',
      description: '',
      icon: 'Document',
      color: '#409EFF',
      route: '',
    })
    const editingTool = ref({
      name: '',
      description: '',
      icon: '🔧',
      color: '#409EFF',
      url: '',
    })

    // 加载项目数据
    onMounted(async () => {
      systemApps.value = [...projectsData.projects]
      commonTools.value = [...toolsData.tools]
      recommendations.value = [...recommendationsData.recommendations]
    })

    // 保存到JSON文件
    const saveToFile = async (data, type, retryCount = 3) => {
      try {
        const endpoint = type === 'tools' ? '/api/tools' : '/api/projects'
        const response = await fetch(endpoint, {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(data),
        })

        const result = await response.json()

        if (!response.ok) {
          throw new Error(result.error || 'Failed to save data')
        }

        if (!result.success) {
          throw new Error('Failed to complete save operation')
        }

        // 更新本地数据
        if (type === 'projects') {
          projectsData.projects = [...data.projects]
        } else if (type === 'tools') {
          toolsData.tools = [...data.tools]
        }

        ElMessage.success(`${type === 'tools' ? 'Tools' : 'Projects'} 已保存`)
        return true
      } catch (error) {
        console.error(`Error saving ${type}:`, error)

        if (retryCount > 0) {
          ElMessage.warning(`Retrying save ${type}... (Remaining retries: ${retryCount})`)
          await new Promise((resolve) => setTimeout(resolve, 1000))
          return saveToFile(data, type, retryCount - 1)
        }

        ElMessage.error(`Save failed: ${error.message}`)
        return false
      }
    }

    // 处理编辑项目状态
    const handleEditProjects = async () => {
      if (isEditing.value) {
        // 保存到 JSON 文件
        const projectsJson = {
          projects: systemApps.value.map((app, index) => ({
            ...app,
            id: index + 1, // 重新生成ID
          })),
        }
        const saveSuccess = await saveToFile(projectsJson, 'projects')
        if (!saveSuccess) {
          ElMessage.error('Failed to save data, please keep editing mode and try again')
          return
        }
      }
      isEditing.value = !isEditing.value
    }

    // 删除项目
    const deleteProject = async (project) => {
      const index = systemApps.value.findIndex((p) => p.id === project.id)
      if (index !== -1) {
        systemApps.value.splice(index, 1)
        // 立即保存到 JSON 文件
        const projectsJson = {
          projects: systemApps.value.map((app, idx) => ({
            ...app,
            id: idx + 1, // 重新生成ID
          })),
        }
        await saveToFile(projectsJson, 'projects')
      }
    }

    // 显示添加项目对话框
    const showAddProjectDialog = () => {
      editingProject.value = {
        id: systemApps.value.length + 1,
        name: '',
        description: '',
        icon: 'Document',
        color: '#409EFF',
        route: '',
      }
      dialogVisible.value = true
    }

    // 保存项目
    const saveProject = () => {
      if (!editingProject.value.name) {
        ElMessage.warning('Please enter project name')
        return
      }

      const index = systemApps.value.findIndex((p) => p.id === editingProject.value.id)
      if (index === -1) {
        systemApps.value.push({ ...editingProject.value })
      } else {
        systemApps.value[index] = { ...editingProject.value }
      }

      dialogVisible.value = false
    }

    // 常用工具
    const commonTools = ref([
      {
        id: 1,
        name: 'Online Code Editor',
        description: '代码在线编辑',
        icon: '📝',
        color: '#409EFF',
        url: 'https://codepen.io',
      },
      {
        id: 2,
        name: '图片压缩',
        description: '在线图片压缩',
        icon: '🖼️',
        color: '#67C23A',
        url: 'https://tinypng.com',
      },
      {
        id: 3,
        name: 'JSON格式化',
        description: 'JSON数据格式化',
        icon: '{}',
        color: '#E6A23C',
        url: 'https://jsonformatter.org',
      },
      {
        id: 4,
        name: '颜色选择器',
        description: '在线颜色工具',
        icon: '🎨',
        color: '#F56C6C',
        url: 'https://colorhunt.co',
      },
      {
        id: 5,
        name: 'API测试',
        description: 'Postman在线版',
        icon: '🔧',
        color: '#909399',
        url: 'https://www.postman.com',
      },
      {
        id: 6,
        name: '正则测试',
        description: '正则表达式测试',
        icon: '🔍',
        color: '#606266',
        url: 'https://regex101.com',
      },
    ])

    // 打开应用
    const openApp = (app) => {
      if (app.route) {
        router.push(app.route)
      }
    }

    // 打开个人资料
    const openProfile = () => {
      router.push('/profile')
    }

    // 打开设置
    const openSettings = () => {
      router.push('/settings')
    }

    // 打开工具
    const openTool = (tool) => {
      if (tool.url) {
        window.open(tool.url, '_blank')
      }
    }

    // 打开时间轴
    const openTimeline = () => {
      router.push('/timeline')
    }

    // 打开推荐应用
    const openRecommendedApp = (app) => {
      // 根据应用类型执行相应操作
      console.log('打开推荐应用:', app)
      ElMessage.info(`正在打开 ${app.name}`)
    }

    // 打开推荐工具
    const openRecommendedTool = (tool) => {
      // 在新窗口中打开工具URL
      if (tool.URL) {
        window.open(`https://${tool.URL}`, '_blank')
      }
    }

    // 打开推荐项目
    const openRecommendedProject = (project) => {
      // 导航到项目详情页面
      if (project.details) {
        router.push(project.details)
      }
    }

    // 处理编辑工具状态
    const handleEditTools = async () => {
      if (isEditingTools.value) {
        // 保存到 JSON 文件
        const toolsJson = {
          tools: commonTools.value.map((tool, index) => ({
            ...tool,
            id: index + 1, // 重新生成ID
          })),
        }
        await saveToFile(toolsJson, 'tools')
      }
      isEditingTools.value = !isEditingTools.value
    }

    // 删除工具
    const deleteTool = async (tool) => {
      const index = commonTools.value.findIndex((t) => t.id === tool.id)
      if (index !== -1) {
        commonTools.value.splice(index, 1)
        // 立即保存到 JSON 文件
        const toolsJson = {
          tools: commonTools.value.map((t, idx) => ({
            ...t,
            id: idx + 1, // 重新生成ID
          })),
        }
        await saveToFile(toolsJson, 'tools')
      }
    }

    // 显示添加工具对话框
    const showAddToolDialog = () => {
      editingTool.value = {
        id: commonTools.value.length + 1,
        name: '',
        description: '',
        icon: '🔧',
        color: '#409EFF',
        url: '',
      }
      toolDialogVisible.value = true
    }

    // 保存工具
    const saveTool = () => {
      if (!editingTool.value.name) {
        ElMessage.warning('Please enter tool name')
        return
      }

      if (!editingTool.value.url) {
        ElMessage.warning('Please enter tool URL')
        return
      }

      const index = commonTools.value.findIndex((t) => t.id === editingTool.value.id)
      if (index === -1) {
        commonTools.value.push({ ...editingTool.value })
      } else {
        commonTools.value[index] = { ...editingTool.value }
      }

      toolDialogVisible.value = false
    }

    return {
      systemApps,
      commonTools,
      recommendations,
      openApp,
      openTool,
      openProfile,
      openSettings,
      openTimeline,
      openRecommendedApp,
      openRecommendedTool,
      openRecommendedProject,
      handleEditProjects,
      handleEditTools,
      isEditing,
      isEditingTools,
      dialogVisible,
      toolDialogVisible,
      editingProject,
      editingTool,
      showAddProjectDialog,
      showAddToolDialog,
      saveProject,
      saveTool,
      deleteProject,
      deleteTool,
      Edit,
      Close,
      Plus,
    }
  },
}
</script>

<style scoped>
.desktop {
  min-height: 100vh;
  height: 100vh;
  width: 100vw;
  background: linear-gradient(135deg, var(--background-left-color), var(--background-right-color));
  padding: 0;
  overflow-y: auto;
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

.apps-section,
.tools-section,
.status-section,
.recent-section,
.timeline-section {
  margin: 80px 24px 24px;
  padding: 20px;
  background: var(--glass-bg);
  backdrop-filter: var(--glass-backdrop);
  border-radius: var(--radius-large);
  box-shadow: var(--glass-shadow);
  border: 1px solid var(--glass-border);
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.section-title {
  font-size: 18px;
  font-weight: bold;
  color: var(--text-color);
  margin: 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

/* 项目网格布局 */
.apps-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 16px;
}

.app-item {
  min-width: 280px;
}

/* 工具网格布局 */
.tools-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 16px;
}

.tool-item {
  min-width: 200px;
}

.app-content,
.tool-content {
  display: flex;
  align-items: center;
  gap: 16px;
  height: 100%;
  padding: 12px 16px;
  box-sizing: border-box;
}

.app-icon,
.tool-icon {
  width: 48px;
  height: 48px;
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  color: white;
}

.app-info,
.tool-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  min-height: 48px;
  overflow: hidden;
}

.app-name,
.tool-name {
  font-size: 15px;
  font-weight: 500;
  color: var(--text-color);
  margin-bottom: 2px;
  line-height: 1.4;
}

.app-description,
.tool-description {
  font-size: 12px;
  color: var(--secondary-color);
  line-height: 1.4;
  margin: 0;
}

.edit-active {
  color: var(--primary-color) !important;
}

.app-card .project-delete-button {
  position: absolute;
  top: 8px;
  right: 8px;
  width: 24px;
  height: 24px;
  border-radius: var(--radius-large);
  background: rgba(255, 255, 255, 0.5);
  backdrop-filter: blur(8px);
  border: 1px solid rgba(255, 255, 255, 0.3);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 2;
}

.app-card .project-delete-button:hover {
  background-color: #f56c6c;
  color: white;
}

.add-card {
  border: 2px dashed var(--glass-border);
  display: flex;
  align-items: center;
  justify-content: center;
  background: rgba(255, 255, 255, 0.3);
  backdrop-filter: var(--glass-backdrop);
  border-radius: var(--radius-large);
}

.add-card:hover {
  border-color: var(--primary-color);
  color: var(--primary-color);
}

.add-icon {
  font-size: 32px;
  color: inherit;
}

.tool-content {
  display: flex;
  align-items: center;
  gap: 12px;
}

.tool-icon {
  width: 48px;
  height: 48px;
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.tool-emoji {
  font-size: 24px;
}

.tool-info {
  flex: 1;
  text-align: left;
}

.tool-name {
  font-size: 14px;
  font-weight: 500;
  color: var(--text-color);
  margin-bottom: 4px;
}

.tool-description {
  font-size: 12px;
  color: var(--secondary-color);
}

.status-card {
  height: 120px;
}

.status-item {
  display: flex;
  align-items: center;
  gap: 16px;
  height: 100%;
}

.status-icon {
  flex-shrink: 0;
}

.status-info {
  flex: 1;
}

.status-title {
  font-size: 14px;
  color: var(--secondary-color);
  margin-bottom: 4px;
}

.status-value {
  font-size: 24px;
  font-weight: bold;
  color: var(--text-color);
  margin-bottom: 8px;
}

.status-time {
  font-size: 12px;
  color: var(--secondary-color);
}

.recent-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 16px;
}

.recent-card {
  max-height: 400px;
  overflow-y: auto;
}

.activity-content strong {
  color: var(--text-color);
}

.activity-content p {
  margin: 4px 0 0;
  color: var(--secondary-color);
  font-size: 14px;
}

:deep(.el-timeline-item__timestamp) {
  font-size: 12px;
  color: var(--secondary-color);
}

/* 推荐内容样式 */
.recommendations-container {
  margin-top: 16px;
}

.recommendation-group {
  margin-bottom: 24px;
}

.recommendation-group:last-child {
  margin-bottom: 0;
}

.recommendation-section {
  margin-bottom: 20px;
}

.recommendation-section:last-child {
  margin-bottom: 0;
}

.recommendation-title {
  font-size: 16px;
  font-weight: 600;
  color: var(--text-color);
  margin: 0 0 12px 0;
  display: flex;
  align-items: center;
  gap: 8px;
}

.recommendation-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 16px;
}

.app-card,
.tool-card,
.project-card,
.recommendation-card {
  background: var(--glass-bg);
  backdrop-filter: var(--glass-backdrop);
  border: 1px solid var(--glass-border);
  border-radius: var(--radius-large);
  cursor: pointer;
  transition: all 0.3s ease;
  height: 100px;
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 12px 16px;
  box-sizing: border-box;
}

.app-card:hover,
.tool-card:hover,
.project-card:hover,
.recommendation-card:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.15);
  background: color-mix(in srgb, var(--glass-bg) 70%, var(--hover-color) 30%);
  border-color: var(--border-color);
}

.card-icon {
  width: 48px;
  height: 48px;
  border-radius: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  color: white;
}

.recommendation-card .app-icon {
  background: linear-gradient(135deg, #409eff, #67c23a);
}

.recommendation-card .tool-icon {
  background: linear-gradient(135deg, #e6a23c, #f56c6c);
}

.recommendation-card .project-icon {
  background: linear-gradient(135deg, #909399, #606266);
}

.card-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: center;
  min-height: 48px;
  overflow: hidden;
}

.card-name {
  font-size: 15px;
  font-weight: 500;
  color: var(--text-color);
  margin-bottom: 2px;
  line-height: 1.4;
}

.card-subtitle {
  font-size: 12px;
  color: var(--secondary-color);
  line-height: 1.4;
  margin: 0;
}

.no-recommendations {
  text-align: center;
  padding: 40px 20px;
}

.dialog-footer {
  display: flex;
  justify-content: flex-end;
  gap: 8px;
}
</style>
