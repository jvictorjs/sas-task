<template>
  <div>
    <CRUD v-if="userLoginData" :userLoginData="userLoginData" />
    <Login v-else @userLoggedIn="handleLogin" />
  </div>
</template>

<script>
export default {
  name: 'IndexPage',

  data() {
    return {
      userLoginData: null,
    }
  },
  methods: {
    handleLogin(loginData) {
      this.userLoginData = loginData.userLoginData
    },
  },
}
</script>
