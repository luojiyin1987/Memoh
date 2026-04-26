<template>
  <div class="text-sm leading-relaxed">
    <div
      v-if="expandable"
      role="button"
      tabindex="0"
      class="group flex items-center gap-1.5 w-full text-left transition-colors cursor-pointer py-0.5 select-none"
      :class="rowClass"
      @click="toggleOpen"
      @keydown.enter.prevent="toggleOpen"
      @keydown.space.prevent="toggleOpen"
    >
      <component
        :is="display.icon"
        class="size-3.5 shrink-0"
      />
      <span
        v-if="!display.hideAction"
        class="shrink-0"
        :class="actionClass"
      >{{ actionLabel }}</span>
      <button
        v-if="display.target && canOpenInFiles"
        class="font-mono truncate hover:underline cursor-pointer"
        :class="targetClass"
        :title="display.fullTarget || display.target"
        @click.stop="handleOpenInFiles"
      >
        {{ display.target }}
      </button>
      <span
        v-else-if="display.target"
        class="font-mono truncate"
        :class="targetClass"
        :title="display.fullTarget || display.target"
      >{{ display.target }}</span>
      <span
        v-if="display.diffAdd"
        class="font-mono shrink-0 text-emerald-600 dark:text-emerald-500"
      >+{{ display.diffAdd }}</span>
      <span
        v-if="display.diffRemove"
        class="font-mono shrink-0 text-rose-600 dark:text-rose-500"
      >-{{ display.diffRemove }}</span>
      <span
        v-if="display.errorSuffix"
        class="font-mono shrink-0"
      >{{ display.errorSuffix }}</span>
      <ChevronRight
        v-if="!open"
        class="size-3.5 shrink-0 ml-auto opacity-60 group-hover:opacity-100"
      />
      <ChevronDown
        v-else
        class="size-3.5 shrink-0 ml-auto opacity-60 group-hover:opacity-100"
      />
    </div>

    <div
      v-else
      class="flex items-center gap-1.5 w-full py-0.5"
      :class="rowClass"
    >
      <component
        :is="display.icon"
        class="size-3.5 shrink-0"
      />
      <span
        v-if="!display.hideAction"
        class="shrink-0"
        :class="actionClass"
      >{{ actionLabel }}</span>
      <button
        v-if="display.target && canOpenInFiles"
        class="font-mono truncate hover:underline cursor-pointer"
        :class="targetClass"
        :title="display.fullTarget || display.target"
        @click="handleOpenInFiles"
      >
        {{ display.target }}
      </button>
      <span
        v-else-if="display.target"
        class="font-mono truncate"
        :class="targetClass"
        :title="display.fullTarget || display.target"
      >{{ display.target }}</span>
      <span
        v-if="display.diffAdd"
        class="font-mono shrink-0 text-emerald-600 dark:text-emerald-500"
      >+{{ display.diffAdd }}</span>
      <span
        v-if="display.diffRemove"
        class="font-mono shrink-0 text-rose-600 dark:text-rose-500"
      >-{{ display.diffRemove }}</span>
      <span
        v-if="display.errorSuffix"
        class="font-mono shrink-0"
      >{{ display.errorSuffix }}</span>
    </div>

    <div
      v-if="expandable && open"
      class="mt-1 ml-5 py-1 space-y-1.5"
    >
      <component
        :is="display.detail"
        v-if="display.detail"
        :block="block"
      />
      <ToolCallDetailGeneric
        v-else
        :block="block"
      />
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, inject, ref } from 'vue'
import { ChevronDown, ChevronRight } from 'lucide-vue-next'
import { useI18n } from 'vue-i18n'
import type { ToolCallBlock } from '@/store/chat-list'
import { openInFileManagerKey } from '../composables/useFileManagerProvider'
import {
  getToolDisplay,
  isDirPathTool,
  isFilePathTool,
} from './tool-call-registry'
import ToolCallDetailGeneric from './tool-call-detail-generic.vue'

const props = defineProps<{ block: ToolCallBlock }>()
const { t } = useI18n()

const openInFileManager = inject(openInFileManagerKey, undefined)

const display = computed(() => getToolDisplay(props.block))

const open = ref(getToolDisplay(props.block).defaultOpen === true)

const expandable = computed(
  () => Boolean(display.value.detail) || display.value.expandable === true,
)

const actionLabel = computed(() => {
  const key = `chat.tools.${display.value.actionKey}`
  return t(key, display.value.actionParams ?? {})
})

const rowClass = computed(() => {
  if (!expandable.value) {
    return display.value.isError ? 'text-destructive' : 'text-muted-foreground'
  }
  return display.value.isError
    ? 'text-destructive hover:text-destructive/90'
    : 'text-muted-foreground hover:text-foreground'
})

const targetClass = computed(() => {
  if (!props.block.done) return 'tool-shimmer-text'
  if (display.value.isError) return 'text-destructive'
  return 'text-foreground/80'
})

const actionClass = computed(() => {
  if (!props.block.done && !display.value.target) return 'tool-shimmer-text'
  return ''
})

const filePath = computed(() => {
  if (!isFilePathTool(props.block.toolName)) return ''
  const input = props.block.input as Record<string, unknown> | undefined
  return (input?.path as string) ?? ''
})

const canOpenInFiles = computed(
  () => Boolean(filePath.value) && Boolean(openInFileManager),
)

function toggleOpen() {
  open.value = !open.value
}

function handleOpenInFiles() {
  if (!filePath.value || !openInFileManager) return
  openInFileManager(filePath.value, isDirPathTool(props.block.toolName))
}
</script>
