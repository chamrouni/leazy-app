<script setup lang="ts">
import { NodeViewWrapper } from '@tiptap/vue-3'
import { useDebounce } from '@vueuse/core'

const props = defineProps(['editor', 'getPos', 'deleteNode'])

const imageUrl = ref('')
const isLoading = ref(false)
const results = ref([])
const currentProvider = ref('unsplash')
const searchQuery = ref('')
const debounced = useDebounce(searchQuery, 1000)

const insertImage = (attrs) => {
  props.editor.chain().focus().insertContentAt(props.getPos(), {
    type: 'image',
    attrs: {
      ...attrs,
      align: 'left',
      width: '100%'
    }
  }).run()
  props.deleteNode()
}

const importImage = () => {
  if (!isValidURL(imageUrl.value)) {
    alert('URL invalide.')
    return
  }
  insertImage({ src: imageUrl.value })
  props.deleteNode()
}

const isValidURL = (url) => {
  const URLRegExp = new RegExp('^(https?://)?' + '((([a-z\\d]([a-z\\d-]*[a-z\\d])*)\\.)+[a-z]{2,}|' + '((\\d{1,3}\\.){3}\\d{1,3}))' + '(\\:\\d+)?(\\/[-a-z\\d%_.~+]*)*' + '(\\?[;&a-z\\d%_.~+=-]*)?' + '(\\#[-a-z\\d_]*)?$', 'i')
  return URLRegExp.test(url)
}

watch(debounced, async () => {
  isLoading.value = true
  const { results: resultsQuery } = await useSearchMedia(currentProvider.value, searchQuery.value, null)
  results.value = resultsQuery.value
  isLoading.value = false
})

const handleProvider = async (provider) => {
  currentProvider.value = provider
  if (searchQuery.value !== '') {
    isLoading.value = true
    const { results: resultsQuery } = await useSearchMedia(provider, searchQuery.value, null)
    results.value = resultsQuery.value
    isLoading.value = false
  }
}

const getImageSource = (item) => {
  if (currentProvider.value === 'unsplash') {
    return { preview: item?.urls?.thumb, src: item?.urls?.full, label: item?.alt_description, label: item?.alt_description, author: { url: item?.user?.links?.html, name: item?.user?.name } }
  } else if (currentProvider.value === 'pexels') {
    return { preview: item?.src?.tiny, src: item?.src?.original, label: item?.alt, label: item?.alt, author: { url: item?.photographer_url, name: item?.photographer } }
  } else if (currentProvider.value === 'pixabay') {
    return { preview: item?.previewURL, src: item?.largeImageURL, label: item?.tags, author: { url: item?.pageURL, name: item?.user } }
  } else if (currentProvider.value === 'giphy') {
    return { preview: item?.images?.preview_gif?.url, src: item?.images?.original?.url, label: item?.title, author: { url: item?.user?.profile_url, name: item?.user?.username } }
  }
}

const tabs = [
  { label: 'Unsplash', value: 'unsplash', icon: 'i-simple-icons-unsplash', click: () => handleProvider('unsplash')},
  { label: 'Pexels', value: 'pexels', icon: 'i-simple-icons-pexels', click: () => handleProvider('pexels')},
  { label: 'Pixabay', value: 'pixabay', icon: 'i-simple-icons-pixabay', click: () => handleProvider('pixabay')},
  { label: 'Giphy', value: 'giphy', icon: 'i-simple-icons-giphy', click: () => handleProvider('giphy')}
]
</script>

<template>
  <NodeViewWrapper :class="[selected ? 'ProseMirror-selectednode' : '', 'imagePlaceholder']">
    <div class="placeholderContainer">
      <UCard :ui="{ body: { padding: 'py-3 sm:p-3' } }">
        <div class="flex flex-col h-[350px] -p-1 overflow-hidden w-full">
          <UTabs :items="tabs" :ui="{ wrapper: 'px-1', list: { tab: { size: 'text-xs' } } }">
            <template #default="{ item, index, selected }">
              <div @click="item.click()" class="flex items-center gap-2 relative truncate">
                <UIcon :name="item.icon" class="w-4 h-4 flex-shrink-0" />

                <span class="truncate">{{ item.label }}</span>

                <span v-if="selected" class="absolute -right-4 w-2 h-2 rounded-full bg-primary-500 dark:bg-primary-400" />
              </div>
            </template>
          </UTabs>

          <div class="flex-1 p-1 max-h-full flex flex-col overflow-hidden">
            <UInput :loading="isLoading" v-model="searchQuery" placeholder="Rechercher un média" icon="i-heroicons-magnifying-glass" size="xs">
              <template v-if="searchQuery !== ''" #trailing>
                <UIcon class="cursor-pointer pointer-events-auto" name="i-heroicons-x-mark" @click="searchQuery = ''" />
              </template>
            </UInput>

            <div v-if="results.length > 0" class="masonry overflow-y-auto mt-4">
              <div class="not-prose mb-4 h-auto max-w-full break-inside-avoid" v-for="(item, index) in results" :key="index">
                <img @click="insertImage({ src: getImageSource(item).src })" class="w-full rounded-sm" :src="getImageSource(item).preview" :alt="getImageSource(item).label" />
              </div>
            </div>
          </div>
        </div>
      </UCard>
      <div class="placeholderText">- ou -</div>
      <div class="flex items-center justify-start gap-2">
        <UInput placeholder="Saisir l'URL de l'image" class="flex-1" v-model="imageUrl"/>
        <UButton label="Importer" @click="importImage" />
      </div>
    </div>
  </NodeViewWrapper>
</template>

<style scoped>
.masonry {
  column-count: 3;
}
</style>
