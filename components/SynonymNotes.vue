<script setup lang="ts">
import { computed } from 'vue'

/**
 * One concept, three names in taf_cat_complete.xsd, three properties in the v4.0.0 telematics ontology.
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(2, props.step)))

const notes = [
  { xsd: 'LocationPrimaryName', where: 'in LocationPrimaryInformation', note: 'no type at all', prop: 'era:locationPrimaryName', rot: -3 },
  { xsd: 'PrimaryLocationName', where: 'in LocationIdent', note: 'optional', prop: 'era:primaryLocationName', rot: 2 },
  { xsd: 'PrimaryLocationNameASCII', where: 'inline, max 255 chars', note: 'ASCII only', prop: 'era:primaryLocationNameASCII', rot: -1.5 },
]
</script>

<template>
  <div class="syn">
    <div class="human" :class="{ on: s >= 0 }">👤 A human reads: <b>"the name of a primary location"</b></div>
    <div class="notes">
      <div v-for="n in notes" :key="n.xsd" class="note" :style="{ transform: `rotate(${n.rot}deg)` }">
        <code>&lt;{{ n.xsd }}&gt;</code>
        <div class="w">{{ n.where }}</div>
        <div class="w i">{{ n.note }}</div>
      </div>
    </div>
    <div class="mid" :class="{ off: s < 1 }">⬇ &nbsp; xsd2rdf: one name, one property &nbsp; ⬇</div>
    <div class="props" :class="{ off: s < 1 }">
      <div v-for="n in notes" :key="n.prop" class="prop"><code>{{ n.prop }}</code></div>
    </div>
    <div class="codes" :class="{ off: s < 2 }">
      The same for the location code: <code>era:primaryCode</code> · <code>era:freightLocationPrimaryCode</code> · <code>era:stationLocationPrimaryCode</code>
    </div>
  </div>
</template>

<style scoped>
.syn { text-align: center; }
.human { font-size: 18px; margin: 6px 0 14px; }
.notes, .props { display: grid; grid-template-columns: repeat(3, 1fr); gap: 28px; }
.note { background: #FFF3B0; color: #3A3000; padding: 14px 10px; border-radius: 4px; box-shadow: 2px 4px 10px rgba(0,0,0,.18); }
.note code { font-size: 16px; font-weight: 700; color: #3A3000; background: transparent; }
.note .w { font-size: 13px; margin-top: 4px; }
.note .w.i { font-style: italic; opacity: .8; }
.mid { margin: 16px 0; color: var(--c-ink-mod); font-size: 14px; transition: opacity .5s; }
.prop { border: 2px solid var(--c-red); background: var(--c-red-bg); border-radius: 10px; padding: 10px; }
.prop code { font-size: 15px; font-weight: 700; color: var(--c-red-ink); background: transparent; }
.codes { margin-top: 22px; font-size: 14px; color: var(--c-ink-mod); transition: opacity .5s; }
.off { opacity: 0; }
</style>
