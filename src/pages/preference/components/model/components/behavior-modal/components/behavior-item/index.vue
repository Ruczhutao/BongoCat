<script setup lang="ts">
import { Button, ListItem } from 'ant-design-vue'

import Shortcut from '@/components/shortcut/index.vue'
import { useKeyPress } from '@/composables/useKeyPress'
import { useShortcutStore } from '@/stores/shortcut'

const { label } = defineProps<{ label: string }>()
const emit = defineEmits(['click'])
const modelValue = defineModel<string>()
const shortcutStore = useShortcutStore()

useKeyPress(modelValue, () => {
  emit('click')
}, shortcutStore.enabled)
</script>

<template>
  <ListItem>
    <span>{{ label }}</span>

    <template #actions>
      <Shortcut v-model="modelValue" />

      <Button
        class="inline-flex items-center justify-center"
        @click="emit('click')"
      >
        <template #icon>
          <div class="i-lucide:play" />
        </template>
      </Button>
    </template>
  </ListItem>
</template>
