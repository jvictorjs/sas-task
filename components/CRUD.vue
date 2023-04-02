<template>
  <div>
    <h1>
      Welcome <b>{{ userLoginData.name }}</b>
    </h1>
    <div class="flex">
      <b-spinner v-if="isLoading" label="Spinning"></b-spinner>
      <b-button
        v-else
        class="mb-2"
        variant="light"
        v-b-modal="'my-modal'"
        size="sm"
        @click="list"
        >LIST</b-button
      >
    </div>
    <div class="flex flex-column">
      <div v-for="item in entities" :key="item.id">{{ item }}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CRUD',
  props: ['userLoginData'],
  data() {
    return {
      isLoading: false,
      entities: [],
    }
  },
  methods: {
    list(event) {
      event.preventDefault()
      const LIST_URL = 'https://sys-dev.searchandstay.com/api/admin/house_rules'
      this.isLoading = true
      async function get(url = '', access_token) {
        console.log(`access_token =`, access_token)
        const response = await fetch(url, {
          method: 'GET',
          mode: 'cors',
          cache: 'no-cache',
          credentials: 'same-origin',
          headers: {
            'Content-Type': 'application/json',
            Authorization: 'Bearer ' + access_token,
          },
          redirect: 'follow',
          referrerPolicy: 'no-referrer',
        })
        return response.json()
      }

      get(LIST_URL, this.userLoginData.access_token).then((data) => {
        if (data.success) {
          this.entities = data.data.entities
        }
        this.isLoading = false
      })
    },
  },
}
</script>
