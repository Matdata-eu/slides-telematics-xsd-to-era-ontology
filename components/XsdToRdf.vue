<script setup lang="ts">
import { computed } from 'vue'

/**
 * The TAF TSI LocationIdent (taf_cat_complete.xsd) and what xsd2rdf makes of it, simplified.
 * Each line carries a group id; step n lights up group n in all three columns at once.
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(4, props.step)))

type L = { t: string; g?: number; i?: number }
const xsd: L[] = [
  { t: '<xs:complexType name="LocationIdent">', g: 1 },
  { t: '<xs:documentation>Indication of the', g: 4, i: 1 },
  { t: '  Railway or Customer Location', g: 4, i: 1 },
  { t: '</xs:documentation>', g: 4, i: 1 },
  { t: '<xs:sequence>', i: 1 },
  { t: '<xs:element ref="CountryCodeISO"/>', g: 2, i: 2 },
  { t: '<xs:element ref="LocationPrimaryCode"/>', g: 2, i: 2 },
  { t: '<xs:element ref="PrimaryLocationName"', g: 3, i: 2 },
  { t: '            minOccurs="0"/>', g: 3, i: 2 },
  { t: '</xs:sequence>', i: 1 },
  { t: '</xs:complexType>' },
]
const shacl: L[] = [
  { t: 'shape:LocationIdent a sh:NodeShape ;', g: 1 },
  { t: 'sh:targetClass era:LocationIdent ;', g: 1, i: 1 },
  { t: 'sh:description "Indication of …" ;', g: 4, i: 1 },
  { t: 'sh:property [', i: 1 },
  { t: 'sh:path era:locationPrimaryCode ;', g: 2, i: 2 },
  { t: 'sh:minCount 1 ; sh:maxCount 1 ] ;', g: 2, i: 2 },
  { t: 'sh:property [', i: 1 },
  { t: 'sh:path era:primaryLocationName ;', g: 3, i: 2 },
  { t: 'sh:minCount 0 ; sh:maxCount 1 ] .', g: 3, i: 2 },
]
const owl: L[] = [
  { t: 'era:LocationIdent a owl:Class ;', g: 1 },
  { t: 'rdfs:label "Location ident"@en ;', g: 4, i: 1 },
  { t: 'rdfs:comment "Indication of the …" .', g: 4, i: 1 },
  { t: '' },
  { t: 'era:locationPrimaryCode', g: 2 },
  { t: 'a owl:DatatypeProperty ,', g: 2, i: 1 },
  { t: '  owl:FunctionalProperty ;', g: 2, i: 1 },
  { t: 'rdfs:domain era:LocationIdent .', g: 2, i: 1 },
  { t: 'era:primaryLocationName …', g: 3 },
]
const cols = [
  { id: 'xsd', title: 'XSD', sub: 'the form template', lines: xsd },
  { id: 'shacl', title: 'SHACL', sub: 'the rules', lines: shacl },
  { id: 'owl', title: 'OWL', sub: 'the concepts', lines: owl },
]
const visible = (c: string) => c === 'xsd' || s.value >= 1
const cls = (l: L) => (l.g && l.g <= s.value ? `g${l.g}` + (l.g === s.value ? ' now' : '') : '')

const captions = [
  'A real piece of the TAF TSI XSD: the type used to identify a location in messages.',
  'A group of boxes becomes a <b>class</b>. The class name is the type name.',
  'A box with a value becomes a <b>property</b>. "Exactly once" becomes a rule, and in OWL a <b>functional property</b>.',
  '<code>minOccurs="0"</code> becomes <b>optional</b> (<code>sh:minCount 0</code>).',
  'The XSD documentation becomes the <b>definition</b>. The label is made by splitting the name: "Location ident".',
]
</script>

<template>
  <div class="x2r">
    <div class="cap" v-html="captions[s]" />
    <div class="cols">
      <template v-for="(c, ci) in cols" :key="c.id">
        <div class="col" :class="{ off: !visible(c.id) }">
          <div class="hd"><b>{{ c.title }}</b> <span>{{ c.sub }}</span></div>
          <pre><code><span v-for="(l, li) in c.lines" :key="li" class="ln" :class="cls(l)" :style="{ paddingLeft: (l.i ?? 0) * 10 + 'px' }">{{ l.t || ' ' }}</span></code></pre>
        </div>
        <div v-if="ci < 2" class="arr" :class="{ off: !visible(cols[ci + 1].id) }">→</div>
      </template>
    </div>
  </div>
</template>

<style scoped>
.cap { min-height: 30px; font-size: 16px; margin-bottom: 8px; }
.cols { display: flex; align-items: stretch; gap: 4px; }
.col { flex: 1; background: var(--c-surface); border-radius: 10px; padding: 8px 10px; border: 1px solid var(--c-stroke); transition: opacity .5s; min-width: 0; }
.col.off, .arr.off { opacity: 0; }
.hd { font-size: 15px; color: var(--c-heading); margin-bottom: 4px; }
.hd span { font-size: 12px; color: var(--c-ink-mod); margin-left: 6px; }
.arr { display: flex; align-items: center; font-size: 26px; color: var(--c-ink-mod); transition: opacity .5s; }
pre { margin: 0; background: transparent !important; }
code { font-size: 10.5px; line-height: 1.55; font-family: ui-monospace, Menlo, Consolas, monospace; }
.ln { display: block; white-space: pre-wrap; word-break: break-word; border-radius: 4px; color: var(--c-ink-mod); transition: background .4s, color .4s; margin-bottom: 1px; }
.ln.g1 { color: var(--c-ink); background: color-mix(in srgb, var(--c-blue) 16%, transparent); }
.ln.g2 { color: var(--c-ink); background: color-mix(in srgb, var(--c-green) 18%, transparent); }
.ln.g3 { color: var(--c-ink); background: color-mix(in srgb, var(--c-amber) 22%, transparent); }
.ln.g4 { color: var(--c-ink); background: color-mix(in srgb, var(--c-purple) 18%, transparent); }
.ln.now { font-weight: 700; }
</style>
