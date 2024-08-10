<template>
  <q-page class="flex flex-center">
    <q-form @submit="pingBackend">
    <q-input
      v-model="backendUrl"
      label="Backend URL"
      :rules="[val => !!val || 'URL is required']"
    />
    <q-btn
      type="submit"
      color="primary"
      label="Ping Backend"
      :loading="isPinging"
    />
    <q-banner v-if="pingResult" :class="pingResult.success ? 'bg-positive' : 'bg-negative'">
      {{ pingResult.message }}
    </q-banner>
  </q-form>
  </q-page>
</template>

<script setup>
defineOptions({
  name: 'IndexPage'
});

import { ref } from 'vue'
import { useQuasar } from 'quasar'

const $q = useQuasar()
const backendUrl = ref('')
const isPinging = ref(false)
const pingResult = ref(null)

const pingBackend = async () => {
  isPinging.value = true
  pingResult.value = null

  try {
    const response = await fetch(backendUrl.value + '/ping', {
      method: 'GET',
      mode: 'cors',
    })

    if (response.ok) {
      pingResult.value = { success: true, message: 'Backend pinged successfully!' }
    } else {
      pingResult.value = { success: false, message: 'Failed to ping backend. Status: ' + response.status }
    }
  } catch (error) {
    pingResult.value = { success: false, message: 'Error pinging backend: ' + error.message }
  } finally {
    isPinging.value = false
  }
}
</script>
