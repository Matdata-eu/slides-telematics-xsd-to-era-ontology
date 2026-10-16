<script setup lang="ts">
import { computed } from 'vue'

/**
 * NeTEx in ERA ontology v4.0.0 (domains/telematics/artefacts/dist/netex/ontology.ttl), counted with rdflib:
 * 3,451 named classes, 2,552 of them named "…Structure". The deepest subclass chain has 14 levels and runs over
 * schema structures; the class netex:Quay itself has no superclass at all.
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(2, props.step)))

const chain = [
  'TaxiStand_VersionStructure', 'Quay_VersionStructure', 'StopPlaceSpace_VersionStructure', 'StopPlaceComponent_VersionStructure',
  'SiteComponent_VersionStructure', 'SiteElement_VersionStructure', 'AddressablePlace_VersionStructure', 'Place_VersionStructure',
  'Zone_VersionStructure', 'GroupOfPoints_VersionStructure', 'GroupOfEntities_VersionStructure', 'DataManagedObjectStructure',
  'EntityInVersionStructure', 'BasicModificationDetailsGroup',
]
const bars = [
  { label: '…_RelStructure', n: 749, hint: 'a list of relations' },
  { label: '…RefStructure', n: 783, hint: 'a pointer to something' },
  { label: '…_VersionStructure', n: 556, hint: 'the fields of a versioned thing' },
  { label: 'other …Structure', n: 464, hint: '' },
  { label: 'everything else', n: 899, hint: 'Quay, StopPlace, ServiceJourney, …' },
]
const total = 3451
</script>

<template>
  <div class="nx">
    <div class="left">
      <div class="big"><b>3,451</b> classes, and <b>2,552</b> (74 %) of them are named "…Structure"</div>
      <div class="bars">
        <div v-for="b in bars" :key="b.label" class="bar">
          <div class="lb"><code>{{ b.label }}</code> <span>{{ b.hint }}</span></div>
          <div class="track"><div class="fill" :class="{ real: b.label === 'everything else' }" :style="{ width: (100 * b.n / total) + '%' }" /><span class="n">{{ b.n }}</span></div>
        </div>
      </div>
      <div v-if="s >= 2" class="quay fade">
        The real thing, <code>netex:Quay</code>, has <b>no superclass at all</b>.<br>
        <span class="muted">Its structure lives in <code>Quay_VersionStructure</code>, which sits under 12 more levels of schema structures.</span>
      </div>
    </div>
    <div class="right" :class="{ off: s < 1 }">
      <div class="lh">Deepest "is a" chain in the NeTEx bundle: <b>14 levels</b></div>
      <div v-for="(c, i) in chain" :key="c" class="rung" :style="{ marginLeft: i * 7 + 'px' }" :class="{ hl: c === 'Quay_VersionStructure' && s >= 2 }">
        <span class="i">{{ i === 0 ? '' : '⊂' }}</span><code>{{ c }}</code>
      </div>
    </div>
  </div>
</template>

<style scoped>
.nx { display: grid; grid-template-columns: 1.05fr 1fr; gap: 26px; }
.big { font-size: 17px; margin-bottom: 10px; }
.bar { margin-bottom: 7px; }
.lb { font-size: 12.5px; } .lb span { color: var(--c-ink-mod); margin-left: 6px; }
.lb code { font-size: 12px; }
.track { position: relative; height: 16px; background: var(--c-surface); border-radius: 4px; }
.fill { height: 100%; background: var(--c-purple); border-radius: 4px; opacity: .75; }
.fill.real { background: var(--c-green); }
.n { position: absolute; right: 6px; top: -1px; font-size: 12px; font-weight: 700; }
.quay { margin-top: 14px; padding: 8px 12px; background: var(--c-amber-bg); border-left: 4px solid var(--c-amber); border-radius: 6px; font-size: 14px; }
.right { transition: opacity .6s; }
.right.off { opacity: 0; }
.lh { font-size: 14px; margin-bottom: 6px; }
.rung { font-size: 11.5px; line-height: 1.6; }
.rung code { font-size: 11px; background: var(--c-surface); padding: 0 6px; border-radius: 4px; }
.rung .i { display: inline-block; width: 16px; color: var(--c-ink-mod); }
.rung.hl code { background: var(--c-amber-bg); outline: 2px solid var(--c-amber); }
.muted { color: var(--c-ink-mod); }
.fade { animation: fadein .7s ease-out; }
@keyframes fadein { from { opacity: 0 } to { opacity: 1 } }
</style>
