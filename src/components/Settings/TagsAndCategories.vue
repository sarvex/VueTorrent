<script setup lang="ts">
import CategoryFormDialog from '@/components/Dialogs/CategoryFormDialog.vue'
import TagFormDialog from '@/components/Dialogs/TagFormDialog.vue'
import { useDialogStore } from '@/stores/dialog'
import { useMaindataStore } from '@/stores/maindata'
import { Category } from '@/types/qbit/models'
import { onBeforeMount, ref, watch } from 'vue'

const dialogStore = useDialogStore()
const maindataStore = useMaindataStore()

const tagDialog = ref('')
const categoryDialog = ref('')

async function deleteTag(tagName: string) {
  await maindataStore.deleteTags([tagName])
  await maindataStore.fetchTags()
}

async function deleteCategory(category: Category) {
  await maindataStore.deleteCategories([category.name])
  await maindataStore.fetchCategories()
}

function openTagFormDialog(initialTag?: string) {
  tagDialog.value = dialogStore.createDialog(TagFormDialog, { initialTag })
}

function openCategoryFormDialog(initialCategory?: Category) {
  categoryDialog.value = dialogStore.createDialog(CategoryFormDialog, { initialCategory })
}

onBeforeMount(async () => {
  await maindataStore.fetchCategories()
  await maindataStore.fetchTags()
})

watch(
  () => dialogStore.isDialogOpened(tagDialog.value),
  value => {
    if (!value) {
      maindataStore.fetchTags()
    }
  }
)

watch(
  () => dialogStore.isDialogOpened(categoryDialog.value),
  value => {
    if (!value) {
      maindataStore.fetchCategories()
    }
  }
)
</script>

<template>
  <v-row>
    <!-- Tags -->
    <v-col cols="12" sm="6">
      <v-list-subheader class="ml-2">{{ $t('settings.tagsAndCategories.tagsSubheader') }}</v-list-subheader>

      <v-sheet rounded="xl" class="d-flex align-center gap" v-for="tag in maindataStore.tags">
        <div class="pl-4 py-1 wrap-anywhere">{{ tag }}</div>
        <v-spacer />
        <div class="d-flex">
          <v-btn icon="mdi-pencil" variant="plain" @click="openTagFormDialog(tag)" />
          <v-btn icon="mdi-delete" color="red" variant="plain" @click="deleteTag(tag)" />
        </div>
      </v-sheet>

      <v-card v-if="maindataStore.tags.length === 0">
        <v-card-text>{{ $t('settings.tagsAndCategories.noTags') }}</v-card-text>
      </v-card>

      <v-btn color="accent" block @click="openTagFormDialog()">
        {{ $t('settings.tagsAndCategories.createNewTag') }}
      </v-btn>
    </v-col>

    <!-- Categories -->
    <v-col cols="12" sm="6">
      <v-list-subheader class="ml-2">{{ $t('settings.tagsAndCategories.categoriesSubheader') }}</v-list-subheader>

      <v-sheet rounded="xl" class="d-flex align-center gap" v-for="category in maindataStore.categories">
        <div class="pl-4 py-1 wrap-anywhere">{{ category.name }}</div>
        <v-spacer />
        <div class="d-flex">
          <v-btn icon="mdi-pencil" variant="plain" @click="openCategoryFormDialog(category)" />
          <v-btn icon="mdi-delete" color="red" variant="plain" @click="deleteCategory(category)" />
        </div>
      </v-sheet>

      <v-card v-if="maindataStore.categories.length === 0">
        <v-card-text>{{ $t('settings.tagsAndCategories.noCategories') }}</v-card-text>
      </v-card>

      <v-btn color="accent" block @click="openCategoryFormDialog()">
        {{ $t('settings.tagsAndCategories.createNewCategory') }}
      </v-btn>
    </v-col>
  </v-row>
</template>

<style scoped lang="scss">
.gap {
  margin-bottom: 8px;

  &:last-child {
    margin-bottom: unset;
  }
}
</style>
