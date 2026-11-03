<script setup lang="ts">
import { computed } from 'vue'

/**
 * Elements named after their role in a message all reuse the XSD type LocationIdent.
 * The release candidates (tag v4.0.0) renamed LocationIdent to era:PrimaryLocation: 27 object properties with
 * range era:PrimaryLocation and 8 subclasses of it (counted with rdflib). The refactored telematics-ontology
 * pipeline (rule 30-harmonised/10-location-ident, #80) keeps LocationIdent as a reference record instead: the
 * 27 references (26 TAF, 1 B5) keep range era:LocationIdent, the 8 TAF classes stay subclasses of it, its
 * code properties go, and era:refersToLocation points it at era:PrimaryLocation ⊔ era:SubsidiaryLocation by RINF IRI.
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(3, props.step)))

const propsIn = [
  'activityLocationIdent', 'arrivalTrackAtLocation', 'associatedAttachedLocationIdent', 'chargingSectionEnd',
  'chargingSectionStart', 'checkingLocation', 'consignmentDestination', 'delayLocation', 'departureJourneyTrack',
  'departureTrackAtLocation', 'destinationLocation', 'differentAcceptancePoint', 'intermediateDestination', 'location',
  'locationActualTrack', 'locationPlannedTrack', 'nextLocation', 'noteLocationIdent', 'pathDepartureLocation',
  'sectionEndLocation', 'sectionFrom', 'sectionStartLocation', 'sectionTo', 'shippingLocation', 'trainLocation',
  'transferPoint', 'wagonEventLocation',
]
const tracks = new Set(['arrivalTrackAtLocation', 'departureJourneyTrack', 'departureTrackAtLocation', 'locationActualTrack', 'locationPlannedTrack'])
const firstFew = new Set(['sectionFrom', 'sectionTo', 'delayLocation', 'departureTrackAtLocation', 'destinationLocation', 'locationPlannedTrack'])
const subclasses = ['Station', 'JourneySectionOrigin', 'JourneySectionDestination', 'StartOfSection', 'EndOfSection', 'PaidUpTo', 'PlannedJourneyLocation', 'PathPlanningReferenceLocation']

const C = { x: 500, y: 195 }
const pts = propsIn.map((name, i) => {
  const a = -Math.PI / 2 + (2 * Math.PI * i) / propsIn.length
  const x = C.x + 300 * Math.cos(a)
  let y = C.y + 172 * Math.sin(a) + (Math.sin(a) > 0.9 ? (i % 2 ? 12 : -2) : 0)
  const ex = C.x + 158 * Math.cos(a), ey = C.y + 62 * Math.sin(a)
  if (name === 'locationPlannedTrack') y += 16
  return { name, x, y, ex, ey, anchor: Math.abs(Math.cos(a)) < 0.05 ? 'middle' : Math.cos(a) > 0 ? 'start' : 'end' }
})
const subs = subclasses.map((name, i) => {
  const a = -Math.PI / 2 + (2 * Math.PI * i) / subclasses.length
  const x = C.x + 340 * Math.cos(a), y = C.y + 150 * Math.sin(a)
  return { name, x, y, ex: C.x + 162 * Math.cos(a), ey: C.y + 66 * Math.sin(a) }
})
const showProp = (n: string) => s.value >= 1 || firstFew.has(n)
const caps = [
  'In the XSD, many elements reuse the type <code>LocationIdent</code>, and are named after their <b>role in a message</b>.',
  'Post-processing keeps <code>LocationIdent</code> as a <b>reference</b>: <b>27 properties</b> point to it, and it points to the RINF location with <code>era:refersToLocation</code>. (The release candidates renamed it to <code>era:PrimaryLocation</code>.)',
  'Five of them describe a <b>track</b>. XSD documentation: <i>"Indicates the track ID on which the train runs"</i>. But a location reference can only point to a <b>primary or subsidiary location</b>, not to a track.',
  'And <code>extension base="LocationIdent"</code> means "is a": <b>8 subclasses</b> of a location reference. A station is not a kind of reference, and the others are a location <b>playing a role</b>.',
]
</script>

<template>
  <div class="hubw">
    <div class="cap" v-html="caps[s]" />
    <svg viewBox="-20 0 1040 400" class="cv">
      <defs>
        <marker id="rh-ah" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="5" markerHeight="5" orient="auto">
          <path d="M0,0 L10,5 L0,10 z" style="fill: var(--c-ink-subtle)" />
        </marker>
        <marker id="rh-ahr" viewBox="0 0 10 10" refX="9" refY="5" markerWidth="5" markerHeight="5" orient="auto">
          <path d="M0,0 L10,5 L0,10 z" style="fill: var(--c-red)" />
        </marker>
        <marker id="rh-tri" viewBox="0 0 12 12" refX="11" refY="6" markerWidth="9" markerHeight="9" orient="auto">
          <path d="M0,0 L12,6 L0,12 z" style="fill: var(--c-bg); stroke: var(--c-ink-mod); stroke-width: 1.5" />
        </marker>
      </defs>
      <g :class="['props', { dim: s >= 3 }]">
        <g v-for="p in pts" :key="p.name" :class="{ hide: !showProp(p.name), red: s >= 2 && tracks.has(p.name) }" class="pg">
          <line :x1="p.x" :y1="p.y" :x2="p.ex" :y2="p.ey" class="ln" :marker-end="s >= 2 && tracks.has(p.name) ? 'url(#rh-ahr)' : 'url(#rh-ah)'" />
          <text :x="p.x" :y="p.y + 4" :text-anchor="p.anchor" class="pt">{{ p.name }}</text>
        </g>
      </g>
      <g v-if="s >= 3" class="fade">
        <g v-for="c in subs" :key="c.name">
          <line :x1="c.x" :y1="c.y" :x2="c.ex" :y2="c.ey" class="isa" marker-end="url(#rh-tri)" />
          <rect :x="c.x - 118" :y="c.y - 14" width="236" height="28" rx="6" class="sc bad" />
          <text :x="c.x" :y="c.y + 5" class="sct">✘ {{ c.name }}</text>
        </g>
      </g>
      <g class="hub">
        <ellipse :cx="C.x" :cy="C.y" rx="154" ry="58" :class="{ pl: s >= 1 }" />
        <text :x="C.x" :y="s >= 1 ? C.y - 18 : C.y + 6" class="ht">{{ s >= 1 ? 'era:LocationIdent' : 'LocationIdent' }}</text>
        <g v-if="s >= 1" class="fade">
          <text :x="C.x" :y="C.y + 4" class="rl">↓ era:refersToLocation</text>
          <text :x="C.x" :y="C.y + 24" class="rt">PrimaryLocation ⊔</text>
          <text :x="C.x" :y="C.y + 40" class="rt">SubsidiaryLocation</text>
        </g>
      </g>
      <text v-if="s >= 1 && s < 3" x="1015" y="392" class="count">27 object properties with range era:LocationIdent</text>
    </svg>
  </div>
</template>

<style scoped>
.cap { min-height: 46px; font-size: 15.5px; line-height: 1.35; }
.cv { width: 100%; height: 400px; display: block; margin-top: -4px; }
.pg { transition: opacity .6s; }
.pg.hide { opacity: 0; }
.props { transition: opacity .6s; }
.props.dim { opacity: .07; }
.ln { stroke: var(--c-ink-subtle); stroke-width: 1.2; }
.pt { font-size: 12px; fill: var(--c-ink-mod); font-family: ui-monospace, Menlo, Consolas, monospace; }
.pg.red .ln { stroke: var(--c-red); stroke-width: 2.2; }
.pg.red .pt { fill: var(--c-red); font-weight: 700; }
.hub ellipse { fill: var(--c-surface); stroke: var(--c-ink-mod); stroke-width: 3; transition: all .6s; }
.hub ellipse.pl { fill: var(--c-blue-bg); stroke: var(--c-blue); }
.ht { font-size: 16px; font-weight: 800; text-anchor: middle; fill: var(--c-heading); font-family: ui-monospace, Menlo, Consolas, monospace; }
.rl { font-size: 12px; text-anchor: middle; fill: var(--c-ink-mod); font-family: ui-monospace, Menlo, Consolas, monospace; }
.rt { font-size: 12px; font-weight: 700; text-anchor: middle; fill: var(--c-blue); font-family: ui-monospace, Menlo, Consolas, monospace; }
.isa { stroke: var(--c-ink-mod); stroke-width: 1.5; }
.sc { stroke-width: 2; }
.sc.ok { fill: var(--c-green-bg); stroke: var(--c-green); }
.sc.bad { fill: var(--c-red-bg); stroke: var(--c-red); }
.sct { font-size: 12.5px; font-weight: 700; text-anchor: middle; fill: var(--c-ink); font-family: ui-monospace, Menlo, Consolas, monospace; }
.count { font-size: 12px; text-anchor: end; fill: var(--c-ink-mod); }
.fade { animation: fadein .7s ease-out; }
@keyframes fadein { from { opacity: 0 } to { opacity: 1 } }
</style>
