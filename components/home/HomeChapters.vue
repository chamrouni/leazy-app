<script setup lang="ts">
import { formatDistanceToNow } from 'date-fns'
import frLocale from 'date-fns/locale/fr'

defineProps({
  chapters: {
    type: Array,
    required: true
  }
})
</script>

<template>
  <UDashboardCard>
    <template #header>
      <div class="flex align-center justify-between w-full">
        <div class="flex items-center gap-4">
          <div>
            <p class="text-gray-900 dark:text-white font-semibold">
              Mes derniers chapitres
            </p>
            <p class="text-sm text-gray-500 dark:text-gray-400">
              Liste de tout mes chapitres
            </p>
          </div>
        </div>
        <NuxtLink :to="localePath({ name: 'library-chapters' })" class="flex items-center text-sm font-medium text-gray-500 dark:text-gray-400 hover:text-gray-900 dark:hover:text-white">
          Voir tout
          <UIcon name="i-heroicons-chevron-right" class="w-4 h-4" />
        </NuxtLink>
      </div>
    </template>
    <NuxtLink v-for="chapter in chapters.slice(0, 4)" :key="chapter.id" :to="localePath({ name: 'library-chapters-id_slug', params: { id: chapter.id, slug: chapter.slug } })" class="px-3 py-2 -mx-2 last:-mb-2 rounded-md hover:bg-gray-50 dark:hover:bg-gray-800/50 cursor-pointer flex items-center gap-3 relative">
      <div class="flex items-center gap-3 text-sm flex-1">
        <img class="w-8 h-8 object-cover rounded-md" src="https://images.unsplash.com/photo-1537202108838-e7072bad1927?q=80&w=3062&auto=format&fit=crop&ixlib=rb-4.0.3&ixid=M3wxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8fA%3D%3D" alt="Image chapitre" />
        <div>
          <p class="text-gray-900 dark:text-white font-medium capitalize">
            {{ chapter.name }}
          </p>
          <p class="text-gray-500 text-xs dark:text-gray-400">
            {{ chapter.updated_at === chapter.created_at ? 'Créé' : 'Modifié' }} {{ formatDistanceToNow(new Date(chapter.updated_at), { locale: frLocale, addSuffix: true }) }}
          </p>
        </div>
      </div>

      <UKbd>
        {{ chapter.id }}
      </UKbd>
    </NuxtLink>
  </UDashboardCard>
</template>
