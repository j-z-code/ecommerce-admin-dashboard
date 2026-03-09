<script setup>
import { reactive, ref, computed } from 'vue'

const loginForm = reactive({
  username: '',
  password: ''
})
const isLoggingIn = ref(false)
const isLoggedIn = ref(false)
const currentUser = ref(null)
const activeMenu = ref('dashboard')

const welcomeText = computed(() =>
  currentUser.value ? `欢迎你，${currentUser.value.username}` : '欢迎登录电商后台'
)

const products = ref([
  { id: 1, name: '无线蓝牙耳机', category: '数码', price: 199, stock: 120 },
  { id: 2, name: '机械键盘', category: '数码', price: 399, stock: 80 },
  { id: 3, name: '保温杯', category: '日用', price: 89, stock: 200 }
])

const orders = ref([
  { id: 'O20240301001', user: '张三', total: 598, status: '已支付' },
  { id: 'O20240302002', user: '李四', total: 199, status: '已发货' }
])

const users = ref([
  { id: 1, username: 'zhangsan', role: '普通用户' },
  { id: 2, username: 'lisi', role: '普通用户' },
  { id: 3, username: 'admin', role: '管理员' }
])

function handleLogin() {
  if (!loginForm.username || !loginForm.password) {
    window.alert('请输入用户名和密码（演示账号：admin / 123456）')
    return
  }
  isLoggingIn.value = true
  setTimeout(() => {
    if (loginForm.username === 'admin' && loginForm.password === '123456') {
      isLoggedIn.value = true
      currentUser.value = { username: 'admin' }
    } else {
      window.alert('用户名或密码错误，演示账号：admin / 123456')
    }
    isLoggingIn.value = false
  }, 600)
}

function handleLogout() {
  isLoggedIn.value = false
  currentUser.value = null
  activeMenu.value = 'dashboard'
  loginForm.username = ''
  loginForm.password = ''
}
</script>

<template>
  <div class="app-root">
    <!-- 登录页 -->
    <div v-if="!isLoggedIn" class="login-page">
      <div class="login-card">
        <h1 class="title">电商后台管理系统</h1>
        <p class="subtitle">演示账号：admin / 123456</p>
        <el-form
          :model="loginForm"
          label-width="0"
          class="login-form"
          @submit.prevent="handleLogin"
        >
          <el-form-item>
            <el-input
              v-model="loginForm.username"
              placeholder="用户名：admin"
              size="large"
            />
          </el-form-item>
          <el-form-item>
            <el-input
              v-model="loginForm.password"
              type="password"
              placeholder="密码：123456"
              size="large"
              show-password
              @keyup.enter="handleLogin"
            />
          </el-form-item>
          <el-form-item>
            <el-button
              type="primary"
              size="large"
              :loading="isLoggingIn"
              class="login-btn"
              @click="handleLogin"
            >
              登 录
            </el-button>
          </el-form-item>
        </el-form>
      </div>
    </div>

    <!-- 后台主界面 -->
    <el-container v-else class="layout">
      <el-aside width="220px" class="aside">
        <div class="logo">电商后台</div>
        <el-menu
          :default-active="activeMenu"
          class="menu"
          @select="(key) => (activeMenu = key)"
        >
          <el-menu-item index="dashboard">数据概览</el-menu-item>
          <el-menu-item index="products">商品管理</el-menu-item>
          <el-menu-item index="orders">订单管理</el-menu-item>
          <el-menu-item index="users">用户管理</el-menu-item>
        </el-menu>
      </el-aside>
      <el-container>
        <el-header class="header">
          <div class="header-left">
            <h2 class="header-title">{{ welcomeText }}</h2>
          </div>
          <div class="header-right">
            <span class="username">{{ currentUser?.username || '管理员' }}</span>
            <el-button type="text" @click="handleLogout">退出登录</el-button>
          </div>
        </el-header>
        <el-main class="main">
          <!-- 仪表盘 -->
          <div v-if="activeMenu === 'dashboard'" class="dashboard">
            <el-row :gutter="16">
              <el-col :span="8">
                <el-card shadow="hover" class="stat-card">
                  <div class="stat-value">{{ orders.length }}</div>
                  <div class="stat-label">订单数量</div>
                </el-card>
              </el-col>
              <el-col :span="8">
                <el-card shadow="hover" class="stat-card">
                  <div class="stat-value">{{ products.length }}</div>
                  <div class="stat-label">在售商品</div>
                </el-card>
              </el-col>
              <el-col :span="8">
                <el-card shadow="hover" class="stat-card">
                  <div class="stat-value">{{ users.length }}</div>
                  <div class="stat-label">注册用户</div>
                </el-card>
              </el-col>
            </el-row>
            <p class="tip">
              这里的数据全部是前端 Mock，面试时可以说明：真实项目只需要把数据改成接口返回即可。
            </p>
          </div>

          <!-- 商品管理 -->
          <div v-else-if="activeMenu === 'products'">
            <el-card>
              <template #header>
                <span>商品列表（Mock）</span>
              </template>
              <el-table :data="products" stripe style="width: 100%">
                <el-table-column prop="id" label="ID" width="80" />
                <el-table-column prop="name" label="商品名称" />
                <el-table-column prop="category" label="分类" width="100" />
                <el-table-column prop="price" label="价格" width="100">
                  <template #default="{ row }">¥{{ row.price }}</template>
                </el-table-column>
                <el-table-column prop="stock" label="库存" width="80" />
              </el-table>
            </el-card>
          </div>

          <!-- 订单管理 -->
          <div v-else-if="activeMenu === 'orders'">
            <el-card>
              <template #header>
                <span>订单列表（Mock）</span>
              </template>
              <el-table :data="orders" stripe style="width: 100%">
                <el-table-column prop="id" label="订单号" />
                <el-table-column prop="user" label="用户" width="120" />
                <el-table-column prop="total" label="金额" width="100">
                  <template #default="{ row }">¥{{ row.total }}</template>
                </el-table-column>
                <el-table-column prop="status" label="状态" width="100" />
              </el-table>
            </el-card>
          </div>

          <!-- 用户管理 -->
          <div v-else-if="activeMenu === 'users'">
            <el-card>
              <template #header>
                <span>用户列表（Mock）</span>
              </template>
              <el-table :data="users" stripe style="width: 100%">
                <el-table-column prop="id" label="ID" width="80" />
                <el-table-column prop="username" label="用户名" />
                <el-table-column prop="role" label="角色" width="120" />
              </el-table>
            </el-card>
          </div>
        </el-main>
      </el-container>
    </el-container>
  </div>
</template>

<style scoped>
.app-root {
  height: 100vh;
}
.login-page {
  height: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
  background: linear-gradient(135deg, #1d1e1f 0%, #2c3e50 100%);
}
.login-card {
  width: 360px;
  padding: 32px 28px 28px;
  background: #fff;
  border-radius: 12px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.25);
}
.title {
  margin: 0 0 8px;
  text-align: center;
  font-size: 22px;
}
.subtitle {
  margin: 0 0 24px;
  text-align: center;
  font-size: 13px;
  color: #909399;
}
.login-form :deep(.el-input__wrapper) {
  border-radius: 8px;
}
.login-btn {
  width: 100%;
  border-radius: 8px;
}
.layout {
  height: 100%;
}
.aside {
  background: #001529;
  color: #fff;
}
.logo {
  height: 56px;
  line-height: 56px;
  text-align: center;
  font-size: 18px;
  font-weight: 600;
}
.menu {
  border-right: none;
}
.header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
  box-shadow: 0 1px 4px rgba(0, 21, 41, 0.12);
  background: #fff;
}
.header-title {
  margin: 0;
  font-size: 16px;
}
.header-right {
  display: flex;
  align-items: center;
  gap: 12px;
}
.username {
  font-size: 14px;
  color: #606266;
}
.main {
  background: #f0f2f5;
  padding: 16px;
}
.stat-card {
  text-align: center;
}
.stat-value {
  font-size: 28px;
  font-weight: 600;
  margin-bottom: 4px;
}
.stat-label {
  font-size: 13px;
  color: #909399;
}
.tip {
  margin-top: 16px;
  font-size: 13px;
  color: #909399;
}
</style>
