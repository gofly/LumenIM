<script lang="ts" setup>
import { ServAuthLogin, ServAuthOauth } from '@/api/auth'
import { setToken } from '@/utils/auth.ts'
import { rsaEncrypt } from '@/utils/rsa'
import { playMusic } from '@/utils/talk'
import { useInject } from '@/hooks'
import { Github } from '@icon-park/vue-next'
import ws from '@/connect'
import { useUserStore } from '@/store'

const { message } = useInject()
const userStore = useUserStore()
const route = useRoute()
const router = useRouter()
const formRef = ref()
const rules = {
  username: {
    required: true,
    trigger: ['blur', 'input'],
    message: '账号不能为空'
  },
  password: {
    required: true,
    trigger: ['blur', 'input'],
    message: '密码不能为空'
  }
}

const loading = ref(false)

const model = reactive({
  username: '',
  password: ''
})

const onLogin = async () => {
  const { code, data } = await ServAuthLogin(
    {
      mobile: model.username,
      password: rsaEncrypt(model.password),
      platform: 'web'
    },
    {
      loading
    }
  )

  if (code !== 200 || !data) return

  setToken(data.access_token, data.expires_in)
  ws.connect()
  message.success('登录成功，即将进入系统')
  userStore.loadSetting()

  const redirect: any = route.params?.redirect || '/'
  router.push(redirect)
}

const onValidate = (e: Event) => {
  e.preventDefault()

  // 谷歌浏览器提示音需要用户主动交互才能播放，登录入口主动交互一次，后面消息提示音就能正常播放了
  playMusic(true)

  formRef.value.validate((errors: any) => !errors && onLogin())
}

</script>

<template>
  <section class="el-container is-vertical login-box login">
    <header class="el-header box-header">登录</header>

    <main class="el-main" style="padding: 3px">
      <n-form ref="formRef" size="large" :model="model" :rules="rules">
        <n-form-item path="username" :show-label="false">
          <n-input
            placeholder="请输入小红书号"
            v-model:value="model.username"
            :maxlength="11"
            @keydown.enter="onValidate"
          />
        </n-form-item>

        <n-form-item path="password" :show-label="false">
          <n-input
            placeholder="请输入密码(默认123456)"
            type="password"
            show-password-on="click"
            v-model:value="model.password"
            @keydown.enter="onValidate"
          />
        </n-form-item>
        <n-button
          type="primary"
          size="large"
          block
          text-color="#ffffff"
          class="mt-t20"
          @click="onValidate"
          :loading="loading"
        >
          立即登录
        </n-button>
      </n-form>
    </main>
  </section>
</template>

<style lang="less" scoped>
@import '@/assets/css/login.less';
</style>
