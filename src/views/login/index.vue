<script lang="ts" setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import { NButton, NCard, NForm, NFormItem, NInput, useMessage } from 'naive-ui'
import { login } from '@/api'
import { useAuthStore } from '@/store'

const router = useRouter()
const authStore = useAuthStore()
const ms = useMessage()
const formValues = ref({
  username: '',
  password: '',
})

const description
  = '欢迎登录，请输入您的用户名和密码。如果没有账号，请联系管理员创建账号。'

const handleSubmit = async () => {
  // 处理登录逻辑
  const name = formValues.value.username.trim()
  const pwd = formValues.value.password.trim()
  if (!name || !pwd) {
    ms.warning('请输入您的用户名和密码。如果没有账号，请联系管理员创建账号。')
    return
  }
  try {
    const data: any = await login(name, pwd)
    authStore.setToken(data.data.access_token)
    ms.success('success')
    router.push('/')
  }
  catch (error: any) {
    ms.error(error.message ?? 'error')
    authStore.removeToken()
  }
}
</script>

<template>
  <div class="flex justify-center items-center h-full">
    <div class="w-1/2 min-w-fit">
      <NCard title="用户登录" size="large" :inline-desc="description">
        <NForm label-position="top">
          <NFormItem label="用户名" required>
            <NInput v-model:value="formValues.username" placeholder="请输入用户名 默认为手机号" />
          </NFormItem>
          <NFormItem label="密码" required>
            <NInput v-model:value="formValues.password" placeholder="请输入密码" type="password" />
          </NFormItem>
          <NFormItem>
            <div class="mx-auto">
              <NButton type="primary" @click="handleSubmit">
                登录
              </NButton>
            </div>
          </NFormItem>
        </NForm>
      </NCard>
    </div>
  </div>
</template>
