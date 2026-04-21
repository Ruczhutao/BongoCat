<script setup lang="ts">
import { computed } from 'vue'

import { useCatStore } from '@/stores/cat'
import { useModelStore } from '@/stores/model'

const modelStore = useModelStore()
const catStore = useCatStore()

const containerStyle = computed(() => {
  const isMirror = catStore.model.mirror
  const hPos = catStore.display.keyHintHorizontalPosition
  return {
    top: `${catStore.display.keyHintPosition}%`,
    width: 'auto',
    height: 'auto',
    ...(isMirror
      ? { left: `${hPos}%`, transform: 'scaleX(-1)' }
      : { right: `${hPos}%` }),
  }
})

const textStyle = computed(() => ({
  fontSize: `${catStore.display.keyHintFontSize}px`,
  color: catStore.display.keyHintColor,
  textShadow: catStore.display.keyHintBorder
    ? `0 0 4px rgba(255,255,255,0.9), 0 0 8px rgba(255,255,255,0.7), 0 2px 3px rgba(0,0,0,0.2)`
    : 'none',
}))

const bubbleStyle = computed(() => ({
  background: catStore.display.keyHintBgColor,
  padding: catStore.display.keyHintBgColor === 'rgba(255, 255, 255, 0)' ? '0' : '4px 10px',
  borderRadius: catStore.display.keyHintBgColor === 'rgba(255, 255, 255, 0)' ? '0' : '8px',
}))

/**
 * 将按键名称转换为友好的显示格式
 * rdev 格式: KeyA -> A, Digit1 -> 1, Num4 -> 4, ControlLeft -> Ctrl
 */
function formatKeyName(key: string): string {
  const keyMap: Record<string, string> = {
    ControlLeft: 'Ctrl',
    ControlRight: 'Ctrl',
    AltLeft: 'Alt',
    AltRight: 'Alt',
    ShiftLeft: 'Shift',
    ShiftRight: 'Shift',
    MetaLeft: '⌘',
    MetaRight: '⌘',
    Space: '␣',
    Enter: '↩',
    Backspace: '⌫',
    Tab: '⇥',
    Escape: '⎋',
    ArrowUp: '↑',
    ArrowDown: '↓',
    ArrowLeft: '←',
    ArrowRight: '→',
  }

  if (keyMap[key]) return keyMap[key]

  // 去掉 Key/Digit/Num 前缀
  if (key.startsWith('Key')) return key.slice(3)
  if (key.startsWith('Digit')) return key.slice(5)
  if (key.startsWith('Num')) return key.slice(3)

  return key
}
</script>

<template>
  <div
    class="key-hint-container"
    :style="containerStyle"
  >
    <TransitionGroup name="key-hint">
      <span
        v-for="key in Object.keys(modelStore.pressedKeys)"
        :key="key"
        class="key-hint-bubble"
        :style="[textStyle, bubbleStyle]"
      >
        {{ formatKeyName(key) }}
      </span>
    </TransitionGroup>
  </div>
</template>

<style scoped lang="scss">
.key-hint-container {
  position: absolute;
  display: flex;
  align-items: center;
  gap: 12px;
  z-index: 9999;
  pointer-events: none;
}

.key-hint-bubble {
  font-weight: 700;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  line-height: 1;
  letter-spacing: 0.5px;
  white-space: nowrap;
  animation: key-pop 0.15s ease-out;
}

.key-hint-enter-active,
.key-hint-leave-active {
  transition: all 0.15s ease-out;
}

.key-hint-enter-from {
  opacity: 0;
  transform: scale(0.7);
}

.key-hint-leave-to {
  opacity: 0;
  transform: scale(0.7);
}

@keyframes key-pop {
  from {
    opacity: 0;
    transform: scale(0.7);
  }
  to {
    opacity: 1;
    transform: scale(1);
  }
}
</style>
