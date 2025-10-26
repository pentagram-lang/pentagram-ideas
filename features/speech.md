# 1 — First principles (ground rules)

1. **Reasoning-first**: Programming is an exercise in externalized thought. Language features must make local reasoning straightforward and minimize the need for background archaeology. (This is the engine behind ergonomics and determinism.)
2. **Sense-agnostic clarity**: Representations should be unambiguous regardless of sensory channel (screen, voice, or internal LLM tokens). The same program meaning must be extractable by ear as by sight.
3. **Composability & locality**: Small, composable units whose behavior is discoverable from local context (no hidden global state). This reduces cognitive load for people and the cost of model inference for LLMs.
4. **Action explicitness**: Side effects, I/O, and mutation must be explicit and isolated so that spoken descriptions and LLM reasoning can surface them without guesswork.
5. **Predictable ergonomics**: Common operations should be short and predictable in both speech and text — think “Huffman” for common words/phrases so that spoken tokens are easy to say and hear.

---

# 2 — Core tensions to resolve

- **Brevity vs clarity in speech**: terse code reads well in text but can be ambiguous when spoken; verbose code is clearer but slower to speak and hear.
- **Linear audio vs spatial text**: audio is sequential and ephemeral; text is spatial and persistent. Language design must support recovering structure from linear audio.
- **Human language variance vs formal precision**: Spoken interactions require forgiving surface forms (paraphrase, synonyms) but the underlying semantics must remain strict for deterministic execution.
- **Tooling dependency**: Voice-first workflows depend on tooling (history, undo, structure navigation). The language must be easy to parse and transform by these tools.

---

# 3 — Users & personas (short)

- **Expert unsighted developer**: fluent in programming, prefers keyboard + speech, needs fast navigation and fine-grained control.
- **Novice using voice-first**: learns by conversation with an LLM, expects helpful guided edits and safety nets.
- **LLM collaborator**: non-human “reader” that needs deterministic, regular syntax and explicit semantics to produce and verify code.

Design decisions should be validated against all three.

---

# 4 — Design dimensions & guidelines

## 4.1 Notation & token design (speech-aware)

- **Phonetic stability**: Avoid tokens that are hard to disambiguate in speech (e.g., punctuation-heavy constructs that read the same when spoken). Prefer words or short spoken symbols that are distinct.
- **Canonical spoken forms**: Define a canonical spoken phrase for each syntactic construct (e.g., “begin block” vs “open brace”) and ensure tooling maps synonyms to that form.
- **Audible separators**: Use clearly speakable separators for nesting (spoken markers like “then” / “end”) so structure can be reconstructed from a single linear stream.
- **Avoid visually dense cues as sole structure**: Don’t rely on indentation, color, or small glyph differences to carry semantic weight that speech cannot convey.

## 4.2 Local reasoning & determinism

- **Explicit data flow**: Prefer constructs where data flow is visible locally (function returns, explicit pipelines) rather than hidden mutation. This reduces the need for the listener to remember previous context.
- **No implicit global state**: Global state increases the burden on memory during audio sessions; side effects must be explicit and localized to named, audibly referenced constructs.
- **Error signals must be verbalizable**: Error types and propagation should be describable in concise speech so a user can reason about them when debugging by voice.

## 4.3 Ergonomics for non-sight

- **Linear navigability primitives**: Provide language and tooling hooks that support audio-friendly navigation: “go to next function”, “list parameters of current block”, “summarize this expression.” These are language-agnostic but the language must make them reliable (stable AST boundaries, predictable names).
- **Semantic chunking**: Make syntactic constructs correspond to semantic units that can be read, summarized, and edited as chunks (e.g., named expressions, self-contained statements).
- **Pronounceable identifiers**: Encourage (or enforce) identifier conventions that avoid confusable characters (underscores vs hyphens vs camelCase) or provide canonical spoken forms (e.g., “snake a b” → `a_b`).
- **Accessible literal representation**: Numeric, string, and binary literals should have clear spoken forms (e.g., hex spelled grouped and read with separators).

## 4.4 Voice-first interaction primitives

- **Command grammar**: Define a small, stable set of voice commands for structural edits (insert, replace, wrap, extract, move), each mapping deterministically to AST transformations.
- **Confirmable diffs**: After an LLM proposes an edit, present a concise spoken summary and an easy accept/reject mechanism. Always allow “undo last n edits” with a spoken count.
- **Plan-driven editing**: Let users state a plan (“I want to extract this into a function used in three places”) and have the LLM propose a sequence of atomic, explainable edits. The language should make those edits atomic and reversible.
- **Granular audit trail**: Maintain an auditable change log that can be queried by voice (“what changed in function X?”), with each entry mapped to explicit language constructs.

## 4.5 Tooling & affordances (language must be tooling-friendly)

- **Stable, round-trip AST**: Language should have a canonical AST that serializes/deserializes without losing user-visible structure — critical for voice + LLM edits.
- **Machine-friendly type & effect annotations**: Types, side-effect descriptions, and I/O capabilities should be expressible in forms that an LLM can reason about succinctly.
- **Verbose & compact presentations**: For speech, provide a “spoken mode” (expanded, explanatory) and a “concise mode” (compact for experts). Both should map to the same underlying code.

---

# 5 — Evaluation rubric (how to judge success)

For each candidate language feature or tooling choice, score/observe using these axes.

## Core axes (0–5, measurable where possible)

1. **Discoverability (spoken)** — Can a new unsighted user discover what the construct does by hearing a short description? (score by timed task + comprehension quiz)
2. **Local reasoning** — Can a user, given only a visible/audible local context (single function/block), predict the behavior with high confidence? (test with correctness probes)
3. **Editability via voice** — Can routine edits be expressed and executed reliably via voice commands without repeated clarification? (measure success rate)
4. **LLM tractability** — Does the LLM produce syntactically correct, minimal edits consistently? (measure edit iteration count to convergence)
5. **Undoability & safety** — Are edits reversible with low cognitive load? (measure time to revert & correctness)
6. **Performance overhead** — Does the feature add runtime or compile-time cost that violates efficiency goals? (benchmarks)
7. **Cognitive load (audio)** — Use cognitive load tests (subjective + task time + error rate) for spoken workflows.

## Qualitative checks

- Does the feature require visual inspection as the primary way to understand it? (Fail)
- Does it increase ambiguity when described in speech? (Fail)
- Is the syntax or semantics easily paraphrased for voice? (Pass)

---

# 6 — Concrete behavioral expectations (what to observe in a healthy system)

- **Single-shot edits**: LLM + voice can accomplish common refactorings (rename, extract, inline) in one or two spoken turns with deterministic AST changes.
- **Low back-and-forth**: The number of clarifying questions from the LLM should drop quickly as the language and toolchain provide stable primitives.
- **Predictable summaries**: The spoken summaries of code are concise, accurate, and map 1:1 to code fragments.
- **Fast error localization**: When errors occur, the combination of language diagnostics and voice UI lets the user localize and fix the issue with no more than a small number of steps.
- **Repeatable automation**: A plan expressed once can be executed repeatedly (idempotent refactor scripts generated by LLM are reliable).

---

# 7 — Tradeoffs & patterns (practical guidance)

- **Favor explicitness over implicit magic**: Implicit conversions or overloaded punctuation are cheap on screens but expensive in voice/LLM contexts.
- **Offer two representations**: human-friendly expanded view (for speech/explanation) and compressed view (for power-users). Keep them semantically identical.
- **Names matter more**: invest in naming conventions and tooling for spoken names — they become the primary anchors in the audio modality.
- **Composeability beats clever one-offs**: Small, orthogonal constructs are easier to explain, to transform, and to automate with LLMs.
- **Make state transitions audible**: When code mutates or performs I/O, tooling should produce a spokenable trace that the user can query.

---

# 8 — Example evaluation checklist (quick audit)

For any language feature, answer the following quickly:

- Can it be read aloud unambiguously in ≤ 10 seconds?
- Can an LLM map a spoken edit request to the correct AST node ≥ 90% of the time (measured)?
- Is the feature reversible without manual inspection?
- Does it preserve determinism (no hidden global effects)?
- Does it add measurable runtime overhead?
  If any “no” appears, require redesign or guardrails.

---

# 9 — Short roadmap for experiments (no implementation detail, just experiments)

1. **Spoken canonicalizer experiment**: Create a mapping from code → canonical spoken script and back; evaluate round-trip fidelity.
2. **Edit-intent benchmark**: Collect a suite of 50 common edits and measure the success rate of LLM+voice achieving them in ≤ 2 turns.
3. **Cognitive load study**: Time-to-comprehension of a function when presented in spoken canonical form vs. sighted text.
4. **Safety & undo trial**: Intentionally produce edits and measure time/cost to revert and recover prior behavior.

---

# 10 — Closing: what success looks like

Success is when an expert unsighted developer and an LLM can carry out nontrivial development cycles — read, modify, refactor, test — primarily by voice, with low error rates, short interaction loops, and predictable, deterministic results. The language will feel like it’s optimized for reasoning: audibly discoverable, locally explainable, and tooling-friendly. Those same properties will make LLM collaboration effortless because the model can depend on stable, explicit structure rather than heuristic guessing. This aligns directly with Pentagram’s goals of ergonomics, determinism, and efficiency.
