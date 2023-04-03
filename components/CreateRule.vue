<template>
  <div>
    <b-spinner v-if="isLoadingAddRule" label="Spinning"></b-spinner>
    <b-button v-else class="m-2" variant="primary" v-b-modal="'add-modal'">
      ADD RULE
    </b-button>
    <b-modal id="add-modal" hide-footer>
      <template #modal-title> Rule details</template>
      <div class="m-2 py-1 d-block text-center">
        <div class="m-3 flex text-center">
          <div class="mt-2 mx-2">Name</div>
          <b-form-input
            v-model="newRule.name"
            placeholder="Name"
          ></b-form-input>
        </div>
        <div class="m-3 flex text-center">
          <div class="mt-2 mx-2">Active</div>
          <b-form-input
            v-model="newRule.active"
            placeholder="Active"
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
      <div class="mt-3 mb-2 flex justify-end">
        <b-dropdown class="mx-3" right text="Add➕" variant="outline-primary">
          <b-dropdown-item
            v-if="!isLoadingAddRule"
            @click="onAddRuleSubmit"
            variant="primary"
          >
            Confirm Add
          </b-dropdown-item>
          <b-dropdown-item v-else> Loading... </b-dropdown-item>
        </b-dropdown>

        <b-button class="mx-3 w-2/12" block @click="$bvModal.hide('add-modal')"
          >Close</b-button
        >
      </div>
    </b-modal>
  </div>
</template>

<script>
export default {
  name: 'CreateRule',
  props: ['userLoginData'],
  data() {
    return {
      isLoadingAddRule: false,
      newRule: { name: '', active: '' },
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
    onAddRuleSubmit(event) {
      event.preventDefault()
      this.isLoadingAddRule = true
      const CREATE_URL =
        'https://sys-dev.searchandstay.com/api/admin/house_rules'
      const CREATE_BODY = {
        house_rules: {
          name: this.newRule.name,
          active: this.newRule.active,
        },
      }
      async function postData(url = '', data = {}, access_token) {
        const response = await fetch(url, {
          method: 'POST',
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

      postData(CREATE_URL, CREATE_BODY, this.userLoginData.access_token).then(
        (data) => {
          if (data.success) {
            this.makeToast('success', 'Success', data.message)
            this.$emit('ruleCreated')
            this.$bvModal.hide('add-modal')
          } else {
            this.makeToast('danger', 'Error', JSON.stringify(data.message))
          }
          this.isLoadingAddRule = false
        }
      )
    },
  },
}
</script>
