<script setup lang="ts">
// Items start in an unsorted pool; each click (step) moves the next one into its bucket.
// step: pass $clicks, and set `clicks:` in frontmatter to at least items.length
interface Item {
  label: string
  example?: string
  bucket: 'fast' | 'slow'
  note?: string
}

const props = defineProps<{
  items: Item[]
  step: number
  fastLabel?: string
  slowLabel?: string
}>()

const sorted = (i: number) => props.step > i
</script>

<template>
  <div class="sort-buckets">
    <div class="sort-buckets__pool">
      <div
        v-for="(item, i) in items"
        :key="item.label"
        class="chip"
        :class="{ gone: sorted(i) }"
      >
        {{ item.label }} <code v-if="item.example">{{ item.example }}</code>
      </div>
    </div>

    <div class="sort-buckets__row">
      <div
        v-for="bucket in (['fast', 'slow'] as const)"
        :key="bucket"
        class="bucket"
        :class="bucket"
      >
        <h4>{{ bucket === 'fast' ? (fastLabel ?? 'Fast') : (slowLabel ?? 'Slow') }}</h4>
        <TransitionGroup name="chip">
          <template v-for="(item, i) in items" :key="item.label">
            <div v-if="item.bucket === bucket && sorted(i)" class="chip">
              {{ item.label }} <code v-if="item.example">{{ item.example }}</code>
              <div v-if="item.note" class="note">{{ item.note }}</div>
            </div>
          </template>
        </TransitionGroup>
      </div>
    </div>
  </div>
</template>

<style scoped>
.sort-buckets__pool {
  display: flex;
  flex-wrap: wrap;
  gap: 0.4rem;
  justify-content: center;
  margin-bottom: 1rem;
}
.sort-buckets__row {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 1rem;
}
.bucket {
  border: 2px solid;
  border-radius: 0.5rem;
  padding: 0.5rem 0.75rem;
  min-height: 13rem;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
}
.bucket h4 {
  margin: 0 0 0.25rem;
}
.bucket.fast {
  border-color: rgba(34, 197, 94, 0.7);
  background: rgba(34, 197, 94, 0.06);
}
.bucket.slow {
  border-color: rgba(239, 68, 68, 0.7);
  background: rgba(239, 68, 68, 0.06);
}
.chip {
  border: 1px solid rgba(128, 128, 128, 0.4);
  border-radius: 0.4rem;
  padding: 0.15rem 0.5rem;
  font-size: 0.8rem;
  transition: opacity 0.3s;
}
.sort-buckets__pool .chip.gone {
  opacity: 0.15;
}
.note {
  font-size: 0.7rem;
  opacity: 0.6;
}
.chip-enter-active {
  transition: all 0.35s ease;
}
.chip-enter-from {
  opacity: 0;
  transform: translateY(-1rem);
}
</style>
