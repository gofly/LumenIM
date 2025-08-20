<script lang="ts" setup>
import EditPassword from '@/components/user/EditorPassword.vue'
import EditEmail from '@/components/user/EditorEmail.vue'
import { useUserStore } from '@/store'

const userStore = useUserStore()
const isShowChangePassword = ref(false)
const isShowChangeEmail = ref(false)


const onChangeEmailSuccess = (value: string) => {
  isShowChangeEmail.value = false
  userStore.email = value
}
</script>

<template>
  <section>
    <h3 class="title">安全设置</h3>

    <div class="view-box">
      <div class="view-list">
        <div class="content">
          <div class="name">账户密码</div>
          <div class="desc">请定期修改密码</div>
        </div>
        <div class="tools">
          <n-button type="primary" text @click="isShowChangePassword = true"> 修改 </n-button>
        </div>
      </div>

      <div class="view-list">
        <div class="content">
          <div class="name">绑定邮箱</div>
          <div class="desc">已绑定邮箱 ：{{ userStore.email || '未设置' }}</div>
        </div>
        <div class="tools">
          <n-button type="primary" text @click="isShowChangeEmail = true"> 修改 </n-button>
        </div>
      </div>
    </div>
  </section>

  <EditPassword v-model="isShowChangePassword" />
  <EditEmail v-model="isShowChangeEmail" @success="onChangeEmailSuccess" />
</template>
<style lang="less" scoped>
@import '@/assets/css/settting.less';
</style>
