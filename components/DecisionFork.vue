<script setup lang="ts">
import { computed } from 'vue'

/** The design choice at the start: convert the XSD structurally, or model conceptually and map. */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(2, props.step)))
</script>

<template>
  <div class="fork">
    <svg viewBox="0 0 1000 145" class="cv">
      <defs>
        <marker id="df-ah" viewBox="0 0 10 10" refX="9" refY="5" markerUnits="userSpaceOnUse" markerWidth="16" markerHeight="16" orient="auto-start-reverse">
          <path d="M0,0 L10,5 L0,10 z" style="fill: var(--c-ink-mod)" />
        </marker>
      </defs>
      <rect x="380" y="8" width="240" height="56" rx="12" class="src" />
      <text x="500" y="33" class="srct">TSI XSDs and XML messages</text>
      <text x="500" y="52" class="srcs">the structural world</text>
      <path d="M 460 64 C 400 110 300 110 250 140" class="arr" :class="{ win: s >= 1 }" marker-end="url(#df-ah)" />
      <path d="M 540 64 C 600 110 700 110 750 140" class="arr" :class="{ lose: s >= 1 }" marker-end="url(#df-ah)" />
    </svg>
    <div class="grid2">
      <div class="opt" :class="{ win: s >= 1 }">
        <div class="tag">Option A</div>
        <h3>Convert automatically</h3>
        <p>Stay as close as possible to the structural source. XSD → SHACL → OWL, by a converter.</p>
        <div class="pros">✔ every valid XML message has an RDF twin<br>✔ repeatable when the XSD changes</div>
        <div class="risk">⚠ <b>Garbage in, garbage out.</b> The ontology inherits the XSD's naming and modelling.</div>
        <div v-if="s >= 1" class="badge fade">🏭 most support in ERA's industry consultation</div>
      </div>
      <div class="opt" :class="{ lose: s >= 1 }">
        <div class="tag">Option B</div>
        <h3>Model the concepts by hand</h3>
        <p>Build a true ontology of the conceptual world, then map the XSD onto it.</p>
        <div class="pros">✔ clean concepts, aligned with the ERA core<br>✔ meaning decided by people</div>
        <div class="risk">⚠ <b>RDF and XML drift apart.</b> Not every message fits the model, and the mapping must be maintained.</div>
      </div>
    </div>
    <div v-if="s >= 2" class="card mt-2 fade">So the quality of the telematics ontology is, by design, the quality of the XSDs, <b>plus whatever post-processing repairs</b>.</div>
  </div>
</template>

<style scoped>
.cv { width: 100%; height: 96px; display: block; }
.src { fill: var(--c-surface); stroke: var(--c-stroke); stroke-width: 2; }
.srct { font-size: 17px; font-weight: 700; text-anchor: middle; fill: var(--c-ink); }
.srcs { font-size: 13px; text-anchor: middle; fill: var(--c-ink-mod); }
.arr { fill: none; stroke: var(--c-ink-mod); stroke-width: 3; transition: all .6s; }
.arr.win { stroke: var(--c-green); stroke-width: 6; }
.arr.lose { opacity: .25; stroke-dasharray: 6 6; }
.grid2 { display: grid; grid-template-columns: 1fr 1fr; gap: 24px; margin-top: -6px; }
.opt { background: var(--c-surface); border-radius: 12px; padding: 10px 16px; border-top: 4px solid var(--c-stroke); font-size: 13.5px; line-height: 1.35; transition: all .6s; position: relative; }
.opt h3 { margin: 2px 0 4px; color: var(--c-heading); font-size: 20px; }
.opt p { margin: 0 0 4px; line-height: 1.35; }
.opt.win { border-top-color: var(--c-green); box-shadow: 0 0 0 2px var(--c-green); }
.opt.lose { opacity: .45; }
.tag { display: inline-block; font-size: 11px; font-weight: 700; padding: 1px 8px; border-radius: 9px; background: var(--c-blue-bg); color: var(--c-blue-strong); }
.pros { color: var(--c-green-ink); font-size: 13px; }
.risk { margin-top: 6px; background: var(--c-amber-bg); color: var(--c-amber-ink); padding: 4px 8px; border-radius: 6px; font-size: 13px; }
.badge { margin-top: 8px; font-weight: 700; color: var(--c-green-ink); background: var(--c-green-bg); border-radius: 8px; padding: 3px 10px; display: inline-block; font-size: 13px; }
.fade { animation: fadein .7s ease-out; }
@keyframes fadein { from { opacity: 0 } to { opacity: 1 } }
</style>
