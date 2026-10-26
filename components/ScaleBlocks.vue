<script setup lang="ts">
import { computed } from 'vue'

/**
 * Size of the three parts of ERA ontology v4.0.0, as squares whose area is proportional to the count.
 * Counts: named owl:Class / owl:DatatypeProperty / owl:ObjectProperty subjects on tag v4.0.0
 * (era-ontology/_core.ttl, domains/telematics/artefacts/dist/{telematics,netex}/ontology.ttl).
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(2, props.step)))

const parts = [
  { id: 'core', name: 'ERA core', sub: 'hand-made, based on the legacy RINF / ERATV XML', classes: 128, dp: 465, op: 342, colour: 'var(--c-blue)' },
  { id: 'tel', name: 'Telematics', sub: 'TAF, TAP B.5, B.10, B.12, B.14, OSDM', classes: 941, dp: 1520, op: 1165, colour: 'var(--c-amber)' },
  { id: 'netex', name: 'NeTEx', sub: 'separate bundle, now in its own netex: namespace', classes: 3451, dp: 4027, op: 3722, colour: 'var(--c-purple)' },
]
const total = (p: typeof parts[number]) => p.classes + p.dp + p.op
const max = Math.sqrt(total(parts[2]))
const side = (p: typeof parts[number]) => Math.round(250 * Math.sqrt(total(p)) / max)
const fmt = (n: number) => n.toLocaleString('en-GB')
</script>

<template>
  <div class="scale">
    <div v-for="(p, i) in parts" :key="p.id" class="col" :class="{ hidden: i > s }">
      <div class="box-wrap">
        <div class="box" :style="{ width: side(p) + 'px', height: side(p) + 'px', borderColor: p.colour, background: `color-mix(in srgb, ${p.colour} 18%, transparent)` }" />
      </div>
      <div class="name" :style="{ color: p.colour }">{{ p.name }}</div>
      <div class="sub">{{ p.sub }}</div>
      <div class="nums">
        <span><b>{{ fmt(p.classes) }}</b> classes</span>
        <span><b>{{ fmt(p.dp + p.op) }}</b> properties</span>
      </div>
      <div class="ratio" :class="{ ghost: i === 0 }">×{{ (total(p) / total(parts[0])).toFixed(1).replace('.0', '') }} the core</div>
    </div>
  </div>
</template>

<style scoped>
.scale { display: grid; grid-template-columns: 1fr 1fr 1.3fr; gap: 24px; align-items: start; margin-top: 8px; }
.col { text-align: center; transition: opacity .6s; }
.col.hidden { opacity: 0; }
.ratio.ghost { visibility: hidden; }
.box-wrap { height: 260px; display: flex; align-items: flex-end; justify-content: center; }
.box { border: 3px solid; border-radius: 10px; transition: all .6s; }
.name { font-size: 22px; font-weight: 700; margin-top: 10px; }
.sub { font-size: 12px; color: var(--c-ink-mod); }
.nums { display: flex; gap: 14px; justify-content: center; font-size: 14px; margin-top: 6px; }
.ratio { display: inline-block; margin-top: 6px; font-size: 13px; font-weight: 700; padding: 1px 10px; border-radius: 10px; background: var(--c-surface); border: 1px solid var(--c-stroke); }
</style>
