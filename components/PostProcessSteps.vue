<script setup lang="ts">
import { computed } from 'vue'

/**
 * The post-processing chain of the telematics-ontology repo (run-pipeline.sh, post-processing/*).
 * Each step lights up one card and shows a real before -> after example from the code or the v4.0.0 output.
 */
const props = withDefaults(defineProps<{ step?: number }>(), { step: 0 })
const s = computed(() => Math.max(0, Math.min(6, props.step)))

const steps = [
  {
    n: 1, icon: '⚙️', title: 'Convert per source', file: 'pipeline-lib.sh',
    what: 'Each XSD on its own: validate with xmllint, then xsd2rdf into the ERA namespace. Same name used as value and as link? Keep both apart with a technical suffix.',
    before: '<TrainLocation> in TAF TSI 3.5', after: 'era:trainLocationOP',
  },
  {
    n: 2, icon: '🔗', title: 'Rename to ERA v3.x terms', file: 'iris-to-replace.csv',
    what: 'A hand-made list maps XSD names onto existing ERA ontology terms. This is where telematics meets RINF.',
    before: 'era:LocationIdent', after: 'era:PrimaryLocation',
  },
  {
    n: 3, icon: '📚', title: 'Tidy the code lists', file: 'common.py',
    what: 'Strip "CodeListFor…", "…CodeList", "…Enumeration", a leading "The". Stable IRIs, one file per list, notations as plain strings, a dct:identifier.',
    before: 'CodeListFor… · …Enumeration', after: 'era-skos-<Name>.ttl',
  },
  {
    n: 4, icon: '🧷', title: 'Add the ERA cross-links', file: 'constraint_queries/*.rq',
    what: 'Every property gets a link to its code list and to its validation rule, the same annotations the ERA core uses, so the documentation treats telematics like RINF.',
    before: 'property … shape … code list', after: 'era:inSkosConceptScheme · era:shaclShapeValidationRule',
  },
  {
    n: 5, icon: '✂️', title: 'Remove the technical suffixes', file: 'normalize_property_iris.py',
    what: 'One plan across all sources at once. Unambiguous: drop the suffix. Real clash between a value and a link: …Value and …Reference. Fails loudly if a clash would remain.',
    before: 'era:tariffDP · era:tariffOP', after: 'era:tariffValue · era:tariffReference',
  },
  {
    n: 6, icon: '🌳', title: 'Merge, group, check', file: 'apply_pg_hierarchy.py',
    what: 'Merge into a telematics and a NeTEx bundle. Build one "Telematics parameters" tree for the ERADoco app guide, like RINF/EVR, and drop self-loops that made it recurse forever.',
    before: 'X subPropertyOf X', after: 'era:telematicsData_pg · era:telematicsObject_pg',
  },
]
const cur = computed(() => (s.value >= 1 ? steps[s.value - 1] : null))
</script>

<template>
  <div class="pp">
    <div class="cards">
      <div v-for="st in steps" :key="st.n" class="c" :class="{ act: s === st.n, done: s > st.n }">
        <div class="n">{{ st.n }}</div>
        <div class="ic">{{ st.icon }}</div>
        <div class="t">{{ st.title }}</div>
      </div>
    </div>
    <div class="detail">
      <template v-if="cur">
        <div class="what">{{ cur.what }}</div>
        <div class="chip">
          <code class="b">{{ cur.before }}</code>
          <span class="ar">→</span>
          <code class="a">{{ cur.after }}</code>
        </div>
        <div class="file">telematics-ontology · <code>{{ cur.file }}</code></div>
      </template>
      <template v-else>
        <div class="what intro">Six steps, all in the <b>telematics-ontology</b> repository, run after xsd2rdf. They are where <b>people's decisions</b> enter the ontology.</div>
      </template>
    </div>
  </div>
</template>

<style scoped>
.cards { display: grid; grid-template-columns: repeat(6, 1fr); gap: 10px; margin-top: 6px; }
.c { background: var(--c-surface); border-radius: 10px; padding: 10px 8px 12px; text-align: center; border: 2px solid var(--c-stroke); transition: all .4s; position: relative; }
.c .n { position: absolute; top: 6px; left: 8px; font-size: 12px; font-weight: 800; color: var(--c-ink-mod); }
.c .ic { font-size: 26px; }
.c .t { font-size: 14px; font-weight: 700; color: var(--c-heading); line-height: 1.2; margin-top: 4px; }
.c.act { border-color: var(--c-amber); background: var(--c-amber-bg); transform: translateY(-6px); box-shadow: 0 6px 16px rgba(0,0,0,.15); }
.c.done { border-color: var(--c-green); }
.detail { margin-top: 22px; min-height: 190px; background: var(--c-surface); border-radius: 12px; padding: 16px 20px; border-top: 4px solid var(--c-amber); }
.what { font-size: 17px; line-height: 1.45; }
.what.intro { font-size: 19px; }
.chip { margin-top: 16px; display: flex; align-items: center; gap: 14px; flex-wrap: wrap; }
.chip code { font-size: 15px; padding: 6px 12px; border-radius: 8px; }
.chip .b { background: var(--c-red-bg); color: var(--c-red-ink); }
.chip .a { background: var(--c-green-bg); color: var(--c-green-ink); font-weight: 700; }
.ar { font-size: 24px; color: var(--c-ink-mod); }
.file { margin-top: 12px; font-size: 12px; color: var(--c-ink-mod); }
</style>
