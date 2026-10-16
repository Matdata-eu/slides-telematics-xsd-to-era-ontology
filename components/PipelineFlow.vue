<script setup lang="ts">
import { computed } from 'vue'

/** The conversion pipeline (telematics-ontology run-pipeline.sh steps 1-5), coloured by who does the work. */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(2, props.step)))

const stages = [
  { id: 'xsd', title: 'XSD', sub: 'NeTEx · TAF · TAP B.5 · TAP B.10', kind: 'src' },
  { id: 'shacl', title: 'SHACL shapes', sub: 'the rules of the message', kind: 'auto' },
  { id: 'owl', title: 'OWL + SKOS', sub: 'derived from the shapes', kind: 'auto' },
  { id: 'post', title: 'Post-processing', sub: 'rename · tidy · link · de-suffix', kind: 'era' },
  { id: 'merge', title: 'Merge & bundle', sub: 'parameter groups · checks', kind: 'era' },
  { id: 'v4', title: 'ERA ontology v4.0', sub: 'telematics + NeTEx bundles', kind: 'out' },
]
const active = (k: string) => (s.value === 1 && k === 'auto') || (s.value === 2 && k === 'era')
const dim = (k: string) => (s.value === 1 && k === 'era') || (s.value === 2 && k === 'auto')
</script>

<template>
  <div class="flow">
    <div class="row">
      <template v-for="(st, i) in stages" :key="st.id">
        <div class="stage" :class="[st.kind, { act: active(st.kind), dim: dim(st.kind) }]">
          <div class="t">{{ st.title }}</div>
          <div class="sb">{{ st.sub }}</div>
        </div>
        <div v-if="i < stages.length - 1" class="arrow">
          <span v-if="i === 0" class="lbl">xsd2rdf</span>
          <span v-else-if="i === 1" class="lbl">7 SPARQL steps</span>
          →
        </div>
      </template>
    </div>
    <div class="side">
      <div class="nonxsd">B.12 ASN.1 · B.14 JSON · OSDM OpenAPI <span class="muted">→ own converters → join at post-processing</span></div>
    </div>
    <div class="legend">
      <span class="lg auto" :class="{ act: s === 1 }">■ automatic and generic: <b>xsd2rdf</b>, the same for any XSD</span>
      <span class="lg era" :class="{ act: s === 2 }">■ ERA-specific repair and alignment: <b>telematics-ontology</b> scripts</span>
    </div>
  </div>
</template>

<style scoped>
.flow { margin-top: 48px; }
.row { display: flex; align-items: stretch; gap: 4px; }
.stage { flex: 1; border-radius: 10px; padding: 26px 8px; text-align: center; border: 2px solid var(--c-stroke); background: var(--c-surface); transition: all .5s; }
.stage .t { font-weight: 700; font-size: 19px; color: var(--c-heading); }
.stage .sb { font-size: 13px; color: var(--c-ink-mod); margin-top: 4px; line-height: 1.25; }
.stage.auto { border-color: var(--c-blue); background: var(--c-blue-bg); }
.stage.era { border-color: var(--c-amber); background: var(--c-amber-bg); }
.stage.out { border-color: var(--c-green); background: var(--c-green-bg); }
.stage.act { transform: translateY(-6px); box-shadow: 0 6px 16px rgba(0,0,0,.15); }
.stage.dim { opacity: .35; }
.arrow { display: flex; flex-direction: column; justify-content: center; align-items: center; font-size: 22px; color: var(--c-ink-mod); min-width: 36px; }
.lbl { font-size: 10.5px; font-weight: 700; color: var(--c-blue); white-space: nowrap; }
.side { display: flex; justify-content: center; margin-top: 30px; }
.nonxsd { font-size: 15px; padding: 5px 14px; border: 1px dashed var(--c-ink-subtle); border-radius: 8px; }
.legend { display: flex; gap: 28px; justify-content: center; margin-top: 40px; font-size: 15px; white-space: nowrap; }
.lg { padding: 4px 10px; border-radius: 8px; transition: all .5s; }
.lg.auto { color: var(--c-blue); } .lg.era { color: var(--c-amber-ink); }
.lg.act { background: var(--c-surface); box-shadow: 0 0 0 2px currentColor; }
.muted { color: var(--c-ink-mod); }
</style>
