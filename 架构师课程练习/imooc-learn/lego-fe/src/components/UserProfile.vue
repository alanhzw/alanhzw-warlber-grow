
<template>
  <a-button type="primary"
            v-if="!user.isLogin"
            @click="login"
            class="user-profile-component">
    <!--  -->
    登录
  </a-button>
  <div v-else>
    <a-dropdown-button class="user-profile-component">
      <router-link to="/setting">{{ user.username }}</router-link>
      <template v-slot:overlay>
        <a-menu class="user-profile-dropdown">
          <a-menu-item key="0"
                       @click="logout">登出</a-menu-item>
        </a-menu>
      </template>
    </a-dropdown-button>
  </div>
</template>

<script lang="ts" setup>
import { defineProps } from 'vue'
import { useStore } from 'vuex'
import { useRouter } from 'vue-router'
import { message } from 'ant-design-vue'
import { UserProps } from '../store/user'
interface Props {
  user: UserProps;
}
const props = defineProps<Props>()
const store = useStore()
const router = useRouter()
const login = () => {
  store.commit("login")
  message.success("登录成功", 2)
}
const logout = () => {
  store.commit('logout')
  message.success('退出登录成功，2秒后跳转到首页', 2)
  setTimeout(() => {
    router.push('/')
  }, 2000)
}

</script>
<style>
.user-profile-dropdown {
  border-radius: 2px !important;
}

.user-operation>* {
  margin-left: 30px !important;
}
</style>