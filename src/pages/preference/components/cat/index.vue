<script setup lang="ts">
import { InputNumber, Select, Slider, Switch } from 'ant-design-vue'
import { computed } from 'vue'

import ProList from '@/components/pro-list/index.vue'
import ProListItem from '@/components/pro-list-item/index.vue'
import { useCatStore } from '@/stores/cat'
import { isWindows } from '@/utils/platform'

const catStore = useCatStore()

const colorOptions = computed(() => [
  { label: '黑色', value: '#000000' },
  { label: '白色', value: '#FFFFFF' },
  { label: '红色', value: '#FF4D4F' },
  { label: '蓝色', value: '#1890FF' },
  { label: '绿色', value: '#52C41A' },
  { label: '橙色', value: '#FA8C16' },
  { label: '紫色', value: '#722ED1' },
])

const bgColorOptions = computed(() => [
  { label: '透明', value: 'rgba(255, 255, 255, 0)' },
  { label: '浅白', value: 'rgba(255, 255, 255, 0.6)' },
  { label: '半透白', value: 'rgba(255, 255, 255, 0.85)' },
  { label: '白色', value: 'rgba(255, 255, 255, 0.95)' },
  { label: '黑色', value: 'rgba(0, 0, 0, 0.75)' },
])

const previewBubbleStyle = computed(() => {
  const isMirror = catStore.model.mirror
  return {
    top: `${catStore.display.keyHintPosition}%`,
    ...(isMirror
      ? { left: `${catStore.display.keyHintHorizontalPosition}%` }
      : { right: `${catStore.display.keyHintHorizontalPosition}%` }),
    fontSize: `${Math.max(10, Math.min(16, catStore.display.keyHintFontSize * 0.6))}px`,
    color: catStore.display.keyHintColor,
    background: catStore.display.keyHintBgColor,
    padding: catStore.display.keyHintBgColor === 'rgba(255, 255, 255, 0)' ? '2px 4px' : '3px 8px',
    borderRadius: catStore.display.keyHintBgColor === 'rgba(255, 255, 255, 0)' ? '0' : '6px',
    textShadow: catStore.display.keyHintBorder
      ? `0 0 2px rgba(255,255,255,0.9), 0 0 4px rgba(255,255,255,0.7)`
      : 'none',
  }
})
</script>

<template>
  <ProList :title="$t('pages.preference.cat.labels.modelSettings')">
    <ProListItem
      :description="$t('pages.preference.cat.hints.mirrorMode')"
      :title="$t('pages.preference.cat.labels.mirrorMode')"
    >
      <Switch v-model:checked="catStore.model.mirror" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.mouseMirror')"
      :title="$t('pages.preference.cat.labels.mouseMirror')"
    >
      <Switch v-model:checked="catStore.model.mouseMirror" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.motionSound')"
      :title="$t('pages.preference.cat.labels.motionSound')"
    >
      <Switch v-model:checked="catStore.model.motionSound" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.behavior')"
      :title="$t('pages.preference.cat.labels.behavior')"
    >
      <Switch v-model:checked="catStore.model.behavior" />
    </ProListItem>

    <ProListItem
      v-if="isWindows"
      :description="$t('pages.preference.cat.hints.autoReleaseDelay')"
      :title="$t('pages.preference.cat.labels.autoReleaseDelay')"
    >
      <InputNumber
        v-model:value="catStore.model.autoReleaseDelay"
        addon-after="s"
        class="w-28"
      />
    </ProListItem>
  </ProList>

  <ProList :title="$t('pages.preference.cat.labels.keyHintSettings')">
    <ProListItem
      :description="$t('pages.preference.cat.hints.showKeyHint')"
      :title="$t('pages.preference.cat.labels.showKeyHint')"
    >
      <Switch v-model:checked="catStore.display.showKeyHint" />
    </ProListItem>

    <ProListItem :title="$t('pages.preference.cat.labels.keyHintPosition')">
      <InputNumber
        v-model:value="catStore.display.keyHintPosition"
        addon-after="%"
        class="w-28"
        :max="50"
        :min="0"
      />
    </ProListItem>

    <ProListItem :title="$t('pages.preference.cat.labels.keyHintHorizontalPosition')">
      <InputNumber
        v-model:value="catStore.display.keyHintHorizontalPosition"
        addon-after="%"
        class="w-28"
        :max="50"
        :min="0"
      />
    </ProListItem>

    <ProListItem :title="$t('pages.preference.cat.labels.keyHintFontSize')">
      <InputNumber
        v-model:value="catStore.display.keyHintFontSize"
        addon-after="px"
        class="w-28"
        :max="48"
        :min="8"
      />
    </ProListItem>

    <ProListItem :title="$t('pages.preference.cat.labels.keyHintColor')">
      <div class="flex items-center gap-2">
        <Select
          v-model:value="catStore.display.keyHintColor"
          class="w-28"
          :options="colorOptions"
        />
        <div
          class="h-5 w-5 border border-gray-300 rounded"
          :style="{ backgroundColor: catStore.display.keyHintColor }"
        />
      </div>
    </ProListItem>

    <ProListItem :title="$t('pages.preference.cat.labels.keyHintBgColor')">
      <div class="flex items-center gap-2">
        <Select
          v-model:value="catStore.display.keyHintBgColor"
          class="w-28"
          :options="bgColorOptions"
        />
        <div
          class="h-5 w-5 border border-gray-300 rounded"
          :style="{ backgroundColor: catStore.display.keyHintBgColor }"
        />
      </div>
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.keyHintBorder')"
      :title="$t('pages.preference.cat.labels.keyHintBorder')"
    >
      <Switch v-model:checked="catStore.display.keyHintBorder" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.keyHintPreview')"
      :title="$t('pages.preference.cat.labels.keyHintPreview')"
      vertical
    >
      <div class="key-hint-preview">
        <div
          class="preview-window"
          :class="{ mirror: catStore.model.mirror }"
        >
          <div class="preview-cat">
            🐱
          </div>
          <div
            class="preview-bubble"
            :style="previewBubbleStyle"
          >
            Abc
          </div>
        </div>
      </div>
    </ProListItem>
  </ProList>

  <ProList :title="$t('pages.preference.cat.labels.windowSettings')">
    <ProListItem
      :description="$t('pages.preference.cat.hints.passThrough')"
      :title="$t('pages.preference.cat.labels.passThrough')"
    >
      <Switch v-model:checked="catStore.window.passThrough" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.alwaysOnTop')"
      :title="$t('pages.preference.cat.labels.alwaysOnTop')"
    >
      <Switch v-model:checked="catStore.window.alwaysOnTop" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.hideOnHover')"
      :title="$t('pages.preference.cat.labels.hideOnHover')"
    >
      <Switch v-model:checked="catStore.window.hideOnHover" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.keepInScreen')"
      :title="$t('pages.preference.cat.labels.keepInScreen')"
    >
      <Switch v-model:checked="catStore.window.keepInScreen" />
    </ProListItem>

    <ProListItem
      :description="$t('pages.preference.cat.hints.windowSize')"
      :title="$t('pages.preference.cat.labels.windowSize')"
    >
      <InputNumber
        v-model:value="catStore.window.scale"
        addon-after="%"
        class="w-28"
        :max="500"
        :min="1"
      />
    </ProListItem>

    <ProListItem :title="$t('pages.preference.cat.labels.windowRadius')">
      <InputNumber
        v-model:value="catStore.window.radius"
        addon-after="%"
        class="w-28"
        :min="0"
      />
    </ProListItem>

    <ProListItem
      :title="$t('pages.preference.cat.labels.opacity')"
      vertical
    >
      <Slider
        v-model:value="catStore.window.opacity"
        class="m-[0]!"
        :max="100"
        :min="10"
        :tip-formatter="(value) => `${value}%`"
      />
    </ProListItem>
  </ProList>
</template>

<style scoped>
.key-hint-preview .preview-window {
  position: relative;
  width: 240px;
  height: 140px;
  background: rgba(0, 0, 0, 0.05);
  border: 1px solid rgba(0, 0, 0, 0.1);
  border-radius: 8px;
  overflow: hidden;
}
.key-hint-preview .preview-cat {
  position: absolute;
  bottom: 10%;
  font-size: 40px;
  line-height: 1;
}
.key-hint-preview .preview-window:not(.mirror) .preview-cat {
  left: 25%;
  transform: translateX(-50%);
}
.key-hint-preview .preview-window.mirror .preview-cat {
  right: 25%;
  transform: translateX(50%);
}
.key-hint-preview .preview-bubble {
  position: absolute;
  font-weight: 700;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  line-height: 1;
  letter-spacing: 0.5px;
  white-space: nowrap;
  pointer-events: none;
}
</style>
