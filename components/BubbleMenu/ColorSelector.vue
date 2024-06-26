<template>
  <Popover>
    <div class="relative">
      <PopoverButton class="flex items-center h-full gap-1 p-2 text-sm font-medium text-stone-600 hover:bg-stone-100 active:bg-stone-200 focus:outline-none">
        <span
          class="px-1 rounded-sm"
          :style="{ color: activeColorItem?.color, backgroundColor: activeHighlightItem?.color }"
        >
          A
        </span>
        <ChevronDown class="w-4 h-4" />
      </PopoverButton>

      <PopoverPanel
        v-slot="{ close }"
        align="start"
        class="z-[99999] absolute my-1 flex max-h-80 w-48 flex-col overflow-hidden overflow-y-auto rounded border border-stone-200 bg-white p-1 shadow-xl animate-in fade-in slide-in-from-top-1"
      >
        <div class="px-2 my-1 text-sm text-stone-500">
          Color
        </div>
        <button
          v-for="(textColor, index) in TEXT_COLORS"
          :key="index"
          class="flex items-center justify-between px-2 py-1 text-sm rounded-sm text-stone-600 hover:bg-stone-100"
          type="button"
          @click="() => { editor.commands.unsetColor(); textColor.name !== 'Default' && editor.chain().focus().setColor(textColor.color || '').run(); close(); }"
        >
          <div class="flex items-center space-x-2">
            <div
              class="px-1 py-px font-medium border rounded-sm border-stone-200"
              :style="{ color: textColor.color }"
            >
              A
            </div>
            <span>{{ textColor.name }}</span>
          </div>
          <Check
            v-if="editor.isActive('textStyle', { color: textColor.color })"
            class="w-4 h-4"
          />
        </button>
        <div class="px-2 mt-2 mb-1 text-sm text-stone-500">
          Background
        </div>
        <button
          v-for="(highlightColor, index) in HIGHLIGHT_COLORS"
          :key="index"
          class="flex items-center justify-between px-2 py-1 text-sm rounded-sm text-stone-600 hover:bg-stone-100"
          type="button"
          @click="() => { editor.commands.unsetHighlight(); highlightColor.name !== 'Default' && editor.commands.setHighlight({ color: highlightColor.color }); close(); }"
        >
          <div class="flex items-center space-x-2">
            <div
              class="px-1 py-px font-medium border rounded-sm border-stone-200"
              :style="{ backgroundColor: highlightColor.color }"
            >
              A
            </div>
            <span>{{ highlightColor.name }}</span>
          </div>

          <Check
            v-if="editor.isActive('highlight', { color: highlightColor.color })"
            class="w-4 h-4"
          />
        </button>
      </PopoverPanel>
    </div>
  </Popover>
</template>

<script setup lang="ts">
import { computed, type PropType } from '#imports'
import { Editor } from '@tiptap/core'
import { Check, ChevronDown } from 'lucide-vue-next'
import { Popover, PopoverButton, PopoverPanel } from '@headlessui/vue'

const { editor } = defineProps({
  editor: {
    type: Object as PropType<Editor>,
    required: true
  }
})

const TEXT_COLORS = [
  {
    name: 'Default',
    color: 'var(--leazy-black)'
  },
  {
    name: 'Purple',
    color: '#9333EA'
  },
  {
    name: 'Red',
    color: '#E00000'
  },
  {
    name: 'Yellow',
    color: '#EAB308'
  },
  {
    name: 'Blue',
    color: '#2563EB'
  },
  {
    name: 'Green',
    color: '#008A00'
  },
  {
    name: 'Orange',
    color: '#FFA500'
  },
  {
    name: 'Pink',
    color: '#BA4081'
  },
  {
    name: 'Gray',
    color: '#A8A29E'
  }
]

const HIGHLIGHT_COLORS = [
  {
    name: 'Default',
    color: 'var(--leazy-highlight-default)'
  },
  {
    name: 'Purple',
    color: 'var(--leazy-highlight-purple)'
  },
  {
    name: 'Red',
    color: 'var(--leazy-highlight-red)'
  },
  {
    name: 'Yellow',
    color: 'var(--leazy-highlight-yellow)'
  },
  {
    name: 'Blue',
    color: 'var(--leazy-highlight-blue)'
  },
  {
    name: 'Green',
    color: 'var(--leazy-highlight-green)'
  },
  {
    name: 'Orange',
    color: 'var(--leazy-highlight-orange)'
  },
  {
    name: 'Pink',
    color: 'var(--leazy-highlight-pink)'
  },
  {
    name: 'Gray',
    color: 'var(--leazy-highlight-gray)'
  }
]

const activeColorItem = computed(() => TEXT_COLORS.find(({ color }) => editor.isActive('textStyle', { color })))
const activeHighlightItem = computed(() => HIGHLIGHT_COLORS.find(({ color }) => editor.isActive('highlight', { color })))
</script>
