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
        v-b-modal="'show-modal'"
        size="sm"
        @click="list"
        >LIST RULES</b-button
      >
    </div>
    <div v-if="entities.length > 0" class="flex flex-column">
      <div class="mb-2 flex shadow-md">
        <div class="mb-2 flex w-8/12 justify-start px-2"><b>Name</b></div>
        <div class="mb-2 flex w-1/12 justify-center px-2"><b>Active</b></div>
        <div class="mb-2 flex w-3/12 justify-center px-2"><b>Details</b></div>
      </div>
      <div v-for="item in entities" :key="item.id" class="mb-2 flex shadow-md">
        <div class="mb-2 flex w-8/12 justify-start px-2">
          {{ item.name }}
        </div>
        <div class="mb-2 flex w-1/12 justify-center px-2">
          {{ item.active === 1 ? '✅' : '❌' }}
        </div>
        <div class="mb-2 flex w-3/12 justify-center px-2">
          <b-button
            v-b-modal="'show-modal'"
            class="mx-2"
            variant="light"
            size="sm"
            @click="show(item.id)"
            >🔍</b-button
          >
        </div>
      </div>
    </div>
    <b-modal id="show-modal" hide-footer>
      <template #modal-title> Rule details</template>
      <div class="d-block text-center">
        {{ ruleToShow }}
      </div>
      <div class="mt-2 flex">
        <b-button class="mt-1 mx-1" block @click="$bvModal.hide('show-modal')"
          >Close</b-button
        >

        <b-dropdown right text="Save💾" variant="outline-primary">
          <b-dropdown-item>Confirm save</b-dropdown-item>
        </b-dropdown>
        <b-dropdown right text="Delete🗑️" variant="outline-danger">
          <b-dropdown-item>Confirm delete</b-dropdown-item>
        </b-dropdown>
      </div>
    </b-modal>
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
      ruleToShow: null,
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
    show(itemId) {
      console.log('show(' + itemId + ')')
      this.ruleToShow = this.entities.filter((rule) => rule.id === itemId)[0]
    },
  },
}
</script>
