<script setup lang="ts">
import { computed } from 'vue'

/**
 * Overlap between ERA core (_core.ttl) and the telematics bundle on tag v4.0.0: era: IRIs typed in both files.
 * 12 terms (plus the ontology IRI itself). The "to discuss" list is post-processing/iris-to-replace-to-discuss.csv.
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(2, props.step)))

const shared = ['PrimaryLocation', 'SubsidiaryLocation', 'Vehicle', 'primaryLocationName', 'freightFlag', 'passengerFlag',
  'containerHandlingFlag', 'handoverPointFlag', 'direction', 'letterMarking', 'faxNumber', 'scope']
const open = [
  ['CompanyCode', 'org:Organization'],
  ['RollingRoadUnit', 'era:Vehicle'],
  ['companyCode', 'era:imCode'],
  ['?', 'era:linkedToPrimaryLocation'],
  ['?', 'era:hasOrganisationRole'],
  ['?', 'era:allocationCompany'],
]
</script>

<template>
  <div class="vn">
    <svg viewBox="0 0 560 330" class="cv">
      <circle cx="170" cy="165" r="120" class="core" />
      <circle cx="380" cy="165" r="160" class="tel" />
      <text x="140" y="160" class="cl k-core">ERA core</text>
      <text x="140" y="180" class="cs">935 classes</text>
      <text x="140" y="195" class="cs">and properties</text>
      <text x="440" y="160" class="cl k-tel">Telematics</text>
      <text x="440" y="180" class="cs">3,626 classes</text>
      <text x="440" y="195" class="cs">and properties</text>
      <text x="255" y="180" class="big">12</text>
      <text x="255" y="204" class="cs mid">shared</text>
    </svg>
    <div class="side">
      <div class="lh">Same IRI, declared in both. Possible because telematics uses the <code>era:</code> namespace and the rename list:</div>
      <div class="tags"><code v-for="t in shared" :key="t">{{ t }}</code></div>
      <div v-if="s >= 1" class="fade">
        <div class="lh mt">Still <b>"to discuss"</b>, not applied in v4.0:</div>
        <table class="td">
          <tr v-for="r in open" :key="r[1]"><td><code>{{ r[0] }}</code></td><td>→</td><td><code>{{ r[1] }}</code></td></tr>
        </table>
      </div>
      <div v-if="s >= 2" class="card warn fade">In the ERA ontology repo, the conflict analysis and the SHACL / OWL / SKOS checks on these bundles are <b>informational only</b>. They never fail the build.</div>
    </div>
  </div>
</template>

<style scoped>
.vn { display: grid; grid-template-columns: 1fr 1fr; gap: 18px; align-items: start; }
.cv { width: 100%; height: 330px; }
.core { fill: color-mix(in srgb, var(--c-blue) 18%, transparent); stroke: var(--c-blue); stroke-width: 3; }
.tel { fill: color-mix(in srgb, var(--c-amber) 16%, transparent); stroke: var(--c-amber); stroke-width: 3; }
.cl { font-size: 20px; font-weight: 800; text-anchor: middle; }
.k-core { fill: var(--c-blue); } .k-tel { fill: var(--c-amber-ink); }
.cs { font-size: 12px; fill: var(--c-ink-mod); text-anchor: middle; }
.big { font-size: 46px; font-weight: 800; fill: var(--c-heading); text-anchor: middle; }
.lh { font-size: 14px; margin-bottom: 6px; }
.lh.mt { margin-top: 12px; }
.tags { display: flex; flex-wrap: wrap; gap: 5px; }
.tags code { font-size: 11.5px; background: var(--c-green-bg); color: var(--c-green-ink); padding: 1px 7px; border-radius: 6px; }
.td { font-size: 12.5px; border: none; }
.td td { padding: 1px 6px !important; border: none !important; }
.td code { font-size: 12px; }
.card { margin-top: 12px; font-size: 13.5px; }
.warn { border-left-color: var(--c-amber); background: var(--c-amber-bg); }
.fade { animation: fadein .7s ease-out; }
@keyframes fadein { from { opacity: 0 } to { opacity: 1 } }
</style>
