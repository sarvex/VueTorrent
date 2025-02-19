<script setup lang="ts">
import PasswordField from '@/components/Core/PasswordField.vue'
import { useAuthStore } from '@/stores/auth'
import { onMounted, reactive, ref } from 'vue'
import { useRouter, useRoute } from 'vue-router'
import { toast } from 'vue3-toastify'
import { useI18n } from 'vue-i18n'

import { LoginPayload } from '@/types/qbit/payloads'

const { t } = useI18n()
const router = useRouter()
const route = useRoute()

const authStore = useAuthStore()

const loginForm = reactive<LoginPayload>({
  username: '',
  password: ''
})
const rulesOk = ref(false)

const rules = {
  username: [(v: string) => !!v || t('login.rules.username_required')],
  password: [(v: string) => !!v || t('login.rules.password_required')]
}

const login = async () => {
  if (!rulesOk.value) return
  await authStore.login(loginForm.username, loginForm.password)

  if (authStore.isAuthenticated) {
    toast.success(t('login.success'))
    redirectOnSuccess()
  } else {
    toast.error(t('login.error'))
  }
}

const redirectOnSuccess = () => {
  if (route.query.redirect) {
    router.push(route.query.redirect as string)
  } else {
    router.push({ name: 'dashboard' })
  }
}

onMounted(() => {
  if (authStore.isAuthenticated) {
    redirectOnSuccess()
  }
})
</script>

<template>
  <v-layout class="mt-16">
    <v-card class="mx-auto" rounded="lg" min-width="250">
      <v-card-title>{{ t('login.title') }}</v-card-title>
      <v-card-subtitle>{{ t('login.subtitle') }}</v-card-subtitle>
      <v-card-text>
        <v-form v-model="rulesOk" @submit.prevent="login">
          <v-text-field v-model="loginForm.username" :label="t('login.username')" autofocus :rules="rules.username" @keydown.enter.prevent="login" variant="outlined">
            <template v-slot:prepend>
              <v-icon color="accent" icon="mdi-account" />
            </template>
          </v-text-field>

          <PasswordField
            v-model="loginForm.password"
            :label="t('login.password')"
            :rules="rules.password"
            prepend-icon="mdi-lock"
            @keydown.enter.prevent="login"
            variant="outlined" />
        </v-form>
      </v-card-text>
      <v-card-actions>
        <v-btn variant="elevated" block color="accent" @click="login">
          {{ t('login.submit') }}
        </v-btn>
      </v-card-actions>
    </v-card>
  </v-layout>
</template>

<style scoped></style>
