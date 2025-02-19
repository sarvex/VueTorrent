<script lang="ts" setup>
import AddTorrentDialog from '@/components/Dialogs/AddTorrentDialog.vue'
import ConfirmDeleteDialog from '@/components/Dialogs/ConfirmDeleteDialog.vue'
import { useDashboardStore } from '@/stores/dashboard'
import { useDialogStore } from '@/stores/dialog'
import { useMaindataStore } from '@/stores/maindata'
import { computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import TopActions from './TopActions.vue'
import TopOverflow from './TopOverflow.vue'

const route = useRoute()
const router = useRouter()
const dashboardStore = useDashboardStore()
const dialogStore = useDialogStore()
const maindataStore = useMaindataStore()

const isOnTorrentDetail = computed(() => route.name === 'torrentDetail')
const hashes = computed(() => (isOnTorrentDetail.value ? [route.params.hash as string] : dashboardStore.selectedTorrents))

function openAddTorrentDialog() {
  dialogStore.createDialog(AddTorrentDialog, { openSuddenly: true })
}

async function resumeTorrents() {
  await maindataStore.resumeTorrents(hashes.value)
}

async function pauseTorrents() {
  await maindataStore.pauseTorrents(hashes.value)
}

function deleteTorrents() {
  if (!hashes.value.length) return

  dialogStore.createDialog(ConfirmDeleteDialog, { hashes: [...hashes.value] })
}

function openSearchEngine() {
  router.push({ name: 'searchEngine' })
}

function openrssArticles() {
  router.push({ name: 'rssArticles' })
}

function openLogs() {
  router.push({ name: 'logs' })
}

function openSettings() {
  router.push({ name: 'settings' })
}
</script>

<template>
  <v-tooltip :text="$t('topbar.addTorrents')" location="bottom">
    <template v-slot:activator="{ props }">
      <v-btn icon="mdi-plus" v-bind="props" @click="openAddTorrentDialog" />
    </template>
  </v-tooltip>

  <v-divider inset vertical />

  <TopOverflow
    v-if="$vuetify.display.mobile"
    @deleteTorrents="deleteTorrents"
    @openLogs="openLogs"
    @openSearchEngine="openSearchEngine"
    @openSettings="openSettings"
    @openrssArticles="openrssArticles"
    @pauseTorrents="pauseTorrents"
    @resumeTorrents="resumeTorrents" />
  <TopActions
    v-else
    @deleteTorrents="deleteTorrents"
    @openLogs="openLogs"
    @openSearchEngine="openSearchEngine"
    @openSettings="openSettings"
    @openrssArticles="openrssArticles"
    @pauseTorrents="pauseTorrents"
    @resumeTorrents="resumeTorrents" />
</template>

<style scoped></style>
