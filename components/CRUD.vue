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
      <div v-if="ruleToShow" class="m-2 py-1 d-block text-center">
        <div class="m-3 flex text-center">
          <div class="mt-2 mx-2">Name</div>
          <b-form-input
            v-model="ruleToShow.name"
            placeholder="Name"
          ></b-form-input>
        </div>
        <div class="m-3 flex text-center">
          <div class="mt-2 mx-2">Active</div>
          <b-form-input
            v-model="ruleToShow.active"
            placeholder="Name"
          ></b-form-input>
        </div>
      </div>
      <b-toast
        id="toast"
        title="BootstrapVue"
        static
        no-auto-hide
        class="b-toaster-bottom-left"
      >
      </b-toast>
      <div class="mt-3 flex justify-end">
        <b-dropdown class="mx-3" right text="Save💾" variant="outline-primary">
          <b-dropdown-item
            v-if="!isLoadingUpdate"
            @click="onUpdateSubmit"
            variant="primary"
          >
            Confirm save
          </b-dropdown-item>
          <b-dropdown-item v-else> Loading... </b-dropdown-item>
        </b-dropdown>
        <b-dropdown class="mx-3" right text="Delete🗑️" variant="outline-danger">
          <b-dropdown-item variant="danger"> Confirm delete </b-dropdown-item>
        </b-dropdown>
        <b-button class="mx- w-2/12" block @click="$bvModal.hide('show-modal')"
          >Close</b-button
        >
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
      isLoadingUpdate: false,
    }
  },
  methods: {
    makeToast(variant = null, title, text) {
      this.$bvToast.toast(text, {
        title: title,
        variant: variant,
        solid: true,
        toaster: 'b-toaster-bottom-center',
      })
    },
    list() {
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
      this.ruleToShow = JSON.parse(
        JSON.stringify(this.entities.filter((rule) => rule.id === itemId)[0])
      )
    },
    onUpdateSubmit() {
      this.isLoadingUpdate = true
      const UPDATE_URL = `https://sys-dev.searchandstay.com/api/admin/house_rules/${this.ruleToShow.id}`
      const UPDATE_BODY = {
        house_rules: {
          name: this.ruleToShow.name,
          active: this.ruleToShow.active,
        },
      }
      console.log('UPDATE_BODY =', UPDATE_BODY)
      async function putData(url = '', data = {}, access_token) {
        const response = await fetch(url, {
          method: 'PUT',
          mode: 'cors',
          cache: 'no-cache',
          credentials: 'same-origin',
          headers: {
            'Content-Type': 'application/json',
            Authorization: 'Bearer ' + access_token,
          },
          redirect: 'follow',
          referrerPolicy: 'no-referrer',
          body: JSON.stringify(data),
        })
        return response.json()
      }

      putData(UPDATE_URL, UPDATE_BODY, this.userLoginData.access_token).then(
        (data) => {
          console.log(data)
          if (data.success) {
            this.makeToast('success', 'Success', data.message)
            this.list()
          } else {
            this.makeToast('danger', 'Error', JSON.stringify(data.message))
          }
          this.isLoadingUpdate = false
        }
      )
    },
  },
}
</script>
