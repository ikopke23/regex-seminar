<script setup lang="ts">
import { ref, watch } from 'vue'

// tabs: { slotName: 'Tab Label', ... } — one named slot per key
// step: pass $clicks to advance tabs with the slide's click steps
const props = defineProps<{
  tabs: Record<string, string>
  height?: string
  step?: number
}>()

const keys = Object.keys(props.tabs)
const active = ref(keys[0])

watch(() => props.step, (step) => {
  if (step != null)
    active.value = keys[Math.min(step, keys.length - 1)]
}, { immediate: true })
</script>

<template>
  <div class="syntax-tabs">
    <div class="syntax-tabs__bar">
      <button
        v-for="(label, key) in tabs"
        :key="key"
        :class="{ active: active === key }"
        @click="active = key"
      >
        {{ label }}
      </button>
    </div>

    <div class="syntax-tabs__panel" :style="{ maxHeight: height ?? '22rem' }">
      <template v-for="(_, key) in tabs" :key="key">
        <div v-show="active === key">
          <slot :name="key" />
        </div>
      </template>
    </div>
  </div>
</template>

<!-- not scoped: slot content is compiled in the slide, so scoped styles wouldn't reach the tables -->
<style>
.syntax-tabs__bar {
  display: flex;
  gap: 0.25rem;
  border-bottom: 1px solid rgba(128, 128, 128, 0.3);
}
.syntax-tabs__bar button {
  padding: 0.3rem 0.8rem;
  font-size: 0.85rem;
  opacity: 0.6;
  border-bottom: 2px solid transparent;
}
.syntax-tabs__bar button.active {
  opacity: 1;
  border-bottom-color: currentColor;
}
.syntax-tabs__panel {
  overflow-y: auto;
  margin-top: 0.5rem;
}
.syntax-tabs__panel table {
  font-size: 0.8rem;
  width: 100%;
}
.syntax-tabs__panel td,
.syntax-tabs__panel th {
  padding: 0.2rem 0.6rem;
}
.syntax-tabs__panel th {
  position: sticky;
  top: 0;
  /* matches Slidev's bg-main so rows scroll under the header */
  background: #fff;
}
html.dark .syntax-tabs__panel th {
  background: #121212;
}
/* highlight matches in example strings with <mark>...</mark> */
.syntax-tabs__panel mark {
  background: rgba(250, 204, 21, 0.45);
  color: inherit;
  border-radius: 2px;
  padding: 0 1px;
}
/* keep back-to-back matches visually separate */
.syntax-tabs__panel mark + mark {
  margin-left: 2px;
}
</style>
