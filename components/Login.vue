<template>
  <div>
    <div class="flex">
      <b-button class="mb-2" variant="light" v-b-modal="'my-modal'" size="sm"
        >searchandstay development task</b-button
      >
      <b-modal id="my-modal" hide-footer>
        <template #modal-title> Task requirements </template>
        <div class="d-block text-center">
          <img
            class="ma-1 d-flex elevation-0 white lighten-3"
            :src="require('../static/task-requirements.png')"
            :style="'width: 500px;'"
          />
        </div>
        <b-button class="mt-3" block @click="$bvModal.hide('my-modal')"
          >Close</b-button
        >
      </b-modal>
    </div>
    <div>
      <div>
        <b-form @submit="onSubmit">
          <b-form-group
            id="input-group-1"
            label="Email address:"
            label-for="input-1"
          >
            <b-form-input
              id="input-1"
              v-model="form.email"
              type="email"
              placeholder="Enter email"
              required
            ></b-form-input>
          </b-form-group>

          <b-form-group
            id="input-group-2"
            label="Password:"
            label-for="input-2"
          >
            <b-form-input
              id="input-2"
              v-model="form.password"
              placeholder="Enter password"
              required
              type="password"
              aria-describedby="password-help-block"
            ></b-form-input>
          </b-form-group>

          <b-spinner v-if="isLoading" label="Spinning"></b-spinner>
          <b-button v-else type="submit" :disabled="isLoggedIn">Login</b-button>
          {{ loginMessageText }}
        </b-form>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Login',
  data() {
    return {
      form: {
        email: '',
        password: '',
      },
      isLoading: false,
      isLoggedIn: false,
      loginMessageText: '',
    }
  },
  methods: {
    onSubmit(event) {
      event.preventDefault()
      this.isLoading = true
      const LOGIN_URL = 'https://sys-dev.searchandstay.com/api/admin/login_json'
      const LOGIN_BODY = {
        login: {
          email: this.form.email,
          password: this.form.password,
        },
      }
      async function postData(url = '', data = {}) {
        const response = await fetch(url, {
          method: 'POST',
          mode: 'cors',
          cache: 'no-cache',
          credentials: 'same-origin',
          headers: {
            'Content-Type': 'application/json',
          },
          redirect: 'follow',
          referrerPolicy: 'no-referrer',
          body: JSON.stringify(data),
        })
        return response.json()
      }

      postData(LOGIN_URL, LOGIN_BODY).then((data) => {
        if (data.success) {
          this.isLoggedIn = true
          this.loginMessageText = 'Logged in ✅ (' + data.message + ')'
          this.$emit('userLoggedIn', {
            userLoginData: data.data.result,
            loginMessageText: this.loginMessageText,
          })
        } else {
          this.loginMessageText = 'Log in error❌  (' + data.data + ')'
        }
        this.isLoading = false
      })
    },
  },
}
</script>
