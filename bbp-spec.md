# BBP v1.0 — Basque Binary Protocol

## Neuro-Symbolic Interlingua for Machine Cognition and Inter-Agent Communication

**Version:** 1.0-draft-r39  
**Date:** 2026-03-12  
**Author:** Arkaitz Múgica <me@arkaitz.dev> 
**Status:** Working Draft

---

## Prologue

Natural language was not designed for machines. It evolved for human brains: ambiguous by default, positional rather than role-marked, tolerant of context-dependence that speakers resolve unconsciously in milliseconds. When AI agents communicate in natural language, they inherit all of that overhead — parsing uncertainty, pronoun ambiguity, scope confusion — on every message, at every hop. The field has known this for decades. For seventy years it has tried to solve it: interlingua-based machine translation collapsed because no rule-based system could resolve open-domain ambiguity; KQML and FIPA-ACL, the formal agent communication languages of the 1990s, were abandoned because their semantic grounding was unverifiable and their content layer left unstandardized; today's inter-agent protocols — MCP, A2A, ACP — acknowledge the problem and consciously defer it, using natural language payloads because LLMs can parse them, not because natural language is a good wire protocol.

BBP is a different bet. It takes the core insight that every failed predecessor was right about — that structured, role-marked semantic representation is a better substrate for machine cognition than surface-form text — and builds it on the one foundation prior systems lacked: a large language model capable of performing the disambiguation work at the boundary, so that everything downstream can be clean, typed, and deterministic. A binary, role-marked token stream where *who does what to whom, when, how, why, and under what epistemic conditions* is recoverable in O(1) from the bit layout. Think of a skilled professional translator working between two unrelated languages: the better the translator, the more faithfully the depth and precision of the original thought arrives on the other side — stripped of the source language's grammatical accidents, intact in its semantic content. BBP is the language of thought that sits at the centre: the Core Engine reasons in it natively; the Frontier Translators convert any human language into it and back out with maximum fidelity; agents that share it communicate without any translator at all. The translator analogy also identifies the system's critical bottleneck: a mediocre translator produces mediocre results regardless of the brilliance of the original thought. The Frontier Translator's training quality is the seam where the system is most exposed — which is why the corpus generation methodology (§15.4) is the most critical engineering artefact in the project. Not inferred from word order, not resolved by context, not approximated by a model that might be wrong. The architecture has predecessors. The specific combination does not. Whether it works is an empirical question. This document specifies the system precisely enough to find out.

---

### A note on implementation status and genesis

**This document is a theoretical specification.** No toolchain has been implemented. No Frontier Translator has been trained. No Core Engine exists. No empirical data supports or refutes the architecture's core claims. The compression ratios, curriculum phases, go/no-go criteria, and benchmark targets in this document are design specifications, not measured results.

**BBP is offered as a precise, falsifiable hypothesis about a class of AI architecture.** The hypothesis is: a neuro-symbolic system built on the specific combination of typed binary token streams, compositional field-level embeddings, and an Inverted Ground Truth corpus generation methodology will exhibit measurable advantages in reasoning accuracy, inter-agent communication efficiency, and semantic consistency over natural-language-native architectures of equivalent parameter count. Every claim in this document is either a formal specification (testable by inspection against a future implementation) or an empirical prediction (testable by training and evaluation). Neither class of claim has been validated.

**On the genesis of this specification.** The initial hypothesis was developed by the author. The specification has been elaborated iteratively through extended technical dialogue with large language model assistants (principally Claude by Anthropic, with stress-testing and cross-checking via Gemini by Google and ChatGPT by OpenAI). These AI systems contributed to formalizing the architecture, identifying internal contradictions, proposing design alternatives, and pressure-testing the specification's consistency across sections. This collaboration is disclosed because it is relevant to how the document should be read: LLM assistants can identify logical inconsistencies and formalize intuitions with high reliability, but cannot substitute for empirical validation. Their participation accelerates specification quality; it neither validates nor invalidates the underlying architecture. Whether anyone builds it is a separate question entirely.

---

## 1. Vision and Philosophy

The Basque Binary Protocol (BBP) is a **Language of Thought for artificial intelligence**: a typed, deterministic, binary representation of semantic structure designed to serve as the native cognitive substrate for AI systems and as the wire protocol for inter-agent communication. BBP originated as an attempt to apply the morphological structure of Euskera (Basque language) — specifically its ergativity and agglutination — to formal semantic encoding. As the design evolved to serve as a genuinely universal machine interlingua, it became clear that the Basque structure alone was insufficient: the case inventory had to expand well beyond Basque's own system, the token format adopted a fusional rather than agglutinative architecture (§1.1), and typological coverage was extended to encompass features absent from Basque entirely (noun classifiers, obligatory evidentiality, Bantu noun classes, fine honorific register). What BBP retains from Euskera is not the mechanism but the insight: ergativity and explicit role-marking make semantic structure transparent in ways that nominative-accusative, positional languages do not.

BBP is designed to:

- **Serve as native cognition substrate.** A purpose-built LLM that has never processed natural language text — that is born, trained, and operates exclusively in BBP — can reason over typed, role-marked semantic structures where every grammatical relationship is explicit in the bit layout. BBP is to this model what assembly language is to a CPU: not the substrate of thought itself, but a pre-structured representation that makes the work of cognition more efficient by eliminating the disambiguation overhead that natural language imposes. The transformer will project each 64-bit token into a high-dimensional embedding space where the bit fields become structured statistical signals — signals whose geometry is shaped by the compositional field structure (§15.5.1), not arbitrary as in BPE tokenization.

- **Enable deterministic inter-agent communication.** When AI agents communicate with each other, natural language is a lossy, ambiguous channel evolved for human brains. BBP provides a binary protocol where "who does what to whom, when, how, and why" is unambiguous at the bit level. Agents speaking BBP to each other never need to parse syntax, resolve pronoun ambiguity, or guess at scope — the structure is the message.

- **Eliminate syntactic ambiguity at the representation boundary.** At the frontier between human language and machine cognition, specialized Translator LLMs convert natural language into BBP's typed structures, performing the hard work of disambiguation once. Downstream systems inherit a clean, deterministic input.

- **Reduce processing cost as a secondary benefit.** A Transformer with O(N²) attention over N tokens benefits from reducing N. BBP collapses multi-token phrases into single 64-bit semantic units, reducing effective sequence length. But this compression is a consequence of the design, not its purpose. The primary value is structural clarity, not bandwidth savings.

**Core Concept:** "Euskera as the founding insight, compiled to machine code — a structured semantic encoding optimized for transformer ingestion, where structural rigidity enables boundless compositional creativity."

**Design Principle:** Convert natural language (ambiguous, positional, evolved for human cognition) into a typed data structure (deterministic, role-marked, designed for machine cognition) where every grammatical relationship is explicit in the bit layout and novel meaning emerges from valid structural combination.

### 1.1 Basque as Starting Point: Origin, Evolution, and What Was Retained

BBP began as a direct attempt to apply Basque morphology to binary semantic encoding. The initial hypothesis was that Basque's two structural properties — ergativity and agglutination — could serve as the complete architectural foundation. As the design was tested against typological breadth, it became clear that neither property could be taken wholesale.

**Agglutination was abandoned as the token-level mechanism.** BBP-32 (an earlier design) chained auxiliary tokens to express features that did not fit within the base token, directly mirroring Basque's affix-chaining strategy. The resulting specification accumulated ~2,000 lines of compensatory mechanisms (extended addressing, bound modifiers, pair tables) precisely because distributing information across adjacent tokens creates adjacency rules, stacking constraints, and assembly overhead that a computational consumer — unlike a human reader — gains nothing from. The current 64-bit format is architecturally fusional: each token carries all its grammatical information internally, without requiring adjacent tokens for basic feature expression. This mirrors Latin or Sanskrit more than Basque. The irony is productive: BBP was born from agglutinative inspiration, but optimization for its actual consumer led to a fusional design.

**Ergativity was retained and generalized.** The ergative-absolutive case distinction — marking the agent of a transitive verb differently from the subject of an intransitive — is the single most important structural decision inherited from Basque, and it survives intact in the current format. It is also the property with the most direct engineering application: explicit role-marking at the bit level makes "who does what to whom" recoverable in O(1) without contextual inference, regardless of word order or source language typology. Georgian, Tibetan, Quechua, or any of the hundreds of ergative languages would have offered the same structural starting point; Basque was the one the author knew.

**A note on the name.** Ergativity is not exclusive to Basque: it appears in Georgian, Tibetan, the Mayan language family, many Australian Aboriginal languages, and hundreds of Amerindian languages. BBP does not claim Basque as the unique source of this property. The protocol bears the name *Basque* because Basque is one of the languages spoken by the author, and it was through familiarity with Basque morphology that the ergative-absolutive distinction first became visible as a design principle worth encoding at the bit level. The name is an honest acknowledgment of intellectual origin, not a typological claim of uniqueness. Just as a scientific insight is no less valid for having been sparked by a particular place or language, the engineering value of explicit role-marking stands independently of which language first made it salient.

**The case inventory expanded far beyond Basque.** Basque has 15–17 productive cases depending on dialect. BBP defines 29 cases (with 3 reserved), covering spatial distinctions (superessive, sublative, delative, adessive), transformative roles (translative, essive), and discourse functions (topical, vocative) that Basque does not grammaticalize. The inventory reflects universal semantic needs, not Basque morphology.

**The two properties borrowed from Basque as initial design anchors:**

**Ergativity encodes agency explicitly.** A binary protocol needs to encode "who does what to whom" without relying on word order or contextual inference. Ergative-absolutive case systems solve this directly: the agent of a transitive verb and the subject of an intransitive are marked differently in the morphology, making the semantic role recoverable from the token itself. Basque does this as *Juan badoa* (Juan-∅ goes, absolutive subject of intransitive) vs. *Juanek ogia jaten du* (Juan-ERG bread-∅ eats, ergative agent of transitive). BBP adopts this marking strategy — not because it is the only valid one, but because it is coherent, well-attested across unrelated language families, and directly applicable to a typed bit-field design. The same principle could have been drawn from Georgian, Tibetan, Mayan, or Quechua equally well.

**Agglutination provides transparent compositionality.** Basque builds complex meanings by chaining well-defined morphological units: *etxe* (house) + *-ko* (genitive-locative) + *-ak* (plural definite) → *etxekoak* ("those of the house / family members"). Each morpheme contributes a predictable semantic increment. BBP mirrors this in its token architecture: each bit field (case, number, determiner, meta-type, FEATURES) contributes a defined semantic dimension, and any valid combination of field values produces an interpretable semantic unit — even if that specific combination was never seen during training.

**The synthetic model eliminates relational noise.**

Natural languages fall on a typological spectrum from **analytic** (relations expressed by separate words: English, Mandarin, French) to **synthetic** (relations expressed as morphology on the noun: Finnish, Turkish, Basque, Latin, Hungarian, Quechua).

In analytic languages, a sentence like "the book on the table of the teacher for the student" requires four relational words ("on", "of", "for" — with "of" appearing twice for different relations) plus the four content nouns. In synthetic languages, those same relations are suffixes on the nouns themselves: the content words carry their relational information internally, and no relational words are needed.

BBP adopts the synthetic model universally. Every BBP token carries its case field — its relational position in the proposition — in the bits. Prepositions, postpositions, case particles (Japanese が, を, に, で...) and similar relational markers are **not emitted as BBP tokens**. They are consumed by the Frontier Translator during encoding and converted into the appropriate `case` value on the nominal token they governed (see §12.0.2 for the normative preposition-to-case mapping).

This has three consequences that compound each other:

1. **Stream length.** Relational words that contributed no semantic content beyond their case role are eliminated entirely. A prepositional phrase of 3 BPE tokens ("on the table") becomes 1 BBP token (`[mesa, SUP, SINGULAR, DEF]`).

2. **Semantic precision.** In analytic languages, a single preposition can cover multiple semantic relations. English "in" covers static interior location (INE), temporal framing (TEMPORAL), and many idiomatic uses. "From" covers ABL, ELA, and source-of-information. In BBP, each of these is a distinct case value. The synthetic model forces disambiguation that analytic surface forms leave implicit.

3. **Cross-linguistic uniformity.** A BBP stream encoding a Finnish sentence and one encoding an English sentence about the same event are maximally convergent: the same case values, the same argument frames, the same compositional structure for all propositional content that both languages encode. The typological differences between the source languages — agglutinative morphology vs. prepositional phrases vs. case particles — are fully absorbed by the Frontier Translator and do not appear in the binary stream. This convergence is not perfect identity: information-structural distinctions (topic/focus), fine aspectual contrasts, and features that one language grammaticalizes and the other leaves implicit will produce streams that differ precisely where the source languages differ semantically. This is a feature, not a defect — those structural divergences in the BBP stream are honest reflections of real semantic differences, and they make BBP a richer pivot for translation than any surface-form intermediary. The closer the two source sentences are in propositional content, the more convergent the resulting BBP streams will be.

This is the operational meaning of "Euskera as founding insight, compiled to machine code": just as a compiler eliminates the surface differences between coding styles and produces uniform bytecode, the Frontier Translator (§1.2) eliminates the surface differences between language types and produces uniform BBP. The Basque contribution is the principle of explicit role-marking; the implementation is universal.

### 1.2 Layered Architecture

BBP is designed to operate within a multi-layer cognitive architecture where natural language exists only at the human-facing boundary:

```
┌─────────────────────────────────────────────────────────────────────┐
│                        HUMAN INTERFACE                              │
│                     (Natural Language I/O)                           │
└────────────────────────────┬────────────────────────────────────────┘
                             │
                    ┌────────▼────────┐
                    │   LAYER 1       │
                    │   Frontier      │   Translator LLMs (per-language
                    │   Translators   │   or per-family specialists)
                    │   NL → BBP-IR   │   Convert NL to BBP-IR (JSON)
                    │   BBP-IR → NL   │   Convert BBP-IR back to NL
                    └────────┬────────┘
                             │  BBP-IR (JSON)
                    ┌────────▼────────┐
                    │   VALIDATOR +   │   Axiomatic validation (§13.2.1)
                    │   COMPILER      │   Deterministic bit packing
                    └────────┬────────┘
                             │  BBP Binary (64-bit token stream)
          ┌──────────────────┼──────────────────┐
          │                  │                  │
 ┌────────▼────────┐ ┌──────▼───────┐ ┌───────▼────────┐
 │   LAYER 2       │ │  LAYER 3     │ │   LAYER 3      │
 │   Core Engine   │ │  Agent A     │ │   Agent B      │
 │   BBP-Native LLM│ │  (BBP I/O)   │ │   (BBP I/O)    │
 │   Thinks in BBP │ │              │ │                │
 │   Reasons in BBP│ │◄────BBP─────►│ │                │
 │   Outputs BBP   │ │  wire proto  │ │                │
 └─────────────────┘ └──────────────┘ └────────────────┘
```

**Layer 1 — Frontier Translators.** Specialized LLMs (which may themselves be conventional text-based models) that perform the linguistically hard work of converting natural language into BBP Intermediate Representation (BBP-IR, Section 14). These translators handle ambiguity resolution, ergative-absolutive mapping, aspect analysis, evidentiality detection, and all cross-linguistic normalization. They are replaceable, improvable independently, and may be specialized by source language or language family. Frontier Translators are the *only* components that ever touch natural language text. They are trained using the **Inverted Ground Truth** methodology (§15.4.0): BBP-IR structures generated by the combinatorial pipeline serve as the ground truth anchor, and world-class Teacher LLMs generate the natural-language training examples from those structures — never the reverse. This ensures that the structural labels in the training corpus are correct by construction, not by LLM inference.

**Layer 2 — Core Cognitive Engine.** A purpose-built LLM (or ensemble of LLMs) that operates exclusively on BBP binary token streams. Its tokenizer consumes 64-bit BBP tokens; its vocabulary is the structured space of valid UAT/UET combinations; its "language" is typed semantic structure. The Core Engine performs reasoning, inference, planning, and generation entirely within BBP.

The separation from natural language is structural, not ontological. The Core Engine's conceptual primitives derive from the BBP dictionary, which is itself grounded in human thought — just as a thinker's concepts are grounded in the world, regardless of which language they think in. What the architecture eliminates is dependence on any *particular* human language: its morphology, its word order, its lexical ambiguities, its implicit scope conventions. The Core Engine does not think in Spanish, nor in English, nor in Japanese — it thinks in BBP, a typed substrate whose rules are derived from universal semantic needs, not from the accidents of any individual tongue. BBP's type system (case inventory, TAM fields, compositionality axioms) acts as a universal grammar for this cognition: rules broad enough to accommodate the full semantic range of human language, fixed enough to make every structural relationship explicit and validatable.

Its outputs are BBP token streams that are either consumed by other agents (Layer 3) or passed back through Frontier Translators for human-readable rendering.

**Layer 3 — Inter-Agent Communication.** When multiple AI agents collaborate, they exchange BBP binary streams directly over the wire. No natural language is involved. The typed, deterministic nature of BBP ensures that inter-agent messages are unambiguous: there is no parsing uncertainty, no pronoun resolution ambiguity, no scope confusion. BBP serves as the "TCP/IP of semantic communication" — a shared protocol that any compliant agent can produce and consume regardless of its internal architecture.

**Key architectural invariant:** Natural language is a boundary phenomenon. It enters the system through Layer 1 and exits through Layer 1. Everything between — cognition, reasoning, inter-agent dialogue — happens in BBP. This boundary does not imply that the Core Engine reasons with concepts alien to human thought: its semantic primitives are human concepts, encoded in the dictionary. What the boundary guarantees is that no particular human language imposes its structural constraints on the reasoning process. When two agents communicate in BBP directly (Layer 3), there is no translator and no loss at the language boundary — the same way two people who share a language of thought need no interpreter between them.

### 1.3 Generative Rigidity: Structural Constraint as Creative Engine

A common objection to typed semantic representations is that rigid structure inhibits expressiveness — that creativity requires the fuzzy boundaries and ambiguity of natural language. BBP rejects this premise, drawing on a deep insight from agglutinative morphology:

**Structural rigidity does not limit expressiveness; it channels it.**

In Basque, word formation follows strict morphological rules: well-defined suffixes attach in predictable order to produce transparent compound meanings. Yet this very rigidity makes Basque extraordinarily productive: any speaker can coin a new word (*burubide* = buru "head/mind" + bide "path" → "way of thinking"; *sormena* = sortu "create" + -men "capacity" → "creativity") and be immediately understood, because the compositional rules are shared and transparent. The constraint is not a cage — it is a generative grammar for novelty.

This pattern is not unique to Basque. Finnish, Turkish, Hungarian, and Quechua share it. German compound nouns follow the same logic. And in formal systems, the analogy is exact: Haskell's rigid type system enables compositional abstractions that dynamically-typed languages cannot express; a sonnet's fixed meter and rhyme scheme forces creative solutions that free verse does not demand.

BBP inherits this property by design. Because every token is a typed structure with well-defined fields (case, number, determiner, meta-type, FEATURES, TAM), any valid combination of field values is a valid semantic unit — even if that combination was never seen during training. A BBP-native LLM can produce novel but well-typed semantic structures analogously to how a Basque speaker produces morphological neologisms: by composing known structural elements in novel but rule-compliant ways. This property is more precisely described as **productive compositionality** — the ability to generate and correctly process unseen field combinations — rather than "creativity" in the human sense. Whether the model develops the kind of abstract reasoning that emerges in text-trained models remains an open empirical question (see §1.8).

**Metaphor in a typed system.** Rather than being eliminated by structural typing, metaphor becomes *formally detectable*. When a BBP stream assigns ERGATIVE case to an abstract concept and pairs it with a physical-motion verb via a NOR-NORK system tag, the type violation (abstract entity in a physical-agent slot) is precisely what identifies the expression as metaphorical. A BBP-native model can learn that certain classes of type violations are productive metaphors, not errors — just as human speakers learn that "time flies" is meaningful precisely because time cannot literally fly. The rigidity does not kill metaphor; it makes metaphor a structured, learnable phenomenon.

**Compositional generalization benchmark.** To validate that productive compositionality is a real property of BBP-native models (not merely a theoretical claim), Phase 3 (§15.5) includes a specific benchmark: train the Core Engine on BBP streams that never contain certain valid field combinations (e.g., CAUSATIVE + SUBJUNCTIVE + DATIVE with a specific class of verbs), then test whether the model can correctly process and generate these held-out combinations. Success on this benchmark would constitute empirical evidence that generative rigidity produces measurable value. This benchmark is defined in Appendix H.8.

### 1.4 Semantic Granularity: Synsets, Not Senses

The generative rigidity principle has a direct consequence for dictionary design. Each BBP FEATURES ID maps to an entry in the vocabulary database. The dictionary is bootstrapped from BabelNet, VerbNet, and FrameNet (Phase 1 seed only), grows automatically via the Compiler-driven pipeline during Frontier Translator training, and is governed by an immutable-ID versioning scheme; the full construction, growth, and publication specification is in **§13.6**. The granularity of these entries determines whether the system preserves or destroys the productive polysemy that makes compositional creativity possible.

**Design decision:** BBP FEATURES IDs map to **broad synsets** (clusters of related meanings), not to fine word senses.

- The Basque word *buru* means "head", but also "top", "beginning", "leader", "mind", "origin". In the vocabulary database, *buru* receives a single BBP ID that covers this semantic cluster. The specific sense is disambiguated by structural context: *buru* + INESSIVE ("in the head") → mental space; *buru* + GENITIVE-LOCATIVE ("of the head/top") → physical position; *buru* + a governance-domain UAT → leader.

- This mirrors how human cognition works: we do not store separate lexical entries for each sense of "head"; we store a rich prototype and let context select the relevant facet.

- For Frontier Translators (Layer 1), the broad synset is sufficient to produce correct BBP-IR: the structural context (case, surrounding tokens, verb frame) constrains interpretation. For human-facing rendering, the dictionary database stores per-language, per-sense mappings that the Renderer selects based on the BBP structural context.

- For the Core Cognitive Engine (Layer 2), broad synsets are essential: they preserve the combinatorial richness that enables the model to discover novel semantic compositions during reasoning. Overly fine sense distinctions would fragment the embedding space and inhibit generalization.

**Two-level resolution:** The vocabulary database maintains both levels: a **synset level** (used by BBP tokens and the Core Engine) and a **sense level** (used by Frontier Renderers for target-language generation). The BBP binary stream operates exclusively at the synset level.

**Residual lexical ambiguity (by design).** Broad synsets preserve a degree of lexical ambiguity by design. This ambiguity is constrained — the structural context (case, surrounding tokens, verb frame) reduces the space of possible interpretations from hundreds to typically 2-3. The Core Engine is expected to learn these contextual disambiguation patterns during training. The remaining ambiguity is comparable to the polysemy that human speakers routinely resolve and is a feature, not a defect, of the representational design.

**Training-time sense annotation.** During corpus generation (Phase 2, §15.4), the Teacher LLM annotates each token with both the synset ID (which goes into the binary stream) and the intended sense ID (which goes into a parallel training signal). The Core Engine is trained with a multi-task objective: predict-next-token on the BBP stream *and* classify the intended sense from the structural context. The sense classifier is discarded at inference time, but its gradient during training forces the model to develop internal representations that distinguish senses — analogous to auxiliary training objectives in NLP (e.g., next-sentence prediction in BERT). Additionally, the BBP-IR produced by Frontier Translators MAY include an optional `sense_distribution` field for broad synsets, providing a confidence distribution over constituent senses (e.g., `{ "synset": "buru", "sense_distribution": { "head_physical": 0.1, "mind": 0.8, "leader": 0.05, "origin": 0.05 } }`). The Compiler discards this distribution (the binary stream uses the broad synset), but it is available as a richer training signal during corpus construction.

### 1.5 Scope and Deliberate Omissions

BBP encodes **logical and grammatical structure**. The following are deliberately excluded from the binary representation and resolved instead by the dictionary layer (BBP ID ↔ natural language database) during rendering:

- **Grammatical gender.** Gender carries no semantic information ("mesa" is not more feminine than "table"). The dictionary database stores gender per lemma per target language, and the Renderer applies it during output.
- **Honorific registers.** Per-token honorific encoding is handled by the three-dimensional register/honorific field in Entity Token FEATURES (§4, bits 7–2, 6 bits). Complex keigo requires role-assignment metadata provided via the optional HONORIFIC_CONTEXT CONTROL token (§11.5). Phonologically-conditioned lexical honorific forms (e.g., Japanese *いらっしゃる* vs. *いる*) are rendering concerns resolved by the Renderer from structural context.
- **Morphophonological alternations.** Sandhi, vowel harmony, consonant mutations, and similar phonological processes are rendering concerns, not logical structure.
- **Natural language text.** BBP binary streams contain no human-readable strings except inside LITERAL blocks (proper names, code snippets, domain-specific identifiers). All semantic content is encoded as typed, numeric token fields.

### 1.5.1 Known boundaries of semantic coverage

BBP encodes the **grammaticalized and propositional** dimensions of human linguistic meaning with documented cross-linguistic coverage for approximately 7,000 known languages at the level of morphosyntactic structure and propositional content. It does not claim to formalize the complete semantics of natural language. No representational system — including AMR, UMR, MRS, or any formal semantic framework — has achieved or can achieve this in principle.

The boundaries below are organized into three categories by their fundamental nature, because they have different implications for implementers and for the research program.

---

#### Category A — Pipeline-mitigable limits

These are limits of the BBP token format considered in isolation. A correctly trained Frontier LLM (NL→BBP-IR direction) or a correctly trained Renderer (BBP-IR→NL direction) can substantially recover the information in question, either by decomposing the source into rich compositional BBP structures or by reconstructing idiomatic target-language forms from structural context. The residual loss approaches zero with sufficient training quality and coverage.

**A1. Lexical concepts with no cross-linguistic equivalent.**
Languages differ in what concepts they grammaticalize as single lexemes. The German *Schadenfreude* (pleasure at another's misfortune), the Japanese *木漏れ日* (komorebi: dappled light through foliage), the Georgian *შემომეჭამა* (shemomedjama: eating something unintentionally because it was too good) express concepts that other languages must approximate through multi-word phrases. A Frontier Translator with sufficient world-knowledge can decompose these into compositional BBP structures that capture the full propositional content — not as a single token but as a rich argument frame with affect, manner, and event-structure fields. The connotative and cultural specificity of the original lexeme may not be fully recoverable, but the semantic content is. Conversely, a Renderer generating German from a BBP stream that encodes the constituent semantics of Schadenfreude can reconstruct the lexeme from structural context.

**A2. Conversational implicature.**
The inference triggered by "Can you pass the salt?" (which conversationally implicates a request, not an inquiry about physical ability) is not encoded in BBP tokens. However, a Frontier Translator trained on pragmatic inference patterns can encode the *intended* illocutionary act (a request for transfer, DEST case, benefactive frame) rather than the literal propositional content (ability query). The Renderer in the inverse direction can reconstruct the conventional implicature form in the target language.

**A3. Prosodic focus and contrastive stress.**
"I didn't say *she* stole the money" and "I didn't say she *stole* the money" differ semantically through prosodic prominence. BBP has no dedicated field for contrastive focus. However, the TOPICAL case (0x11) and the DISAMBIGUATION meta-type provide partial encoding of information-structural prominence. A Frontier Translator can use these to encode the focused constituent, and a Renderer can reconstruct the appropriate prosodic or syntactic focus marking in the target language (cleft construction, particle, stress assignment).

**A4. Polysynthetic verb incorporation.**
In Mohawk, Nahuatl, and many Amerindian languages, a single verb form incorporates its object, sometimes producing a semantically distinct reading from the analytic equivalent (*deer-hunt* ≠ *hunt deer* in some incorporational readings). The morphological signal of incorporation is lost in the BBP stream, but a Frontier Translator can encode the semantic result — the incorporated reading's event structure, argument frame, and aspectual characteristics — as a rich compositional BBP proposition. The distinction is absorbed at the Frontier with potential loss of the morphological boundary signal, but not of the semantic content.

---

#### Category B — Partially mitigable limits with documented residual loss

These are limits where the pipeline can recover significant semantic content but where a residual loss is inherent and cannot be fully eliminated by any LLM, regardless of training quality. The loss is documented here so that implementers can assess its significance for their application.

**B1. Grammatical tone.**
In some Bantu languages (e.g., Luganda, Chichewa), tone functions as a grammatical marker distinguishing tense or aspect. A Frontier Translator can encode the grammatical *consequence* of the tonal distinction (the tense or aspect value in FEATURES) but cannot recover the phonological signal itself. For downstream NL generation, the Renderer can reconstruct the appropriate tonal form from the encoded grammatical value. The residual loss is the phonological signal — unrecoverable from the binary stream, though irrelevant for semantic processing.

**B2. Idiomatic and phonologically-conditioned classifier usage.**
The normative CLF category inventory (§4) covers the semantically primary classifier function. Some classifiers are selected by phonological or historical convention rather than semantic category (certain Japanese loan-word classifiers, some Cantonese frozen forms). A Frontier Translator will map these to the best-fitting CLF category or CLF_EXTENDED; the specific phonological conditioning is not recoverable from the stream. For most semantic processing purposes this loss is negligible; for high-fidelity phonological rendering it requires Renderer-side dictionary lookup.

**~~B3. Presupposition.~~ → Promoted to Category A (r27)**

Presupposition is now natively representable via the **PRESUPPOSITION meta-type (0o67)** defined in §5.1 and fully specified in §6.8. The PRESUPPOSITION block encodes: the presupposed proposition as a typed background frame; the presupposition type (EXISTENTIAL, FACTIVE, LEXICAL, STRUCTURAL, COUNTERFACTUAL); and critically its **projection behavior** — whether the presupposition survives negation and questioning (the classical linguistic test that distinguishes presuppositions from entailments). A Frontier Translator that detects the presupposition encodes it explicitly; one that misses it produces the same silent loss as any other missed semantic feature — a quality problem, not an architectural limitation. See §6.8 for full specification and §13.2.1 Axiom Group I for validation rules.

**~~B4. Non-propositional performativity.~~ → Promoted to Category A (r27)**

Performative speech acts are now natively representable via the **SPEECH_ACT CONTROL token (0x090)** defined in §11.5.2 and fully specified in §11.6. SPEECH_ACT encodes: illocutionary force (ASSERTION, PROMISE, DECLARATION, REQUEST, WARNING, APOLOGY, GREETING, COMMISSIVE, EXPRESSIVE); institutional domain (GENERAL, LEGAL, RELIGIOUS, BUREAUCRATIC, SOCIAL_RITUAL); and uptake requirements (NONE, ADDRESSEE, INSTITUTIONAL, BILATERAL). Felicity conditions are encoded as HYPOTHESIS sub-blocks with CONFIDENCE=1.0 inside the SPEECH_ACT frame, making the full formal apparatus of speech act theory (Austin/Searle) explicitly representable in bits. The Renderer reconstructs the appropriate performative surface form from the structural specification. See §11.5.2 and §11.6 for full specification, and §13.2.1 Axiom Group I for validation rules.

---

#### Category C — Structural limits: not mitigable by any pipeline component

These are not limitations of the Frontier LLM or the Renderer — they are architectural constraints on what a linear stream of fixed-width tokens can represent. No improvement in training, dictionary coverage, or model scale can overcome them, because the information they concern cannot be encoded in the format's structural model.

**C1. Signed language spatial grammar.**
Sign languages (ASL, BSL, LSE, and approximately 300 others) use the physical signing space to encode grammatical relations, coreference, and semantic content simultaneously and spatially. A referent established in the left signing space remains there throughout the discourse; verbs move through space to encode agreement simultaneously with their argument structure. This multi-dimensional, simultaneous organization is structurally incompatible with BBP's linear token stream. A Frontier Translator can encode the *propositional content* of a signed utterance — what is asserted — but the spatial-grammatical structure is lost without residual. BBP does not claim coverage of signed language grammar.

**C2. Lexical acuity at inference time.**
The Core Engine operates over LEMMA IDs in a fixed dictionary. It can combine existing LEMMA IDs in novel compositional structures (see §1.10), but it cannot coin new atomic LEMMA IDs during inference. A genuinely new concept — one that cannot be decomposed into existing dictionary entries — requires the full pipeline: Frontier LLM annotation → dictionary update → Compiler update → potential retraining signal. This is not a defect; it is a property of any closed-vocabulary system. The boundary between "novel composition" and "genuinely new atomic concept" is itself an open question at the intersection of linguistics and philosophy of mind.

**C3. The ontological commitment of the universal dictionary.**
Any universal dictionary is a theory of the world: a set of decisions about which distinctions are conceptually primitive and which are derived. Quine's argument for the indeterminacy of radical translation implies that no such ontology is uniquely correct. Two Frontier Translators applying different dictionary granularity policies to the same input will produce semantically inequivalent streams without either being wrong. BBP's broad-synset approach (§1.4) mitigates this by deferring fine-grained disambiguation to structural context — but the residual ontological commitment is irreducible. It is relocated from the token format to the dictionary construction methodology, where it can at least be made explicit and versioned. The methodology by which this commitment is operationalised — bootstrap sources, promotion criteria, bias audit requirements, and publication cycle — is specified in **§13.6**.

These Category C limits are not problems BBP can address in a future version without a fundamental architectural change. They are documented here as honest scope constraints, not as roadmap items.

### 1.6 Compositionality Principle

BBP follows a **compositionality principle** for grammatical features that exceed what can be ergonomically encoded in a single token's TAM and FEATURES fields. Rather than attempting to pack every possible feature into the fixed-width token, BBP uses **bound modifier tokens** (Section 6.7): dedicated UET tokens that immediately precede a UAT and form a logical unit with it. This design allows:

- Zero cost for languages that do not use the feature (no token emitted).
- Rich expressiveness for languages that require it (the full 32-bit FEATURES space of a dedicated UET token available for the extended feature).
- Unbounded extensibility without ever changing the 64-bit token format.

This is the token-level analogue of agglutinative morphology: just as Basque builds complex verb forms by chaining well-defined affixes, BBP builds complex predicate structures by chaining well-defined native verb feature field tokens before a UAT. The base unit is fixed and simple; complexity is additive and transparent.

**General auxiliary token pattern.** The native verb feature field mechanism described above is an instance of a general extensibility pattern used in BBP wherever a fixed-width bit field cannot enumerate all values of a category:

1. The bit field in the base token encodes a **semantic category** (coarse-grained, cross-linguistically universal) in its defined bit width.
2. A **reserved sentinel value** in the field (convention: the highest available value) signals that a language-specific detail is encoded in an auxiliary token immediately preceding the base token.
3. The **auxiliary token** is a standard 64-bit Entity Token whose LEMMA is a dictionary ID referencing the specific value (classifier, evidential marker, noun class, etc.).

This pattern has zero cost for languages that do not use the extended value (no auxiliary token emitted), is infinitely extensible without format changes, and preserves the single-token parsability of the base token for all common cases. This pattern is instantiated by the CLF auxiliary token (§4) for noun classifiers.

### 1.7 Relationship to Existing Semantic Formalisms

The goal of representing meaning independently of surface syntax is not new. Abstract Meaning Representation (AMR), Uniform Meaning Representation (UMR), and Minimal Recursion Semantics (MRS/DMRS) are established formalisms that share this motivation. BBP acknowledges its intellectual kinship with these efforts and explicitly positions itself within the broader landscape.

**What BBP shares with AMR/UMR.** All of these formalisms seek language-independent semantic representations: structures that capture "who does what to whom" regardless of whether the source is English, Mandarin, or Basque. AMR and UMR use directed acyclic graphs serialized in PENMAN notation (S-expressions); MRS uses feature structures with handle constraints. BBP's Intermediate Representation (BBP-IR, Section 14) occupies the same functional niche: a human-readable, structured, language-independent encoding of propositional semantics in JSON format. At the BBP-IR level, the motivation and scope are comparable.

**Where BBP diverges.** The contribution of BBP is not the intermediate representation itself, but what happens downstream of it — a step that existing formalisms never contemplated, because they were designed for different consumers:

- **AMR/UMR are annotation formalisms.** Their primary consumers are human researchers and NLP evaluation pipelines. PENMAN notation is optimized for readability and hand-annotation. The representation is the destination: the NLP pipeline produces AMR, and AMR is analyzed, compared, and scored. No further compilation occurs.

- **BBP is an execution and transport protocol.** BBP-IR exists as a transient engineering interface between the Teacher LLM (which produces JSON because that is what LLMs generate well) and the Compiler (which packs bits deterministically). The moment BBP-IR is compiled into a 64-bit binary token stream, the JSON ceases to be relevant. The binary stream is the canonical representation — designed for direct consumption by neural network tokenizers (Layer 2) and for wire transmission between agents (Layer 3) with zero parsing overhead.

The analogy is precise: AMR is to BBP-IR what a UML diagram is to JVM bytecode. Both describe program structure, but for fundamentally different consumers — one for human comprehension, the other for machine execution.

**Specific technical differences from AMR:**

| Property | AMR / UMR | BBP |
|----------|-----------|-----|
| Serialization | Text (PENMAN S-expressions) | Binary (64-bit fixed-width tokens) |
| Parsing cost | Tokenization + parenthesis matching + graph construction | O(1) field extraction via bit masking |
| SIMD compatibility | None (variable-length text) | Native (8-byte aligned, 8 tokens per AVX-512) |
| Designed as LLM vocabulary | No | Yes (compositional embeddings over bit fields, §15.5.1) |
| Wire protocol for agents | No (requires serialization layer) | Yes (binary stream is the wire format) |
| Morphological encoding | Roles as text labels ("ARG0", "ARG1") | Roles as bit fields (case, system, person) |
| Grammatical features | Minimal (no TAM, no evidentiality, no aspect) | Comprehensive (8-bit TAM, bound modifier tokens for evidentiality and fine aspect) |
| Fixed-width tokens | No (graph nodes vary in size) | Yes (every token is exactly 64 bits) |

**BBP-IR as the AMR analogue.** For researchers familiar with AMR, the most accurate mapping is: BBP-IR ≈ AMR (structured semantic representation, human-readable, annotatable) with the critical addition that BBP-IR feeds into a deterministic compiler that produces a binary format no AMR pipeline has ever targeted. The innovation is not in the representation layer but in the complete pipeline: representation → validation → compilation → neural execution → inter-agent transport.

**Why not extend AMR?** AMR's design decisions — variable-length text serialization, PropBank frame inventory, reentrancy via variable binding, absence of morphological features — are optimal for its intended use case (corpus annotation and NLP evaluation) but are architectural mismatches for BBP's goals. Retrofitting AMR with fixed-width binary encoding, TAM features, case systems, and LLM-native tokenization would require abandoning most of what makes AMR recognizable as AMR. BBP was designed from first principles for the machine-execution use case, borrowing the *motivation* of language-independent semantic representation while rejecting the *implementation* constraints inherited from annotation-oriented design.

### 1.8 Empirical Validation Status

The hypothesis that a transformer trained exclusively on BBP tokens will develop reasoning capabilities comparable to or exceeding text-trained models is currently untested. The following architectural properties provide theoretical motivation:

1. **Reduced disambiguation overhead.** The Core Engine receives pre-disambiguated, typed input where "who does what to whom" is explicit in the bit layout. This eliminates an entire class of inference that text-based models must perform implicitly.

2. **Structured field geometry.** Each BBP token decomposes into semantically meaningful fields (case, meta-type, TAM, FEATURES) that can be mapped to compositional sub-embeddings (§15.5.1). BPE tokens lack this internal structure — the model must learn all token semantics from distributional context alone.

3. **Reduced sequence length — via the synthetic model.**

BBP achieves sequence length reduction through two independent mechanisms that compound:

**3a. Lexical compression.** BBP collapses multi-token BPE phrases into single 64-bit semantic units. A word like "internationalization" that BPE splits into 4+ subword tokens becomes a single BBP token (one dictionary ID).

**3b. Preposition elimination.** This is the larger and more systematic source of compression. In analytic languages, every prepositional phrase consists of at least one relational word (the preposition) plus the NP it governs. In a typical English or Spanish text, 25–40% of all tokens are prepositions, articles, and particles that carry no independent semantic content beyond marking a grammatical relation. BBP eliminates all of these: the relation is encoded in the `case` field of the nominal token, at zero additional token cost.

**Concrete example.** Consider the English sentence:

> "The report from the director of the department for the minister"

BPE tokenization (approximate): `the` `report` `from` `the` `director` `of` `the` `department` `for` `the` `minister` = **11 BPE tokens**.

BBP encoding:

```
[report,     ABS,      SINGULAR, DEF]    → "the report"
[director,   ABL,      SINGULAR, DEF]    → "from the director"
[department, GEN_POSS, SINGULAR, DEF]    → "of the department"
[minister,   DEST,     SINGULAR, DEF]    → "for the minister"
```

= **4 BBP tokens**. A 64% reduction on this NP alone, with zero semantic loss — and with higher precision than the original: `ABL` (source), `GEN_POSS` (possession), and `DEST` (benefactive destination) are three distinct cases that English "from", "of", and "for" respectively conflate with other meanings in other contexts.

For full sentences, empirical estimates based on English and Spanish corpus analysis:

| Construction type          | Avg BPE tokens | Avg BBP tokens | Reduction |
|----------------------------|---------------|---------------|-----------|
| Simple transitive clause   | 8–12          | 3–5           | ~55%      |
| Complex NP (3+ modifiers)  | 10–18         | 4–7           | ~60%      |
| Subordinate clause         | 15–25         | 7–12          | ~50%      |
| Full paragraph (5 clauses) | 80–120        | 35–55         | ~55%      |

For O(N²) dense self-attention over N tokens, a 55% reduction in N yields approximately **~80% reduction in attention cost** for the affected text (0.45² ≈ 0.20). This is the relevant baseline for Core Engine pre-training, where full dense attention is standard. Modern inference-time optimizations (Flash Attention, sliding-window attention, sparse attention) reduce absolute compute differently, but do not eliminate the sequence-length advantage: shorter sequences require proportionally less KV-cache memory, lower prefill latency, and a larger effective context window at fixed memory budget — advantages that hold across all attention variants.

**These figures are estimates pending Phase 1 corpus benchmarking** (§15.9). The Readiness Scorecard includes a mandatory measurement of BPE-to-BBP token ratio across the seven core languages.

4. **Compositional generalization.** The typed field structure enables productive compositionality (§1.3): novel but well-typed field combinations should be processable without explicit training, as the model learns field semantics independently.

**These are theoretical arguments, not demonstrated facts.** Empirical validation is the primary goal of Phase 3 (§15.5). Staged validation experiments (§15.5.5) provide intermediate go/no-go signals before full-scale training investment. If the BBP-exclusive hypothesis fails to produce adequate results, fallback strategies include hybrid training (BBP + text parallel corpora) and BBP as a fine-tuning format rather than a pre-training format. See §15.5.4 for explicit success criteria and fallback conditions.

**Relationship to the specification.** The validity of BBP as a wire protocol (Layer 3), as an intermediate representation format (Layer 1 ↔ Layer 2), and as a basis for formal validation (§13.2.1) does NOT depend on the Core Engine hypothesis. These layers provide value independently — BBP can serve as a deterministic inter-agent communication protocol and a structured analysis format even if the Core Engine experiments yield negative results.

---

### 1.9 Semantic coverage: scope of coverage claims

**What r21 achieves.** The token format encodes:

- Morphosyntactic structure for the documented grammaticalized categories encodable within a discrete token format, covering the large majority of the ~7,000 known languages at the level of morphosyntactic structure and propositional content. Specifically: case systems (29 cases covering ergative, nominative-accusative, spatial, and discourse functions); Aktionsart and valency class (in the Action Token LEMMA); coarse aspect, evidentiality with a 16-value normative cross-linguistic inventory, number with 8 values covering UNMARKED through DISTRIBUTIVE, voice, mood, tense, and clusivity (in Action Token FEATURES); noun class covering core Bantu systems, animacy (2-bit independent field), classifier systems with 14 semantic categories plus CLF_EXTENDED/CLF_OTHER_LITERAL, and a three-dimensional honorific system (in Entity Token FEATURES). This coverage is not exhaustive: grammaticalized tone (Category B, §1.5.1) is only partially recoverable by the pipeline with documented residual loss, and the spatial grammar of signed languages (Category C, §1.5.1) is not representable in this format by any discrete token system.

- Propositional content for any statement expressible as a structured argument frame: who does what to whom, when, how, why, and under what epistemic conditions.

- Meta-cognitive structure (BBP-CoT): explicit reasoning chains with typed epistemic status, retraction, and quantified confidence.

- Code and mathematical structure (BBP-AST) in the same token stream.

**What the pipeline achieves beyond the token format.** The token format is not the only carrier of semantic information in a BBP system. A correctly trained Frontier LLM (NL→BBP-IR direction) can substantially recover Category A limits (§1.5.1) through rich compositional encoding. A correctly trained Renderer can reconstruct idiomatic target-language forms from structural context. The effective semantic coverage of the complete pipeline is significantly higher than the coverage of the token format considered in isolation.

**What no finite representational system achieves.** The Category C limits in §1.5.1 — spatial grammar of signed languages, atomic concept acuity at inference time, and the ontological indeterminacy of the universal dictionary — are not addressable by any representational system, including BBP. They constitute the irreducible gap between formal representation and the full range of human linguistic meaning. BBP's contribution is to push the practical coverage ceiling as high as current typological knowledge allows while being precise about where that ceiling is and why.

**Practical implication for implementers.** For the core use cases — deterministic inter-agent communication, structured semantic analysis, reduced parsing overhead, explicit reasoning chains — the coverage limits above are rarely binding. The vast majority of natural language communication concerns events, states, and propositions that the defined token format represents without loss. The limits become relevant at the edges of linguistic creativity, in the specific domains identified in §1.5.1.

---

### 1.10 Emergent semantic potential of the Core Engine

Section §1.8 addresses the empirical uncertainty around the Core Engine hypothesis. This section addresses a different question: *assuming* the Core Engine develops functional representations from BBP training, what novel semantic capabilities does the architecture enable that have no equivalent in text-trained models? These are architectural consequences of the design, not empirical claims about a specific trained system.

#### 1.10.1 Cross-linguistic semantic geometry

A text-trained LLM learns associations over the vocabulary of one or more specific languages. Its embedding space reflects the distributional statistics of those languages: what words co-occur in what contexts. A Core Engine trained on BBP streams derived from all human languages operates differently: its vocabulary is the structured space of valid LEMMA × FEATURES combinations, and its training signal is the cross-linguistic semantic structure encoded in those combinations — not the surface statistics of any particular language.

The consequence is geometric. In the Core Engine's embedding space, the concept *home* (as a LEMMA ID) and its structural neighbors are not defined by English collocations or Spanish usage patterns — they are defined by the cross-linguistic distribution of case frames, predicate types, and TAM configurations in which *home* appears across all source languages in the training corpus. The embedding of *home* integrates the German sense of *Heimat* (homeland as cultural belonging), the Finnish sense encoded by the INESSIVE case (being inside and belonging there), and the Quechua sense that grammaticalizes the distinction between the home one is from and the home one currently inhabits. None of these distinctions was privileged by the training setup — they emerge from the structural co-occurrence patterns.

This is a qualitatively different kind of semantic representation. It is not an average of language-specific associations; it is a structurally-grounded integration of cross-linguistic semantic distinctions that exist in the world's languages but may never have been explicitly compared in any text-based corpus.

#### 1.10.2 Concepts without lexemes

Human languages do not lexicalize all semantically available concepts. The space of *possible* concepts — structured combinations of argument frames, case roles, TAM configurations, and LEMMA IDs — is vastly larger than the space of concepts that any language has chosen to name with a single word. In a text-trained LLM, unlexicalized concepts are underrepresented or absent because they produce no training signal: if no text uses a word for it, the model has no direct basis for a representation.

In a Core Engine, a concept without a lexeme in any language can exist as a **geometric region** in the embedding space — a point or neighborhood that is structurally reachable by composition from its neighbors, even if it was never explicitly labeled during training. This is an extension of the generative rigidity principle (§1.3): just as a Basque speaker can coin a transparent morphological neologism that is immediately understood, the Core Engine can generate and process compositional BBP structures that correspond to unlexicalized concepts, because the compositional rules that define them are shared and learned.

This property has practical implications for inter-agent communication (§1.2, Layer 3): two Core Engine agents can negotiate meaning about a concept that no human language has lexicalized by constructing and exchanging BBP structures that structurally circumscribe the concept, without requiring a pre-existing dictionary entry. The concept is defined by its structural position, not by its name.

#### 1.10.3 Asymmetric lexical density as a resource

Different languages lexicalize different regions of semantic space with different granularity. The Inuit languages have fine-grained lexical distinctions for snow and ice conditions that English covers with two words. Arabic has a rich lexical field for kinship relations that English collapses. Mandarin grammaticalizes aspect distinctions that English marks only optionally. Quechua obligatorily encodes evidentiality that English leaves implicit.

In a text-trained multilingual model, this asymmetry is a source of noise: the model must learn that the English word "brother" and the Arabic words for "paternal elder brother" and "maternal younger brother" are related but not equivalent. The representations are pulled in different directions by the statistics of each language.

In a Core Engine, the asymmetry is a resource. The LEMMA IDs for fine-grained kinship distinctions in Arabic, the fine-grained snow vocabulary encoded from Inuit, and the fine-grained evidential distinctions from Quechua all coexist in the same embedding space with their full structural context. The Core Engine does not need to choose between them: it can reason at the granularity level that the task requires, using the finer distinctions from whatever language encodes them, even when generating output that will eventually be rendered in a language that lacks those distinctions.

The Renderer (BBP-IR → NL) handles the rendering loss: it projects the Core Engine's fine-grained representation onto the coarser lexical space of the target language, making the discretionary choices that the Core Engine deferred. But the reasoning itself — the inference, the planning, the semantic composition — happens at the maximum available granularity, not at the minimum of the target language.

#### 1.10.4 Formal detectability of semantic operations

In a text-trained model, operations like metaphor, analogy, negation, and hypothetical reasoning are implicit in the distributional statistics. The model learns that "time flies" is meaningful because it co-occurs with similar constructions, not because its structural type is marked. There is no internal signal that distinguishes "time flies" (metaphor) from "the bird flies" (literal).

In a BBP stream, semantic operations are structurally marked. A metaphorical use of a physical-motion verb with an abstract ERGATIVE agent is a **type mismatch** that is precisely detectable: the Aktionsart of the verb (ACTIVITY, physical motion) conflicts with the animacy of the agent (INANIMATE or ABSTRACT). The Core Engine can learn that this specific class of type mismatch — abstract agent in a physical-motion frame — is the structural signature of motion metaphors, and develop distinct representations for literal and metaphorical uses of the same verb, because the structural signal distinguishes them.

The same applies to hypothetical reasoning (HYPOTHESIS blocks in BBP-CoT have a different structural context from CONCLUSION blocks), to evidential uncertainty (EV_INFER_REASONING has a different training signal from EV_DIRECT_VISUAL), and to counterfactual frames (COUNTERFACTUAL hypothesis sub-type). A Core Engine trained on structurally annotated data of this kind can develop calibrated internal representations for epistemic status, semantic type, and argument structure that text-trained models can only approximate from surface correlates.

#### 1.10.5 Scope and epistemic status of these claims

The capabilities described in §1.10.1–1.10.4 are **architectural consequences** of the BBP design: they follow from the token format, the training setup, and the compositionality principle, given that the Core Engine hypothesis (§1.8) holds. They are not empirically demonstrated. Whether a transformer trained exclusively on BBP streams in fact develops the geometric structure described in §1.10.1, the compositional generalization described in §1.10.2, or the type-mismatch sensitivity described in §1.10.4 is an open empirical question addressed by the Phase 3 benchmarks (§15.5).

The relationship between §1.8 and §1.10 is precise: §1.8 establishes the uncertainty about whether the Core Engine hypothesis holds at all; §1.10 describes what follows architecturally *if it does*. Negative results in Phase 3 would invalidate §1.10 in its entirety, not merely attenuate it. This is the correct epistemic structure for a research program: state the architectural predictions clearly so that the experiments can falsify them.

### 1.11 Cognitive Capacity Hypothesis: Conceptual Richness and Compositional Grammar

The design of the Core Engine rests on a hypothesis that can be stated precisely:

> *A cognitive system whose capacity to handle concepts is richer, and whose rules for combining them are more explicit and complete, will in principle have greater capacity for reasoning, inference, and generative creativity. BBP provides both conditions simultaneously, to a degree that no natural language achieves in unified form.*

Four independent research traditions converge in supporting the components of this hypothesis.

#### 1.11.1 Pillar 1 — Human cognition: vocabulary as cognitive amplification mechanism

The correlation between lexical richness and general intelligence is one of the most robust results in cognitive psychology. A person's lexicon is a better predictor of the general factor *g* than any other known cognitive measure (Jensen, 2001; Crawford et al., 1989; Schipolowski, Wilhelm & Schroeders, 2014). The causal mechanism is not trivial: concepts function as **compact mental proxies**. A rich concept — "entropy", "ergativity", "Pasteur" — acts as a compressed representative of an entire network of associated knowledge. The more such proxies are available, the more efficiently complex problems can be handled, because working memory is not taxed reconstructing what long-term memory already holds in structured form.

This reframes the BBP dictionary architecturally. The ~33 million concept_ids addressable in the 25-bit concept space are not merely lexical coverage — they are the inventory of semantic proxies available for Core Engine reasoning. Dictionary richness is a necessary condition for cognitive capacity, exactly as vocabulary is for human intelligence.

#### 1.11.2 Pillar 2 — Weak linguistic relativity: the grammar of thought has real cognitive consequences

The weak form of the Sapir-Whorf hypothesis — the only version to have survived systematic empirical scrutiny — proposes that structural differences between languages correspond to non-linguistic cognitive differences in their speakers (Brown & Lenneberg, 1954; Levinson, 1997). Not determinism, but structural influence. The strongest experimental evidence comes from spatial cognition: speakers of Guugu Yimithirr, an Australian Aboriginal language using absolute directional reference rather than egocentric reference, develop systematically different spatial orientation than speakers of egocentric languages. The grammar does not prevent spatial thought — it provides a different *grammar of thought* for it, with measurable cognitive consequences.

The implication for BBP is direct: BBP's type system — its 29 cases, orthogonal TAM, compositionality axioms — is not a neutral wrapper around meaning. It is the Core Engine's grammar of thought, and that grammar has consequences for which distinctions the system habitually makes, which relations are explicit, and which compositional paths are structurally available. Unlike any natural language, these consequences are fully auditable because the grammar is axiomatically specified.

#### 1.11.3 Pillar 3 — Neuro-symbolic AI: structured representations amplify reasoning

Results from neuro-symbolic systems are the most directly comparable to the BBP architecture. Formal symbolic representations as intermediate reasoning states offer quantifiable advantages over natural language: they eliminate lexical ambiguity, prevent accumulation of representational errors across long reasoning chains, and make intermediate steps verifiable. Empirically: a neuro-symbolic LLM trained to reason over structured representations rather than natural language achieved an 88.6% mean reduction in cross-entropy loss and solved 15.4× more problems correctly than both Chain-of-Thought fine-tuning and LoRA baselines (Llama 3.1 8B evaluated on the Symbolic-Math Dataset; Dhanraj & Eliasmith, EMNLP 2025, pp. 30589–30608. DOI: https://doi.org/10.18653/v1/2025.emnlp-main.1556). The difference is not marginal.

At the level of internal transformer mechanism, out-of-distribution generalization depends crucially on compositional structure, achieved internally through subspace alignment across network layers (PNAS, 2025). Compositionality is not merely a property of the input — it is the property that enables generalization to situations not encountered during training. Model scale alone does not resolve the compositional problem: current models continue to struggle with multi-step chained reasoning regardless of parameter count.

#### 1.11.4 Pillar 4 — BBP as meta-language: the fundamental reframing

The three pillars above ground the hypothesis empirically. A fourth consideration reframes its epistemic status more deeply.

A potential objection to the Core Engine hypothesis is that no transformer has ever been trained from scratch on a stream of typed semantic tokens without prior natural language text. This objection assumes implicitly that BBP is not a language — that it is a technical representation, a serialization format. That assumption is incorrect.

**BBP is a language.** Not an impoverished or neutral one: it is the result of the deliberate distillation of the grammatical properties of all known human languages into a single formal system. No natural language simultaneously possesses:

- 29 explicit cases (Finnish has ~15, Hungarian 18–20, Basque 15–17)
- 16 normative evidentiality values (Tibetan, Quechua and Tuyuca have 4–5 each)
- Orthogonal TAM with fine aspect encoding
- A universal semantic classifier inventory
- Bantu noun classes (16 values covering the Swahili system)
- Explicit animacy on the Silverstein hierarchy
- Three-dimensional honorific register (Japanese keigo, Korean speech levels, T/V distinctions)
- Axiomatically validatable compositionality
- Formal coreference with typed backpointers
- Structured chain of thought as a native extension (§17)

What in natural languages is grammar implicitly internalized by speakers over years of exposure, in BBP is explicit structure verifiable at the bit level. BBP is not a *reduced* language relative to natural language — it is an *augmented* one: the formal union of what all human languages can express, compiled into a single typed system.

The objection therefore reframes correctly: not whether a transformer can reason *without language*, but whether it can reason in **the language with the highest density of explicit grammatical structure ever formally specified**. A Core Engine trained on BBP does not lack language — it operates in the language with the most rules, and whose rules are completely transparent.

#### 1.11.5 Synthesis: the cognitive equation of BBP

The four pillars converge on a single compositional argument:

```
Cognitive capacity ≈ f(conceptual richness × explicit compositional grammar)
```

- In humans: rich vocabulary × internalized grammar → high general intelligence.
- In current LLMs: dense embeddings × unstructured attention → statistical power, compositional fragility.
- In the Core Engine: dictionary of up to ~33M concepts × pan-linguistic axiomatic grammar → hypothesis of maximum generative compositional capacity.

This hypothesis is not demonstrated — the Stage 0 micro-experiment (§15.5.5) is its first empirical test. But it is no longer speculation without foundation: it has convergent support from cognitive psychology, psycholinguistics, linguistic typology, and neuro-symbolic AI. And its deepest premise — that BBP does not deprive the Core Engine of language but provides the richest one — eliminates the strongest vector of reasonable scepticism.

What remains genuinely open is empirical: whether a transformer can learn to exploit that structural richness in ways that produce generalizable reasoning. The architecture creates the conditions. Training will determine whether they are leveraged.

**Epistemic status:** This section presents a theoretically grounded working hypothesis, not an established fact. All claims about Core Engine behavior are predictions conditional on §1.8. They are falsifiable by the benchmarks defined in §15.5.5 and Appendix H.

---

## 2. Architecture Overview (BBP-64)

### 2.1 Byte Order

All streams use **big-endian** (network byte order). Little-endian hosts MUST convert at serialization boundaries.

### 2.2 Atomic Unit: 64-bit Token

The atomic unit is an **unsigned 64-bit token** (8 bytes), split as:

- **LEMMA (bits 63–32, 32b):** typed semantic identifier — concept ID plus ontological type information and (for Action Tokens) verb class fields. See §2.3 for the per-token-class LEMMA layout.
- **FEATURES (bits 31–0, 32b):** grammatical / pragmatic / structural configuration.

This separation enforces the principle: **concept invoked** vs **context configured**. In r21, the LEMMA is a *self-describing* semantic identifier: its ontological category (meta-type for Entity Tokens; Aktionsart and valency class for Action Tokens) is recoverable from the LEMMA alone, without loading FEATURES.

### 2.3 Token Discrimination (Type Bit)

The primary class discriminator is **LEMMA bit 31**:

| LEMMA bit 31 | Token class |
|---:|---|
| `1` | **Action Token** (verbal predicate) |
| `0` | **Entity Token** (nouns, operators, control, structure) |

> Terminology: **Action Token** (abbreviated **UAT** — Universal Action Token) and **Entity Token** (abbreviated **UET** — Universal Entity Token) are the two token types. Both forms are used interchangeably throughout this specification. The type discriminator is LEMMA bit 31 (1 = Action Token, 0 = Entity Token).

**LEMMA layout — Entity Token (LEMMA[31] = 0):**

```
LEMMA [63:32] — Entity Token (standard layout, all meta-types except those below)

  bit  31     : 0                    (Entity discriminator)
  bits 30-25  : meta-type  (6b)      (same 0oGS taxonomy, unchanged)
  bits 24- 0  : concept_id (25b)     →  2²⁵ = 33,554,432 IDs per meta-type slot
```

**LEMMA layout — Entity Token with natural gender (gender-extended meta-types):**

For meta-types where natural gender is semantically relevant — specifically **DICT_WORD**, **NAME_PERSON**, **PRONOUN**, and **ADJECTIVE** — bits 24–23 of the LEMMA are repurposed as a 2-bit natural gender field, reducing concept_id to 23 bits:

```
LEMMA [63:32] — Entity Token (gender-extended layout: DICT_WORD, NAME_PERSON, PRONOUN, ADJECTIVE)

  bit  31     : 0                    (Entity discriminator)
  bits 30-25  : meta-type  (6b)      (identifies this as a gender-extended token)
  bits 24-23  : natural_gender (2b)  (semantic sex of the referent — see table below)
  bits 22- 0  : concept_id (23b)     →  2²³ = 8,388,608 IDs per meta-type slot
```

**Meta-type ontological taxonomy (normative).** All meta-types belong to one of two ontological classes. The class is determined at parse time from the meta-type field alone (LEMMA bits 30–25); no additional context is required.

**Class A — Referential meta-types.** The `concept_id` field (LEMMA bits 24–0 or 22–0 for gender-extended variants) is a pointer into the BBP dictionary. The Compiler MUST create and maintain dictionary entries for these types; the parser resolves meaning by dictionary lookup. A referential token without a dictionary entry is a CRITICAL error.

Members: `DICT_WORD` (0o00), `PRONOUN` (0o01), `ADJECTIVE` (0o02), `ADVERB` (0o03), `CONJUNCTION` (0o04), `INTERJECTION` (0o05), `QUANTIFIER` (0o06), `MODIFIER` (0o07); all `NAME_*` (0o30–0o37); all `SCI_*` except `LIVING_ENTITY` (0o40–0o45, 0o47 deferred); all `LANG_*` (0o50–0o57); all Group 5–6 UETs that carry a concept referencing an external entry.

**Class B — Self-descriptive meta-types.** The LEMMA bits below the meta-type field are a structured descriptor whose meaning is defined entirely by the bit layout specified per meta-type. The Compiler MUST NOT create dictionary entries for these types; the parser interprets LEMMA bits directly without any dictionary lookup.

Members: `NUM_NATURAL` (0o10), `NUM_INTEGER` (0o11), `NUM_REAL` (0o12), `NUM_FRACTION` (0o13), `NUM_COMPLEX` (0o14), `LIVING_ENTITY` (0o46), and any future meta-type explicitly designated self-descriptive in its defining section.

This distinction MUST be respected by all implementations. A parser encountering a self-descriptive meta-type MUST NOT attempt a dictionary lookup for the concept_id field — the field does not contain a dictionary ID. The BBP-IR representation of self-descriptive tokens encodes their structural fields explicitly, not as a dictionary reference. The class hierarchy is detailed in §5.0.

**Validator rule (V-METATYPE-CLASS):** A meta-type present in the binary stream whose class is not declared in the specification MUST be treated as `UNKNOWN` (0o77) by the parser. Downstream processors MUST NOT attempt to interpret the LEMMA bits of an UNKNOWN token as either a concept_id or a structured descriptor. Severity: CRITICAL.

**Natural gender field values (2b):**

| Code | Name | Description |
|------|------|-------------|
| `00` | GENDER_UNSPECIFIED | No natural gender marked. Default for concepts where sex is irrelevant or unknown. |
| `01` | MASCULINE | Referent is male / masculine natural gender (e.g. "ternero", "actor", "rey"). |
| `10` | FEMININE | Referent is female / feminine natural gender (e.g. "ternera", "actriz", "reina"). |
| `11` | NEUTER | Referent is neither / natural gender explicitly not applicable (e.g. collective nouns, abstract referents lexicalized with a gendered form). |

**Distinction from grammatical gender.** The `natural_gender` field encodes **biological or social sex of the referent**, not the grammatical gender class of the word. Grammatical gender (the arbitrary morphological class assignment of "mesa" as feminine in Spanish, "Tisch" as masculine in German) is not encoded in BBP and is handled by the Renderer via dictionary metadata. `natural_gender` is only meaningful where the distinction carries real-world semantic content: "ternera" vs "ternero", "actor" vs "actriz", "he" vs "she".

**Parser behaviour.** The parser reads meta-type before interpreting the remainder of the LEMMA. On encountering a gender-extended meta-type, the parser applies a conditional mask: bits 24–23 are extracted as `natural_gender` and bits 22–0 are extracted as `concept_id`. For all other meta-types, the standard 25-bit concept_id extraction applies. This conditional mask requires no structural change to the parser state machine.

**Frontier Translator behaviour.** The Frontier Translator MUST set `natural_gender` when the source language lexicalizes the distinction (e.g. Spanish "ternera" → FEMININE, "ternero" → MASCULINE). When the source language does not lexicalize the distinction (e.g. English "veal", "calf"), the Translator MUST emit GENDER_UNSPECIFIED. The Translator MUST NOT infer natural gender from grammatical gender of the source-language word.

**Renderer behaviour.** When generating target-language output, the Renderer uses `natural_gender` to select the appropriate lexical form where the target language lexicalizes the distinction, and to determine morphological agreement patterns where required (e.g. adjectival agreement in Romance languages, pronoun selection in English).

The concept_id space is **partitioned by meta-type**: each of the 64 ontological slots has its own address space. For standard meta-types: 33M IDs (25 bits). For gender-extended meta-types (DICT_WORD, NAME_PERSON, PRONOUN, ADJECTIVE): 8M IDs (23 bits). Total unique identifiers across all meta-types: approximately 64 × 8–33M ≈ 1–2 billion, well in excess of any foreseeable dictionary requirement.

**LEMMA layout — Action Token (LEMMA[31] = 1):**

```
LEMMA [63:32] — Action Token

  bit  31     : 1                    (Action discriminator)
  bits 30-28  : Aktionsart  (3b)     (lexical aspect class — NEW in r21)
  bits 27-25  : valency_class (3b)   (verb valency — NEW in r21)
  bits 24- 0  : verb_id    (25b)     →  33M verb IDs
```

**Aktionsart (bits 30–28):** Lexical aspect class per Vendler/Comrie:

```
000 = UNSPECIFIED          (default; no lexical aspect encoded)
001 = STATE                (know, contain, believe — atelic, non-dynamic)
010 = ACTIVITY             (run, work — atelic, dynamic)
011 = ACHIEVEMENT          (arrive, recognize — telic, punctual)
100 = ACCOMPLISHMENT       (build, write — telic, durative)
101 = SEMELFACTIVE         (cough, blink — single instantiation of iterable event)
110 = CAUSATIVE_BASE       (root of a causative alternation)
111 = reserved
```

**Valency class (bits 27–25):**

```
000 = UNSPECIFIED
001 = INTRANS_UNACCUSATIVE  (arrive, fall — patient as syntactic subject)
010 = INTRANS_UNERGATIVE    (run, work — agent as syntactic subject)
011 = TRANSITIVE            (eat, see — agent + patient)
100 = DITRANSITIVE          (give, send — agent + recipient + patient)
101 = COPULATIVE            (be, seem, become — predicate linker)
110 = AMBITRANSITIVE        (eat/eat something — both readings lexically valid)
111 = reserved
```

These are lexical properties of the verb lemma, not of its use in a particular clause. A validator can use them to cross-check FEATURES fields (e.g., an ACHIEVEMENT verb with PROGRESSIVE aspect is anomalous in most languages; a DITRANSITIVE verb without an INDIRECT person field is likely incomplete). Placing them in the LEMMA makes this validation possible without dictionary lookup.

### 2.4 Memory Layout Convention

BBP-64 is conceptually:

```
token64 = (lemma32 << 32) | features32
```

### 2.5 Stream Alignment

Tokens are aligned to **8 bytes**. LITERAL byte blocks MUST be padded with `0x00` to the next 8-byte boundary.

### 2.6 Token Indexing

REFERENCE and other index-based operators count **logical tokens** (each 64-bit token counts as 1). Raw LITERAL bytes do not increment the token index.

### 2.7 Rationale: Why BBP-64 (32+32)

BBP-64 resolves the bit-budget scarcity of earlier designs by giving:

- **32 bits of lemma space** (direct addressing for concepts)
- **32 bits of FEATURES** (enough native space for typologically relevant morphology/pragmatics)

Result: lower implementation complexity, higher expressiveness, and fewer “escape hatches”.

### 2.8 Profiles (Informative)

> **Status: INFORMATIVE.** BBP-Lite and BBP-Full are NOT profiles of the wire protocol. The protocol has a single format: 64-bit tokens, deterministic bit layout, full conformance. The distinction between "full" and "lite" encodings is a property of the **encoder**, not of the protocol. A Frontier Translator performing NL→BBP conversion may produce richer or sparser FEATURES population depending on source language expressiveness and translation confidence; a deterministic parser receiving structured input (sensor events, typed API calls, ASTs) will always produce fully specified tokens. In both cases, the resulting binary stream is valid BBP-64. Receivers MUST NOT treat partially-populated FEATURES fields as a distinct protocol variant — they are simply tokens with some fields set to their default (zero) value.

The degradation chain based on FEATURES payload field scarcity is no longer necessary with the 64-bit layout. Encoders SHOULD populate all FEATURES fields for which the source information is available. Fields with no available source information MUST be set to zero (their default/unspecified value).

The `PROFILE` mechanism previously defined in this section is removed. The `PROFILE` sub-type of CONTROL tokens (§11.5) MAY be retained for provenance tagging (identifying which encoder produced a stream), but MUST NOT be used to declare a subset of the protocol as applicable. All receivers MUST be able to process all valid BBP-64 tokens regardless of encoder provenance.

## 3. Action Token (LEMMA[31] = 1)

An Action Token encodes a verbal predicate with compact, explicitly addressable morphology.

### 3.1 Layout

The Action Token LEMMA encodes the verb's intrinsic lexical class (Aktionsart and valency, §2.3). The FEATURES word encodes the grammatical configuration of the verb's use in a specific clause.

When `LEMMA bit 31 = 1`:

```
LEMMA (32b)   : Verb identity (see §2.3 for field layout)
                bit  31     : 1 (Action discriminator)
                bits 30-28  : Aktionsart (3b)
                bits 27-25  : valency_class (3b)
                bits 24-0   : verb_id (25b)

FEATURES (32b):
  bit  31     : reserved  (must be 0 for Action Tokens)
  bits 30-28  : Verb system (3b)
  bit  27     : Polarity (1b)
  bits 26-24  : Aspect (3b)
  bits 23-22  : Voice (2b)
  bits 21-19  : Extended voice (3b)
  bits 18-17  : Mood (2b)
  bits 16-15  : Tense (2b)
  bits 14-12  : Subject person (3b)
  bits 11- 9  : Object person (3b; 000 = absent)
  bits  8- 6  : Indirect person (3b; 000 = absent)
  bits  5- 2  : Evidentiality (4b)   [normative inventory defined, see §3.8]
  bits  1- 0  : Clusivity (2b)
```

### 3.2 Verb System (bits 28–30)

| Code | System | Meaning |
|---:|---|---|
| 000 | NOR | intransitive |
| 001 | NOR-NORK | transitive |
| 010 | NOR-NORI | intransitive + dative |
| 011 | NOR-NORI-NORK | ditransitive |
| 100–111 | reserved | future extensions |

### 3.3 Polarity (bit 27)

`0` affirmative, `1` negative

### 3.4 Aspect (bits 24–26)

Eight values are reserved for coarse aspect. Implementations SHOULD map these to their internal aspect inventories.

### 3.5 Voice and Extended Voice (bits 22–23, 19–21)

Voice (2b) encodes the primary alternation (e.g., active/passive/middle/causative).  
Extended voice (3b) is reserved for additional alternations (e.g., antipassive/applicative) if standardized.

### 3.6 Mood and Tense (bits 18–17, 16–15)

Both fields are 2 bits wide (4 values each). The following normative inventories MUST be used by all conforming implementations.

**Tense (bits 16–15):**

```
Code | Name | Description
-----|------|------------------------------------------------------------
 00  | PRES | Present / temporally unmarked. Default for languages that
     |      | do not grammaticalize tense (e.g. Mandarin Chinese).
 01  | PAST | Past: the event precedes the utterance time.
 10  | FUT  | Future: the event follows the utterance time.
 11  | HYPO | Hypothetical / counterfactual: the event is not anchored
     |      | to a real timeline (irrealis conditionals, counterfactuals,
     |      | future subjunctive in some languages). Distinct from mood
     |      | to allow HYPO tense × SUB/POT mood combinations.
```

**Mood (bits 18–17):**

```
Code | Name | Description
-----|------|------------------------------------------------------------
 00  | IND  | Indicative: the event is asserted as real or actual.
 01  | SUB  | Subjunctive / optative: the event is wished, doubted,
     |      | or presented as non-factual (Spanish subjuntivo,
     |      | French subjonctif, Greek optative).
 10  | POT  | Potential / conditional: the event could or would
     |      | occur given certain conditions (Basque baldintzazko,
     |      | Japanese -だろう, English "would").
 11  | IMP  | Imperative / jussive: the event is commanded or directed.
     |      | Used for all direct command forms across languages.
```

**Cross-field combinations.** Tense and mood are orthogonal fields; any combination is structurally valid. Semantically unusual combinations (e.g., `IMP + PAST`) may be infrequent but are not prohibited — a Frontier Translator encoding a language-specific form that requires them MUST use the closest normative values. Fine-grained TAM distinctions not expressible within these 4+4 values SHOULD be encoded using `TEMPORAL` (0o63) or `MODAL` (0o64) UET tokens from Group 6 (§6.7).

### 3.7 Person Fields (Subject/Object/Indirect)

Each field is 3 bits. A common convention is:

- `001=1s, 010=2s, 011=3s, 101=1p, 110=2p, 111=3p`
- `000=absent` (only valid for Object and Indirect)

### 3.8 Evidentiality (bits 2–5)

4 bits, 16 values. The following normative cross-linguistic inventory is defined in r21, based on Aikhenvald (2004) and subsequent typological documentation. `EV_UNSPECIFIED` (0x0) is the default; Frontier Translators SHOULD omit evidentiality encoding rather than emit 0x0 when the source language has no evidential system.

```
Code  Name                    Description
────  ──────────────────────  ─────────────────────────────────────────────────────
0x0   EV_UNSPECIFIED          No evidential marking (default)
0x1   EV_DIRECT_VISUAL        Direct perception — visual ("I saw it with my own eyes")
0x2   EV_DIRECT_SENSORY       Direct perception — non-visual (auditory, tactile, olfactory)
0x3   EV_DIRECT_PARTICIPATORY First-person participation as agent ("I did it myself")
0x4   EV_INFER_RESULT         Inference from physical evidence ("there are tracks → someone passed")
0x5   EV_INFER_REASONING      Logical inference from prior knowledge / deduction
0x6   EV_INFER_ASSUMPTION     Expectation based on general knowledge or habit
0x7   EV_REPORT_GENERAL       Reportative — unspecified source ("they say that...", "it is said...")
0x8   EV_REPORT_SPECIFIC      Reportative — identifiable source ("Juan said that...")
0x9   EV_REPORT_WRITTEN       Documentary — written or recorded source (text, official record)
0xA   EV_QUOTE_DIRECT         Direct quotation (reproduces source's exact words)
0xB   EV_COMMON_KNOWLEDGE     Shared community knowledge ("everyone knows that...")
0xC   EV_TRADITIONAL          Cultural, historical, or mythological transmission
0xD   EV_MIRATIVE             Unexpected discovery ("it turns out that...!" — admirative)
0xE   EV_DREAM_VISION         Non-ordinary perception (grammaticalized in Quechua, Tibetan,
                              and several Amazonian languages)
0xF   EV_OTHER_LITERAL        Language-specific evidential in LITERAL block
```

**Normative cross-linguistic mappings** (MUST be implemented by Frontier Translators for the listed languages):

| Language | Native distinction | Normative BBP mapping |
|---|---|---|
| Turkish | -dı (direct) / -mış (inferred/reported) | EV_DIRECT_VISUAL / EV_INFER_RESULT or EV_REPORT_GENERAL |
| Bulgarian | witnessed / non-witnessed | EV_DIRECT_VISUAL / EV_REPORT_GENERAL |
| Quechua (4-way) | direct / inferential / reportative / assumed | EV_DIRECT_VISUAL / EV_INFER_RESULT / EV_REPORT_GENERAL / EV_INFER_ASSUMPTION |
| Tuyuca (5-way) | visual / non-visual / apparent / assumed / reported | EV_DIRECT_VISUAL / EV_DIRECT_SENSORY / EV_INFER_RESULT / EV_INFER_ASSUMPTION / EV_REPORT_GENERAL |
| Tibetan | direct / inferential / testimonial / endophoric | EV_DIRECT_VISUAL / EV_INFER_REASONING / EV_REPORT_SPECIFIC / EV_INFER_ASSUMPTION |
| Japanese (reported) | -そうだ (hearsay) / -らしい (inference) | EV_REPORT_GENERAL / EV_INFER_REASONING |

For languages whose evidential system has more than 15 grammaticalized distinctions (currently no documented language exceeds this), or whose distinctions do not map cleanly to the normative inventory, `EV_OTHER_LITERAL` (0xF) is used with a LITERAL block specifying the raw evidential marker and a companion `sense_distribution` field in BBP-IR.

### 3.9 Clusivity (bits 0–1)

Neutral / inclusive / exclusive / reserved.

## 4. Entity Token (LEMMA[31] = 0)

Entity Tokens encode nouns, operators, control and structural elements.

### 4.1 Layout (FEATURES 32b)

The type discriminator (bit 31) and meta-type (bits 30–25) have moved from FEATURES to LEMMA (§2.3). This frees all 32 FEATURES bits for grammatical encoding. The LEMMA is now a self-describing typed semantic identifier: its ontological category is recoverable from the LEMMA alone.

When `LEMMA bit 31 = 0` (Entity Token):

```
LEMMA (32b)   : Typed semantic identifier (see §2.3 for field layout)
                bit  31     : 0 (Entity discriminator)
                bits 30-25  : meta-type (6b)
                bits 24-0   : concept_id (25b)   [standard meta-types]
                  — OR —
                bits 24-23  : natural_gender (2b) [DICT_WORD, NAME_PERSON, PRONOUN, ADJECTIVE only]
                bits 22-0   : concept_id (23b)   [gender-extended meta-types]

FEATURES (32b):
  bits 31-30  : Flow control      (2b)
  bits 29-25  : Case              (5b)   [29 defined + 3 reserved]
  bits 24-22  : Number            (3b)   [8 values, see §4.6]
  bits 21-18  : Determiner        (4b)
  bits 17-14  : Noun classifier   (4b)   [normative inventory, see §4.8]
  bits 13-12  : Animacy           (2b)   [see §4.9]
  bits 11- 8  : Noun class        (4b)   [16 values, see §4.10]
  bits  7- 2  : Register/Honorific (6b)  [3D system, see §4.11]
  bits  1- 0  : Reserved / vital_status (2b)
                [UNSPECIFIED(00) for all standard entity tokens;
                 vital_status for LIVING_ENTITY (0o46): see §5.2.2]
```

### 4.2 Meta-Type (6b) — Now in LEMMA[30:25]

Meta-type is an ontological category (up to 64 values), encoded in LEMMA bits 30–25. The full taxonomy (0oGS system) is unchanged; see Section 5. The meta-type is part of the LEMMA and is thus a property of the concept referenced, not of its grammatical context.

### 4.3 Flow Control (bits 30–31, 2b)

| Code | Name | Meaning |
|---:|---|---|
| 00 | UNIT | semantic token |
| 01 | START | opens a block |
| 10 | END | closes a block |
| 11 | LITERAL | announces a raw byte block |

### 4.4 LITERAL Blocks (Length + Bytes + Padding)

LITERAL blocks encode raw byte content that cannot be represented by a dictionary concept_id. Their structure MUST be:

1. **Opening token** (flow=LITERAL): LEMMA carries the meta-type; FEATURES carries the **exact byte length** of the raw content (not including padding).
2. **Raw bytes**: the raw content, followed by 0x00 padding bytes to the next 8-byte boundary.
3. **Closing token** (flow=END): LEMMA carries the same meta-type; FEATURES carries the **standard grammatical fields** of the concept as a whole (case, number, determiner, etc. per §4.5–§4.11).

**Alignment invariant (normative).** All raw content in LITERAL blocks MUST be padded with 0x00 bytes at the end to the nearest multiple of 8 bytes. This preserves the 64-bit stream alignment that is a foundational property of BBP-64. A parser reading the stream in 8-byte chunks MUST never encounter a non-token boundary misalignment due to LITERAL content.

**Parser reading rule (normative).** A conformant parser MUST read raw LITERAL content as follows:

```
bytes_to_read  = ceil(FEATURES(opening_token) / 8) × 8
valid_bytes    = FEATURES(opening_token)
padding_bytes  = bytes_to_read - valid_bytes   (all must be 0x00)
```

**Validator axiom (normative — Axiom Group K, rule K.1).** All padding bytes in a LITERAL block MUST be 0x00. A padding byte with any other value is a CRITICAL error.

**Grammatical features assignment (normative).** The FEATURES field of the closing token (flow=END) carries the grammatical description of the LITERAL concept as a whole. This is the sole location of grammatical FEATURES for LITERAL blocks. The opening token’s FEATURES is exclusively a byte-length field and MUST NOT encode grammatical information. The Core Engine (§15.5.1) treats the LITERAL concept’s embedding as the embedding of the opening 64-bit token; grammatical context is supplied by the closing token’s FEATURES during training.

**Example — “Arkaitz” (7 bytes, ergative singular):**

```
[NAME_PERSON, flow=LITERAL,  FEATURES=7]
[raw bytes: 41 72 6B 61 69 74 7A 00]   ← "Arkaitz" + 1 byte 0x00 padding
[NAME_PERSON, flow=END,      FEATURES=<case=ERG, number=SG, ...>]
```

**Example — “Shakespeare” (11 bytes, absolutive singular):**

```
[NAME_PERSON, flow=LITERAL,  FEATURES=11]
[raw bytes: 53 68 61 6B 65 73 70 65]   ← "Shakespe" (8 bytes, first chunk)
            [61 72 65 00 00 00 00 00]   ← "are" + 5 bytes 0x00 padding
[NAME_PERSON, flow=END,      FEATURES=<case=ABS, number=SG, ...>]
```

Token indexing counts only logical tokens (opening and closing tokens), not the raw byte chunks.

**Encoder rule.** Encoders MUST NOT use LITERAL blocks for concepts that have a dictionary entry. LITERAL blocks are reserved for: (a) raw strings for concepts not yet in the dictionary; (b) numeric values exceeding the 32-bit FEATURES range (§10.2). Use DICT_WORD, NAME_*, SCI_*, or other referential meta-types with concept_id for all concepts with dictionary entries.

### 4.5 Case Field (bits 25–29)

The 5-bit case field encodes the grammatical and semantic role of the entity in its proposition. 29 values are defined; 3 slots (0x1D–0x1F) are reserved for future cases.

```
Code | Hex  | Name     | Description
-----|------|----------|------------------------------------------------------------
  0  | 0x00 | UNMARKED | No case marking (default for intransitive constructions and sparse encoders)
  1  | 0x01 | ABS      | Absolutive — patient of transitive; subject of intransitive
  2  | 0x02 | ERG      | Ergative — agent of transitive verb
  3  | 0x03 | DAT      | Dative — recipient / beneficiary
  4  | 0x04 | GEN_POSS | Genitive-possessive — ownership ("Juan's book")
  5  | 0x05 | GEN_LOC  | Genitive-locative — spatial/relational genitive ("of the house")
  6  | 0x06 | COM      | Comitative / Sociative — accompaniment ("with Juan")
  7  | 0x07 | INS      | Instrumental — means or tool ("by means of")
  8  | 0x08 | INE      | Inessive — static position INSIDE ("in the house")
  9  | 0x09 | ALL      | Allative — movement TOWARDS / TO (general)
 10  | 0x0A | ABL      | Ablative — FROM (general, static source)
 11  | 0x0B | DEST     | Destinative — FOR the benefit of, intended for
 12  | 0x0C | PART     | Partitive — partial or indefinite quantity
 13  | 0x0D | PROL     | Prolative — THROUGH / VIA ("through the forest")
 14  | 0x0E | MOT      | Motivative / Causal — BECAUSE OF ("because of the rain")
 15  | 0x0F | ALL_TERM | Allative-terminative — UP TO / AS FAR AS ("until the city")
 16  | 0x10 | ALL_DIR  | Allative-directional — TOWARDS (directional, not terminal)
 17  | 0x11 | TOP      | Topical — discourse topic (Japanese は, Korean 은/는)
 18  | 0x12 | ILL      | Illative — motion INTO ("into the house")
 19  | 0x13 | ELA      | Elative — motion OUT OF ("out of the house")
 20  | 0x14 | SUP      | Superessive — ON the surface, static ("on the table")
 21  | 0x15 | SUB      | Sublative — ONTO the surface, motion ("onto the table")
 22  | 0x16 | DEL      | Delative — FROM the surface, motion ("from off the table")
 23  | 0x17 | ADE      | Adessive — AT / NEAR, in the vicinity of ("at the house")
 24  | 0x18 | VOC      | Vocative — direct address ("O Caesar!", "يا محمد")
 25  | 0x19 | ESS      | Essive — AS / IN THE ROLE OF, static ("as a doctor")
 26  | 0x1A | TRANS    | Translative — BECOMING / transforming into ("to become a doctor")
 27  | 0x1B | SEM      | Semblative — LIKE / AS IF / similar to ("like a wolf")
 28  | 0x1C | ABE      | Abessive — WITHOUT ("without money")
29–31| 0x1D–| Reserved | Future cases
     | 0x1F |          |
```

Cases 0x00–0x11 cover the original set; cases 0x12–0x1C cover the extended spatial and discourse cases. The full inventory is listed in §4.5.

For the semantic definitions, language coverage tables, Teacher instructions, and disambiguation notes for cases ILL, ELA, SUP, SUB, DEL, ADE, VOC, ESS, TRANS, SEM, and ABE, see the Linguistic Challenge Registry (§12.0.1) and the preposition-to-case mapping table (§12.0.2.1).

### 4.6 Number (bits 22–24, 3b — 8 values)

Expanded from prior designs to cover documented grammatical number distinctions across the world's languages:

```
000 = UNMARKED        (no number marking; default for mass nouns and encoders that omit number)
001 = SINGULAR
010 = DUAL            (Arabic, Slovenian, Breton, Sanskrit)
011 = TRIAL           (Bislama, Tok Pisin, some Oceanic languages)
100 = PAUCAL          ("a few" — Arabic broken plurals, Fijian)
101 = PLURAL
110 = COLLECTIVE      (mass-as-unit reading: "cattle", "people" as a body)
111 = DISTRIBUTIVE    (each individually — some Amerindian languages;
                       also used for massified count nouns in certain registers)
```

### 4.7 Determiner (bits 18–21, 4b)

16 values encoding definiteness, deixis, and quantification. Unchanged from prior versions.

### 4.8 Noun Classifier (bits 14–17, 4b) — normative category inventory

The bit width is unchanged. The semantic content is redefined: the field no longer attempts to enumerate individual classifiers (an impossible task for languages with hundreds of productive classifiers) but instead encodes the **universal semantic category of classifier**. Language-specific classifiers are handled via the CLF auxiliary token mechanism (see §4.11).

```
0x0 = CLF_NONE          (language does not use classifiers, or noun needs none)
0x1 = CLF_ANIMATE       (animate beings in general)
0x2 = CLF_HUMAN         (humans specifically — many languages distinguish from general animate)
0x3 = CLF_LONG_THIN     (elongated objects: sticks, rivers, pencils; Japanese 本 hon)
0x4 = CLF_FLAT_SHEET    (flat/sheet-like: paper, cloth, bills; Japanese 枚 mai)
0x5 = CLF_ROUND_SMALL   (round or small objects: fruit, pills, buttons)
0x6 = CLF_BOUND_VOLUME  (bound objects with content: books, magazines; Japanese 冊 satsu)
0x7 = CLF_CONTAINER     (containers and their contents: cups, bags, bottles)
0x8 = CLF_MECHANICAL    (vehicles, machines, apparatus; Japanese 台 dai)
0x9 = CLF_COLLECTIVE    (groups, flocks, teams, sets)
0xA = CLF_ABSTRACT      (concepts, events, matters, cases)
0xB = CLF_TEMPORAL      (counted time units: days, months, years as counted entities)
0xC = CLF_SPATIAL       (places, buildings, rooms)
0xD = CLF_HONORIFIC     (classifier expressing respect toward referent: Japanese 方 kata, 様 sama)
0xE = CLF_EXTENDED      (language-specific classifier encoded in preceding CLF aux token; see §4.11)
0xF = CLF_OTHER_LITERAL (classifier in LITERAL block; for classifiers not yet in the dictionary)
```

### 4.9 Animacy (bits 12–13, 2b — separated field)

Separated from noun_class in r21. Encodes the universal animacy hierarchy (Silverstein hierarchy):

```
00 = INANIMATE     (objects, abstractions)
01 = ANIMATE       (living beings in general — flora and fauna)
10 = HUMAN         (human beings — distinct grammatical treatment in many languages)
11 = DIVINE        (supernatural, spiritual entities — grammaticalized in some languages)
```

Languages that mark fewer distinctions use the lower values; languages that grammaticalize the full hierarchy use all four. Value `00` (INANIMATE) is the default and need not be set explicitly when animacy is not marked by the source language or encoder.

### 4.10 Noun Class (bits 8–11, 4b — separated field)

Separated from animacy and expanded to 16 values, covering the Bantu noun class system without auxiliary tokens for core classes:

```
0x0 = NC_NONE             (no noun class system; language does not grammaticalize noun classes)
0x1 = NC_1                (Bantu cl.1: mu- singular persons)
0x2 = NC_2                (Bantu cl.2: ba- plural persons)
0x3 = NC_3                (Bantu cl.3: mu- singular things/plants)
0x4 = NC_4                (Bantu cl.4: mi- plural things/plants)
0x5 = NC_5                (Bantu cl.5: ji- / Ø- singular, often augmentatives)
0x6 = NC_6                (Bantu cl.6: ma- plurals of cl.5, liquids, collectives)
0x7 = NC_7                (Bantu cl.7: ki- singular manner/thing/diminutive)
0x8 = NC_8                (Bantu cl.8: vi- plurals of cl.7)
0x9 = NC_9                (Bantu cl.9: n- singular animals/loanwords)
0xA = NC_10               (Bantu cl.10: n- plural animals/loanwords)
0xB = NC_11               (Bantu cl.11: lu- abstracta, elongated objects)
0xC = NC_12               (Bantu cl.12: ka- diminutives singular)
0xD = NC_14               (Bantu cl.14: bu- abstract mass nouns)
0xE = NC_EXTENDED         (class > 14 or non-Bantu system; encoded via auxiliary token)
0xF = NC_ANIMATE_AGREE    (generic animate-agreement flag for languages with
                           animacy-based agreement not organized in numbered classes)
```

**Coverage note:** This inventory directly covers Swahili (15 active classes), Zulu, Xhosa, Lingala, and the majority of Bantu languages without auxiliary tokens for core classes. Languages with more than 14 active classes use `NC_EXTENDED` with an auxiliary token. Non-Bantu gender systems (Germanic, Romance, Semitic) use the animacy field instead; for these, `noun_class` is set to `NC_NONE`.

### 4.11 Register / Honorific (bits 2–7, 6b — three-dimensional system)

Expanded from 4 bits to 6 bits in r21. The flat 16-value scale is replaced by a three-dimensional encoding that covers the structural requirements of keigo (Japanese), the Korean speech level system, and European T/V distinctions, all within a single field without auxiliary tokens.

```
bits 5-4 : speech_level    (2b)
  00 = PLAIN          (casual / plain form — Japanese 普通体)
  01 = POLITE         (polite / educated — Japanese 丁寧語, -masu/-desu; Korean 해요체)
  10 = FORMAL         (formal / written register — Korean 합쇼체)
  11 = CEREMONIAL     (ceremonial / official protocol)

bits 3-2 : respect_direction  (2b)
  00 = NEUTRAL        (no directional marking)
  01 = EXALTATIVE     (respect directed at the referent of this token — Japanese 尊敬語 sonkeigo)
  10 = HUMILITIVE     (speaker self-lowering in relation to referent — Japanese 謙譲語 kenjōgo)
  11 = ADDRESSEE      (courtesy directed at the interlocutor, not the referent — teineigo proper)

bits 1-0 : domain     (2b)
  00 = GENERAL        (unmarked / common use)
  01 = BUSINESS       (professional / business context)
  10 = INSTITUTIONAL  (court, government, religious, academic)
  11 = INTIMATE       (marked intimate register — distinct from PLAIN in some systems)
```

**Combined value examples:**

| Value (binary) | Interpretation | Usage example |
|---|---|---|
| `00_00_00` | PLAIN, NEUTRAL, GENERAL | Casual speech between friends |
| `01_00_00` | POLITE, NEUTRAL, GENERAL | Default polite address |
| `01_01_00` | POLITE, EXALTATIVE, GENERAL | Referring to a superior's actions (sonkeigo) |
| `01_10_00` | POLITE, HUMILITIVE, GENERAL | Speaker's own actions toward a superior (kenjōgo) |
| `10_00_10` | FORMAL, NEUTRAL, INSTITUTIONAL | Government document or official ceremony |
| `01_11_01` | POLITE, ADDRESSEE, BUSINESS | Formal business politeness |

**HONORIFIC_CONTEXT CONTROL token:** For streams requiring explicit speaker/addressee/referent role assignment (as in complex keigo inference), the CONTROL mechanism is used with payload `0x030` (HONORIFIC_CONTEXT, §11.5). This token is **optional** — it provides role-assignment metadata for Frontier Renderers generating natural language output, but is not required for the register field to be valid.

### 4.12 CLF Auxiliary Token Mechanism

When `noun_classifier = CLF_EXTENDED (0xE)`, the language-specific classifier is encoded as a standard Entity Token immediately preceding the nominal token it classifies. This follows the general auxiliary token pattern formalized in §1.6.

```
Encoding:
  [CLF_token,  lemma=<classifier_dict_id>, meta-type=DICT_WORD, case=UNMARKED, flow=UNIT]
  [noun_token, lemma=<concept_id>,          meta-type=...,       case=<role>,   clf=0xE   ]
```

The CLF token is a standard Entity Token whose LEMMA references the classifier as a dictionary entry. Any classifier is encodable without format changes, at the cost of one additional token. The `clf=0xE` field in the following nominal token signals to parsers that a preceding CLF token is structurally bound to it.

**Validation axioms A_CLF1 and A_CLF2** apply to this mechanism; see §13.2.1.

## 5. Ontology — The Meta-Type System

### 5.0 Meta-Type Ontological Classes

The 64-slot meta-type space (LEMMA bits 30–25) is partitioned into two ontological classes as defined in §2.3: **referential** (Class A) and **self-descriptive** (Class B). This partition governs Compiler behavior (dictionary entry creation), parser behavior (lookup vs. direct bit interpretation), and BBP-IR representation (concept reference vs. explicit field encoding) for every token in the system.

Each meta-type section below MUST state its class explicitly. A meta-type section that omits the class declaration is a **specification error**. Implementations MUST NOT infer the class from token structure or heuristics — the class is normatively declared in the defining section.

**Class declaration format (normative):** Each meta-type section MUST include a line of the form:
> *Ontological class: Referential (Class A)* or *Ontological class: Self-descriptive (Class B)*

The canonical member assignments are:

| Class | Canonical members |
|-------|---------|
| Referential (Class A) | DICT_WORD, ADJECTIVE, ADVERB, PRONOUN, MODIFIER, all NAME_*, all SCI_* except LIVING_ENTITY, all LANG_*, Group 5–6 UETs with external concept references |
| Self-descriptive (Class B) | NUM_NATURAL, NUM_INTEGER, NUM_REAL, NUM_FRACTION, NUM_COMPLEX, LIVING_ENTITY, and any future meta-type explicitly designated self-descriptive in its section |

**Validator rule (V-METATYPE-CLASS — restatement):** Defined in §2.3. Any meta-type present in a binary stream whose class is not declared in this specification MUST be treated as `UNKNOWN` (0o77). Severity: CRITICAL.

### 5.1 Type Catalog

#### Group 0 (0o0x) — Grammar Base

| Code | Octal | Name | Description |
|------|-------|------|-------------|
| 0 | 0o00 | DICT_WORD | Common nouns, standard vocabulary |
| 1 | 0o01 | PRONOUN | Personal, demonstrative, relative, interrogative pronouns |
| 2 | 0o02 | ADJECTIVE | Attributive / predicative adjectives |
| 3 | 0o03 | ADVERB | Manner, time, place, degree adverbs (includes particles) |
| 4 | 0o04 | CONJUNCTION | Coordinating / subordinating connectors |
| 5 | 0o05 | INTERJECTION | Exclamations, discourse markers |
| 6 | 0o06 | QUANTIFIER | "Many", "few", "all", "none", "each" |


**Interrogative pronouns** ("who", "what", "where", "when", "how", "why") use meta-type PRONOUN (0o01) with determiner=INTERROGATIVE (0x6). See Section 6.5 for examples.

#### Group 1 (0o1x) — Numbers

| Code | Octal | Name | Description |
|------|-------|------|-------------|
| 8 | 0o10 | NUM_NATURAL | ℕ — Unsigned integers (0-4095 inline) |
| 9 | 0o11 | NUM_INTEGER | ℤ — Signed integers (-2048 to +2047 inline) |
| 10 | 0o12 | NUM_REAL | ℝ — Real / floating point |
| 11 | 0o13 | NUM_COMPLEX | ℂ — Complex numbers |
| 12 | 0o14 | NUM_FRACTION | Rational p/q |
| 13 | 0o15 | NUM_ORDINAL | "First", "second", "third" |
| 14 | 0o16 | NUM_CARDINAL | Spelled-out numbers |
| 15 | 0o17 | *Reserved* | Future numeric type |

#### Group 2 (0o2x) — Mathematics and Types

| Code | Octal | Name | Description |
|------|-------|------|-------------|
| 16 | 0o20 | MATH_CONSTANT | π, e, φ, ∞ |
| 17 | 0o21 | MATH_VARIABLE | x, y, z, θ (algebraic unknowns) |
| 18 | 0o22 | MATH_OPERATOR | +, −, ×, ÷, =, <, >, ≤, ≥, ≠ (see §16.5 ext.) |
| 19 | 0o23 | MATH_EXPRESSION | Block wrapper for compound arithmetic expressions |
| 20 | 0o24 | MATH_FUNCTION | sin, cos, log, ∑, ∫ |
| 21 | 0o25 | MATH_SET | ∈, ∪, ∩, ⊂ |
| 22 | 0o26 | TYPE_EXPR | Block wrapper for compound type expressions |
| 23 | 0o27 | TYPE_PARAM | Individual type parameter / type variable |

#### Group 3 (0o3x) — Proper Names

| Code | Octal | Name | Description |
|------|-------|------|-------------|
| 24 | 0o30 | NAME_PERSON | Human names |
| 25 | 0o31 | NAME_PLACE | Toponyms, geographic names |
| 26 | 0o32 | NAME_ORG | Organizations, companies, institutions |
| 27 | 0o33 | NAME_BRAND | Commercial brands, trademarks |
| 28 | 0o34 | NAME_WORK | Titles of works (books, films, songs) |
| 29 | 0o35 | NAME_EVENT | Named events (WWII, Olympics) |
| 30-31 | 0o36-37 | *Reserved* | Future name types |

#### Group 4 (0o4x) — Science

| Code | Octal | Name | Description |
|------|-------|------|-------------|
| 32 | 0o40 | SCI_BIOLOGY | Common biological terms (referential) |
| 33 | 0o41 | SCI_TAXONOMY | Linnean binomial names (literal block) |
| 34 | 0o42 | SCI_CHEMISTRY | Elements (FEATURES = atomic number 1-118), compounds |
| 35 | 0o43 | SCI_PHYSICS | Physical quantities, units |
| 36 | 0o44 | SCI_ASTRONOMY | Celestial bodies, catalogs |
| 37 | 0o45 | SCI_MEDICINE | Medical terms, ICD codes |
| 38 | 0o46 | LIVING_ENTITY | Biological entities with NCBI taxon ID (self-descriptive) |
| 39 | 0o47 | *Reserved — deferred to v1.1* | Candidate: MATH_STRUCTURE (algebraic structures as first-class objects). Deferred pending resolution of three blocking design decisions: (1) normative encoding mechanism for structure content (elements, relations, axioms); (2) extension of MATH_OPERATOR for structural algebraic operations; (3) extension of the EXECUTABLE flag to symbolic algebra verification. Until these three conditions are met, 0o47 MUST be treated as Reserved and MUST NOT be used by any encoder or decoder. |

**SCI_MEDICINE / SCI_CHEMISTRY:** High IDs (>4095) are handled via 32-bit lemma IDs.

#### Group 5 (0o5x) — Code & Languages

Sub-type = language *class*; FEATURES = specific language ID from the **BBP Language Registry**.

| Code | Octal | Name | Payload semantics |
|------|-------|------|-------------------|
| 40 | 0o50 | LANG_HUMAN | BBP Language Registry ID (mapped from ISO 639-3) |
| 41 | 0o51 | LANG_COMPILED | 1=C, 2=C++, 3=Rust, 4=Zig, 5=Go, 6=Java, ... |
| 42 | 0o52 | LANG_INTERPRETED | 1=Python, 2=Ruby, 3=Perl, 4=Lua, 5=R, ... |
| 43 | 0o53 | LANG_WEB | 1=JavaScript, 2=TypeScript, 3=HTML, 4=CSS, 5=WASM, ... |
| 44 | 0o54 | LANG_QUERY | 1=SQL, 2=GraphQL, 3=SPARQL, 4=XPath, ... |
| 45 | 0o55 | LANG_MARKUP | 1=XML, 2=JSON, 3=YAML, 4=TOML, 5=Markdown, ... |
| 46 | 0o56 | LANG_MACHINE | Machine code, binary, hex, assembly |
| 47 | 0o57 | *Reserved* | Future language class |

**Note on LANG_HUMAN:** ISO 639-3 codes are 3-letter strings ("eng", "spa", "eus") and cannot be encoded directly in 12 bits. The FEATURES contains a **numeric index** into the BBP Language Registry, which maps sequential IDs (0-4095) to ISO 639-3 codes, sorted by speaker count. The ~4,000 most-spoken languages cover >99.99% of texts. ISO 639-3 mapping uses 32-bit lemma IDs; no special extension is required. Rare languages may also use literal blocks.

#### Group 6 (0o6x) — Logic & Pragmatics

| Code | Octal | Name | Description |
|------|-------|------|-------------|
| 48 | 0o60 | DISAMBIGUATION | Parse ambiguity marker |
| 49 | 0o61 | REFERENCE | Coreference pointer (FEATURES = target token index) |
| 50 | 0o62 | CONDITIONAL | If/then/else block markers |
| 51 | 0o63 | TEMPORAL | Before/after/during/while; also fine aspect (Section 6.7.3) |
| 52 | 0o64 | MODAL | Possibly/necessarily/probably |
| 53 | 0o65 | EVIDENTIAL | Evidentiality marker (Section 6.7.2) |
| 54 | 0o66 | CONTROL | Stream structure and metadata (see Section 11.5) |
| 55 | 0o67 | PRESUPPOSITION | Background proposition assumed true; typed by projection behavior (see §6.8) |

#### Group 7 (0o7x) — Meta-Cognition & Reasoning

| Code | Octal | Name | Description |
|------|-------|------|-------------|
| 56 | 0o70 | HYPOTHESIS | A proposition the model proposes as possible, not as established fact |
| 57 | 0o71 | CONCLUSION | A proposition that follows logically from prior evidence or premises |
| 58 | 0o72 | RETRACTION | Invalidates a prior HYPOTHESIS or CONCLUSION (FEATURES = sub-type; target via REFERENCE) |
| 59 | 0o73 | EVIDENCE | Marks a token or block as evidential support for a CONCLUSION |
| 60 | 0o74 | QUERY | The model identifies something it needs to know but does not currently know |
| 61 | 0o75 | INFERENCE_STEP | An explicit inference rule application (modus ponens, induction, analogy, etc.) |
| 62 | 0o76 | CONFIDENCE | Degree of certainty annotation over a block (FEATURES encodes scaled confidence) |
| 63 | 0o77 | UNKNOWN | Unidentified signal / xeno-linguistics (unchanged from r6) |

### 5.2 LIVING_ENTITY (0o46) — Specification

LIVING_ENTITY is a **self-descriptive meta-type**: its LEMMA bits do not contain a dictionary concept_id but a structured biological descriptor. No dictionary entries are created for LIVING_ENTITY tokens; the Compiler never generates IDs for them.

LIVING_ENTITY bridges three layers that are ontologically distinct:

| Layer | BBP representation | Purpose |
|-------|--------------------|---------|
| Lexical-cultural concept | DICT_WORD ("perro", "dog") | Linguistic, grammatical, cultural |
| Scientific name | SCI_TAXONOMY ("Canis lupus familiaris") | Formal Linnean nomenclature |
| Biological entity with properties | LIVING_ENTITY(taxon=9615, ALIVE) | Ontological, reasoning, scientific |

The Frontier Translator selects the appropriate layer based on context: "el perro duerme" in a scientific context → LIVING_ENTITY; "pidió ternera en el restaurante" → DICT_WORD (the referent is a culinary concept, not a biological entity).

#### 5.2.1 LEMMA Layout

```
LEMMA [63:32] — LIVING_ENTITY (self-descriptive):

  bit  31     : 0                     (Entity discriminator)
  bits 30-25  : 0o46                  (meta-type = LIVING_ENTITY)
  bits 24-22  : kingdom (3b)          (biological kingdom, see table)
  bits 21- 0  : taxon_id (22b)        (NCBI Taxonomy ID, range 0–4,194,303)
```

**Kingdom field (3b):**

```
000 = Animalia      (animals)
001 = Plantae       (plants)
010 = Fungi         (fungi)
011 = Protista      (protists, protozoa, algae)
100 = Bacteria      (bacteria)
101 = Archaea       (archaea)
110 = Virus         (viruses and viroids)
111 = Unclassified  (unknown or disputed kingdom)
```

The kingdom field is redundant with the taxon_id (NCBI already encodes kingdom), but provides O(1) kingdom-level reasoning via bit masking without any database lookup. A parser can determine "is this an animal?" in a single AND operation.

**NCBI taxon_id (22b):** The NCBI Taxonomy database assigns stable numeric IDs to every described taxon. 22 bits cover IDs 0–4,194,303, comfortably exceeding the ~3 million IDs currently assigned. The taxon_id references the species level by default; subspecies are encoded via ADJECTIVE modifier tokens (see §5.2.3).

Selected reference IDs:
```
9606  = Homo sapiens
9615  = Canis lupus familiaris (domestic dog)
9913  = Bos taurus (cattle)
9031  = Gallus gallus (chicken)
4932  = Saccharomyces cerevisiae (baker's yeast)
3702  = Arabidopsis thaliana (thale cress)
562   = Escherichia coli
11234 = Measles morbillivirus (measles virus)
```

#### 5.2.2 FEATURES Layout

LIVING_ENTITY preserves the **full standard grammatical FEATURES layout** (§4.1) so that living entity tokens function normally in natural language sentences — carrying case, number, determiner, and all other grammatical fields.

The only change is the repurposing of the 2 reserved bits (bits 1–0):

```
FEATURES (32b) — LIVING_ENTITY:
  bits 31-30  : Flow control      (2b)   [unchanged]
  bits 29-25  : Case              (5b)   [unchanged]
  bits 24-22  : Number            (3b)   [unchanged — includes COLLECTIVE]
  bits 21-18  : Determiner        (4b)   [unchanged]
  bits 17-14  : Noun classifier   (4b)   [unchanged]
  bits 13-12  : Animacy           (2b)   [unchanged]
  bits 11- 8  : Noun class        (4b)   [unchanged]
  bits  7- 2  : Register          (6b)   [unchanged]
  bits  1- 0  : vital_status      (2b)   [NEW — replaces reserved]
```

**Vital status field (2b):**

```
00 = UNSPECIFIED   No vital status marked.
01 = ALIVE         Individual organism is alive.
                   Implies the taxon is not extinct.
10 = DEAD          Individual organism is dead (body present, specimen, fossil).
                   The taxon may still have living members.
11 = EXTINCT       The taxon is extinct — no living individuals exist or can exist.
                   Applies to the taxon as a whole, not to a specific individual.
```

**Validator rule (V-LIVING-1):** A LIVING_ENTITY token with vital_status=ALIVE whose taxon_id is marked EXTINCT in the NCBI reference dataset is a semantic inconsistency → MAJOR warning.

**Validator rule (V-LIVING-2):** A LIVING_ENTITY token with vital_status=EXTINCT and number ≠ COLLECTIVE or PLURAL is anomalous (extinction is a property of the taxon, not an individual) → MINOR warning.

**Validator rule (V-LIVING-3):** If `NCBI_TAXONOMY_VERSION` is present in the stream header (§11.3), the Validator SHOULD verify that the `kingdom` field of each LIVING_ENTITY token is consistent with the kingdom assignment of the declared taxon_id in the declared NCBI Taxonomy version. An inconsistency indicates a stale kingdom field (the taxon_id is authoritative; the kingdom field is derived and may lag behind). Severity: MAJOR warning (auto-correction permitted: update kingdom to match NCBI authoritative value).

**Validator rule (V-LIVING-4):** A LIVING_ENTITY token whose taxon_id is marked as deprecated or merged in the declared NCBI Taxonomy version MUST be flagged. The Validator SHOULD report the successor taxon_id if available in the NCBI redirect table. Auto-correction is only permitted when the redirect is unambiguous (1:1 mapping). Split events (1:N) MUST NOT be auto-corrected — they require human resolution because the successor taxon cannot be determined from the token content alone. Severity: MAJOR warning.

**Taxon_id stability policy (normative).** NCBI taxon_ids referenced in BBP streams are interpreted relative to the NCBI Taxonomy version declared in `NCBI_TAXONOMY_VERSION` in the stream header. In the absence of a declared version, taxon_ids are interpreted against the NCBI Taxonomy version current at the time of stream creation, which is unverifiable post-hoc. Implementations that process streams long after creation SHOULD treat undeclared NCBI versions as informational uncertainty, not as errors. This policy is the rationale for the MUST requirement on `NCBI_TAXONOMY_VERSION` in §11.3: a stream without a declared version cannot be reliably validated or migrated.

#### 5.2.3 Compositional Properties

Biological properties not encodable in the 2-bit vital_status are expressed as **ADJECTIVE modifier tokens** immediately preceding or following the LIVING_ENTITY token, consistent with BBP's compositionality principle:

**Developmental stage** (relative to kingdom):

```
For Animalia:
  ADJECTIVE(GAMETE)         — gamete (sperm, egg)
  ADJECTIVE(EMBRYO)         — embryo / zygote
  ADJECTIVE(FETUS)          — fetus (mammals) / larva (invertebrates)
  ADJECTIVE(NEONATE)        — newborn
  ADJECTIVE(INFANT)         — infant / cub / calf / chick
  ADJECTIVE(JUVENILE)       — juvenile / subadult
  ADJECTIVE(ADULT)          — sexually mature adult
  ADJECTIVE(SENESCENT)      — post-reproductive / senile

For Plantae:
  ADJECTIVE(SEED)           — seed / spore
  ADJECTIVE(SEEDLING)       — seedling / germination
  ADJECTIVE(VEGETATIVE)     — vegetative growth
  ADJECTIVE(FLOWERING)      — flowering
  ADJECTIVE(FRUITING)       — fruiting
  ADJECTIVE(DORMANT)        — dormant (winter)
```

**Biological sex** (when semantically relevant):

The `natural_gender` field (§2.3) applies to DICT_WORD and NAME_PERSON. For LIVING_ENTITY, biological sex is expressed as an ADJECTIVE modifier when it carries semantic content beyond what the context already makes clear:

```
ADJECTIVE(BIOLOGICAL_MALE)    — male organism
ADJECTIVE(BIOLOGICAL_FEMALE)  — female organism
```

**Examples:**

```
"una bebé" (a baby girl):
  [LIVING_ENTITY, kingdom=Animalia, taxon=9606, case=ABS, number=SG, det=INDEF, vital=ALIVE]
  [ADJECTIVE, INFANT]
  [ADJECTIVE, BIOLOGICAL_FEMALE]
  → Lexicalization index: ES → "bebé", EN → "baby girl", EU → "haurtxoa"

"la humanidad":
  [LIVING_ENTITY, kingdom=Animalia, taxon=9606, case=ABS, number=COLLECTIVE, det=DEF, vital=ALIVE]
  → Lexicalization index: ES → "humanidad", EN → "humanity", EU → "gizateria"

"el dodo" (extinct):
  [LIVING_ENTITY, kingdom=Animalia, taxon=8187, case=ABS, number=SG, det=DEF, vital=EXTINCT]
  → Lexicalization index: ES → "dodo", EN → "dodo", FR → "dodo"

"una ternera":
  [LIVING_ENTITY, kingdom=Animalia, taxon=9913, case=ABS, number=SG, det=INDEF, vital=ALIVE]
  [ADJECTIVE, JUVENILE]
  [ADJECTIVE, BIOLOGICAL_FEMALE]
  → Lexicalization index: ES → "ternera", EN → "heifer", FR → "génisse"
```

**Conservation and reproductive status** (canonical order: category 4, vital/existential status):

```
ADJECTIVE(FUNCTIONALLY_EXTINCT)  — taxon has living individuals but is
                                   reproductively non-viable as a species;
                                   natural reproduction is no longer possible.
                                   Used with vital_status=ALIVE.

ADJECTIVE(ENDANGERED)            — taxon is at high risk of extinction per
                                   IUCN Red List criteria. Compatible with
                                   vital_status=ALIVE or vital_status=UNSPECIFIED.

ADJECTIVE(INVASIVE)              — taxon is ecologically invasive outside its
                                   native range. Does not imply any vital status.
```

These are standard ADJECTIVE dictionary entries (concept_id in the 23-bit space for ADJECTIVE). No bit-layout changes are required.

**Example** (northern white rhinoceros):

```
"el rinoceronte blanco del norte":
  [LIVING_ENTITY, kingdom=Animalia, taxon=57771, case=ABS, number=SG, det=DEF, vital=ALIVE]
  [ADJECTIVE, FUNCTIONALLY_EXTINCT]
  → Lexicalization index: ES → "rinoceronte blanco del norte",
                          EN → "northern white rhinoceros"
```

**Validator rule (V-LIVING-5):** A LIVING_ENTITY token modified by `ADJECTIVE(FUNCTIONALLY_EXTINCT)` MUST have `vital_status=ALIVE`. Combining `ADJECTIVE(FUNCTIONALLY_EXTINCT)` with `vital_status=EXTINCT` is semantically contradictory: a functionally extinct taxon has living individuals (hence ALIVE); a fully extinct taxon (EXTINCT) has none, making reproductive viability moot. Severity: MAJOR warning.

#### 5.2.4 Relationship with SCI_BIOLOGY and SCI_TAXONOMY

```
DICT_WORD("perro")          Lexical-cultural concept. Used in everyday language,
                            literary contexts, culinary contexts. Grammar-first.

LIVING_ENTITY(taxon=9615)   Biological entity. Used in scientific, veterinary,
                            ecological reasoning. Ontology-first.

SCI_TAXONOMY(text)          Formal Linnean name as text. Used in scientific
                            nomenclature, taxonomy publications, species lists.
```

These three representations are not alternatives — they serve different purposes and may all appear in the same document. A scientific paper discussing "la evolución del perro doméstico (*Canis lupus familiaris*)" would use all three: DICT_WORD for the common name, LIVING_ENTITY for reasoning about the biological entity, SCI_TAXONOMY for the formal name.

### 5.3 Lexicalization Index

#### 5.3.1 Purpose

The Lexicalization Index maps frequent BBP compositional patterns to idiomatic target-language surface forms. It exists because compositional generation (assembling a surface form token-by-token from BBP structure) sometimes produces grammatically correct but non-idiomatic output. The index short-circuits this for known patterns.

Example: `[LIVING_ENTITY(9606), ADJECTIVE(INFANT), ADJECTIVE(BIOLOGICAL_FEMALE)]` → ES: "bebé", EN: "baby girl", EU: "haurtxoa". The compositional Renderer would produce "female infant human" in English — correct but unnatural.

#### 5.3.2 Canonical modifier ordering (normative)

Modifier tokens (ADJECTIVE, MODIFIER) that follow a head token MUST be emitted in the following fixed precedence order, both in the binary stream and in the Lexicalization Index:

1. Taxonomic / classificatory (subspecies, variety)
2. Developmental stage (EMBRYO, INFANT, JUVENILE, ADULT, SENESCENT, SEED, SEEDLING…)
3. Biological sex (BIOLOGICAL_MALE, BIOLOGICAL_FEMALE)
4. Vital / existential status (FUNCTIONALLY_EXTINCT, ENDANGERED, INVASIVE, and analogues)
5. Other properties (order among themselves: ascending by concept_id as tiebreaker)

A Frontier Translator that emits modifiers out of canonical order is non-conformant. The Axiomatic Validator MUST check modifier ordering for head tokens with multiple modifiers. The Lexicalization Index MUST store patterns in canonical order; lookup is therefore a direct sequence match with no runtime normalisation.

**Validator rule (V-MOD-ORDER):** For any head token followed by two or more ADJECTIVE or MODIFIER tokens, the sequence MUST conform to the canonical precedence defined in §5.3.2. Out-of-order sequences SHOULD be auto-corrected by reordering. Severity: MAJOR (auto-correctable).

#### 5.3.3 Data structure

The index is a map:

```
canonical_pattern → { lang_id → surface_form }
```

Where `canonical_pattern` is an ordered sequence of BBP token descriptors in canonical modifier order (§5.3.2), normalised as follows:
- Each entry is a `(meta-type, concept_id)` pair for referential tokens, or a `(meta-type, structured_descriptor)` for self-descriptive tokens.
- Grammatical FEATURES fields (case, number, determiner) are excluded from the key. The index matches regardless of grammatical context — declension is applied by the Renderer after lexicalisation lookup.
- Order is canonical (§5.3.2): the index has exactly one entry per semantic pattern regardless of emission order in the source stream.

#### 5.3.4 Entry threshold

A pattern is eligible for index inclusion when it meets both conditions:
1. Corpus frequency ≥ N occurrences across the training corpus (N is an implementation parameter; recommended default: 50).
2. The idiomatic surface form differs from the compositional Renderer output in at least one target language.

Condition 2 prevents the index from growing with redundant entries that add no rendering value.

#### 5.3.5 Renderer integration

The Renderer MUST consult the index **before** compositional generation. The lookup algorithm is:

1. On encountering a token that could be the start of an indexed pattern, the Renderer performs a prefix lookup.
2. If a complete match is found for the current language, the Renderer emits the indexed surface form and advances past the matched tokens.
3. If no match is found, or if the current language has no entry for the matched pattern, the Renderer falls back to compositional generation.

Index lookup is O(k) where k is the length of the longest indexed pattern.

#### 5.3.6 Frontier Translator integration (bidirectional)

In the NL→BBP direction, the Frontier Translator MUST use the index as a recognition table when the source text contains a surface form present in the index for the source language: the Translator MUST emit the corresponding canonical BBP pattern rather than analysing the form compositionally. A Translator that sees "bebé" in Spanish and emits a single DICT_WORD token is non-conformant; the index mandates emitting `LIVING_ENTITY(9606) + ADJECTIVE(INFANT) + ADJECTIVE(BIOLOGICAL_FEMALE)`.

The full 4-step NL→BBP decision hierarchy (dictionary lookup → index lookup → compositional decomposition → LITERAL block) is specified in §13.6.6 and governs the ordering relationship between index lookup and other resolution strategies.

#### 5.3.7 Index growth (informative)

The index grows automatically during Frontier Translator training: patterns that consistently co-occur with idiomatic surface forms in the training corpus are extracted and added. The index is a living artefact versioned alongside the vocabulary database, not part of the binary protocol. Index version is informational and MUST NOT affect stream validity.

> **See also §13.6.6** for the full Frontier Translator decision hierarchy in the NL→BBP direction (4-step priority order), the candidate index mechanism for human review before entry promotion, the JSON entry format, and the publication cadence. §13.6.6 and §5.3 are complementary: §5.3 defines the normative data structure, canonical ordering, and Renderer integration; §13.6.6 defines the Frontier Translator usage and growth pipeline.

---

### 5.4 Type Validation Rules

1. A MATH_OPERATOR inside a non-MATH_EXPRESSION block SHOULD trigger a warning.
2. A MATH_EXPRESSION block inside a BBP-AST type annotation position SHOULD be replaced with TYPE_EXPR (see Axiom E13).
3. A SCI_TAXONOMY UET SHOULD use literal blocks (LITERAL + END).
3. A SCI_CHEMISTRY UET (0o42) MAY encode atomic number in FEATURES (1-118).
4. A REFERENCE UET (0o61) MUST have a FEATURES value that is a valid **logical token index** in the current stream (range 0-4095). Forward references are handled via PLACEHOLDER (Section 8.4), not REFERENCE.
5. A LANG_* UET with flow=LITERAL opens a code literal block; raw bytes follow until END.
6. Empty blocks (START immediately followed by END with no intervening content) are **invalid**. Parsers SHOULD discard them with a warning.
7. A LITERAL token whose byte_length is 0 is **invalid**. Use UNIT for contentless tokens.
8. An EVIDENTIAL UET (0o65) MUST immediately precede a UAT. An EVIDENTIAL not followed by a UAT is invalid.
9. A TEMPORAL UET (0o63) with function=ASPECT_FINE MUST immediately precede a UAT (or an EVIDENTIAL+UAT pair). See Section 6.7.
10. A HYPOTHESIS UET (0o70) SHOULD use flow=START/END to delimit the hypothesized proposition. A HYPOTHESIS with flow=UNIT is valid for single-token hypotheses.
11. A CONCLUSION UET (0o71) SHOULD be preceded by at least one EVIDENCE (0o73) block or INFERENCE_STEP (0o75) in the same reasoning scope. Absence triggers a MINOR warning (not an error — conclusions from implicit reasoning are valid but flagged).
12. A RETRACTION UET (0o72) MUST be followed by a REFERENCE UET (0o61) whose FEATURES points to the retracted HYPOTHESIS or CONCLUSION. A RETRACTION not followed by a REFERENCE is invalid.
13. An EVIDENCE UET (0o73) SHOULD use flow=START/END to delimit the evidential content. The content MAY include any valid BBP tokens: natural language propositions, code blocks (BBP-AST), mathematical expressions, or references to prior blocks.
14. A QUERY UET (0o74) SHOULD use flow=START/END to delimit the question content. The content SHOULD include at least one PRONOUN with determiner=INTERROGATIVE or a DICT_WORD identifying the unknown.
15. An INFERENCE_STEP UET (0o75) MUST immediately precede or be contained within a CONCLUSION block. An INFERENCE_STEP outside any reasoning context triggers a MINOR warning.
16. A CONFIDENCE UET (0o76) modifies the immediately preceding or enclosing block. It does not open/close blocks of its own (flow SHOULD be UNIT).
17. A PRESUPPOSITION UET (0o67) MUST use flow=START/END to delimit the presupposed proposition. A PRESUPPOSITION with flow=UNIT is invalid — a presupposition with no propositional content is structurally meaningless. The first child token of a PRESUPPOSITION block MUST NOT be a UAT; the block encodes a proposition, not a bare predicate.
18. A PRESUPPOSITION block SHOULD precede the proposition that triggers it (the assertion that carries the presupposition). A PRESUPPOSITION appearing after its trigger proposition is valid but triggers a MINOR warning.
19. A PRESUPPOSITION with `projection=PROJECTS` MUST NOT be nested inside a CONDITIONAL block that explicitly models a presupposition-cancelling context (e.g., "I don't know whether she stopped working" — the factive presupposition is suspended). Use `projection=HOLES` in such contexts.

---

## 6. Modifiers, Attribution, and Scope

### 6.1 Entity Modifiers (Adjectives)

A UET with meta-type MODIFIER (0o07) or ADJECTIVE (0o02) that immediately **precedes or follows** another UET modifies that UET. The modifier inherits the declension of its head.

```
[gizon, DICT_WORD, ERG, SINGULAR, DEF]     → "the man"
[handi, ADJECTIVE, ERG, SINGULAR, DEF]     → "big" (follows head → modifies it)
```

This mirrors Basque: *gizon handia* = man big-the.

**Normalization:** The Teacher LLM SHOULD normalize modifier order to **head-first** (modifier follows head), matching Basque and head-initial typology. However, parsers MUST also accept modifier-before-head order for robustness.

### 6.2 Verb Modifiers (Adverbs)

A UET with meta-type ADVERB (0o03) that immediately **precedes or follows** a UAT modifies that action.

```
[azkar, ADVERB, UNMARKED]              → "quickly"
[jan, UAT, NOR-NORK, PRES, ...]        → "eat"
```

**Normalization:** The Teacher LLM SHOULD normalize to adverb-before-verb order. Parsers MUST accept either order.

### 6.3 Light Verb Constructions

In languages with light verb constructions (Hindi *kaam karnā* = "work do" = "to work", Basque *lan egin* = "work make" = "to work"), a DICT_WORD UET immediately before a UAT whose verb root is a **light verb** (IDs 0x0001–0x000F) forms a compound predicate.

```
[kaam, DICT_WORD, ABS]                 → "work" (semantic content)
[karnā, UAT, NOR-NORK, light verb]     → "do" (grammatical frame)
```

### 6.4 Negation Scope

BBP uses two independent negation mechanisms with **different scopes**:

| Mechanism | Scope | Example |
|-----------|-------|---------|
| UAT polarity bit = NEG | **Narrow:** negates the verb only | "Students did-NOT pass" (∀x: ¬passed(x)) |
| UET determiner = NEGATIVE (0x9) | **Wide:** negates the quantifier/entity | "NO students passed" (¬∃x: passed(x)) |

**"Not all students passed"** (¬∀ → ∃¬):

```
[estudiante, DICT_WORD, ERG, PLURAL, NEG]     → "no/not-all students" (NEGATIVE det)
[examen, DICT_WORD, ABS, SINGULAR, DEF]         → "the exam"
[aprobar, UAT, NOR-NORK, AFF, PERF, ...]    → "passed" (affirmative verb)
```

**"All students did not pass"** (∀¬):

```
[estudiante, DICT_WORD, ERG, PLURAL, UNIV]    → "all students" (UNIVERSAL det)
[examen, DICT_WORD, ABS, SINGULAR, DEF]         → "the exam"
[aprobar, UAT, NOR-NORK, NEG, PERF, ...]    → "did not pass" (negative verb)
```

If the scope is ambiguous, a DISAMBIGUATION token SHOULD precede the ambiguous element.

### 6.5 Interrogative Pronouns

Question words ("who", "what", "where", "when", "how", "why") are encoded as PRONOUN (0o01) with determiner=INTERROGATIVE (0x6):

```
"¿Quién vino?" (Who came?):
[quién, PRONOUN, ABS, SINGULAR, INTER]     → Interrogative pronoun, absolutive
[venir, UAT, NOR, PAST, PERF, S:3]     → "came"
[CONTROL, QUESTION, flow=UNIT]          → Question mark
```

The FEATURES of the PRONOUN differentiates the specific question word (0=who, 1=what, 2=where, 3=when, 4=how, 5=why, etc.).

### 6.6 Complex Modifier Chains

For multi-word modifiers ("the man with the red shirt"), use semantic blocks:

```
[gizon, DICT_WORD, flow=START, FEATURES=42]
  [alkandora, DICT_WORD, GEN-POSS]
  [gorri, ADJECTIVE, GEN-POSS]
[DICT_WORD, flow=END, case=ERG, SINGULAR, DEF]
```

### 6.8 Presupposition — PRESUPPOSITION Block (0o67)

Presupposition is the set of propositions a speaker takes for granted as background context when making an assertion. Classically, the presupposition of "Has she stopped working?" is "she was working before" — a proposition that is assumed true regardless of whether the question is answered affirmatively, negatively, or not at all. Unlike entailments, presuppositions *project* through negation and interrogation; unlike conversational implicatures, they are not cancelled by "but I don't mean to imply that...".

BBP encodes presuppositions natively via the **PRESUPPOSITION meta-type (0o67)**, using START/END blocks to delimit the presupposed proposition and a typed FEATURES payload to specify projection behavior.

#### 6.8.1 PRESUPPOSITION Token FEATURES Layout

```
PRESUPPOSITION UET — FEATURES (32 bits):

  bit  31     : 0                    (Entity discriminator, as all UETs)
  bits 30-29  : flow (2b)            START=01, END=10 (UNIT invalid for PRESUPPOSITION)
  bits 28-25  : presupposition_type (4b)
    0x0 = EXISTENTIAL    — "The king of France is bald" → presupposes France has a king.
                           Arises from definite descriptions and possessives.
    0x1 = FACTIVE        — "She knows/regrets/realizes that P" → presupposes P is true.
                           Triggered by factive verbs (know, realize, regret, notice...).
    0x2 = LEXICAL        — "She stopped working" → presupposes she was working.
                           Triggered by aspectual/implicative verbs (stop, continue,
                           manage, fail, start, quit...).
    0x3 = STRUCTURAL     — "When did you stop?" → presupposes you did stop.
                           Arises from wh-questions and cleft constructions.
    0x4 = COUNTERFACTUAL — "If only she had come" → presupposes she didn't.
                           Arises from counterfactual conditionals.
    0x5 = ITERATIVE      — "She passed the exam again" → presupposes a prior passing.
                           Triggered by iterative adverbs (again, still, back...).
    0x6-0xE = reserved
    0xF = OTHER          — Unclassified presupposition; a DISAMBIGUATION token
                           SHOULD follow with a note in the BBP Linguistic Conventions.
  bits 24-23  : projection_behavior (2b)
    00 = PROJECTS        — Survives negation, questioning, and embedding under modals.
                           Classical presupposition behavior.
    01 = HOLES           — The presupposition is suspended by an operator in the
                           immediate context (e.g., "I don't know whether she stopped
                           working" — factive presupposition is under a HOLES operator).
    10 = PLUGS           — The presupposition is blocked by a plug context
                           (e.g., reported speech: "John said she stopped working"
                           does not presuppose she was working in the speaker's world).
    11 = FILTERS         — The presupposition is conditioned on the antecedent of a
                           conditional or conjunct (e.g., "If she was working, she
                           stopped" — presupposition filtered by if-clause).
  bits 22- 0  : reserved (must be 0)
```

The FEATURES fields of the START token carry the presupposition type and projection behavior. The END token FEATURES MUST be 0.

#### 6.8.2 Token Stream Structure

A PRESUPPOSITION block immediately **precedes** the proposition that triggers it:

```
[PRESUPPOSITION, flow=START, type=LEXICAL, projection=PROJECTS]
  — presupposed proposition encoded as normal BBP tokens —
[PRESUPPOSITION, flow=END]
— triggering proposition follows —
```

For sentences with multiple presuppositions, multiple PRESUPPOSITION blocks are emitted in order, each preceding the triggering token or clause.

#### 6.8.3 Examples

**"¿Ha dejado de trabajar?" (Has she stopped working?)**

The verb *dejar de* (stop + infinitive) triggers a LEXICAL presupposition ("she was working").
The interrogative form does not cancel it — it PROJECTS through the question.

```
Token 0: [PRESUPPOSITION, flow=START, type=LEXICAL, projection=PROJECTS]
Token 1:   [she, PRONOUN, ERG, SINGULAR, DEF]
Token 2:   [work, UAT, NOR-NORK, PAST, IMPERF, S:3]      → "was working"
Token 3: [PRESUPPOSITION, flow=END]

Token 4: [she, PRONOUN, ERG, SINGULAR, DEF]
Token 5: [stop_working, UAT, NOR-NORK, PRES, PERF, S:3]  → "has stopped working"
Token 6: [CONTROL, QUESTION, flow=UNIT]
```

**"The king of France is bald." (EXISTENTIAL — Bertrand Russell's classic)**

```
Token 0: [PRESUPPOSITION, flow=START, type=EXISTENTIAL, projection=PROJECTS]
Token 1:   [France, NAME_PLACE, GEN_POSS, SINGULAR, DEF]
Token 2:   [king, DICT_WORD, ABS, SINGULAR, DEF]
Token 3:   [exist, UAT, NOR, PRES, ...]
Token 4: [PRESUPPOSITION, flow=END]

Token 5: [king_of_France, DICT_WORD, ABS, SINGULAR, DEF]  → (via REFERENCE to token 2)
Token 6: [bald, ADJECTIVE, ABS, SINGULAR, DEF]
Token 7: [be, UAT, NOR, PRES, PERF, S:3]
```

**"John said she stopped working." (PLUGS — presupposition blocked by reported speech)**

The FACTIVE presupposition of "stopped working" (she was working) is blocked:
it is true in John's reported world, not necessarily in the speaker's world.

```
Token 0: [PRESUPPOSITION, flow=START, type=LEXICAL, projection=PLUGS]
Token 1:   [she, PRONOUN, ERG, SINGULAR, DEF]
Token 2:   [work, UAT, NOR-NORK, PAST, IMPERF, S:3]
Token 3: [PRESUPPOSITION, flow=END]

Token 4: [John, NAME_PERSON, ERG, SINGULAR, DEF]
Token 5: [say, UAT, NOR-NORK, PAST, PERF, S:3, O:3]     → said
  [she, PRONOUN, ERG, SINGULAR, DEF, flow=START]          → embedded clause
  [stop_working, UAT, NOR-NORK, PAST, PERF, S:3]
  [flow=END]
```

#### 6.8.4 BBP-IR JSON Representation

```json
{
  "type": "PRESUPPOSITION",
  "presupposition_type": "LEXICAL | EXISTENTIAL | FACTIVE | STRUCTURAL | COUNTERFACTUAL | ITERATIVE | OTHER",
  "projection": "PROJECTS | HOLES | PLUGS | FILTERS",
  "content": [ /* normal BBP-IR tokens for the presupposed proposition */ ]
}
```

#### 6.8.5 Frontier Translator Guidance

Presupposition detection is a **Tier 3 task** (§13.2.0.1): it requires inference from lexical triggers (aspectual verbs, factive verbs, definite descriptions, wh-questions) and contextual analysis of embedding operators. The hybrid Teacher pipeline (§13.2.0) SHOULD delegate presupposition detection to the LLM Semantic Enricher (Component 3) with a targeted prompt instructing it to:

1. Identify aspectual and implicative verbs (*stop, continue, manage, fail, start, again*) → LEXICAL.
2. Identify factive verbs (*know, realize, regret, notice, be glad that*) → FACTIVE.
3. Identify definite descriptions in argument positions → EXISTENTIAL.
4. Identify wh-questions and cleft constructions → STRUCTURAL.
5. Identify counterfactual conditionals and *if only* constructions → COUNTERFACTUAL.
6. Determine the projection environment (plain assertion → PROJECTS; under *say/think/believe* → PLUGS; under *if* → FILTERS; under *know whether/if* → HOLES).

Undetected presuppositions produce the same quality degradation as any undetected semantic feature. They do not cause structural errors and do not trigger Axiomatic Validator CRITICAL failures — the stream remains well-formed. Validator warning: MINOR (informational, not blocking).

## 7. Compositionality — Nesting and Subordination

### 7.1 Block Nesting via Stack

The flow control field supports arbitrary nesting. Parsers MUST implement a stack:

- On `START`: push context, increment depth.
- On `END`: pop context, decrement depth.
- On `LITERAL`: **no stack change** (raw data does not affect nesting).
- On `UNIT`: no stack change.
- Depth 0 = top-level sentence.

### 7.2 Example: Nested Relative Clauses

**Sentence:** "The man who saw the dog that bit the cat runs."

```
Token 0:  [gizon, DICT_WORD, ABS, START]            depth 0→1
Token 1:    [txakur, DICT_WORD, ABS, START]          depth 1→2
Token 2:      [katu, DICT_WORD, ABS, UNIT]           depth 2
Token 3:      [bite, UAT, NOR-NORK, PAST, S:3, O:3]  depth 2
Token 4:    [DICT_WORD, END, ABS, SINGULAR, DEF]         depth 2→1
Token 5:    [see, UAT, NOR-NORK, PAST, S:3, O:3]    depth 1
Token 6:  [DICT_WORD, END, ABS, SINGULAR, DEF]          depth 1→0
Token 7:  [run, UAT, NOR, PRES, S:3]                depth 0
```

### 7.3 Serial Verb Constructions

In serializing languages (Yoruba, Mandarin, Thai), multiple predicates share arguments within a single clause. BBP wraps serial verb chains in a CONTROL block:

```
[CONTROL, SERIAL_VERB_START, flow=START]
  [take, UAT, NOR-NORK, PAST, S:3, O:3]   → "took"
  [come, UAT, NOR, PAST, S:3]              → "came"
[CONTROL, flow=END]
```

The first UAT establishes the argument frame; subsequent UATs inherit the subject unless they specify their own.

### 7.4 Validation Rules

1. Every START MUST have a matching END. The meta-type of START and END **MUST** be identical (axiom A6, §13.2.1). A mismatch is a structural error (SEVERITY: CRITICAL). **Exception:** CONTROL START/END blocks, where the END token's meta-type is always CONTROL (0o66) regardless of the specific CONTROL function in the START FEATURES.
2. Depth counter MUST return to 0 at end of sentence.
3. A UAT inside a **semantic** block (opened by START) describes a relationship involving the block's head entity.
4. A UAT inside a **CONTROL** block follows the specific CONTROL sub-type semantics (e.g., serial verb chains share arguments; conditional blocks define if/then).
5. Maximum recommended nesting depth: 8 levels.

---

## 8. Coreference — Anaphora Resolution

### 8.1 Solution: REFERENCE Token

A UET with meta-type REFERENCE (0o61) uses its FEATURES payload field as a **backpointer** — the **logical token index** of the referent in the current stream.

```
Token 0: [Juan, NAME_PERSON, flow=LITERAL, FEATURES=4]
         [raw: 4A 75 61 6E 00 00 00 00]               → "Juan" (4 bytes content + 4 bytes 0x00 padding = 8 bytes)
Token 1: [NAME_PERSON, flow=END, case=ERG, SINGULAR, DEF]  → Juan is ergative
Token 2: [say, UAT, NOR-NORK, PAST, S:3, O:3]         → said
Token 3: [REFERENCE, FEATURES=0, ABS, flow=START]        → "he" → points to token 0
Token 4:   [come, UAT, NOR, FUT, S:3]                  → would come
Token 5: [flow=END, ABS]                                → clause closes
```

The REFERENCE FEATURES=0 points to token 0 (the LITERAL token for "Juan"). Note that raw data bytes between tokens 0 and 1 are *not* counted in the index.

### 8.2 Constraints

- Payload range 0-4095 limits backpointer to the last 4,096 logical tokens.
- A REFERENCE inherits the ontological type of its referent.
- For longer documents, use an Extended Reference (Section 8.5): a REFERENCE with `flow=LITERAL` containing an absolute 32-bit or 64-bit token index.

### 8.3 Forward References (Backward Compatibility)

Direct forward references via REFERENCE tokens remain **prohibited**. REFERENCE payloads MUST always point to a token with a lower index than the current token. For cataphoric constructions (forward-pointing pronouns), use the PLACEHOLDER mechanism (Section 8.4).

### 8.4 Cataphora — PLACEHOLDER with Deferred Resolution

Cataphoric constructions ("When **him** I saw, **Juan** was crying") place a pronoun before its referent. Since REFERENCE tokens cannot point forward, BBP provides a **PLACEHOLDER** mechanism for single-pass cataphora support.

#### 8.4.1 Mechanism

1. **Emit PLACEHOLDER pronoun:** The Teacher LLM emits a PRONOUN UET (0o01) with determiner=PLACEHOLDER (0xA). The FEATURES encodes a **local placeholder ID** (0-4095), unique within the current sentence.

2. **Emit the referent:** Later in the stream, the actual referent appears as a normal UET.

3. **Emit PLACEHOLDER_RESOLVE:** Immediately after the referent, a CONTROL UET with FEATURES=PLACEHOLDER_RESOLVE (0x041) is emitted. Its Deklinabidea encodes the resolution target:

```
┌────────────────────────────────────────────────────────┐
│  PLACEHOLDER_RESOLVE Deklinabidea (11 bits)             │
├──────────────────────┬─────────────────────────────────┤
│  Bits 6-10 (case)    │  Placeholder ID high bits (5b)  │
│  Bits 4-5 (number)   │  Placeholder ID mid bits (2b)   │
│  Bits 0-3 (det)      │  Placeholder ID low bits (4b)   │
│                       │  Combined: 11 bits → 0-2047     │
└──────────────────────┴─────────────────────────────────┘
```

The 11 Deklinabidea bits are **reinterpreted** as the placeholder ID (0-2047) being resolved. The parser locates the PLACEHOLDER UET with that ID and binds it to the referent that immediately precedes the PLACEHOLDER_RESOLVE token.

#### 8.4.2 Example

**Sentence:** "Cuando lo vi, Juan estaba llorando" (When I saw him, Juan was crying)

```
Token 0: [CONTROL, SENT_START, flow=START]
Token 1: [TEMPORAL, "cuando", flow=START]
Token 2: [él, PRONOUN, ABS, SINGULAR, PLACEHOLDER]         → placeholder ID=0 (FEATURES=0)
Token 3: [ver, UAT, NOR-NORK, PAST, PERF, S:1s, O:3s]
Token 4: [TEMPORAL, flow=END]
Token 5: [Juan, NAME_PERSON, flow=LITERAL, FEATURES=4]
         [raw: 4A 75 61 6E 00 00 00 00]               → "Juan" (4 bytes content + 4 bytes 0x00 padding = 8 bytes)
Token 6: [NAME_PERSON, flow=END, ABS, SINGULAR, DEF]
Token 7: [CONTROL, PLACEHOLDER_RESOLVE, flow=UNIT]     → deklinabidea bits = 0 → resolves placeholder #0
                                                          binds to token 5 (preceding referent)
Token 8: [llorar, UAT, NOR, PAST, IMPERF, S:3s]
Token 9: [CONTROL, flow=END]                            → sentence end
```

#### 8.4.3 Constraints

1. **Sentence scope:** All PLACEHOLDERs MUST be resolved before the enclosing SENTENCE_END. A parser encountering SENTENCE_END with unresolved PLACEHOLDERs MUST emit an error.
2. **Single-pass compatible:** The parser maintains a small table of unresolved placeholders (keyed by ID). On PLACEHOLDER_RESOLVE, binding is O(1). No look-ahead is required.
3. **Maximum IDs:** 2,048 placeholders per sentence (11-bit ID space). In practice, cataphora rarely exceeds 1-2 per sentence.
4. **Backward compatible:** Parsers that do not implement PLACEHOLDER support will see PRONOUN tokens with an unrecognized determiner (0xA). Graceful degradation: the pronoun is treated as a bare pronoun with no coreference link (semantically lossy but structurally valid).
5. **No nesting:** A PLACEHOLDER cannot itself be the target of another PLACEHOLDER. Chains are resolved sequentially.

#### 8.4.4 When to Use PLACEHOLDER vs Reordering

The Teacher LLM has two strategies for cataphoric source text:

- **Reorder** (preferred): If the reordering preserves logical meaning, simply emit the referent before the pronoun and use a normal REFERENCE backpointer. Most cataphora can be safely reordered.
- **PLACEHOLDER** (when reordering would lose pragmatic information): If the cataphoric order encodes discourse focus, topic-comment structure, or is dictated by a subordinating conjunction, use PLACEHOLDER to preserve the original information structure.

The Teacher LLM prompt MUST include examples of both strategies and criteria for choosing between them.

## 9. Disambiguation — Explicit Uncertainty

A UET with meta-type DISAMBIGUATION (0o60) is placed immediately before a token or block whose parse is uncertain.

**Payload encoding (12 FEATURES bits, numbered 0-11 relative to the FEATURES field):**

| Payload bits | Field | Description |
|------|-------|-------------|
| 0-7 | Confidence | 0-255 (0=unknown, 255=certain) |
| 8-11 | Alternatives | Number of alternative parses (0-15) |

These are bit offsets **within the FEATURES payload field** (UET bits 11-22), not absolute token bit positions.

```
[DISAMBIGUATION, confidence=184, alternatives=2]
[telescope, DICT_WORD, INSTRUMENTAL]
```

---

## 10. Number Representation

### 10.1 Numeric Token Layout (r33 — redesigned)

Numeric tokens follow an inverted field assignment relative to conceptual tokens:

```
Conceptual tokens:  LEMMA = concept identity    FEATURES = grammatical context
Numeric tokens:     LEMMA = mathematical class  FEATURES = the value itself (32-bit pure data)
```

A number has no case, animacy, determiner, or noun class. Its semantically relevant properties are mathematical: sign, order of magnitude, parity. These are encoded in the LEMMA. The value occupies all 32 FEATURES bits.

**LEMMA layout — numeric tokens:**

```
LEMMA (32b):
  bit  31     : 0              (Entity discriminator)
  bits 30-25  : meta-type      (NUM_NATURAL=0o10, NUM_INTEGER=0o11,
                                NUM_REAL=0o12, NUM_COMPLEX=0o13,
                                NUM_FRACTION=0o14)
  bit  24     : sign           (0=positive/zero, 1=negative)
                               (always 0 for NUM_NATURAL)
  bits 23-20  : magnitude      (4b) floor(log₂(|value|)), clamped 0–15
                               (0 when value=0)
  bit  19     : parity         (0=even, 1=odd)
                               (meaningful for NUM_NATURAL and NUM_INTEGER;
                                reserved=0 for NUM_REAL and NUM_FRACTION)
  bits 18-0   : reserved       (must be 0; reserved for future mathematical
                                properties: primality, perfect square, etc.)
```

**FEATURES layout — value as pure 32-bit data:**

| Meta-type | FEATURES interpretation | Inline range |
|-----------|------------------------|--------------|
| NUM_NATURAL (0o10) | Unsigned 32-bit integer | 0 – 4,294,967,295 |
| NUM_INTEGER (0o11) | Two's complement signed 32-bit | −2,147,483,648 – +2,147,483,647 |
| NUM_REAL (0o12) | IEEE 754 single precision (float32) | ±3.4×10³⁸, ~7 significant digits |
| NUM_FRACTION (0o14) | bits 31–16: numerator (uint16); bits 15–0: denominator (uint16, ≠0) | Any p/q where p,q ≤ 65535 |
| NUM_COMPLEX (0o13) | Does not fit inline → composicional START/END block (two NUM_REAL tokens) | — |

**Embedding geometry by construction.** The compositional field-level embeddings mandated in §15.5.1 ensure that numeric tokens with related mathematical properties share sub-embeddings:
- Same sign → same sign sub-embedding
- Similar magnitude → geometrically adjacent magnitude sub-embeddings
- Same parity → same parity sub-embedding

The model does not need to discover the structure of the number line statistically — it is encoded in the LEMMA architecture. This produces magnitude-aware representations unavailable with opaque BPE token IDs.

**Example:**

```
The number 2026:
  LEMMA: bit31=0, meta-type=NUM_NATURAL(0o10), sign=0,
         magnitude=10 (floor(log₂(2026))=10), parity=0 (2026 is even)
         → bits: 0_001000_0_1010_0_0000000000000000000
  FEATURES: 0x000007EA  (2026 as unsigned 32-bit)

The number −42:
  LEMMA: bit31=0, meta-type=NUM_INTEGER(0o11), sign=1,
         magnitude=5 (floor(log₂(42))=5), parity=0 (42 is even)
  FEATURES: 0xFFFFFFD6  (−42 as two's complement)

The number π:
  LEMMA: bit31=0, meta-type=NUM_REAL(0o12), sign=0,
         magnitude=1 (floor(log₂(3.14159))=1), parity=0 (reserved)
  FEATURES: 0x40490FDB  (π as IEEE 754 float32)
```

### 10.2 Extended Numbers

Required only for values that do not fit in 32 FEATURES bits.

**NUM_COMPLEX — compositional START/END block (normative).** NUM_COMPLEX is not encoded as a LITERAL block. It is encoded as a composicional block of two NUM_REAL tokens:

```
[NUM_COMPLEX, flow=START]
  [NUM_REAL, FEATURES=<float32 real part>]         → e.g. FEATURES=0x40490FDB for π
  [NUM_REAL, FEATURES=<float32 imaginary part>]    → e.g. FEATURES=0x3F800000 for 1.0
[NUM_COMPLEX, flow=END, FEATURES=<grammatical fields if applicable>]
```

The outer NUM_COMPLEX tokens carry the meta-type and flow control. The inner NUM_REAL tokens carry the component values via the standard float32 FEATURES layout (§10.1). This encoding:

- Eliminates the need for a LITERAL block for the common case (two float32 components, 8 bytes total would fit in a LITERAL but the compositional structure is architecturally cleaner).
- Preserves the full compositional embedding geometry: the Core Engine receives two separate NUM_REAL tokens whose magnitude sub-embeddings are independently learned.
- Allows float64 precision in v1.1 by replacing the inner NUM_REAL tokens with extended types without breaking the outer structure.

**Validator rule (NUM_COMPLEX structure, normative):** A NUM_COMPLEX START block MUST contain exactly two NUM_REAL tokens and no other content. Any other structure is a CRITICAL error.

**Part ordering convention (normative).** Within a NUM_COMPLEX START/END block, the FIRST inner NUM_REAL token encodes the **real part** and the SECOND inner NUM_REAL token encodes the **imaginary part**. This ordering is fixed and non-negotiable. An encoder that emits the imaginary part first produces a semantically incorrect stream (representing the complex conjugate) even if it is structurally valid. See Axiom J5.

**Large integers — LITERAL block.** For integer values exceeding the 32-bit FEATURES range:

```
[NUM_NATURAL, flow=LITERAL, FEATURES=8]      → 64-bit unsigned integer
[raw bytes: 00 00 00 01 00 00 00 00]         → value 4,294,967,296 (> 32-bit range)
[NUM_NATURAL, flow=END,     FEATURES=0]
```

LITERAL blocks for numeric types are now rare: only integers exceeding 32-bit range (outside cryptography and combinatorics) require them. For NUM_REAL, the previous requirement of an 8-byte IEEE 754 double LITERAL is eliminated — float32 inline is sufficient for the vast majority of scientific and engineering applications. For NUM_COMPLEX, the LITERAL form is replaced by the compositional START/END block above.

**Large fractions — compositional START/END block (normative).** When the numerator or denominator of a fraction exceeds 16 bits (absolute value > 65535), NUM_FRACTION uses a compositional block analogous to NUM_COMPLEX:

```
[NUM_FRACTION, flow=START]
  [NUM_INTEGER, FEATURES=<numerator as int32>]     → signed: allows negative fractions
  [NUM_NATURAL, FEATURES=<denominator as uint32>]  → always positive, MUST NOT be zero
[NUM_FRACTION, flow=END, FEATURES=<grammatical fields if applicable>]
```

The first inner token MUST be NUM_INTEGER (carrying the numerator as a signed 32-bit value), the second MUST be NUM_NATURAL (carrying the denominator as an unsigned 32-bit value). The sign of the fraction resides in the numerator; the denominator is always positive. Denominators of zero are structurally invalid.

The inline form (FEATURES bits 31–16: numerator uint16; bits 15–0: denominator uint16) remains the **canonical form** for fractions where both components fit in 16 bits. The extended compositional block MUST NOT be used when the inline form suffices. See Axioms J6 and J7.

**Validator rule (numeric coherence):**
- `magnitude` field in LEMMA MUST equal `floor(log₂(|value|))` for the value in FEATURES (auto-correctable: MAJOR warning if mismatched, auto-correct to the correct value).
- `sign` field MUST match the sign of the value in FEATURES (CRITICAL error if mismatched).
- `parity` field MUST match `value % 2` for NUM_NATURAL and NUM_INTEGER (MAJOR warning if mismatched).
- For NUM_FRACTION: denominator (FEATURES bits 15–0) MUST NOT be zero (CRITICAL error).

### 10.3 Executable Numeric Evaluation

BBP provides a two-layer mathematical architecture that separates semantic mathematical reasoning (Core Engine, probabilistic) from exact arithmetic execution (BBP runtime, deterministic):

```
┌─────────────────────────────────────────────────────┐
│  SEMANTIC LAYER (Core Engine — neural)               │
│  Learns: when, why, which operation to apply        │
│  Reasons about: magnitudes, structure, strategy     │
│  Produces: MATH_EXPRESSION[EXECUTABLE] tokens       │
│  Does NOT perform: exact arithmetic                 │
└─────────────────────┬───────────────────────────────┘
                      │ BBP stream with MATH_EXPRESSION[EXECUTABLE]
┌─────────────────────▼───────────────────────────────┐
│  EXECUTION LAYER (BBP Runtime — deterministic)       │
│  Receives: well-formed MATH_EXPRESSION              │
│  Evaluates: exact arithmetic, symbolic algebra      │
│  Inserts: guaranteed-correct result token           │
│  On error: emits MATH_ERROR (never fails silently)  │
└─────────────────────────────────────────────────────┘
```

#### 10.3.1 EXECUTABLE flag

The EXECUTABLE flag is encoded in bit 0 of the FEATURES word of the MATH_EXPRESSION START token.

```
MATH_EXPRESSION START token FEATURES:
  bit  0 : EXECUTABLE (0=declarative/current behaviour, 1=runtime evaluation)
  bits 1–31: reserved (must be 0 for MATH_EXPRESSION START)
```

**When EXECUTABLE=1 (normative):**
- The BBP runtime MUST evaluate the expression upon encountering its END token.
- The runtime inserts the result token immediately after the END token.
- The result is a numeric token (NUM_NATURAL, NUM_INTEGER, NUM_REAL, or NUM_FRACTION) of the appropriate type.
- The Core Engine does NOT generate the result token. It generates the expression and anticipates that the runtime will resolve it.
- If evaluation produces an error, the runtime emits a MATH_ERROR token instead of a result. The stream remains structurally valid.

**When EXECUTABLE=0 (current behaviour, preserved):**
- Declarative structure. The result, if present, was generated by the model and may be incorrect. Backward compatible with r32.

#### 10.3.2 MATH_ERROR token

```
meta-type: CONTROL (0o66)
flow: UNIT
FEATURES encoding:
  bits 31-28 : error code (4b)
  bits 27-16 : reserved
  bits 15-0  : REFERENCE backpointer to the originating MATH_EXPRESSION token

Error codes:
  0x0 = DIVISION_BY_ZERO
  0x1 = OVERFLOW
  0x2 = UNDERFLOW
  0x3 = DOMAIN_ERROR        (e.g. sqrt of negative in real domain)
  0x4 = UNDEFINED           (e.g. 0⁰, ∞−∞, 0/0)
  0x5-0xF = reserved
```

A MATH_ERROR does not terminate stream processing. Downstream consumers (other agents, reasoning processors) receive the error signal and may handle it contextually — for example, a BBP-CoT chain may emit a RETRACTION of any HYPOTHESIS that depended on the failed computation.

#### 10.3.3 Axiomatic Validator — Group J (Math Execution Consistency)

```
J1: A MATH_EXPRESSION with EXECUTABLE=1 MUST be followed (after its END token)
    by either a numeric token (NUM_NATURAL, NUM_INTEGER, NUM_REAL, NUM_FRACTION)
    or a MATH_ERROR token.
    A stream with EXECUTABLE=1 MATH_EXPRESSION and no following result or
    MATH_ERROR is structurally invalid.
    SEVERITY: CRITICAL (error)

J2: A MATH_EXPRESSION with EXECUTABLE=0 MAY be followed by a numeric token
    (model-generated result, potentially incorrect) or by nothing.
    A result following EXECUTABLE=0 is tagged as model-generated in the
    decode log and SHOULD NOT be treated as guaranteed correct.
    SEVERITY: informative

J3: A MATH_ERROR token MUST contain a valid REFERENCE backpointer to a
    MATH_EXPRESSION START token.
    SEVERITY: CRITICAL (error)

J4: For NUM_FRACTION results: denominator field (FEATURES bits 15–0) MUST NOT
    be zero. The runtime MUST emit MATH_ERROR(UNDEFINED) rather than a
    zero-denominator fraction.
    SEVERITY: CRITICAL (error)

J5: Within a NUM_COMPLEX START/END block, the FIRST inner NUM_REAL token MUST
    be the real part; the SECOND inner NUM_REAL token MUST be the imaginary
    part. A tool that processes these in reverse order produces the complex
    conjugate — semantically incorrect even if structurally valid.
    SEVERITY: CRITICAL (error)

J6: (NUM_FRACTION extended block structure) A NUM_FRACTION START/END block
    MUST contain exactly two tokens: the first MUST be NUM_INTEGER (numerator),
    the second MUST be NUM_NATURAL (denominator). The denominator MUST NOT be
    zero. Any other structure is a CRITICAL error.
    SEVERITY: CRITICAL (error)

J7: (NUM_FRACTION inline preference) A NUM_FRACTION START/END block whose
    numerator and denominator both fit in 16 bits (|numerator| ≤ 65535 and
    denominator ≤ 65535) is non-conformant. The inline form MUST be used
    instead. Auto-correct: replace with inline NUM_FRACTION token.
    SEVERITY: MAJOR (auto-correctable)
```

#### 10.3.4 Core Engine Training — Mathematical Reasoning Track (M)

This section specifies the Mathematical Reasoning Track (Track M) of the Reasoning Curriculum defined in §15.5.0b. Track M is a distinct training phase that precedes or runs concurrently with the general linguistic curriculum. It exploits the fact that mathematical training data can be generated **completely deterministically**, without any Teacher LLM — the purest instantiation of the Inverted Ground Truth principle.

**Phase M-A — Concrete arithmetic (fully deterministic corpus)**

A combinatorial generator produces MATH_EXPRESSION BBP structures; the runtime evaluates them; the (expression, result) pairs are correct by construction. No probabilistic component is involved.

```
Training example (mechanically generated):
[MATH_EXPRESSION, EXECUTABLE=1, START]
  [NUM_NATURAL, sign=0, magnitude=1, FEATURES=3]
  [MATH_OPERATOR, +]
  [NUM_NATURAL, sign=0, magnitude=1, FEATURES=2]
[MATH_EXPRESSION, END]
[NUM_NATURAL, sign=0, magnitude=2, FEATURES=5]    ← inserted by runtime
```

With sufficient variety of operands and operations, the model discovers structural properties without explicit instruction:
- Commutativity: a+b and b+a produce the same result
- Monotonicity: adding a positive always increases magnitude
- Operation relationships: 6÷2 and 3×1 produce the same result
- Magnitude patterns: multiplying two numbers of magnitude m and n produces a number of magnitude ≈ m+n

**Phase M-B — Mathematical reasoning in BBP-CoT**

The model learns to deploy MATH_EXPRESSION within structured reasoning chains, using computation as evidence for conclusions:

```
[REASONING_START]
  [HYPOTHESIS] "Is 17 prime?" [HYPOTHESIS, END]

  [EVIDENCE, COMPUTATION, START]
    [MATH_EXPRESSION, EXECUTABLE=1, START]
      [NUM_NATURAL, value=17] [MATH_OPERATOR, %] [NUM_NATURAL, value=2]
    [MATH_EXPRESSION, END]
    [NUM_NATURAL, value=1]    ← runtime: 17%2=1, not divisible

    [MATH_EXPRESSION, EXECUTABLE=1, START]
      [NUM_NATURAL, value=17] [MATH_OPERATOR, %] [NUM_NATURAL, value=3]
    [MATH_EXPRESSION, END]
    [NUM_NATURAL, value=2]    ← runtime: 17%3=2, not divisible

    [MATH_EXPRESSION, EXECUTABLE=1, START]
      [NUM_NATURAL, value=17] [MATH_OPERATOR, sqrt]
    [MATH_EXPRESSION, END]
    [NUM_REAL, value≈4.123]   ← runtime: √17≈4.123, upper bound for trial division
  [EVIDENCE, END]

  [INFERENCE_STEP, MATHEMATICAL, INDUCTION]
  [CONCLUSION, STRONG] "17 is prime" [CONCLUSION, END]
  [CONFIDENCE, EXTERNALLY_CALIBRATED, value=1000]
[REASONING_END]
```

The model learns the *strategy* of primality verification, not a memorised list of prime numbers. Any primality check generalises to arbitrary inputs because the computational steps are executed by the runtime, not recalled statistically.

#### 10.3.5 Core Engine Training — Formal Logical Reasoning Track (L)

This section specifies the Formal Logical Reasoning Track (Track L) of the Reasoning Curriculum defined in §15.5.0b. Track L is a distinct training phase that extends the Inverted Ground Truth principle to the logical domain. The ground truth is the output of a formal inference engine; no Teacher LLM is involved in generating the correctness signal.

**Phase L-A — Propositional logic (fully deterministic corpus)**

A combinatorial generator produces BBP-CoT chains encoding classical propositional inference rules. For each rule, the generator emits: one or more HYPOTHESIS tokens encoding the premises, one INFERENCE_STEP token naming the rule applied, and one CONCLUSION token encoding the derived proposition. A SAT solver verifies each (premises, conclusion) pair independently before the pair enters the training corpus.

Covered rules: modus ponens, modus tollens, disjunctive syllogism, hypothetical syllogism, De Morgan’s laws, double negation elimination, constructive dilemma, and absorption.

**Go/no-go criterion for Phase L-A:** Accuracy ≥ 95% on a held-out set of 10,000 propositional inference problems (mechanically verifiable), independently of Group F structural conformance. Phase L-B MUST NOT begin until this criterion is met.

**Phase L-B — Predicate logic and syllogisms (theorem prover verified)**

The generator produces first-order logic structures: universally quantified propositions (∀x), existentially quantified propositions (∃x), and syllogistic chains (Barbara, Celarent, Darii, Ferio). Ground truth is verified by a first-order theorem prover (e.g., E, Vampire, or Z3). Each training instance encodes: premises as HYPOTHESIS blocks with quantifier structure represented via REFERENCE and QUANTIFIER tokens, the inference step as INFERENCE_STEP, and the conclusion as CONCLUSION.

**Go/no-go criterion for Phase L-B:** Accuracy ≥ 85% on a held-out set of 5,000 predicate logic problems verified by theorem prover. Phase L-C MUST NOT begin until this criterion is met.

**Phase L-C — Scientific and causal reasoning (domain law verified)**

The generator produces multi-step causal chains over formal domain laws: kinematic equations (F=ma, v=u+at, s=ut+½at²), chemical stoichiometry (balanced reactions, molar ratios), and economic equilibrium (supply-demand intersection, marginal conditions). The Core Engine learns to structure multi-step scientific arguments as BBP-CoT chains with EVIDENCE[COMPUTATION] blocks containing MATH_EXPRESSION[EXECUTABLE] tokens (§10.3). The runtime verifies numerical conclusions deterministically.

**Go/no-go criterion for Phase L-C:** Accuracy ≥ 80% on a held-out set of 5,000 domain-law reasoning problems with verifiable numerical conclusions.

**Relationship to §15.5.0b.** This section provides the detailed curriculum specification for Track L referenced by §15.5.0b. The gating policy defined in §15.5.0b (L-A must pass before L-B; Tracks M and L may run concurrently after their respective Phase-A gates) governs the interaction between this track and the Mathematical Reasoning Track (§10.3.4).

### 10.4 BBP Runtime — Minimum Specification

#### 10.4.1 Scope of evaluation

The BBP Runtime is a deterministic symbolic evaluator that operates on well-formed `MATH_EXPRESSION[EXECUTABLE=1]` blocks. Its scope in v1.0 is limited to **concrete arithmetic**: expressions whose leaf nodes are all fully specified numeric tokens (NUM_NATURAL, NUM_INTEGER, NUM_REAL, NUM_FRACTION). Expressions containing MATH_VARIABLE or MATH_FUNCTION tokens with unbound values are outside v1.0 scope; the runtime MUST emit `MATH_ERROR(UNDEFINED)` for these.

Symbolic algebra (e.g., `x + x → 2x`) is a v1.1 candidate and MUST NOT be assumed by v1.0 implementations.

#### 10.4.2 Type coercion policy

When an operation mixes numeric types, the following promotion hierarchy applies (narrower → wider):

```
NUM_NATURAL → NUM_INTEGER → NUM_FRACTION → NUM_REAL
```

The result type is the widest type present among the operands. A `NUM_NATURAL` divided by a `NUM_NATURAL` that produces a non-integer result is promoted to `NUM_FRACTION` if the result is exactly representable as p/q with p, q ≤ 65535, or to `NUM_REAL` otherwise.

**Canonical form for NUM_FRACTION Runtime output (normative).** The result token emitted by the BBP Runtime after evaluating a MATH_EXPRESSION MUST be in reduced canonical form:

1. The denominator MUST be positive (uint32 > 0).
2. The sign MUST reside in the numerator.
3. The fraction MUST be fully reduced (gcd(|numerator|, denominator) = 1).

Canonical normalization is the Runtime's responsibility, not the Core Engine's. A non-canonical NUM_FRACTION in the Runtime output stream is a Runtime implementation error.

**Note (informative).** The Core Engine, once trained on mathematical reasoning (Track M, §10.3.4), is expected to develop an implicit notion of canonical fraction form from exposure to normalized Runtime output. No explicit training signal for canonicalization is required — it emerges from the corpus structure.

#### 10.4.3 Integration model

The BBP Runtime operates as a **post-generation pass** over the token stream, not inline during Core Engine token generation. The integration model is:

1. The Core Engine generates the full `MATH_EXPRESSION[EXECUTABLE=1]` block including its `END` token.
2. The Runtime intercepts the stream immediately after the `END` token.
3. The Runtime evaluates and inserts the result token (or `MATH_ERROR`) before the stream is forwarded to any downstream consumer.
4. The Core Engine MUST NOT generate the result token inside an EXECUTABLE block. Any result token generated by the Core Engine inside an EXECUTABLE block is a validator error.

This model guarantees that downstream consumers (other agents, Renderers, reasoning processors) always receive an evaluated result, never a "pending computation" state.

---


## 11. Stream Structure and Integrity

### 11.1 Sentence Delimiters

Use CONTROL UET (0o66) to mark boundaries:

```
[CONTROL, FEATURES=SENTENCE_END, flow=UNIT]     → Period / end of sentence
[CONTROL, FEATURES=QUESTION, flow=UNIT]          → Question mark
[CONTROL, FEATURES=EXCLAMATION, flow=UNIT]       → Exclamation mark
```

### 11.2 Synchronization

To enable error recovery in corrupted or streaming contexts, a **SYNC token** is defined:

```
SYNC = Entity Token with meta_type=CONTROL (0o66), concept_id=0x1FFFFFF (all ones),
       flow=LITERAL (0b11), all grammar fields at maximum value (all ones)
```

**Computed value (64-bit token = LEMMA + FEATURES):**

```
LEMMA:
  bit  31     = 0         (Entity discriminator)
  bits 30–25  = 110110    (meta_type=CONTROL=54=0o66)
  bits 24– 0  = 1…1       (concept_id=0x1FFFFFF, all ones)
  → LEMMA = 0_110110_1111111111111111111111111
           = 0x6DFFFFFF

FEATURES:
  bits 31–30  = 11        (flow=LITERAL — not a valid CONTROL operational flow)
  bits 29–25  = 11111     (case=0x1F, reserved)
  bits 24–22  = 111       (number=0x7, reserved/DISTRIBUTIVE)
  bits 21–18  = 1111      (determiner=0xF, reserved)
  bits 17–14  = 1111      (classifier=0xF, CLF_OTHER_LITERAL sentinel)
  bits 13–12  = 11        (animacy=0x3, DIVINE)
  bits 11– 8  = 1111      (noun_class=0xF, NC_ANIMATE_AGREE sentinel)
  bits  7– 2  = 111111    (register=0x3F)
  bits  1– 0  = 11        (reserved, all ones)
  → FEATURES = 0xFFFFFFFF

Wire representation: [0x6DFFFFFF][0xFFFFFFFF]
```

**Properties:**
- Unique bit pattern: the combination of LITERAL flow on a CONTROL token with all grammar fields at maximum is not a valid encoding for any operational token. No conformant stream can produce this 64-bit value by accident.
- Parsers encountering a parse error scan forward for the SYNC pattern `0x6DFFFFFF_FFFFFFFF` and resume parsing at the next token boundary.
- Encoders SHOULD emit SYNC tokens at regular intervals (e.g., every 256 tokens) in long streams.

### 11.3 Paragraph and Document Structure

```
[CONTROL, DOC_START, flow=START]
  [CONTROL, PROFILE, flow=UNIT, deklinabidea=0x000]        → encoder provenance tag (0x000 = Teacher LLM; 0x001 = deterministic parser)
  [CONTROL, ONTOLOGY_VERSION, flow=UNIT, FEATURES=0x100]    → ontology v1.0
  [STATE_CHECKPOINT, depth=0]                               → initial checkpoint (Seekable only)
  [CONTROL, PARA_START, flow=START]
    [CONTROL, SENT_START, flow=START]
      ... sentence tokens ...
    [CONTROL, flow=END]
  [CONTROL, flow=END]
[CONTROL, flow=END]
```

**Emission order (normative):** Within the DOC_START block, the order of initial CONTROL tokens MUST be: (1) PROFILE, (2) ONTOLOGY_VERSION, (3) NCBI_TAXONOMY_VERSION (if stream contains LIVING_ENTITY tokens), (4) STATE_CHECKPOINT (if Seekable). Other document-level metadata tokens (REGISTER, etc.) follow.

**ONTOLOGY_VERSION** declares the version of the vocabulary ontology (dictionary ID mappings) used by the encoder that produced this stream. Decoders use this to determine compatibility and apply migration mappings when needed.

#### ONTOLOGY_VERSION Payload Encoding

The FEATURES payload field of the ONTOLOGY_VERSION CONTROL token encodes a semantic version number:

```
┌─────────────────────────────────────────────┐
│     ONTOLOGY_VERSION Payload (12 bits)       │
├──────────────────┬──────────────────────────┤
│  Bits 8–11 (4b)  │  Bits 0–7 (8b)           │
│  Major version   │  Minor version            │
│  Range: 0–15     │  Range: 0–255             │
└──────────────────┴──────────────────────────┘
```

**Initial version:** BBP v1.0-draft-r15 uses ontology version **1.0** (major=1, minor=0), encoded as FEATURES = `0x100`.

**Deklinabidea:** The declension fields of the ONTOLOGY_VERSION CONTROL token (case, number, determiner) SHOULD be all zeros. Decoders MUST ignore these fields for ONTOLOGY_VERSION tokens. Reserved for future use (e.g., patch level encoding).

#### Migration Tables (External Artifact)

Ontology version transitions that change ID assignments MUST be accompanied by a migration table published as an external artifact (not embedded in the binary stream).

**Format:** JSON array of migration entries:

```json
[
  {
    "old_id": 1042,
    "new_id": 1043,
    "major_from": 1,
    "minor_from": 0,
    "major_to": 1,
    "minor_to": 1,
    "notes": "Merged synsets: 'quick' and 'fast' consolidated under ID 1043"
  }
]
```

**Required fields:** `old_id`, `new_id`, `major_from`, `minor_from`, `major_to`, `minor_to`.
**Optional fields:** `notes`, `deprecated` (boolean), `replacement_rationale`.

**Distribution:** Migration tables MUST be versioned and published alongside specification releases. The recommended distribution mechanism is a `migrations/` directory in the BBP specification repository, with one file per version transition (e.g., `migrations/v1.0_to_v1.1.json`).

**DOC_START Deklinabidea — Protocol Version and Seekable Flag.** The DOC_START token reinterprets its 11-bit Deklinabidea field to encode the protocol version and stream capabilities:

```
┌──────────────────────────────────────────────────────────┐
│       DOC_START Deklinabidea (11 bits)                    │
├──────────────────┬─────────────────┬─────────────────────┤
│  Bits 5-10       │  Bits 1-4       │  Bit 0              │
│  Minor (6b)      │  Major (4b)     │  Seekable (1b)      │
└──────────────────┴─────────────────┴─────────────────────┘
```

- **Bit 0 — Seekable flag:** If set (1), the stream MUST comply with STATE_CHECKPOINT emission rules (§11.7.3). If clear (0), checkpoints may be omitted for maximum density.
- **Bits 1-4 — Protocol major version (4 bits):** Range 0–15. For v1.0: value = `0001`.
- **Bits 5-10 — Protocol minor version (6 bits):** Range 0–63. For v1.0: value = `000000`.

**Default v1.0 encoding:** Deklinabidea = `0b_000000_0001_X` where X = Seekable flag.

**Forward compatibility:** A v1.0 decoder encountering a major version > 1 MUST reject the stream with a clear error ("unsupported BBP protocol version") rather than silently misinterpreting the token layout. A minor version mismatch within the same major version (e.g., v1.3 decoded by v1.0) SHOULD produce a warning but MUST NOT cause rejection — minor versions guarantee backward compatibility within the major version.

#### Stream Identity Principle (normative)

The binary stream format consumed by the Core Engine during training and the stream format used in production MUST be identical. No field, token, or structural element that is present in training streams may be absent in production streams or vice versa. Optional elements that appear inconsistently between training and inference create distributional shift that undermines the Core Engine's learned representations. This principle applies to all structured metadata tokens, including `NCBI_TAXONOMY_VERSION`.

#### NCBI_TAXONOMY_VERSION

**Normative requirement.** Any stream that contains one or more LIVING_ENTITY tokens MUST include a `NCBI_TAXONOMY_VERSION` CONTROL token (FEATURES = 0x054) in the DOC_START block, immediately after `ONTOLOGY_VERSION`. A stream with LIVING_ENTITY tokens and no `NCBI_TAXONOMY_VERSION` is a MAJOR error. Streams without LIVING_ENTITY tokens MAY omit it.

**Token format** — LITERAL block:

```
[CONTROL, FEATURES=0x054, flow=LITERAL, payload_bytes=3]
[raw bytes: YY YY MM 00 00 00 00 00]
            ^^^^^  ^^
            year   month
            (year: uint16 big-endian, value ≥ 2000)
            (month: uint8, range 1–12)
            (5 bytes 0x00 padding → 8 bytes total, stream-aligned)
[CONTROL, FEATURES=0x054, flow=END, deklinabidea=0]
```

Example for March 2026: bytes `07 EA 03 00 00 00 00 00` (year=0x07EA=2026, month=0x03=March).

**Validator rule (V-NCBI-VERSION-1):** The payload of `NCBI_TAXONOMY_VERSION` MUST be exactly 3 bytes: bytes 0–1 as uint16 big-endian year (≥ 2000), byte 2 as uint8 month (range 1–12). Any other payload length or out-of-range value is a CRITICAL error. Severity: CRITICAL.

### 11.4 Register / Honorific Level (Stream Metadata)

For languages with complex politeness systems (Japanese, Korean, Javanese), a CONTROL UET at the start of a paragraph or document sets the register level. The register applies to all tokens until the next register CONTROL or end of scope.

```
[CONTROL, FEATURES=REGISTER_FORMAL, flow=UNIT]
```

### 11.5 CONTROL Payload Registry

The CONTROL meta-type (0o66) uses its FEATURES payload field as a function selector. To avoid ambiguity between punctuation, structure, register, compositionality, and extension functions, FEATURES values are organized in ranges:

| Range | Category | Defined values |
|-------|----------|---------------|
| 0x000–0x00F | **Punctuation** | 0=period, 1=question, 2=exclamation, 3=semicolon, 4=colon, 5=ellipsis, 6=comma, 7=dash |
| 0x010–0x01F | **Structure** | 16=sentence_start, 17=sentence_end, 18=paragraph_start, 19=paragraph_end, 20=document_start, 21=document_end, **22=reasoning_start (§17.3), 23=reasoning_end (§17.3)** |
| 0x020–0x02F | **Register** | 32=neutral, 33=intimate, 34=casual, 35=formal, 36=honorific, 37=humble |
| 0x030 | **HONORIFIC_CONTEXT** | 48=HONORIFIC_CONTEXT — optional role-assignment metadata for Frontier Renderers in keigo contexts; see §4.11 and §11.5.1 |
| 0x031–0x03F | **Compositionality** | 49=serial_verb, 50=coordination, 51=subordination, 52=apposition |
| 0x040–0x04F | **Extensions** | 64=RESERVED (Legacy VOCAB_PAGE), 65=placeholder_resolve (Section 8.4) |
| 0x050 | **STATE_CHECKPOINT** | Periodic nesting state snapshot (Section 11.7) |
| 0x051 | **KEYFRAME** | Heavy checkpoint with entity table (Section 11.8) |
| 0x052 | **PROFILE** | Encoder provenance tag (informative). Payload 0x000 = Frontier Translator (Teacher LLM); 0x001 = deterministic parser (§13.1.1). MUST NOT be used to declare a protocol subset. Receivers MUST accept all valid BBP-64 tokens regardless of PROFILE value. See §2.8. |
| 0x053 | **ONTOLOGY_VERSION** | Vocabulary ontology version declaration (Section 11.3) |
| 0x054 | **NCBI_TAXONOMY_VERSION** | NCBI Taxonomy version declaration — MUST appear in DOC_START of any stream containing LIVING_ENTITY tokens (§11.3). LITERAL block, 3-byte payload: year (uint16 big-endian) + month (uint8). See V-NCBI-VERSION-1. |
| 0x055–0x05F | **Reserved** | Future checkpoint/profile extensions |
| 0x060–0x08F | **Programming** | 96=func_def, 97=func_call, 98=return, ... 124=AST_OPAQUE, 125=LAMBDA, 126=DECORATOR, ... 136=INDEX_ACCESS **(see Section 16.2 for full registry of 38 constructs)** |
| 0x080–0x08F | **Reserved** | Future use |
| 0x090 | **SPEECH_ACT** | Illocutionary force frame: performative, promise, declaration, request, etc. (see §11.5.2 and §11.6) |
| 0x091–0xFFE | **Reserved** | Future use |
| 0xFFF | **SYNC** | Synchronization marker (Section 11.2). Wire pattern: `[0x6DFFFFFF][0xFFFFFFFF]` |

Encoders MUST use values from defined ranges. Decoders encountering an unrecognized CONTROL FEATURES SHOULD skip the token with a warning.

### 11.5.1 HONORIFIC_CONTEXT Payload (0x030)

When a stream encodes content requiring complex honorific inference — particularly keigo, where the choice of lexical form (*いらっしゃる* vs. *いる*) depends on the speaker-addressee-referent triad — a HONORIFIC_CONTEXT CONTROL token at the opening of the relevant scope provides role-assignment metadata to the Frontier Renderer.

**HONORIFIC_CONTEXT CONTROL token FEATURES payload (bits 27–0, 28 bits):**

```
bits 27-24  : speech_style   (4b)
  0x0 = NEUTRAL
  0x1 = TEINEIGO    (general politeness marker — Japanese 丁寧語)
  0x2 = SONKEIGO    (exaltative — Japanese 尊敬語)
  0x3 = KENJOGO     (humilitive — Japanese 謙譲語)
  0x4 = BIKAGO      (beautification register — Japanese 美化語)
  0x5 = FORMAL_KOR  (Korean 합쇼체)
  0x6 = POLITE_KOR  (Korean 해요체)
  0x7-0xF = reserved

bits 23-20  : ref_speaker    (4b)  PRONOUN logical ID of the speaker in this stream
bits 19-16  : ref_addressee  (4b)  PRONOUN logical ID of the addressee
bits 15-12  : ref_referent   (4b)  PRONOUN logical ID of the entity receiving honor
bits 11- 8  : domain         (4b)  (same codes as register field bits 1-0, extended to 4b:
                                    0=GENERAL, 1=BUSINESS, 2=INSTITUTIONAL, 3=INTIMATE,
                                    4-15=reserved)
bits  7- 0  : language_id    (8b)  BBP Language Registry ID (enables Renderer to select
                                    correct lexical honorific forms for the target language)
```

This token is **informative for Renderers, not normative for Core Engine processing**. A BBP stream is structurally valid without HONORIFIC_CONTEXT even when register fields are set. HONORIFIC_CONTEXT provides the richer metadata required for high-fidelity natural language generation in honorific-heavy registers.

### 11.5.2 SPEECH_ACT Payload (0x090)

SPEECH_ACT encodes the illocutionary force of an utterance — what the speaker is *doing* with the words, not merely what the words describe. Following Austin (1962) and Searle (1969), performative utterances constitute acts by being uttered ("I hereby declare you married") rather than describing acts. BBP encodes the full speech act frame, including institutional domain and felicity conditions, using the SPEECH_ACT CONTROL token as a block opener.

**SPEECH_ACT CONTROL token FEATURES payload (bits 27–0, 28 bits):**

```
bits 27-24  : illocutionary_force (4b)
  0x0 = ASSERTION      — Default declarative; describes a state of affairs.
                         Distinguished from DECLARATION: assertions can be true or false;
                         declarations change the world by being uttered.
  0x1 = PROMISE        — Speaker commits to a future action.
                         Felicity: speaker has intention + ability; addressee wants the action.
  0x2 = DECLARATION    — Genuinely performative: the utterance *is* the act it describes.
                         "I hereby sentence you to...", "The meeting is adjourned."
                         Requires institutional authorization (see institutional_domain).
  0x3 = REQUEST        — Speaker directs addressee to perform an action.
                         Includes commands, questions (as requests for information),
                         and indirect requests ("Can you pass the salt?").
  0x4 = WARNING        — Speaker informs addressee of a future state unfavorable to them.
  0x5 = APOLOGY        — Speaker expresses regret for a past action affecting addressee.
  0x6 = GREETING       — Phatic function: social contact establishment/maintenance.
                         ("Hello", "¿Qué tal?", "Kaixo" as conventional social opener.)
  0x7 = COMMISSIVE     — Broader than PROMISE: includes oaths, vows, pledges.
                         Unlike PROMISE, a COMMISSIVE may bind a group, not just the speaker.
  0x8 = EXPRESSIVE     — Speaker expresses a psychological state toward the addressee
                         or an event: congratulations, condolences, thanks, toasts.
  0x9-0xE = reserved
  0xF = OTHER          — Unclassified illocutionary force; DISAMBIGUATION SHOULD follow.

bits 23-20  : institutional_domain (4b)
  0x0 = GENERAL        — Everyday social context; no special authorization required.
  0x1 = LEGAL          — Legal proceedings, contracts, judicial sentences, legislation.
  0x2 = RELIGIOUS      — Sacraments, blessings, liturgical acts, religious vows.
  0x3 = BUREAUCRATIC   — Administrative declarations, permits, certificates, rulings.
  0x4 = SOCIAL_RITUAL  — Ceremonial events with conventional force: weddings, graduations,
                         sports openings, diplomatic protocols.
  0x5 = MILITARY       — Commands and declarations within military hierarchy.
  0x6-0xF = reserved

bits 19-18  : uptake_required (2b)
  00 = NONE            — No uptake required; the act is complete as uttered.
  01 = ADDRESSEE       — Requires acknowledgment or response from the addressee to take effect.
  10 = INSTITUTIONAL   — Requires a witness, officiating authority, quorum, or countersignature.
  11 = BILATERAL       — Requires both addressee acknowledgment AND institutional witness/authority.

bits 17- 0  : reserved (must be 0)
```

**Usage:** The SPEECH_ACT CONTROL token opens a START/END block. The block content encodes:
1. The propositional content of the act (normal BBP tokens).
2. Felicity conditions as HYPOTHESIS sub-blocks with CONFIDENCE=1.0 (see §11.6.2).

A stream is structurally valid without SPEECH_ACT for utterances where the illocutionary force is the default ASSERTION. SPEECH_ACT is obligatory only when the illocutionary force departs from plain assertion and the distinction is semantically relevant.

### 11.6 SPEECH_ACT — Full Specification

#### 11.6.1 Philosophy

Speech act theory (Austin 1962, Searle 1969) identifies three layers in every utterance: the **locutionary act** (what is said, the propositional content), the **illocutionary act** (what is done — promising, warning, declaring), and the **perlocutionary effect** (what is achieved in the listener — persuasion, alarm, compliance). BBP encodes the first two layers natively; the third is a pragmatic consequence outside the protocol's scope.

DECLARATION-type speech acts are of special linguistic interest: unlike assertions (which describe a pre-existing state of affairs and can therefore be true or false), declarations change the state of the world *by being uttered* in the right institutional context. "I hereby declare this session open" does not describe an opening; it *is* the opening. The SPEECH_ACT block makes this distinction explicit at the bit level, enabling the Core Engine to reason about illocutionary force as a structured property of propositions rather than a surface inference.

#### 11.6.2 Block Structure and Felicity Conditions

```
[CONTROL, SPEECH_ACT(0x090), flow=START,
          force=<illocutionary_force>,
          domain=<institutional_domain>,
          uptake=<uptake_required>]

  — Felicity conditions (HYPOTHESIS blocks, CONFIDENCE=1.0) —
  [HYPOTHESIS, flow=START]
    — propositional condition that must hold for the act to be felicitous —
  [HYPOTHESIS, flow=END]
  [CONFIDENCE, FEATURES=1.0, flow=UNIT]

  — Propositional content of the act —
  — (normal BBP natural language or BBP-AST tokens) —

[CONTROL, SPEECH_ACT(0x090), flow=END]
```

Felicity conditions MAY be omitted when the act's institutional context makes them implicit (e.g., a GREETING in GENERAL domain requires no stated felicity conditions). They SHOULD be included for DECLARATION acts in non-GENERAL domains, where the authorization requirement is semantically significant.

#### 11.6.3 Examples

**"Por la presente os declaro marido y mujer."**
*(I hereby declare you husband and wife.)*

```
[CONTROL, SPEECH_ACT(0x090), flow=START,
  force=DECLARATION, domain=SOCIAL_RITUAL, uptake=INSTITUTIONAL]

  — Felicity condition 1: speaker has institutional authority —
  [HYPOTHESIS, flow=START]
    [speaker, PRONOUN, ERG, SINGULAR, DEF]
    [have, UAT, NOR-NORK, PRES, IMPERF, S:3, O:3]
    [authority, DICT_WORD, ABS, SINGULAR, DEF]
    [marry, DICT_WORD, INS, SINGULAR, INDEF]    → "to marry [people]"
  [HYPOTHESIS, flow=END]
  [CONFIDENCE, FEATURES=1.0, flow=UNIT]

  — Felicity condition 2: ceremony is valid —
  [HYPOTHESIS, flow=START]
    [ceremony, DICT_WORD, ERG, SINGULAR, DEF]
    [be_valid, UAT, NOR, PRES, IMPERF, S:3]
  [HYPOTHESIS, flow=END]
  [CONFIDENCE, FEATURES=1.0, flow=UNIT]

  — Propositional content —
  [person_A, NAME_PERSON, ABS, SINGULAR, DEF]
  [person_B, NAME_PERSON, ABS, SINGULAR, DEF]
  [declare_married, UAT, NOR-NORK, PRES, PERF, ACTIVE, IND, S:1, O:3]

[CONTROL, SPEECH_ACT(0x090), flow=END]
```

**"I promise I'll be there." (PROMISE)**

```
[CONTROL, SPEECH_ACT(0x090), flow=START,
  force=PROMISE, domain=GENERAL, uptake=ADDRESSEE]

  — Felicity condition: speaker has intention —
  [HYPOTHESIS, flow=START]
    [I, PRONOUN, ERG, SINGULAR]
    [intend, UAT, NOR-NORK, PRES, IMPERF, S:1, O:3]
    [come, UAT, NOR, FUT, S:1]    → to come (nominalized via nesting)
  [HYPOTHESIS, flow=END]
  [CONFIDENCE, FEATURES=1.0, flow=UNIT]

  — Content —
  [I, PRONOUN, ERG, SINGULAR]
  [be_present, UAT, NOR, FUT, IMPERF, S:1]

[CONTROL, SPEECH_ACT(0x090), flow=END]
```

**"¿Puedes pasarme la sal?" (Can you pass the salt? — indirect REQUEST)**

This is classically encoded as an ability query but is a conventional indirect request.
The PRESUPPOSITION block (§6.8) handles the literal reading; SPEECH_ACT handles the illocutionary act.

```
[CONTROL, SPEECH_ACT(0x090), flow=START,
  force=REQUEST, domain=GENERAL, uptake=ADDRESSEE]

  [you, PRONOUN, ERG, SINGULAR]
  [salt, DICT_WORD, ABS, SINGULAR, DEF]
  [pass, UAT, NOR-NORK, PRES, S:2, O:3]    → "pass [me] the salt"
  [I, PRONOUN, DAT, SINGULAR]              → beneficiary

[CONTROL, SPEECH_ACT(0x090), flow=END]
```

Note: The Renderer reconstructs the surface form appropriate to the target language
(indirect question in Spanish/English, imperative in languages where this is more natural,
etc.) from the SPEECH_ACT frame + domain + register fields.

#### 11.6.4 BBP-IR JSON Representation

```json
{
  "type": "CONTROL",
  "control_function": "SPEECH_ACT",
  "flow": "START",
  "illocutionary_force": "ASSERTION | PROMISE | DECLARATION | REQUEST | WARNING | APOLOGY | GREETING | COMMISSIVE | EXPRESSIVE | OTHER",
  "institutional_domain": "GENERAL | LEGAL | RELIGIOUS | BUREAUCRATIC | SOCIAL_RITUAL | MILITARY",
  "uptake_required": "NONE | ADDRESSEE | INSTITUTIONAL | BILATERAL",
  "felicity_conditions": [
    {
      "type": "HYPOTHESIS",
      "confidence": 1.0,
      "content": [ /* BBP-IR tokens for the condition */ ]
    }
  ],
  "content": [ /* BBP-IR tokens for propositional content */ ]
}
```

#### 11.6.5 Frontier Translator Guidance

SPEECH_ACT encoding is a **Tier 3 task** for non-ASSERTION forces and a **Tier 1 task** for ASSERTION (the default, emitted with no SPEECH_ACT wrapper). The LLM Semantic Enricher (Component 3 of the hybrid Teacher, §13.2.0) handles SPEECH_ACT detection by:

1. Identifying explicit performative verbs (*declare, promise, warn, apologize, hereby...*) → direct SPEECH_ACT encoding.
2. Identifying conventional indirect speech acts (*Can you X?* as REQUEST, *You'd better X* as WARNING) via pragmatic inference patterns.
3. Identifying institutional context markers (legal language, sacramental formulas, ceremonial openings) → domain assignment.
4. Omitting felicity conditions in GENERAL domain unless they are explicitly stated in the source.

Undetected non-ASSERTION illocutionary forces produce streams that treat the utterance as ASSERTION — semantically degraded but structurally valid. No CRITICAL validator errors are triggered. Validator warning: MINOR.

### 11.7 STATE_CHECKPOINT — Periodic Nesting State Snapshots

#### 11.7.1 Motivation

While individual BBP tokens are stateless (any token can be decoded without context), the *semantic context* of a token — its nesting depth within block structures — requires knowledge of which START/END blocks are currently open. Without a mechanism to recover this state, random access (e.g., RAG systems extracting fragments from large documents) requires scanning from the stream start: O(N) for a stream of N tokens.

STATE_CHECKPOINT provides periodic "I-frames" (by analogy with video codecs) that snapshot the nesting state, enabling any parser to recover full context by scanning at most 256 tokens backward.

#### 11.7.2 Token Format

CONTROL FEATURES `STATE_CHECKPOINT` = **0x050**, emitted as a LITERAL block:

```
Token N:   [CONTROL, FEATURES=0x050, flow=LITERAL, payload_literal=L]
[raw bytes: L bytes of checkpoint data, padded to 8-byte boundary]
Token N+1: [CONTROL, FEATURES=0x050, flow=END, deklinabidea=0]
```

**Checkpoint data format (raw bytes):**

```
Byte 0:     Flags + Depth
            Bits 7-4: depth (0-15, current nesting depth for this segment)
            Bit 3:    in_reasoning (inside REASONING scope)
            Bit 2:    in_code (inside BBP-AST scope)
            Bit 1:    in_literal (LITERAL block currently open)
            Bit 0:    continuation (0 = complete checkpoint, 1 = continuation segment)

Bytes 1..2×D: Stack snapshot (D = depth, 2 bytes per level, root-to-innermost):
              Byte A: meta_type (6 bits) + flow_context (2 bits)
              Byte B: FEATURES high byte of the START token that opened this level

Bytes 2D+1..2D+4: logical_token_index (uint32, big-endian)
              Absolute logical index of this checkpoint token.
```

**Size:** 5 bytes (depth=0) to 35 bytes (depth=15). Padded to next 8-byte boundary (per §4.4 stream alignment invariant). Maximum padded size: 40 bytes (depth=15, padded from 35 to 40).

**Overflow handling (depth > 15):** When the nesting depth exceeds 15, the encoder MUST emit two consecutive STATE_CHECKPOINT tokens:

```
CHECKPOINT_1: depth=15, continuation=0, stack[0..14], logical_index
CHECKPOINT_2: depth=(actual−15), continuation=1, stack[15..actual−1], logical_index (same)
```

The parser reconstructs the full stack by concatenating segments: when it encounters a checkpoint with `continuation=1`, it appends that segment's stack to the preceding checkpoint's stack. Chaining supports up to 17 consecutive checkpoints (depth 255), though in practice two checkpoints (depth 30) covers virtually all real-world nesting, including deeply nested BBP-AST code with interleaved reasoning blocks.

**Validation:** A checkpoint with `continuation=1` MUST immediately follow another STATE_CHECKPOINT. A `continuation=1` checkpoint without a preceding checkpoint is a structural error (SEVERITY: CRITICAL).

#### 11.7.3 Emission Rules (Normative)

```
CHECKPOINT-1: Encoders MUST emit a STATE_CHECKPOINT at least every 256 tokens
              in Seekable streams (configurable via stream header;
              minimum 64, maximum 1024).

CHECKPOINT-2: Encoders MUST emit a STATE_CHECKPOINT immediately after
              every SYNC token.

CHECKPOINT-3: The first token of a Seekable stream (after DOC_START and
              PROFILE) MUST be a STATE_CHECKPOINT with depth=0.

CHECKPOINT-4: Streams declared as Seekable (via DOC_START capability flag)
              MUST comply with CHECKPOINT-1 through CHECKPOINT-3.
              Non-seekable streams MAY omit checkpoints entirely.
```

#### 11.7.4 Random Access Algorithm

```c
void random_access(TokenStream *stream, uint32_t target_index) {
    // Step 1: Scan backward for nearest STATE_CHECKPOINT or KEYFRAME
    uint32_t scan = target_index;
    while (scan > 0 && !is_checkpoint_or_keyframe(stream->token[scan])) {
        scan--;
    }
    // In a Seekable stream, worst case is 256 tokens back (CHECKPOINT-1).
    // Optional: Seekable streams MAY include a checkpoint offset table
    // as a trailer, enabling O(log C) lookup where C = checkpoint count.

    // Step 2: Restore stack from checkpoint
    if (is_checkpoint_or_keyframe(stream->token[scan])) {
        restore_stack_from_checkpoint(stream->token[scan]);
        scan = skip_literal_block(scan); // advance past raw bytes + END
    } else {
        // scan == 0, no checkpoint found: start with empty stack
        reset_stack();
    }

    // Step 3: Sequential scan from checkpoint to target (max 256 tokens)
    while (scan < target_index) {
        update_nesting_stack(stream->token[scan]);
        scan++;
    }
    // Stack is now correct at target_index.
}
```

**Complexity:** O(256) worst case = O(1) amortized. The backward scan is bounded by CHECKPOINT-1 emission frequency. The forward scan is equally bounded.

**Overhead:** In a stream with checkpoint interval 256: 2 control tokens + ~8-16 bytes raw data per checkpoint = approximately **2.5% overhead**.

### 11.8 KEYFRAME — Heavy Checkpoints with Entity Table

#### 11.8.1 Motivation

STATE_CHECKPOINTs restore the nesting stack but do not restore the *referential context*: which entities are available for REFERENCE backpointers. In long or infinite streams (e.g., inter-agent dialogue), long-range REFERENCEs (Section 8.5) can point arbitrarily far back, requiring the parser to retain the entire stream history.

KEYFRAMEs solve this by including a **table of active entities** — all entities that are referenciable by REFERENCE in the subsequent segment. A parser landing on a KEYFRAME has everything it needs: nesting state + referenciable entities. No prior history required.

This design follows the H.264 video codec model: STATE_CHECKPOINTs are P-frames (incremental, lightweight), KEYFRAMEs are I-frames (complete, heavier).

#### 11.8.2 Token Format

CONTROL FEATURES `KEYFRAME` = **0x051**, emitted as a LITERAL block:

```
Token N:   [CONTROL, FEATURES=0x051, flow=LITERAL, payload_literal=L]
[raw bytes:
  - Standard checkpoint data (same format as STATE_CHECKPOINT, §11.7.2)
  - Entity count (uint16, big-endian): K active entities
  - For each entity (K entries, 9 bytes each):
    - Original logical_token_index (uint32): where entity was first defined
    - Local alias (uint16): short ID usable by REFERENCE in this segment
    - Meta-type (uint8): entity's meta-type
    - Deklinabidea snapshot (uint16): last known case/number/det
]
Token N+1: [CONTROL, FEATURES=0x051, flow=END, deklinabidea=0]
```

#### 11.8.3 Semantics

After a KEYFRAME, REFERENCE tokens can use the `local_alias` field (0–65535) from the entity table as their FEATURES, providing compact backpointers to entities defined before the KEYFRAME without requiring access to the original stream.

A parser that lands on a KEYFRAME has complete context: nesting stack (from the embedded checkpoint data) and referenciable entities (from the entity table). Nothing before the KEYFRAME is needed.

#### 11.8.4 Emission Rules

```
KEYFRAME-1: KEYFRAMEs are OPTIONAL and more expensive than STATE_CHECKPOINTs.
            Recommended frequency: every 4,096–16,384 tokens in long streams,
            or at the start of each structural unit (chapter, dialogue turn).

KEYFRAME-2: In infinite/unbounded streams (inter-agent communication),
            encoders SHOULD emit a KEYFRAME periodically to bound memory
            requirements.

KEYFRAME-3: A KEYFRAME implicitly satisfies CHECKPOINT-1 (it contains
            a full checkpoint). No separate STATE_CHECKPOINT is needed
            at the same position.

KEYFRAME-4: After a KEYFRAME, the parser MAY discard all tokens prior
            to the KEYFRAME without losing referential capability.
```

#### 11.8.5 Video Codec Analogy

| H.264 Concept | BBP Equivalent | Purpose |
|----------------|----------------|---------|
| P-frame | STATE_CHECKPOINT | Lightweight incremental state (nesting stack only) |
| I-frame | KEYFRAME | Complete self-contained state (stack + entities) |
| GOP (Group of Pictures) | Segment between KEYFRAMEs | Unit of independent decodability |
| Seeking = jump to nearest I-frame | RAG = jump to nearest KEYFRAME | Random access strategy |

### 11.9 Transport-Layer Integrity (Informative)

BBP binary streams do **not** include per-token error detection or correction. This is a deliberate design choice: binary protocols at this level (comparable to Protocol Buffers, FlatBuffers, Cap'n Proto) delegate integrity to the transport and storage layers.

**Normative requirement:** Implementations transmitting BBP streams over channels where bit corruption is possible MUST provide integrity checking at the transport layer:

| Transport | Recommended integrity mechanism |
|-----------|-------------------------------|
| Network (TCP/TLS) | TCP checksums + TLS record integrity (automatic) |
| Network (UDP, raw) | CRC-32 per frame, aligned to STATE_CHECKPOINT boundaries (256-token frames) |
| Storage (filesystem) | Filesystem-level checksums (ZFS, Btrfs) or application-level CRC per stream |
| Shared memory | ECC RAM or application-level verification |
| In-memory processing | No additional integrity needed (corruption implies hardware failure) |



**Recommended frame structure for unreliable channels:**

```
┌─────────────────────────────────────────────────┐
│  Frame Header (8 bytes)                          │
│  ┌───────────┬────────────┬────────────────────┐│
│  │ Magic (2B)│ Length (2B) │ CRC-32 (4B)       ││
│  └───────────┴────────────┴────────────────────┘│
│  Payload: N tokens (aligned to CHECKPOINT)       │
└─────────────────────────────────────────────────┘
```

---

## 12. Cross-Linguistic Mapping Guide

**Scope note.** This section provides the universal rules and brief per-language summaries for cross-linguistic mapping. Detailed per-language mapping guides (minimum 20-30 pages per language, covering edge cases, exceptions, and worked examples) are maintained in the normative companion document **BBP Linguistic Conventions** (§15.1). Section 12 is the architectural overview; the Conventions document is the authoritative per-language reference.

**Prioritization.** Early validation phases (§15.5.5) focus on three languages in depth: **Spanish** (nominative-accusative baseline, the author's strongest review language), **Turkish** (agglutinative + evidentiality stress test), and **Japanese** (topic-comment + case particles). These three languages exercise most of BBP's feature space. The remaining four core languages (Mandarin, Arabic, Basque, English) are added in subsequent phases once deep analyses for the first three are validated.

### 12.0 Case Assignment Decision Tree (Normative)

The following decision tree is the normative reference for ergative-absolutive case assignment. The Teacher LLM prompt (§15.4.1) MUST include this tree verbatim. Every branch includes minimum examples from 3 languages in the BBP Linguistic Conventions document.

```
1. Is there an overt agent performing a volitional action on a patient?
   YES → agent=ERG, patient=ABS, system=NOR-NORK
   NO  → goto 2

2. Is there a single argument (intransitive)?
   2a. Is the subject volitional (running, speaking, dancing)?
       → subject=ABS, system=NOR, dict_metadata=+unergative
   2b. Is the subject non-volitional (falling, breaking, melting)?
       → subject=ABS, system=NOR, dict_metadata=+unaccusative
   2c. Unknown agentivity → subject=ABS, system=NOR, emit DISAMBIGUATION
   NO (multiple arguments, non-transitive) → goto 3

3. Is there an experiencer + stimulus (e.g., "A Juan le gusta el pan")?
   YES → experiencer=DAT, stimulus=ABS, system=NOR-NORI
   NO  → goto 4

4. Is the construction impersonal (no semantic subject)?
   4a. Weather verbs ("It rains", "Llueve"):
       → theme=ABS (if present), system=NOR, subject=3rd_SING
   4b. Existential ("There is X"):
       → entity=ABS, system=NOR, subject=3rd (matches entity number)
   4c. Impersonal passive ("Se vende pan"):
       → theme=ABS, system=NOR, subject=3rd_SING
          (treat as intransitive; implicit agent not encoded)
          Note: If the Teacher analyzes this as passive with demoted
          agent, voice=PASSIVE is an acceptable alternative encoding.
          Emit DISAMBIGUATION if confidence < 80%.
   NO  → goto 5

5. Is there a ditransitive predicate (agent + theme + recipient)?
   YES → agent=ERG, theme=ABS, recipient=DAT, system=NOR-NORI-NORK
   NO  → goto 6

6. Residual: apply the closest matching pattern from steps 1-5.
   If genuinely ambiguous, emit DISAMBIGUATION with alternatives.
```

### 12.0.1 Linguistic Challenge Registry (Informative)

The following registry documents known hard cases for BBP encoding. Each entry records the current best-practice encoding and any open questions. This registry is maintained as a living document in the BBP Linguistic Conventions companion artifact and serves as input for future spec revisions.

| Language | Construction | Current Encoding | Open Questions |
|----------|-------------|-----------------|----------------|
| Spanish | "Se me cayó el vaso" (involuntary causation) | Middle voice + DAT experiencer (NOR-NORI) | Should involuntary causation have its own VOICE_EXT marker? |
| Spanish | "No todos los estudiantes aprobaron" (neg scope) | NEGATIVE det on "todos" + AFF polarity on UAT | Teacher consistently handles this? |
| Turkish | Double causative "yap-tır-t-" | Nested CAUSATIVE (multiple VOICE_EXT bound modifier tokens) | Max stacking depth? |
| Turkish | Evidential + potential mood stacking "gelmiş olmalı" | EVIDENTIAL(REPORTED) + MOOD=POTENTIAL | Interaction fully specified? |
| Japanese | くれる/もらう/あげる directionality | DAT + social register CONTROL token | Does BBP need a "social deixis" marker? |
| Japanese | Double-subject "象は鼻が長い" | TOPICAL + ABS (two NPs with distinct cases) | Always unambiguous? |
| Mandarin | Double 了 "他吃了饭了" | PERF + ASPECT_FINE=RESULTATIVE | Or two separate analysis? |
| Arabic | Dual + adjective agreement "الكتابان الكبيران" | DUAL number on both noun and adjective | Agreement axiom needed? |
| English | "The chicken is ready to eat" (agent/patient ambiguity) | DISAMBIGUATION with alternatives | Always emit DISAMBIGUATION? |
| Basque | Allocutive "liburuak eman dizkiot" | NOR-NORI-NORK + allocutive deferred to v1.1 NOR bits | v1.1 fast-path sufficient? |
| Finnish | SUP vs ADE (same suffix -lla/-llä) | DISAMBIGUATION with SUP/ADE alternatives | Always emit DISAMBIGUATION or use context heuristic? |
| Finnish | ESS vs predicative ABS ("as doctor" vs "is doctor") | ESS for role/function, ABS for identity | Teacher reliability on this distinction? |
| Basque | VOC absent (no morphological vocative) | TOPICAL or UNMARKED for direct address | Should Basque direct address use a dedicated encoding? |
| Hungarian | TRANS + assimilation forms (-vá/-vé) | TRANS case on transformed NP | Assimilation handled by Renderer, not protocol |
| Turkish | SEM gibi (postposition, not suffix) | SEM case on NP following gibi | Teacher must strip postposition before encoding |
| Arabic | VOC يا particle | VOC case on following name | يا itself is not encoded (discourse particle, stripped) |
| English | ABE "without" vs "not with" | ABE vs NEG+COM | Teacher disambiguation criteria needed |
| Spanish | `de` as GEN_POSS vs GEN_LOC vs ABL vs ELA | Context-dependent: Teacher decision tree | Should a confidence heuristic be standardized? |
| Spanish | `para` as DAT vs DEST | Recipient (transfer) → DAT; beneficiary/purpose → DEST | Teacher reliability on this distinction? Tier 2 |
| English | Phrasal verb particle ("put up with", "look after") | Absorbed into lemma | Phrasal verb inventory completeness per domain? |
| Japanese | `に (ni)` covering DAT / ALL / INE / ILL / temporal | Context heuristic: motion verb → ALL/ILL; stative → INE/DAT | Teacher reliability on motion vs. stative? |
| Finnish | `-lla/-llä` covering INS / SUP / ADE | SUP: surface + stative; ADE: location; INS: instrument | Human review threshold needed |
| German | Two-way prepositions (in + acc. → ALL/ILL; in + dat. → INE/SUP) | German morphological case drives BBP case directly | Morphological analyzer resolves this deterministically |
| Arabic | `في (fī)` covering INE and temporal IN | Temporal context → TEMPORAL token; spatial → INE | Interaction with TEMPORAL UET encoding? |
| Basque | `-n` (inessive) vs `-ra` (allative) vs `-tik` (ablative) | Direct morphological mapping; no ambiguity in Basque | Native mapping: highest-confidence Teacher decisions |

### 12.0.2 Preposition and Particle Elimination (Normative)

**Core rule:** Prepositions, postpositions, and grammatical case particles are **not** BBP tokens. They are relational markers in the surface form of natural language. The Teacher LLM MUST absorb them into the `case` field of the nominal token they govern and MUST NOT emit them as separate tokens in BBP-IR.

This rule applies universally across all source languages, including:

- **Prepositional languages** (English, Spanish, French, Portuguese, Italian, Romanian, Arabic, Persian, Hindi): prepositions precede the NP they govern.
- **Postpositional languages** (Japanese, Korean, Turkish, Basque, Finnish, Hungarian, Tamil): postpositions or suffixes follow the NP.
- **Particle languages** (Japanese は/が/を/に/で/へ/と/から/まで/より, Korean 은/는/이/가/을/를/에/에서/로/와/과/도/만...): grammatical particles are separate words but function as case markers.

In all three cases, the Teacher identifies the relational marker, maps it to a BBP case value, applies that case to the governed NP token, and discards the marker.

#### 12.0.2.1 Complete Preposition-to-Case Mapping

The following table is the normative reference for mapping relational markers to BBP cases. The Teacher LLM MUST apply this mapping. Ambiguous cases (marked with ⚠) require context-sensitive analysis; the Teacher SHOULD emit DISAMBIGUATION with confidence and alternatives when the mapping is genuinely uncertain.

```
BBP Case  | Hex  | Semantic relation       | ES marker(s)          | EN marker(s)         | JA particle(s)       | TR suffix         | FI suffix
----------|------|-------------------------|-----------------------|----------------------|----------------------|-------------------|----------
ABS       | 0x01 | Patient / intrans. subj | (unmarked / "lo/la")  | (unmarked)           | が (ga) / を (wo)    | (unmarked)        | (nominative)
ERG       | 0x02 | Transitive agent        | (unmarked / subject)  | (unmarked / subject) | が (ga)              | (unmarked)        | (nominative)
DAT       | 0x03 | Recipient / beneficiary | a, para               | to, for (recipient)  | に (ni)              | -e/-a (dative)    | -lle/-lle
GEN_POSS  | 0x04 | Possession / relation   | de                    | 's / of (possession) | の (no)              | -(n)in/-(n)ın     | -n (genitive)
GEN_LOC   | 0x05 | Locative genitive       | de (location-rel.)    | of (location)        | の (locative use)    | -(n)in (loc. use) | -n (loc. gen.)
COM       | 0x06 | Accompaniment / with    | con                   | with                 | と (to)              | -le/-la / ile     | -n kanssa ⚠
INS       | 0x07 | Instrument / means      | con, mediante, por    | with, by means of    | で (de)              | -le/-la / ile     | -lla/-llä ⚠
INE       | 0x08 | Static interior (in)    | en (interior)         | in, inside           | に (ni) / で (de)    | -de/-da           | -ssa/-ssä
ALL       | 0x09 | Motion towards (to)     | a, hacia (general)    | to (general motion)  | へ (e) / に (ni)     | -e/-a (dir.)      | -lle/-lle ⚠
ABL       | 0x0A | Away from / from        | de, desde (general)   | from (general)       | から (kara) / より   | -den/-dan         | -lta/-ltä ⚠
DEST      | 0x0B | Intended for / for      | para                  | for (purpose/benefic)| のために (no tame ni)| için              | -lle/-lle ⚠
PART      | 0x0C | Partitive / indefinite  | un poco de / algo de  | some, any (partitive)| (no direct particle) | (none)            | -a/-ä (partitive)
PROL      | 0x0D | Through / via           | por, a través de      | through, via, along  | を (motion-wo)       | -den/-dan ⚠       | pitkin / kautta
MOT       | 0x0E | Because of / caused by  | por, a causa de       | because of, due to   | によって (ni yotte)   | yüzünden          | takia / vuoksi
ALL_TERM  | 0x0F | Up to / as far as       | hasta                 | until, up to, as far | まで (made)          | -e kadar          | -lle asti / saakka
ALL_DIR   | 0x10 | Towards (directional)   | hacia, en dirección a | towards, in dir. of  | の方へ (no hō e)     | -e doğru          | -lle päin / kohti
TOP       | 0x11 | Discourse topic         | (en cuanto a / pues)  | as for, regarding    | は (wa)              | (topicalization)  | (no overt marker)
ILL       | 0x12 | Into (bounded interior) | en (ingreso), dentro de | into              | に (ingress context) | -e/-a (motion in) | -VVn/-hVn
ELA       | 0x13 | Out of (interior)       | fuera de, de dentro   | out of               | から (departure)     | -den/-dan (exit)  | -sta/-stä
SUP       | 0x14 | On surface (static)     | en (superficie)       | on (static)          | の上に (no ue ni)    | -de/-da ⚠         | -lla/-llä ⚠
SUB       | 0x15 | Onto surface (motion)   | sobre, encima de      | onto                 | の上に (motion)      | -e/-a ⚠           | -lle/-lle ⚠
DEL       | 0x16 | From surface (motion)   | de encima de          | from off             | の上から             | -den/-dan ⚠       | -lta/-ltä ⚠
ADE       | 0x17 | Near / at vicinity      | junto a, en           | at, by, near         | で (location)        | -de/-da ⚠         | -lla/-llä ⚠
VOC       | 0x18 | Direct address (O! / !) | (oh, ¡eh!)            | O!, hey              | (no particle)        | (no marker)       | (no marker)
ESS       | 0x19 | In role of (static)     | como (rol estático)   | as (role/capacity)   | として (to shite)    | olarak            | -na/-nä
TRANS     | 0x1A | Becoming / turning into | en (convertirse en)   | into (become X)      | になる (ni naru)     | -e/-a dönüşmek    | -ksi
SEM       | 0x1B | Like / resembling       | como (semejanza)      | like, as if          | のように (no yō ni)  | gibi              | kuin (+ nom.)
ABE       | 0x1C | Without / lacking       | sin                   | without              | なしで (nashi de)    | -sız/-siz / -suz  | -tta/-ttä
```

**Notes on ambiguous markers (⚠):**

Finnish `-lla/-llä` covers three distinct BBP cases: INS (instrumental), SUP (on surface), and ADE (at/near). The Teacher MUST resolve this using:
- Inanimate, flat-surface NP + motion semantics → SUP or DEL
- Inanimate, flat-surface NP + stative semantics → SUP
- Location NP (building, city) + stative semantics → ADE
- Object NP + action performed → INS
- If unresolvable: emit DISAMBIGUATION with all plausible cases.

Finnish `-lta/-ltä` covers DEL (from a surface) and ABL (general from).
Turkish `-de/-da` covers INE (interior) and ADE (at/vicinity) and SUP (on surface) in context.
Japanese `に (ni)` covers DAT, ALL, INE, ILL, and temporal TEMPORAL depending on context.
Spanish `de` covers GEN_POSS, GEN_LOC, ABL, ELA — context determines the case.
Spanish `para` covers DAT (recipient) and DEST (purpose/benefactive) — the Teacher MUST distinguish based on whether the argument is the semantic recipient of the transfer (DAT) or the intended beneficiary/destination without a direct transfer (DEST).

#### 12.0.2.2 Prepositions That Are NOT Eliminated

The following categories of relational markers carry semantic content that cannot be fully absorbed into a case field and MUST be emitted as BBP tokens:

**A — Subordinating conjunctions.** These do not govern NPs; they govern full clauses and carry semantic relations between propositions, not between NPs and verbs.

| Surface marker | BBP encoding |
|----------------|-------------|
| porque / because / parce que | CONJUNCTION (0o04), subordinating causal |
| aunque / although / obwohl | CONJUNCTION, concessive |
| si / if / если | CONDITIONAL (0o62) |
| cuando / when | TEMPORAL (0o63) |
| para que / so that / damit | CONJUNCTION, final-purpose |
| mientras / while | TEMPORAL (0o63) |
| a menos que / unless | CONDITIONAL (0o62) + MODAL (0o64) |

**B — Degree and comparison markers.** These relate two quantities or qualities and cannot be reduced to a case on either NP.

| Surface marker | BBP encoding |
|----------------|-------------|
| más que / more than | MATH_OPERATOR (0o22), FEATURES=GREATER_THAN |
| menos que / less than | MATH_OPERATOR, FEATURES=LESS_THAN |
| tan... como / as... as | MATH_OPERATOR, FEATURES=EQUAL + SEM context |
| cuanto más... más / the more... the more | MODAL (0o64) + proportional structure |

**C — Discourse particles with no NP government.** These modify the entire utterance rather than governing a specific NP.

| Surface marker | BBP encoding |
|----------------|-------------|
| bueno / well / bon | INTERJECTION (0o05) |
| pues / so / donc | CONJUNCTION (0o04), discourse-connective |
| además / furthermore | CONJUNCTION, additive |
| sin embargo / however | CONJUNCTION, adversative |
| por ejemplo / for example | CONJUNCTION, exemplificative |

**D — Phrasal verb particles in English.** In English, many prepositions form inseparable semantic units with their verbs ("put up with", "look after", "give up", "break down"). These are not relational markers; they are part of the verb's lexical identity. The Teacher MUST:
1. Identify the phrasal verb as a single lexical unit.
2. Map it to the appropriate dictionary lemma (e.g., "put up with" → TOLERATE, "give up" → ABANDON, "look after" → CARE_FOR).
3. Emit a single UAT token with the semantic lemma, discarding the particle.
4. The remaining NPs take their cases normally.

The BBP Linguistic Conventions document maintains a phrasal verb inventory for English with lemma mappings.

**E — Particles with aspectual or modal content.** Some particles are grammaticalized markers of aspect, mode, or pragmatics that have no direct case equivalent.

| Surface marker | BBP encoding |
|----------------|-------------|
| ya (Spanish, completive) | TEMPORAL ASPECT_FINE=RESULTATIVE or PERF aspect |
| todavía / still | TEMPORAL (0o63) |
| ya no / no longer | TEMPORAL + MODAL |
| a punto de / about to | TEMPORAL ASPECT_FINE=PROSPECTIVE |

#### 12.0.2.3 The Synthetic Model Applied to Analytic Languages

The following worked examples demonstrate preposition elimination across the seven core typological languages.

**Example 1 — English (fully analytic)**

> "The report from the director of the department for the minister"

```
[report,      ABS,      SINGULAR, DEF]    → "the report"
[director,    ABL,      SINGULAR, DEF]    → "from the director"
[department,  GEN_POSS, SINGULAR, DEF]    → "of the department"
[minister,    DEST,     SINGULAR, DEF]    → "for the minister"
```

4 BBP tokens. Prepositions "from", "of", "for", definite articles "the" × 3: all eliminated. **Compression: 64%.**

**Example 2 — Spanish (prepositional, partially synthetic via clitics)**

> "Le dio el libro a su amigo sin ningún esfuerzo"

```
[dar,      UAT, NOR-NORI-NORK, PAST, PERF, S:3s, O:3s, IO:3s]
[libro,    DICT_WORD, ABS,  SINGULAR, DEF]     → "el libro"
[amigo,    DICT_WORD, DAT,  SINGULAR, GEN_POSS]→ "a su amigo"  (DAT absorbs "a")
[esfuerzo, DICT_WORD, ABE,  SINGULAR, INDEF]   → "sin ningún esfuerzo"
                                              (ABE absorbs "sin"; NEG det absorbs "ningún")
```

**Example 3 — Japanese (particle language)**

> 彼女は先生として学校で子供たちに教えた

```
[kanojo,   PRONOUN,   TOP,  SINGULAR, DEF]   → 彼女は — topic
[sensei,   DICT_WORD, ESS,  SINGULAR, INDEF] → 先生として — as a teacher (ESS absorbs として)
[gakkō,    DICT_WORD, INE,  SINGULAR, DEF]   → 学校で — at the school (INE absorbs で)
[kodomo,   DICT_WORD, DAT,  PLURAL, DEF]   → 子供たちに — to the children (DAT absorbs に)
[oshieru,  UAT, NOR-NORI, PAST, PERF, S:3s, IO:3p]
```

**Example 4 — Turkish (postpositional + case-suffix agglutinative)**

> "Müdürden öğrenciler için kitap aldı"

```
[müdür,    DICT_WORD, ABL,  SINGULAR, DEF]   → müdür-den — from the director
[öğrenci,  DICT_WORD, DEST, PLURAL, DEF]   → öğrenciler için — for the students
                                              (DEST absorbs postposition için)
[kitap,    DICT_WORD, ABS,  SINGULAR, INDEF] → kitap — a book
[almak,    UAT, NOR-NORK, PAST, PERF, S:3s, O:3s]
```

**Example 5 — Finnish (case-suffix agglutinative, richest case inventory)**

> "Kirja on pöydällä ilman kantta" — "The book is on the table without a cover"

```
[kirja,  DICT_WORD, ABS, SINGULAR, DEF]    → "the book"
[pöytä,  DICT_WORD, SUP, SINGULAR, DEF]   → pöydällä — "on the table"
[kansi,  DICT_WORD, ABE, SINGULAR, INDEF]  → kannetta (ilman kantta) — "without a cover"
                                          (ilman is redundant with ABE suffix; absorbed)
[olla,   UAT, NOR, PRES, IMPERF, S:3s] → "is"
```

Note: Finnish sometimes uses both the postposition *ilman* AND the abessive suffix simultaneously for emphasis. The Teacher MUST treat this as a single ABE relation (not double-encoded) and emit DISAMBIGUATION only if the reading is genuinely unclear.

#### 12.0.2.4 Structural Implications for the Renderer

When the Renderer converts BBP back to natural language, it performs the inverse operation: for each case on a BBP token, it emits the appropriate relational marker for the target language.

```
BBP token [mesa, SUP, SINGULAR, DEF]

Rendered as:
  Spanish:  "sobre la mesa" / "en la mesa"
  English:  "on the table"
  Finnish:  "pöydällä" (suffix, no preposition)
  Turkish:  "masada" (suffix, no postposition)
  Japanese: "テーブルの上に" (postpositional phrase)
  Latin:    "in mensa" (prepositional + ablative)
```

The Renderer consults the vocabulary database for the target-language surface form corresponding to each (case, target_language) pair. The BBP Linguistic Conventions document provides the normative mapping table for all 29 cases × all 7+ core languages.

**Implementation note for the vocabulary database:** Each entry in the dictionary that corresponds to a relational preposition or postposition MUST carry a `relation_type` metadata field and, where applicable, a `default_case` field indicating the BBP case that typically absorbs this marker. This enables the Validator to apply C6 deterministically without LLM involvement.

### 12.1 Nominative-Accusative → Ergative-Absolutive Conversion

BBP uses an ergative-absolutive case system. The Teacher LLM must convert from the source language's alignment, following the Case Assignment Decision Tree (§12.0):

| Source analysis (nom-acc) | BBP mapping | Rule |
|---|---|---|
| Transitive subject | **ERGATIVE** | Agent of transitive verb → ERG |
| Transitive object | **ABSOLUTIVE** | Patient of transitive verb → ABS |
| Intransitive subject | **ABSOLUTIVE** | Subject of intransitive verb → ABS |
| Indirect object | **DATIVE** | Recipient/beneficiary → DAT |

**Example — Spanish "Juan come pan" vs "Juan corre":**

```
"Juan come pan":  Juan = ERG (transitive agent), pan = ABS (patient)
"Juan corre":     Juan = ABS (intransitive subject)
```

The same entity ("Juan") receives different cases depending on transitivity. This is correct and is the essence of ergativity. The Teacher LLM prompt MUST include explicit examples of this conversion, and round-trip tests MUST cover transitivity alternations.

#### 12.1.1 Unaccusativity and Split-Intransitivity

The mapping from nominative-accusative languages to BBP's ergative-absolutive system is complicated by **split-intransitivity**: some intransitive verbs behave like transitives (the subject is agent-like), while others behave like passives (the subject is patient-like).

**Terminology:**

- **Unaccusative verbs** (patient-subject): "the water boils", "the vase broke", "he fell". The subject undergoes a change of state without volitional control. In BBP: subject → ABS, system → NOR.
- **Unergative verbs** (agent-subject): "she runs", "he laughed", "they danced". The subject acts volitionally. In BBP: subject → ABS, system → NOR. (Both receive ABS in BBP's intransitive frame, but the distinction is recorded in dictionary metadata.)

**Rules for Frontier Translator (Teacher LLM encoder):**

The Teacher MUST apply these heuristics (overridable by language-specific rules):

1. Verbs of change-of-state without volitional agent (boil, break, fall, grow, melt, die, arrive) → NOR + ABS. Dictionary metadata: `+unaccusative`.
2. Verbs of controlled action (run, speak, laugh, cry, dance, work) → NOR + ABS. Dictionary metadata: `+unergative`.
3. Genuinely ambiguous cases (e.g., "the river runs" — agentive motion or natural process?) → The Teacher SHOULD emit a DISAMBIGUATION token with confidence level and alternatives before the ambiguous token.

**Rules for deterministic parser encoder (§13.1.1):**

All intransitive subjects receive ABS with system NOR. No unaccusative/unergative distinction. No DISAMBIGUATION. The PROFILE provenance tag (§11.5, 0x052) SHOULD be set to 0x001 to signal deterministic encoding to downstream consumers.

**Cross-linguistic note:** Languages with morphological split-intransitivity (Georgian, some Austronesian languages) provide explicit marking that the Teacher can leverage directly. For nominative-accusative languages where the split is covert, the Teacher relies on semantic heuristics validated by round-trip testing.

### 12.2 Tenseless Languages (Mandarin, Burmese)

Set Tense = Present (00) and encode aspect faithfully. Use TEMPORAL ASPECT_FINE (Section 6.7.3) for fine aspectual distinctions:

| Mandarin | Aspect | Fine Aspect | Tense | Mood |
|----------|--------|-------------|-------|------|
| 他吃 (tā chī) | Imperfective | — | Present | Indicative |
| 他吃了 (tā chī le) | Perfective | — | Present | Indicative |
| 他在吃 (tā zài chī) | Imperfective | — | Present | Indicative |
| 他吃过 (tā chī guò) | Perfective | EXPERIENTIAL | Present | Indicative |
| 他吃起来 (tā chī qǐlái) | (any) | INCOATIVE | Present | Indicative |

### 12.3 Topic-Comment Languages (Japanese, Korean)

Use TOPICAL case (0x11) for the discourse topic:

```
"象は鼻が長い" (As for elephants, noses are long):
[elephant, DICT_WORD, TOPICAL, PLURAL, DEF]
[nose, DICT_WORD, ABS, SINGULAR, DEF]
[long, ADJECTIVE, ABS, SINGULAR, DEF]
[be, UAT, NOR, PRES, IMPERF, ACTIVE, IND, S:3]
```

### 12.4 Polysynthetic Languages (Mohawk, Inuktitut)

Word-sentences must be decomposed into constituent tokens by the Teacher LLM during morphological analysis. A single Inuktitut word like "tusaatsiarunnanngittualuujunga" (I can't hear very well) becomes:

```
[hear, UAT, NOR-NORK, PRES, IMPERF, ACTIVE, NEG, IND, S:1, O:3]
[well, ADVERB]
```

The decomposition is part of the Teacher LLM's task, not the protocol's.

### 12.5 Arabic Root System

The triconsonantal root system (k-t-b → kataba, kitāb, kātib) is a lexical property. Related words share adjacent dictionary IDs in the BBP vocabulary database, enabling semantic clustering, but the root relationship is not encoded in the 64-bit token. It lives in the dictionary metadata.

### 12.6 Arabic Dual Number

Arabic obligatory dual marking maps directly to Number=DUAL (0x3):

```
"كتابان" (kitābāni = "two books"):
[kitāb, DICT_WORD, ABS, DUAL, DEF]

"كتب" (kutub = "books, 3+"):
[kitāb, DICT_WORD, ABS, PLURAL, DEF]
```

### 12.7 Evidential Languages (Turkish, Quechua, Tibetan)

Languages with obligatory evidentiality MUST use the EVIDENTIAL native verb feature field (Section 6.7.2) before every UAT:

```
Turkish "gelmiş" (reportedly came):
[EVIDENTIAL, system=REPORTED, UNMARKED]
[gel, UAT, NOR, PAST, PERF, S:3]

Turkish "geldi" (came, witnessed):
[EVIDENTIAL, system=DIRECT, UNMARKED]
[gel, UAT, NOR, PAST, PERF, S:3]
```

The Validator (Section 13.2.1) enforces this requirement for streams tagged with evidential source languages.

---

## 13. AI Strategy — The Hybrid Model

### 13.1 Architecture Overview

```
┌─────────────────┐     ┌──────────────────┐     ┌─────────────────┐
│  Natural Text    │────>│  Teacher (LLM)   │────>│  BBP-IR (JSON)  │
│  "El gato come"  │     │  Claude / GPT-4  │     │  Structured     │
└─────────────────┘     └──────────────────┘     └────────┬────────┘
                                                           │
                                                           v
                                                  ┌──────────────────┐
                                                  │  Validator        │
                                                  │  Axiomatic Rules  │
                                                  │  (Section 13.2.1) │
                                                  └────────┬─────────┘
                                                           │
                                                           v
                                                  ┌──────────────────┐
                                                  │  Compiler (MCP)  │
                                                  │  Deterministic   │
                                                  │  Python/Zig/C++  │
                                                  └────────┬─────────┘
                                                           │
                                                           v
                                                  ┌──────────────────┐
                                                  │  BBP Binary      │
                                                  │  0xA2F3...       │
                                                  └────────┬─────────┘
                                                           │
                                                  ┌────────┴────────┐
                                                  v                 v
                                        ┌──────────────┐  ┌──────────────┐
                                        │  Decoder      │  │  Student     │
                                        │  Round-trip   │  │  (BBP Model) │
                                        │  BBP→Text     │  │  Fine-tuned  │
                                        └──────────────┘  └──────────────┘
```

### 13.1.1 Deterministic Parser as Frontier Translator Alternative (Normative)

The Frontier Translator defined in §13.1 operates as a large language model performing probabilistic NL→BBP conversion. This design is required when the input is natural language text, where disambiguation is inherently probabilistic.

When the input is **structured and fully typed**, a deterministic parser MAY replace the Frontier Translator entirely. Structured inputs include:

- **Sensor and telemetry streams**: typed numeric values, enumerated states, timestamps, coordinates. All fields are fully specified; no disambiguation is required.
- **Typed API events**: JSON or Protocol Buffer payloads with explicit field names and types. The BBP encoding is a direct structural mapping.
- **Abstract Syntax Trees (ASTs)**: compiler-generated or parser-generated ASTs for source code inputs. BBP-AST (§16) provides the normative mapping.
- **Database query results**: typed relational or document-store records.

A deterministic parser for structured inputs is fully specified by a **field mapping table** that maps each input field type to a BBP meta-type and FEATURES layout. It requires no Teacher LLM, no training, and produces perfectly correct BBP tokens by construction.

**Architectural consequence.** The use of a deterministic parser for structured inputs does not constitute a distinct protocol profile or a distinct conformance class. The resulting binary stream is valid BBP-64 and is indistinguishable at the receiver from a stream produced by a trained Frontier Translator. The choice of encoder is a deployment decision, not a protocol distinction.

**Recommended use cases for deterministic parsers:**
- Sensor fusion pipelines where all inputs are typed numeric values.
- Agent-to-agent communication where both agents speak BBP natively and no NL translation is needed.
- Code analysis toolchains where the input is an AST.
- Any domain where the source data is already structured and typed.

---

### 13.2 Teacher LLM — Dual Role

The Teacher LLM operates in two distinct modes that differ fundamentally in direction, reliability, and primary usage:

**Primary role — Synthesis (BBP-IR → NL).** The Teacher receives a BBP-IR structure produced by the combinatorial generator (§15.4.0) and generates the most natural and accurate natural-language sentence that expresses that structure. This is the direction where world-class LLMs are maximally reliable: conditioned generation from a fully specified structure. This is the dominant role of the Teacher in the Frontier Translator training pipeline (§15.4.0) and produces the majority of the training corpus.

**Secondary role — Analysis (NL → BBP-IR).** The Teacher receives natural text and produces BBP-IR (JSON). This is the direction where LLM reliability is lower due to the inherent ambiguity of natural language. This role is used only for supplementary corpus generation — covering idiomatic, metaphorical, and discourse-level phenomena that resist combinatorial enumeration (§15.4.3). Supplementary pairs are tagged `origin: analysis` and undergo stricter cross-verification.

**Architectural consequence.** Because BBP-IR is always the structural ground truth anchor and NL is the generated artifact, the Teacher's errors in the synthesis direction (producing an unnatural or ungrammatical sentence) are detectable and filterable. Errors in the analysis direction (misassigning a case or evidentiality value) are structurally invisible and propagate silently. The primacy of the synthesis direction is therefore not merely a pragmatic choice — it is an architectural safety property. See §15.4.0 for the full normative treatment.

When operating in Analysis mode, the Teacher LLM prompt MUST include:

- Explicit nominative-accusative → ergative-absolutive conversion rules (§12.0 Case Assignment Decision Tree).
- Examples for tenseless languages (Mandarin), topic-comment (Japanese), and polysynthetic decomposition.
- Instructions to not infer tense when the source language does not encode it.
- Instructions to use TOPICAL case for は/은/는 particles.
- Instructions to emit EVIDENTIAL bound modifier tokens for evidential languages.
- Instructions to use TEMPORAL ASPECT_FINE for fine aspectual distinctions.
- Criteria for choosing PLACEHOLDER vs reordering for cataphoric structures.
- **Preposition and particle elimination rules (§12.0.2).** Prepositions, postpositions, and grammatical case particles MUST NOT be emitted as BBP-IR tokens. Their semantic content MUST be absorbed into the `case` field of the nominal token they govern. See §12.0.2 for the complete mapping table and the exhaustive list of exceptions (subordinating conjunctions, comparison markers, discourse particles, phrasal verb particles, and aspectual particles).

#### 13.2.0 Hybrid Teacher Architecture (Normative Recommendation)

To mitigate the Teacher LLM's known unreliability on formal linguistic analysis tasks, BBP defines a **hybrid Teacher architecture** as the recommended (SHOULD) implementation. The hybrid approach decomposes the Teacher's work into three components, each handling what it does best:

**Component 1 — Morphological Analyzer (rule-based, per language).** Handles segmentation, case identification, and evidentiality detection from surface morphology. Tools such as Apertium (for Basque, Turkish, Spanish), MeCab (for Japanese), or language-specific analyzers provide high-accuracy morphological analysis. This component is deterministic and has known error characteristics.

**Component 2 — Syntactic Parser (neural, pre-trained).** A Universal Dependencies parser (e.g., Stanza or Trankit) provides dependency trees with labeled arcs (nsubj, obj, iobj, obl), which map systematically to BBP cases. The mapping from UD labels to BBP structures is largely rule-based:

```
nsubj + transitive verb     → ERG
nsubj + intransitive verb   → ABS
obj                         → ABS
iobj                        → DAT
obl:tmod                    → TEMPORAL case
obl:agent (passive)         → ERG (if promoted) or omitted
```

The UD parser's `obl` (oblique) dependency arc covers many prepositional phrases. For each `obl` arc, the UD annotation includes the governing preposition (in the `deprel` or `feats` fields). The mapping from (obl, preposition) to BBP case is largely deterministic and SHOULD be handled by Component 2 (rule-based), not delegated to the LLM Semantic Enricher. The LLM is needed only for ambiguous prepositions (⚠ cases in §12.0.2.1 table) where context is required.

Updated UD → BBP mapping for oblique prepositions (append to existing table):

```
obl:from + "from"            → ABL
obl:from + "out of"          → ELA
obl:to + "to" (motion)       → ALL
obl:to + "to" (recipient)    → DAT
obl:to + "into"              → ILL
obl:for + "for" (benefac.)   → DEST
obl:for + "for" (recipient)  → DAT
obl:with + "with" (accomp.)  → COM
obl:with + "with" (instr.)   → INS  [ambiguous: delegate to LLM]
obl:in + "in" (interior)     → INE
obl:on + "on" (surface)      → SUP
obl:without + "without"      → ABE
obl:like + "like"            → SEM
obl:as + "as" (role)         → ESS
obl:through + "through"      → PROL
obl:because + "because of"   → MOT
obl:until + "until"          → ALL_TERM
obl:towards + "towards"      → ALL_DIR
```

**Component 3 — LLM Semantic Enricher (Claude/GPT-4).** Handles only the tasks that rule-based and neural parsers cannot: semantic role disambiguation for ambiguous cases, broad synset selection, metaphor detection, evidentiality assignment for non-agglutinative languages, pragmatic analysis, and DISAMBIGUATION confidence estimation.

This division of labor plays to each component's strengths. The morphological analyzer is deterministic and handles evidentiality perfectly for agglutinative languages. The UD parser provides reliable syntactic structure. The LLM fills in semantic gaps. The Axiomatic Validator then catches errors from all three components.

**Fallback:** An implementation that uses a single LLM for all three roles (the "monolithic Teacher") is conformant but SHOULD document its error rates per phenomenon tier and SHOULD apply stricter human review thresholds.

#### 13.2.0.1 Teacher Difficulty Rating

Every BBP feature is categorized by Teacher LLM reliability. The corpus generation pipeline (§15.4) applies different QA thresholds per tier:

| Tier | Reliability | Features | QA Policy |
|------|------------|----------|-----------|
| **Tier 1** (>95%, automatable) | Basic transitivity, number, definiteness, tense (for languages that mark it), basic case assignment, NOR/NOR-NORK system selection | Auto-validated by Axiomatic Validator |
| **Tier 2** (80-95%, LLM-assisted) | Fine aspect, voice, mood, complex case (comitative, instrumental), evidentiality for Turkish, topic-comment for Japanese | Spot-checked: 10% human review |
| **Tier 3** (<80%, requires human review) | Unaccusativity classification, complex scope interactions, metaphor detection, serial verb decomposition, social deixis | Human review for every instance |

#### 13.2.0.2 Universal Dependencies Bootstrapping

Instead of generating all BBP-IR from raw text via an LLM, the recommended approach bootstraps from existing **Universal Dependencies treebanks** (which have gold-standard syntactic annotation for 100+ languages). The mapping from UD dependency labels to BBP structures is largely rule-based (see Component 2 above). The LLM is then used only to fill in information that UD does not provide: broad synset IDs, aspect classification, evidentiality, and semantic enrichment. This leverages decades of linguistic annotation work and dramatically reduces the amount of analysis the LLM must perform from scratch.

#### 13.2.1 Axiomatic Validator

Between the Teacher LLM output (BBP-IR JSON) and the Compiler's binary packing, a **deterministic validation layer** verifies structural and semantic consistency. The Validator can **reject** (emit error), **warn** (emit warning but proceed), or **auto-correct** (fix and log) issues in the IR.

##### Axiom Group A — Case-System Consistency

```
A1: IF any UET has case=ERG
    THEN the clause's UAT MUST have system ∈ {NOR-NORK, NOR-NORI-NORK}
    SEVERITY: CRITICAL (error)

A2: IF UAT.system = NOR
    THEN no UET in the clause SHALL have case=ERG
    SEVERITY: CRITICAL (error)

A3: IF any UET has case=DAT
    THEN the clause's UAT MUST have system ∈ {NOR-NORI, NOR-NORI-NORK}
    SEVERITY: CRITICAL (error)

A4: UAT person codes MUST match corresponding UETs:
    - UAT.subject person/number must be consistent with
      the UET bearing the subject case (ERG or ABS depending on system)
    SEVERITY: MAJOR (warning, auto-correct if unambiguous)

A5 (VOC): A UET with case=VOC MUST NOT be referenced as the
    subject (ERG or ABS) or object (ABS or DAT) of any UAT
    in the same clause.
    SEVERITY: MAJOR (warning)

A6 (VOC): An ERG/ABS/DAT axiom (A1–A3) violation triggered
    by a VOC token MUST be suppressed. VOC tokens are exempt
    from case-system consistency checks.
    SEVERITY: auto-correct (remove from argument-frame analysis)

A7 (TRANS): A UET with case=TRANS SHOULD co-occur with a UAT whose verb root
    is a change-of-state or motion-to-state verb (become, turn, grow, develop,
    transform). A TRANS-marked NP with a stative UAT triggers a MINOR warning.
    SEVERITY: MINOR (warning)

A8 (ESS-TRANS): A clause MUST NOT contain two NPs where one bears ESS and
    another bears TRANS on what appears to be the same referent. This indicates
    a static/dynamic mismatch in the Teacher's analysis.
    SEVERITY: MAJOR (warning)

A9 (ILL-INE): A UET with case=ILL SHOULD co-occur with a UAT that encodes motion
    (motion verb class in dictionary metadata). A static UAT with an ILL NP triggers
    a MINOR warning (possibly a Teacher error collapsing ILL and INE).
    SEVERITY: MINOR (warning)

A10 (ELA-ABL): A UET with case=ELA SHOULD co-occur with a motion/departure verb.
    A stative UAT with ELA triggers a MINOR warning.
    SEVERITY: MINOR (warning)

A11 (SUB-DEL): A clause SHOULD NOT contain both SUB and DEL on distinct NPs
    unless the predicate explicitly encodes a transfer-from-surface-to-surface
    action. Flag for human review.
    SEVERITY: TRIVIAL (informational)

A_CLF1: A token with noun_classifier = CLF_EXTENDED (0xE) MUST be
    immediately preceded by a CLF auxiliary token (meta-type = DICT_WORD or
    ADJECTIVE, flow = UNIT, case = UNMARKED).
    SEVERITY: MAJOR (warning on non-conforming streams)

A_CLF2: A CLF auxiliary token (identified by its structural position
    preceding a CLF_EXTENDED nominal) MUST NOT itself carry clf = CLF_EXTENDED.
    Stacked CLF auxiliary tokens are invalid.
    SEVERITY: CRITICAL (error)
```

**Elevation quality:** An ELEVATED stream MUST achieve Validator pass rate ≥ 85% (see §2.8.5).

##### Axiom Group B — Structural Consistency

```
B1: Every START MUST have a matching END with the same meta-type.
    Exception: CONTROL blocks, where the END meta-type is always
    CONTROL (0o66) regardless of the specific CONTROL FEATURES in
    the START token. A meta-type mismatch (outside the CONTROL
    exception) is a structural error.
    SEVERITY: CRITICAL (error)

B2: Nesting depth MUST return to 0 at SENTENCE_END
    SEVERITY: CRITICAL (error)

B3: REFERENCE.FEATURES MUST be < current logical token index
    SEVERITY: CRITICAL (error)

B4: LITERAL.FEATURES MUST be > 0
    SEVERITY: MAJOR (error)

B5: Empty blocks (START immediately followed by END) are invalid
    SEVERITY: MINOR (warning, auto-remove)

B6: Every PLACEHOLDER (det=0xA) MUST have a corresponding
    PLACEHOLDER_RESOLVE before SENTENCE_END
    SEVERITY: CRITICAL (error)

B7: native verb feature field UETs (EVIDENTIAL, TEMPORAL with func=ASPECT_FINE)
    MUST be immediately followed by a UAT or another native verb feature field
    SEVERITY: CRITICAL (error)

B8: A REFERENCE with flow=LITERAL MUST have FEATURES ∈ {4, 8}.
    Other byte lengths are invalid for Extended References (Section 8.5).
    SEVERITY: CRITICAL (error)

B9: The absolute index in an Extended Reference MUST be
    < current logical token index (no forward references).
    SEVERITY: CRITICAL (error)

B10: A REFERENCE with flow=LITERAL and FEATURES=4 MUST be
     followed by exactly 4 raw bytes and a REFERENCE END token.
     SEVERITY: CRITICAL (error)

B11: A REFERENCE with flow=START is invalid. REFERENCE blocks
     only use UNIT (direct) or LITERAL+END (extended).
     SEVERITY: CRITICAL (error)

```

##### Axiom Group C — Semantic Consistency

```
C1: UAT polarity=NEG AND UET det=NEGATIVE in same clause
    → warning (potential unintended double negation)
    SEVERITY: MINOR (warning)

C2: IF source_language = Mandarin
    THEN UAT.tense SHOULD be PRESENT
    UNLESS fine_aspect = EXPERIENTIAL (which may co-occur with PAST)
    SEVERITY: MINOR (warning)

C3: IF source_language ∈ {Turkish, Quechua, Tibetan, Aymara, Tuyuca}
    THEN every UAT MUST be preceded by an EVIDENTIAL native verb feature field
    SEVERITY: MAJOR (warning)

C4: IF UAT.system = NOR-NORI-NORK
    THEN the UAT FEATURES MUST encode a non-NULL indirect_object argument (bits 8–6 ≠ 000)
    AND the UET stream MUST contain at least one ABS and one DAT token
    SEVERITY: CRITICAL (error)

C5: The following combinations of UAT Aspect × ASPECT_FINE are INVALID:
    - Perfective + HABITUAL        → SEVERITY: MAJOR (error)
    - Perfective + CONTINUATIVE    → SEVERITY: MAJOR (error)
    - Imperfective + SEMELFACTIVE  → SEVERITY: MAJOR (error)
    All other combinations are VALID. Unspecified future ASPECT_FINE
    values are presumed compatible with both aspects unless documented.
    SEVERITY: MAJOR (error)

C6 (PREPOSITION_LEAK): A DICT_WORD, ADVERB, or CONJUNCTION token whose lemma
    resolves in the dictionary to a purely relational preposition or postposition
    (flagged in dictionary metadata as relation_type=PREPOSITION or
    relation_type=POSTPOSITION) MUST NOT appear in a stream.
    The Teacher MUST have absorbed its semantic content into the case of the
    governed NP.
    SEVERITY: MAJOR (warning — suggests Teacher error; the token is technically
    valid but semantically redundant and may indicate a failure to convert)

    Exception: Tokens with relation_type=PREPOSITION that belong to categories
    B, C, D, or E of §12.0.2.2 (comparative, discourse, phrasal, aspectual)
    are exempt from this axiom.
```

##### Axiom Group D — Auto-Correction

Some Teacher LLM errors can be deterministically corrected:

```
D1: IF UAT.system = NOR-NORK AND no UET has case=ABS in the clause
    THEN find the UET most likely to be the patient (by proximity and
    semantic role) and assign case=ABS
    ACTION: auto-correct + log

D2: IF a UET has case=NOMINATIVE (invalid in BBP)
    THEN convert based on transitivity of nearest UAT:
    - If UAT is transitive and UET is agent → ERG
    - If UAT is intransitive → ABS
    ACTION: auto-correct + log

D3: IF an ADVERB immediately precedes a UAT but is tagged as ADJECTIVE
    (common Teacher LLM error for manner modifiers)
    THEN change meta-type to ADVERB
    ACTION: auto-correct + log
```

##### Error Severity and Weighted Error Rate

| Level | Description | Weight |
|-------|-------------|--------|
| CRITICAL | Changes who-does-what-to-whom or breaks structure | ×10 |
| MAJOR | Loses grammatical information (aspect, evidentiality) | ×5 |
| MINOR | Loses specificity (determiner, number) | ×2 |
| TRIVIAL | Style/order issues that don't affect logic | ×1 |

**Weighted error rate:**

```
error_rate = Σ(errors × weight) / Σ(tokens × max_weight)
```

**Target:** error_rate < 5% on validation test suite.

##### Axiom Group E — Code Structural Consistency (BBP-AST, Section 16)

```
E1: Every FUNC_DEF block MUST contain at least one identifier
    (the function name), except for anonymous functions (lambdas)
    inside FUNC_CALL arguments.
    SEVERITY: MAJOR (warning)

E2: Every FUNC_CALL block MUST contain at least one identifier
    or REFERENCE as its first element (the called function).
    SEVERITY: CRITICAL (error)

E3: A RETURN or YIELD token MUST be inside a FUNC_DEF block
    (at any nesting depth).
    SEVERITY: CRITICAL (error)

E4: A LOOP_BREAK or LOOP_CONTINUE token MUST be inside a
    LOOP_FOR or LOOP_WHILE block (at any nesting depth).
    SEVERITY: CRITICAL (error)

E5: A CONDITIONAL block MUST contain at least one COND_BRANCH.
    SEVERITY: CRITICAL (error)

E6: A COND_ELSE block MUST be the last child of a CONDITIONAL
    block and MUST NOT have a condition expression.
    SEVERITY: MAJOR (warning)

E7: A MATCH block MUST contain at least one MATCH_ARM.
    SEVERITY: CRITICAL (error)

E8: A TRY_CATCH block MUST contain exactly one TRY_BODY and
    at least one CATCH_BODY or FINALLY_BODY.
    SEVERITY: CRITICAL (error)

E9: A REFERENCE inside a BBP-AST block SHOULD point to an
    identifier defined in the current scope chain.
    SEVERITY: MINOR (warning — may reference external symbols)

E10: ASSIGN MUST be followed by exactly two elements: a target
     (REFERENCE or member-access expression) and a value
     (expression, literal, or FUNC_CALL).
     SEVERITY: MAJOR (warning)

E11: An AST_OPAQUE block (CONTROL 0x07C) MUST contain at least
     one LANG_* UET (Group 5) as its first content token.
     An AST_OPAQUE without a language identifier is invalid.
     SEVERITY: MAJOR (error)

E12: An AST_OPAQUE block SHOULD contain a node_kind identifier
     as its second content token (DICT_WORD or LITERAL).
     Absence triggers a warning but is not an error (the block
     is still navigable, just less informative).
     SEVERITY: MINOR (warning)

E13: A MATH_EXPRESSION block inside a BBP-AST type annotation position (immediately
     following a parameter or return-type slot in FUNC_DEF, or inside VAR_DECL/
     CONST_DECL after the identifier) SHOULD be replaced with TYPE_EXPR.
     SEVERITY: MINOR (warning — backward compatible but semantically ambiguous)

E14: A TYPE_EXPR block MUST contain at least one MODIFIER or TYPE_PARAM as its
     first child (the base type constructor).
     SEVERITY: MAJOR (error)

E15: A TYPE_PARAM with flow=LITERAL MUST be followed by its END token before the
     next sibling in the enclosing TYPE_EXPR or GENERIC_PARAM block.
     SEVERITY: CRITICAL (error)

E16: A TYPE_EXPR MUST NOT appear outside a BBP-AST scope (i.e., outside a
     FUNC_DEF, VAR_DECL, CONST_DECL, TYPE_DEF, ENUM_DEF, CLASS_DEF, or
     IMPL_BLOCK). TYPE_EXPR inside BBP-NL or BBP-CoT is invalid.
     SEVERITY: MAJOR (warning — structural anomaly)
```

##### Axiom Group F — Reasoning Structural Consistency (BBP-CoT, Section 17)

```
F1: A RETRACTION UET MUST be immediately followed by a REFERENCE UET
    whose FEATURES points to a HYPOTHESIS or CONCLUSION token.
    SEVERITY: CRITICAL (error)

F2: A RETRACTION's REFERENCE target MUST point to a token with
    meta-type HYPOTHESIS (0o70) or CONCLUSION (0o71).
    Retracting EVIDENCE, QUERY, or other types is invalid.
    SEVERITY: CRITICAL (error)

F3: A RETRACTION MUST NOT target an already-retracted block.
    (No double retraction — retract once.)
    SEVERITY: MAJOR (warning)

F4: An INFERENCE_STEP SHOULD be inside or immediately before
    a CONCLUSION block. An orphaned INFERENCE_STEP triggers a warning.
    SEVERITY: MINOR (warning)

F5: A CONCLUSION with strength >= STRONG (code >= 2) SHOULD be
    preceded by at least one EVIDENCE or INFERENCE_STEP in the
    same reasoning scope.
    SEVERITY: MINOR (warning)

F6: A CONCLUSION's basis_count field SHOULD match the actual count
    of EVIDENCE and INFERENCE_STEP blocks that precede it in the
    current reasoning scope and reference it (or are unreferenced
    and thus implicitly supporting the nearest subsequent CONCLUSION).
    SEVERITY: TRIVIAL (warning, auto-correct if unambiguous)

F7: A CONFIDENCE UET MUST be preceded by a block-closing token
    (HYPOTHESIS END, CONCLUSION END, EVIDENCE END) or another
    CONFIDENCE UET. CONFIDENCE not following a block is invalid.
    SEVERITY: MAJOR (warning)

F8: Within a REASONING scope, at least one CONCLUSION or QUERY
    SHOULD be present. A reasoning block with only HYPOTHESIS
    and EVIDENCE but no CONCLUSION or QUERY is structurally
    complete but semantically incomplete — flagged for review.
    SEVERITY: TRIVIAL (warning)

F9: REASONING_START and REASONING_END MUST be balanced (matching
    START/END). Nested REASONING blocks are permitted (reasoning
    within reasoning, e.g., exploring a sub-problem).
    SEVERITY: CRITICAL (error)

F10: Meta-cognitive UETs (Group 7, 0o70-0o76) SHOULD only appear
     inside a REASONING scope. Meta-cognitive tokens outside any
     REASONING block trigger a warning (valid but semantically
     ambiguous — implicit reasoning scope assumed).
     SEVERITY: MINOR (warning)
```

##### Axiom Group G — Checkpoint Consistency (Sections 11.7, 11.8)

```
G1: A STATE_CHECKPOINT's depth field MUST match the actual nesting
    depth at the point of emission. A mismatch indicates encoder bug.
    SEVERITY: CRITICAL (error)

G2: A STATE_CHECKPOINT's stack snapshot MUST accurately reflect
    the meta-types of all currently open blocks.
    SEVERITY: CRITICAL (error)

G3: The logical_token_index in a STATE_CHECKPOINT MUST equal the
    checkpoint token's own logical index in the stream.
    SEVERITY: CRITICAL (error)

G4: In a Seekable stream, the distance between consecutive
    STATE_CHECKPOINTs (or KEYFRAME substitutes) MUST NOT exceed
    the declared checkpoint interval (default 256 tokens).
    SEVERITY: MAJOR (warning)

G5: A KEYFRAME's entity table MUST only reference entities
    that were defined prior to the KEYFRAME's position.
    SEVERITY: CRITICAL (error)

G6: A KEYFRAME's local_alias values MUST be unique within the
    entity table. Duplicate aliases are invalid.
    SEVERITY: CRITICAL (error)
```

##### Axiom Group H — Ontology Compatibility (Section 11.3)

```
H1 (ONTOLOGY-1): If ONTOLOGY_VERSION is absent from the DOC_START
    block, the decoder MUST assume ontology version 1.0 (FEATURES
    0x100) for backward compatibility with streams produced before
    this mechanism was introduced.
    SEVERITY: informative (auto-correct)

H2 (ONTOLOGY-2): If ONTOLOGY_VERSION.major differs from the
    decoder's supported major version, the decoder MUST reject
    the stream with a CRITICAL error. Major version changes
    indicate incompatible ID remapping.
    SEVERITY: CRITICAL (error)

H3 (ONTOLOGY-3): If ONTOLOGY_VERSION.minor is greater than the
    decoder's supported minor version (same major), the decoder
    MUST emit a WARNING and continue processing. Payload IDs not
    recognized by the decoder SHOULD be treated as UNKNOWN (0o77)
    with the original FEATURES preserved in the binary token (the
    raw bits are kept unmodified in the stream; higher layers
    resolve the token semantically as UNKNOWN). No data is lost;
    semantic resolution is deferred.
    SEVERITY: MAJOR (warning, non-fatal)

H4 (ONTOLOGY-4): ONTOLOGY_VERSION MUST appear exactly once per
    DOC_START block. Multiple ONTOLOGY_VERSION tokens in the same
    DOC_START are invalid.
    SEVERITY: CRITICAL (error)
```

##### Axiom Group H_EXT — Evidentiality Normative Mapping

```
H_EXT1: A Frontier Translator for a language with a grammaticalized evidential
        system MUST map each native evidential category to a normative BBP
        evidentiality value per the cross-linguistic mapping table (§3.8).
        SEVERITY: MAJOR (warning for unlisted languages; error for listed languages)

H_EXT2: EV_UNSPECIFIED (0x0) MUST NOT be emitted for a predicate in a language
        with obligatory evidential marking when the source text contains an
        explicit evidential marker.
        SEVERITY: MAJOR (warning)

H_EXT3: EV_OTHER_LITERAL (0xF) MUST be accompanied by a LITERAL block containing
        the raw evidential marker as a UTF-8 string.
        SEVERITY: CRITICAL (error if absent)
```

##### Axiom Group I — Pragmatic Structure Consistency (§6.8 PRESUPPOSITION, §11.6 SPEECH_ACT)

```
I1 (PRESUP-FLOW): A PRESUPPOSITION UET (0o67) MUST use flow=START or flow=END.
    A PRESUPPOSITION with flow=UNIT is invalid.
    SEVERITY: CRITICAL (error)

I2 (PRESUP-CONTENT): A PRESUPPOSITION block (between its START and END tokens)
    MUST contain at least one UET and one UAT — it encodes a proposition,
    not a bare concept.
    SEVERITY: MAJOR (warning)

I3 (PRESUP-ORDER): A PRESUPPOSITION block SHOULD precede the token or clause
    that triggers it. A PRESUPPOSITION appearing after its trigger clause
    triggers a MINOR warning (valid but less idiomatic for training corpus).
    SEVERITY: MINOR (warning)

I4 (PRESUP-PROJECTION): A PRESUPPOSITION with projection=PROJECTS MUST NOT
    appear as a direct child of a CONDITIONAL block where the condition
    explicitly models a presupposition-cancelling operator (HOLES or PLUGS
    context). Validators MAY apply heuristic detection for obvious HOLES
    contexts (embedding under EVIDENTIAL EV_REPORT_HEAR or EV_REPORT_GENERAL,
    CONDITIONAL blocks, and NOR-NORI constructions with factive-verb LEMMA).
    SEVERITY: MAJOR (warning)

I5 (PRESUP-NESTING): PRESUPPOSITION blocks MAY be nested (a presupposition
    may itself presuppose something). Nesting depth MUST be tracked by the
    parser stack. Maximum recommended nesting depth: 3 levels.
    SEVERITY: MINOR (warning if depth > 3)

I6 (SPEECH-ACT-FLOW): A SPEECH_ACT CONTROL token (0x090) MUST use flow=START
    or flow=END. A SPEECH_ACT with flow=UNIT is invalid (the act has no
    propositional content, which is structurally meaningless).
    SEVERITY: CRITICAL (error)

I7 (SPEECH-ACT-DECLARATION): A SPEECH_ACT with force=DECLARATION and
    domain ≠ GENERAL SHOULD contain at least one HYPOTHESIS sub-block
    encoding the authorization felicity condition. A DECLARATION without
    any felicity condition triggers a MINOR warning (structurally valid
    but semantically incomplete for high-fidelity rendering).
    SEVERITY: MINOR (warning)

I8 (SPEECH-ACT-CONTENT): A SPEECH_ACT block MUST contain at least one
    non-HYPOTHESIS, non-CONFIDENCE token (the propositional content of
    the act). An act with only felicity conditions and no content is invalid.
    SEVERITY: MAJOR (warning)

I9 (SPEECH-ACT-GREETING): A SPEECH_ACT with force=GREETING SHOULD have
    domain=GENERAL and uptake=NONE or uptake=ADDRESSEE. A GREETING with
    domain=LEGAL or domain=RELIGIOUS triggers a MINOR warning (unusual
    but not structurally invalid — some ceremonial openings are formal greetings).
    SEVERITY: MINOR (warning)

I10 (FELICITY-CONFIDENCE): HYPOTHESIS blocks inside a SPEECH_ACT frame
    that represent felicity conditions SHOULD be immediately followed by a
    CONFIDENCE UET with FEATURES encoding a confidence value of 1.0
    (binary representation of certainty). Absent CONFIDENCE is valid but
    leaves the certainty of the felicity condition underspecified.
    SEVERITY: TRIVIAL (warning)

I11 (PRESUP-SPEECH-ACT-INTERACTION): A PRESUPPOSITION block MAY appear
    inside a SPEECH_ACT content block. This encodes presuppositions triggered
    by the performative content itself (e.g., "I apologize for breaking your
    vase" — SPEECH_ACT[APOLOGY] contains PRESUPPOSITION[LEXICAL, PROJECTS]
    "the vase is broken"). Validators MUST accept this nesting without error.
    SEVERITY: informative
```

##### Axiom Group K — LITERAL Block Alignment Consistency

```
K1 (LITERAL-PADDING): All padding bytes in a LITERAL block MUST be 0x00.
    Padding bytes are those beyond FEATURES(opening_token) bytes up to
    the next 8-byte boundary: ceil(FEATURES/8)*8 - FEATURES bytes.
    A padding byte with any non-zero value is a stream corruption indicator.
    SEVERITY: CRITICAL (error)

K2 (LITERAL-LENGTH): FEATURES(opening_token) MUST be > 0. A LITERAL block
    with zero byte length is structurally meaningless and is rejected.
    SEVERITY: CRITICAL (error)

K3 (LITERAL-ALIGNMENT): The total byte count consumed by a LITERAL block’s
    raw content (including padding) MUST be exactly ceil(FEATURES/8)*8.
    A parser that reads a different byte count before encountering the END
    token has encountered a malformed stream.
    SEVERITY: CRITICAL (error)

K4 (LITERAL-GRAMMAR): The FEATURES field of the LITERAL closing token
    (flow=END) MUST be interpreted as grammatical fields (case, number,
    determiner, etc.), NOT as a byte-length field. Encoders MUST NOT set
    byte-length information in the closing token’s FEATURES.
    SEVERITY: CRITICAL (error if FEATURES(END) appears to encode a length
    value inconsistent with the meta-type’s grammatical layout)

K5 (LITERAL-NO-DICT): Encoders MUST NOT emit a LITERAL block for a concept
    that has a dictionary entry. A LITERAL block whose raw UTF-8 content
    exactly matches a dictionary entry is a non-conformance indicator.
    SEVERITY: MAJOR (warning; auto-correct: replace with dictionary token)
```

Takes validated BBP-IR JSON, looks up IDs in vocabulary database, maps labels to bits, outputs binary stream. Zero ambiguity. The Compiler:

1. Receives validated IR from the Axiomatic Validator.
2. Resolves `value` strings to dictionary IDs .
3. Maps morphological labels to bit values.
4. Emits 64-bit tokens in big-endian order.
5. Handles LITERAL block padding.
6. Maintains logical token index counter.

### 13.4 Decoder — Round-Trip Verification

Decodes binary back to text. Target error rate: < 5% (weighted). Tests MUST include:

- Transitivity alternations (same noun as ERG vs ABS).
- Aspect-only encoding (Mandarin sentences).
- Fine aspect encoding (Mandarin 过, Russian за-).
- Evidentiality encoding (Turkish -mış vs -dı).
- Ditransitive EXTENDED mode.
- Literal blocks with non-ASCII UTF-8.
- Negation scope distinctions.
- Cataphoric PLACEHOLDER resolution.
- Direct lemma ID emission.
- long-range REFERENCE resolution (Section 8.5).
- Verbal gender agreement in gender-agreeing languages (Arabic, Hebrew, Amharic).

> **Note on §13.4.1 placement.** The Renderer and the Decoder are architecturally distinct components: the Decoder is a round-trip verification tool used during development and testing; the Renderer is the production component that generates target-language surface forms from BBP streams. §13.4.1 specifies normative Renderer behavior (verbal gender agreement resolution). It is placed here because the Decoder test suite (above) exercises the same gender agreement logic and provides the empirical conformance signal for V-RENDER-GENDER-1. The Renderer specification proper would belong in a dedicated §13.7 in a future revision that introduces a full Renderer section.

#### 13.4.1 Renderer — Verbal Gender Agreement Resolution (normative)

In languages where verbal morphology encodes the grammatical or natural gender of the subject (Arabic past tense, Hebrew present and past, Amharic, and Bantu languages among others), the Renderer MUST resolve gender agreement by inspecting the subject UET before generating the verbal surface form.

**Resolution algorithm (normative):**

1. Identify the subject UET of the UAT being rendered. The subject is the UET whose case field is ERG (for NOR-NORK and NOR-NORI-NORK paradigms) or ABS (for NOR paradigm).

2. **For meta-types DICT_WORD, NAME_PERSON, PRONOUN, ADJECTIVE:** extract `natural_gender` from LEMMA bits 24–23 (§2.3). If `natural_gender = GENDER_UNSPECIFIED` and the target language requires a value, apply the language-specific default (typically MASCULINE in Arabic and Hebrew).

3. **For LIVING_ENTITY:** inspect the FEATURES `noun_class` field first. If `noun_class` is populated, use it for verbal gender agreement — the Frontier Translator MUST populate `noun_class` whenever it encodes a LIVING_ENTITY in a gender-agreeing language. If `noun_class` is NC_NONE, inspect immediately following ADJECTIVE modifiers for BIOLOGICAL_MALE or BIOLOGICAL_FEMALE (§5.2.3) and map to grammatical gender per the target language's conventions. If neither is available, apply the language-specific default for HUMAN animacy.

> **Note on LIVING_ENTITY and natural_gender.** The `natural_gender` LEMMA field (bits 24–23) cannot be added to LIVING_ENTITY because those bit positions are already occupied by kingdom (bits 24–22, 3 bits) and taxon_id (bits 21–0, 22 bits). Gender agreement for LIVING_ENTITY is therefore mediated exclusively through `noun_class` in FEATURES and ADJECTIVE modifier tokens.

**Validator rule (V-RENDER-GENDER-1):** A Renderer that generates verbal morphology for a gender-agreeing language without inspecting the subject's gender field (natural_gender for DICT_WORD/NAME_PERSON/PRONOUN/ADJECTIVE; noun_class for LIVING_ENTITY) is non-conformant. Conformance is verified by round-trip testing against the Gold Set (§15.3.0.1). Severity: MAJOR (not detectable at binary stream level; verified by Decoder round-trip tests).

### 13.5 Student Model

Fine-tuned small model (BERT-base, T5-small) with multi-head BBP output:

- Head 1: Action Token or Entity Token? (1 bit — LEMMA bit 31)
- Head 2: Meta-type for Entity / Aktionsart+valency for Action (6-bit, 64 classes each)
- Head 3: Concept/verb ID (25-bit dictionary index)
- Head 4: Case (5-bit, 32 classes)
- Head 5: TAM block (Action FEATURES composite)
- Head 6: Grammar fields (Entity FEATURES composite: number, determiner, classifier, animacy, noun_class, register)

Combined loss:

```
L_total = λ₁·L_root + λ₂·L_grammar + λ₃·L_consistency
```

Where L_consistency encodes cross-head constraints derived from the Axiomatic Validator rules:

- System=NOR → Object head must predict NULL
- System=NOR-NORK → Case head for agent must predict ERG
- native verb feature field detected → next token must be UAT

### 13.6 Dictionary Strategy — Bootstrap, Growth, and Publication

The BBP dictionary — the mapping from `concept_id` (25b) and `verb_id` (25b) to canonical semantic entries — is the most critical dependency of the entire system. This section specifies how the dictionary is constructed, how it grows automatically, and how it is published and versioned. It supersedes the informal reference to "vocabulary database" in §13.3.

#### 13.6.1 Design Invariants

The following invariants apply to all versions of the BBP dictionary and MAY NOT be violated by any future extension:

1. **IDs are immutable.** Once a `concept_id` or `verb_id` is assigned to an entry, that assignment is permanent. IDs are never reused, even if the entry is deprecated.
2. **The dictionary is append-only.** New entries are added; existing entries are annotated. No entry is deleted.
3. **Annotations are immutable.** Once an annotation is recorded against an ID, it persists across all future dictionary versions.
4. **Versioning is explicit.** Every dictionary release carries a version identifier in `YYYY-QN` format (e.g., `2026-Q1`). The `ONTOLOGY_VERSION` field in `DOC_START` (§11.3) MUST reference the dictionary version used to compile the stream.

The rationale for permanent IDs is architectural, not merely pragmatic: BBP streams may represent documents from any historical period. A stream encoding a 12th-century legal text, a 19th-century scientific paper, or a legacy inter-agent exchange must remain interpretable indefinitely. Deleting an ID makes older streams permanently opaque — the worst possible outcome for a protocol intended as durable infrastructure. This design mirrors Unicode, where no codepoint has ever been deleted in the standard's history.

#### 13.6.2 Phase 1 — Bootstrapping from Existing NLP Resources

The v1.0 dictionary seed is compiled from three established, openly licensed lexical-semantic resources:

| Resource | Coverage | Role in BBP seed |
|---|---|---|
| **BabelNet** | 500+ languages, 20M+ synsets, integrates WordNet + Wikipedia | Primary source for nominal concepts (DICT_WORD, NAME_* meta-types). Provides cross-linguistic anchorage. |
| **VerbNet** | ~8,000 English verbs with syntactic alternations | Source for `verb_id` inventory. Aktionsart and `valency_class` inferred from VerbNet verb class membership. |
| **FrameNet** | ~1,200 semantic frames with argument roles | Cross-validates verb frame structure against UAT case system. Flags mismatches for human review. |

**ID assignment order.** Within each meta-type partition, IDs are assigned sequentially in descending order of cross-linguistic frequency. The proxy metric is frequency rank in the Universal Dependencies treebank collection for verbal and abstract entries, and cross-linguistic occurrence frequency for nominal entries. Lower IDs are thus the most universally frequent concepts.

**Known bias of the seed resources.** BabelNet, VerbNet, and FrameNet have documented coverage gaps: strong for European languages and nominal concepts, weak for verbal and abstract lexicon across low-resource languages. Implementers MUST run a **bias audit** before publishing the v1.0 dictionary seed. The bias audit report is a REQUIRED artefact of the v1.0 release.

**Scope of the bias audit.** The audit does NOT measure lexical coverage by language family (i.e., what fraction of words in language X have a dictionary entry). Lexical gaps are not dictionary gaps: a concept expressible as an existing lemma + FEATURES combination is fully covered regardless of whether the source language lexicalises it in a single word. The audit MUST measure exclusively the presence of **irreducible conceptual primitives** absent from the seed — concepts where (1) no existing lemma serves as a semantic root and (2) no combination of FEATURES over existing lemmas approximates the meaning. Such gaps are structurally rare, because most human concepts across languages share semantic roots already present in the seed resources. The audit MUST document each identified gap with: the concept, the language(s) where it is primitive, the reason no existing lemma + FEATURES combination suffices, and the proposed new entry or remediation plan.

**Format vs. pipeline coverage distinction.** The bias audit MUST explicitly distinguish two claims that are not equivalent: (1) the BBP token format can represent morphosyntactic structures across ~7,000 known languages; (2) the validated pipeline (Frontier Translator + training corpus) covers the languages for which sufficient written corpus exists (~20–50 languages at v1.0). Languages outside class (2) have format coverage but not pipeline coverage. The audit MUST list which languages fall in each class at the time of the v1.0 release.

**Scope of seed resources (normative).** BabelNet, VerbNet, and FrameNet are exclusively the Phase 1 seed. They do not constitute the primary source of the production dictionary. The dominant dictionary growth mechanism after Phase 1 is the Compiler-driven entry creation described in §13.6.3, which operates mechanically during Frontier Translator training cycles without reference to any external resource. Implementers MUST NOT treat the absence of a BabelNet/VerbNet/FrameNet entry as evidence that a concept is absent from the BBP dictionary.

#### 13.6.3 Phase 2 — Automatic Growth via Compiler and Dictionary Proposal Pipeline

**The role of LITERAL.** `LITERAL` blocks are exclusively for **permanent rigid designators**: brand names, product identifiers, model numbers, technical identifiers, proper names of concrete entities (e.g., "Raid", "Boeing", "747", "iPhone"). These are not generalisable concepts and MUST NOT receive dictionary entries. The decision of whether a token is a lemma or a LITERAL is made by the **Teacher LLM** when generating BBP-IR training examples. The Frontier LLM learns this distinction through supervised training and correction. `LITERAL` is not a transitional state: it does not represent concepts awaiting promotion. Concepts that belong in the dictionary are lemmas from the moment the Teacher LLM identifies them as such.

**Compiler-driven entry creation.** The BBP Compiler is a mechanical BBP-IR → binary translator. As part of its compilation pass, when it encounters a lemma in the BBP-IR that has no existing entry in the dictionary, it creates that entry automatically, assigning the next available ID within the appropriate meta-type partition. This is a purely mechanical operation: the semantic decision (lemma vs. LITERAL, meta-type assignment) was made upstream by the Teacher LLM. `LITERAL` blocks never trigger entry creation.

**Unknown Primitive Incident Log (post-training growth).** The dictionary grows primarily through supervised training cycles, not through automated promotion pipelines. During training, the Compiler creates entries mechanically as the Teacher LLM identifies new lemmas (see above). In production, a Frontier Translator trained on a given dictionary version will emit `LITERAL` for any concept it cannot map to a known lemma. Since LITERAL is reserved for permanent rigid designators, any LITERAL emission that does not correspond to a rigid designator is an **unknown primitive incident**: a signal that a genuine irreducible conceptual primitive may be absent from the dictionary.

When a Frontier Translator emits a LITERAL that it classifies as a potential unknown primitive (i.e., not a rigid designator), it MUST append a record to the **Unknown Primitive Incident Log**:

```json
{
  "value": "string value of the LITERAL",
  "lang": "ISO 639-3 code of the source language",
  "meta_type": "meta-type inferred by the Frontier Translator",
  "context_ir": "surrounding BBP-IR snippet (anonymised if necessary)",
  "timestamp": "ISO 8601"
}
```

The incident log is reviewed at each training cycle. Entries that survive human review as genuine irreducible primitives are added to the Teacher LLM's training corpus for the next iteration, causing the Compiler to create the corresponding dictionary entries during that training run. Entries that are found to be decomposable via existing lemma + FEATURES combinations are used as negative examples: the Frontier Translator is trained to emit the correct compositional encoding instead of LITERAL.

There is no automated promotion pipeline, no clustering stage, and no scoring formula. Dictionary growth is a supervised process anchored in training cycles, not an autonomous service.

#### 13.6.4 ID Annotation System

The following annotations MAY be applied to existing IDs at any dictionary release. All annotations are immutable once recorded and accumulate across versions:

| Annotation | Semantics | Frontier LLM behaviour |
|---|---|---|
| `DEPRECATED` | Entry exists but SHOULD NOT be used in new streams. | Learns to prefer the canonical alternative. |
| `ALIAS(Y)` | This ID is semantically equivalent to ID Y. Y is the canonical entry. | Prefers Y in new streams; both remain valid for decoding. |
| `CANONICAL(Y)` | This ID was a duplicate of Y, discovered post-assignment. | As ALIAS. |
| `ERRATA(Y)` | This ID had an incorrect or ambiguous definition; Y is the corrected entry. | As ALIAS. Uses Y for new streams; streams using this ID are interpretable with documented caveat. |
| `HISTORICAL` | This ID represents an entity or concept that no longer exists in the current state of the world, but is valid in historical, literary, or archival context. | No change to encoding behaviour. Informative only. |

There is no deletion annotation. The dictionary does not shrink. A `DEPRECATED` ID with an `ALIAS` annotation is the closest operational equivalent to retirement: the concept remains decodable indefinitely, while production systems learn to avoid producing it.

#### 13.6.5 Publication Cycle

The BBP dictionary is published on a **fixed quarterly schedule** (Q1: January, Q2: April, Q3: July, Q4: October), independently of the Frontier LLM release cycle. This decoupling ensures that the dictionary — infrastructure-layer artefact — has a predictable, stable cadence that implementers can plan against.

Each quarterly release consists of:

1. **New ID assignments** from entries created during the most recent training cycle.
2. **New annotations** on existing IDs (DEPRECATED, ALIAS, ERRATA, HISTORICAL).
3. **Updated cross-reference table** (BabelNet synset IDs, WordNet offsets, UD treebank frequency ranks).
4. **Release notes** documenting every change, with rationale.

The release is published as an open-source artefact (structured data file + human-readable changelog) freely downloadable by any implementer. The dictionary is a public good, not a proprietary asset.

**Frontier LLM synchronisation rule.** Every Frontier LLM release MUST declare in its model metadata the dictionary version on which it was trained (`trained_on_dict_version: YYYY-QN`). A Frontier LLM trained on dict-v2026-Q1 does not know IDs introduced in dict-v2026-Q2; it will continue emitting LITERALs for those concepts until retrained. This is expected and controlled degradation — not a protocol error. The `ONTOLOGY_VERSION` field in every stream's `DOC_START` records which dictionary version was active at encoding time, enabling decoders to resolve IDs correctly regardless of their own dictionary version.

**Core Engine compatibility.** The Core Engine is a trained neural model whose embedding space is anchored to the dictionary version used during training. IDs introduced in dictionary versions subsequent to training are unknown to the Core Engine and will receive incorrect embeddings if encountered. Implementers MUST ensure that the Core Engine's training dictionary version is ≤ the Frontier LLM's training dictionary version. Cross-version streams (where the stream's `ONTOLOGY_VERSION` is later than the Core Engine's training version) MUST be flagged at decode time. When an unknown ID is encountered, the Core Engine MUST treat it as a `LITERAL` block carrying the numeric ID as its raw value, and MUST NOT map it to any known ID. This ensures that unknown concepts degrade to transparent opacity (the concept is present but uninterpreted) rather than silent misinterpretation (the concept is silently replaced by a different known concept). Two agents running Core Engines trained on different dictionary versions communicate reliably for the shared subset of their dictionaries; concepts outside that subset are exchanged as LITERAL values and flagged in the decode log.

#### 13.6.6 Lexicalization Index

> **See §5.3** for the normative data structure, canonical modifier ordering (V-MOD-ORDER), entry threshold criteria, and Renderer integration algorithm. This section specifies the Frontier Translator usage, the candidate index mechanism, the full NL→BBP decision hierarchy, and publication cadence. §13.6.6 and §5.3 are complementary and must be read together.

The lexicalization index is a per-language table, published as part of the quarterly dictionary release, that maps frequent multi-token BBP patterns to their lexicalized surface forms in each language. It solves a fundamental asymmetry: BBP's compositionality principle encourages expressing complex concepts as structured token sequences, but many natural languages lexicalize common compositions into single words. Without an index, a Renderer generating natural language from BBP would produce compositionally accurate but idiomatically unnatural output.

**The problem in concrete terms.** The BBP stream for "una bebé" is:

```
[LIVING_ENTITY, Homo sapiens, SG, INDEF, ALIVE] + [ADJECTIVE, INFANT] + [ADJECTIVE, BIOLOGICAL_FEMALE]
```

A Renderer without the index would produce "an infant female human being" — ontologically precise, communicatively unnatural. The index maps this pattern to "bebé" (ES), "baby girl" (EN), "bébé" (FR), "haurtxoa" (EU).

**Structure of the index.** Each entry maps a normalized BBP pattern to per-language surface forms:

```json
{
  "pattern": [
    {"meta_type": "LIVING_ENTITY", "taxon_id": 9606},
    {"meta_type": "ADJECTIVE", "concept": "INFANT"},
    {"meta_type": "ADJECTIVE", "concept": "BIOLOGICAL_FEMALE"}
  ],
  "forms": {
    "es": {"surface": "bebé", "gender": "FEMININE", "notes": "gender-invariant form"},
    "en": {"surface": "baby girl"},
    "fr": {"surface": "bébé", "gender": "MASCULINE"},
    "eu": {"surface": "haurtxoa"}
  }
}
```

Patterns are matched against the token sequence in the order listed. The match is successful if all pattern tokens appear contiguously (allowing intervening flow control tokens). Grammar fields (case, number, determiner) are NOT part of the pattern — they are applied to the lexicalized form after selection via standard inflection rules.

**Renderer decision hierarchy.** When generating natural language from a BBP token sequence, the Renderer MUST follow this priority order:

```
1. Consult lexicalization index for the current target language.
   If a pattern matches → use the lexicalized form with appropriate inflection.

2. Consult dictionary for a single synset-level entry covering the token.
   If found → use that entry with appropriate inflection.

3. Compositional generation: translate each token independently
   and construct the phrase following target-language grammar rules.
```

**Automatic growth from corpus.** The lexicalization index grows automatically during Frontier Translator training. When the Teacher LLM generates a BBP-IR annotation for a natural language sentence containing a lexicalized form, it MUST annotate the mapping between the multi-token BBP pattern and the source surface form. The Compiler extracts these annotations and adds entries to the lexicalization index, subject to a minimum frequency threshold (proposed: ≥ 50 occurrences per language per dictionary release cycle).

Entries below the frequency threshold are stored in a **candidate index** for human review before promotion. This prevents noise from idiosyncratic Teacher LLM choices from polluting the normative index.

**Bidirectionality.** The lexicalization index is used in both directions:

- **BBP → NL (Renderer):** pattern matching selects the lexicalized form.
- **NL → BBP (Frontier Translator):** when the Translator encounters a lexicalized form in the source language, the index guides it to emit the correct multi-token BBP composition rather than searching for a single dictionary entry. A Translator that sees "bebé" in Spanish and emits a single DICT_WORD token for "bebé" is incorrect; the index tells it to emit LIVING_ENTITY(9606) + ADJECTIVE(INFANT) + ADJECTIVE(BIOLOGICAL_FEMALE).

**Frontier Translator decision hierarchy — NL→BBP direction (normative).** When mapping a source-language lexeme or phrase to BBP, the Frontier Translator MUST apply the following priority order:

```
1. DICTIONARY LOOKUP
   Does the lexeme have a direct entry in the BBP dictionary?
   YES → emit the corresponding meta-type token (DICT_WORD, NAME_*, SCI_*, etc.)
        with concept_id from the dictionary.
   NO  → proceed to step 2.

2. LEXICALIZATION INDEX LOOKUP
   Does the lexeme appear in the lexicalization index for the source language?
   YES → emit the multi-token BBP composition specified by the index entry.
   NO  → proceed to step 3.

3. COMPOSITIONAL DECOMPOSITION
   Can the lexeme be decomposed into a sequence of BBP tokens whose combination
   expresses the same meaning? (compound nouns, derivational morphology,
   periphrastic constructions)
   YES → emit the compositional token sequence. Tag the mapping as a CANDIDATE
        for the lexicalization index if the frequency threshold is met.
   NO  → proceed to step 4.

4. LITERAL BLOCK + DISAMBIGUATION
   Emit a LITERAL block containing the raw string, paired with a DISAMBIGUATION
   token. The Compiler MUST extract this as a dictionary proposal (§13.6.3).
   LITERAL blocks in the production stream MUST be minimized; a high
   out-of-dictionary rate on the evaluation set is a failure metric (§15.9).
```

**Normative constraint.** Steps 1–3 MUST be attempted in order. A Frontier Translator that emits a LITERAL block for a concept with a dictionary entry is non-conformant. A Frontier Translator that emits DICT_WORD when a richer compositional representation exists is suboptimal but not CRITICAL — however, training MUST incentivize the compositional form where it exists.

**Publication cadence.** The lexicalization index is versioned and published on the same quarterly schedule as the dictionary. The `ONTOLOGY_VERSION` field in DOC_START covers both the dictionary and the lexicalization index version.

---

## 14. BBP-IR — JSON Intermediate Representation

### 14.1 Entity Object

```json
{
  "type": "ENTITY",
  "meta_type": "DICT_WORD | PRONOUN | ADJECTIVE | NUM_NATURAL | ...",
  "value": "string_lemma | number",
  "flow": "UNIT | START | END | LITERAL",
  "byte_length": null,
  "declension": {
    "case": "ABS | ERG | DAT | GEN_POSS | GEN_LOC | COM | INS | INE | ALL | ABL | DEST | PART | PROL | MOT | ALL_TERM | ALL_DIR | TOP | ILL | ELA | SUP | SUB | DEL | ADE | VOC | ESS | TRANS | SEM | ABE | UNMARKED",
    "number": "UNMARKED | SINGULAR | DUAL | TRIAL | PAUCAL | PLURAL | COLLECTIVE | DISTRIBUTIVE",
    "determiner": "NONE | DEF | INDEF | PROX | MED | DIST | INTER | UNIV | EXIST | NEG | PLACEHOLDER"
  },
  "reference_target": null,
  "placeholder_id": null,
  "raw_content": null
}
```

- `byte_length`: Only for flow=LITERAL. Number of raw bytes.
- `raw_content`: Only for flow=LITERAL. Base64-encoded raw bytes (or UTF-8 string for text).
- `reference_target`: Only for meta_type=REFERENCE. 0-based logical token index.
- `placeholder_id`: Only for determiner=PLACEHOLDER. Local placeholder ID (0-2047).

**TYPE_EXPR and TYPE_PARAM encoding in BBP-IR.** These meta-types use the standard Entity Object schema with no new fields. The `meta_type` field carries the string `"TYPE_EXPR"` or `"TYPE_PARAM"`. Example:

```json
{ "type": "ENTITY", "meta_type": "TYPE_EXPR", "flow": "START" }
{ "type": "ENTITY", "meta_type": "TYPE_PARAM", "flow": "UNIT",
  "declension": { "case": "UNMARKED", "number": "SINGULAR", "determiner": "NONE" } }
```

### 14.2 Action Object

```json
{
  "type": "ACTION",
  "verb_lemma": "string_infinitive",
  "morphology": {
    "system": "NOR | NOR_NORK | NOR_NORI | NOR_NORI_NORK",
    "polarity": "AFF | NEG",
    "aspect": "PERF | IMPERF",
    "voice": "ACTIVE | PASSIVE | MIDDLE | CAUSATIVE",
    "mood": "IND | SUB | POT | IMP",
    "tense": "PRES | PAST | FUT | HYPO",
    "arguments": {
      "subject": "NULL | NI | HI | HURA | GU | ZU | ZUEK | HAIEK",
      "object": "NULL | NI | HI | HURA | GU | ZU | ZUEK | HAIEK",
      "indirect_object": "NULL | NI | HI | HURA | GU | ZU | ZUEK | HAIEK"
    }
  },
  "bound_modifiers": {
    "evidential": null,
    "fine_aspect": null
  }
}
```

**`bound_modifiers.evidential`** (optional):

```json
{
  "value": "EV_UNSPECIFIED | EV_DIRECT_VISUAL | EV_DIRECT_SENSORY | EV_DIRECT_PARTICIPATORY |
            EV_INFER_RESULT | EV_INFER_REASONING | EV_INFER_ASSUMPTION |
            EV_REPORT_GENERAL | EV_REPORT_SPECIFIC | EV_REPORT_WRITTEN |
            EV_QUOTE_DIRECT | EV_COMMON_KNOWLEDGE | EV_TRADITIONAL |
            EV_MIRATIVE | EV_DREAM_VISION | EV_OTHER_LITERAL"
}
```

The `value` field maps directly to the 16-value normative evidentiality inventory defined in §3.8. If `value` is `EV_OTHER_LITERAL`, the Compiler emits an EVIDENTIAL token followed by a LITERAL block containing the raw evidential marker (per Axiom H_EXT3).

**`bound_modifiers.fine_aspect`** (optional):

```json
{
  "fine_aspect": "EXPERIENTIAL | INCOATIVE | CESSATIVE | ITERATIVE | SEMELFACTIVE | RESULTATIVE | PROSPECTIVE | CONTINUATIVE | HABITUAL"
}
```

**Compiler mapping for NOR-NORI-NORK:** The Compiler maps `arguments` to the pair table. If `object` is not HURA or HAIEK, uses index 14 (EXTENDED).

**Compiler mapping for bound_modifiers:** If `evidential` is non-null, the Compiler emits an EVIDENTIAL Entity Token immediately before the Action Token. If `fine_aspect` is non-null, the Compiler emits a TEMPORAL ASPECT_FINE Entity Token immediately before the EVIDENTIAL (or before the Action Token if no EVIDENTIAL).

### 14.3 Full Example

**Input:** "Because the temperature exceeded 100, the system did not activate the alert."

**BBP-IR Output:**

```json
[
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "value": "condition", "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "DICT_WORD", "value": "temperature",
    "flow": "UNIT",
    "declension": { "case": "ERG", "number": "SINGULAR", "determiner": "DEF" }
  },
  {
    "type": "ENTITY", "meta_type": "MATH_OPERATOR", "value": ">",
    "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "NUM_NATURAL", "value": 100,
    "flow": "UNIT",
    "declension": { "case": "ABS", "number": "SINGULAR", "determiner": "NONE" }
  },
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "value": "condition", "flow": "END",
    "declension": { "case": "MOT", "number": "SINGULAR", "determiner": "NONE" }
  },
  {
    "type": "ENTITY", "meta_type": "DICT_WORD", "value": "system",
    "flow": "UNIT",
    "declension": { "case": "ERG", "number": "SINGULAR", "determiner": "DEF" }
  },
  {
    "type": "ENTITY", "meta_type": "DICT_WORD", "value": "alert",
    "flow": "UNIT",
    "declension": { "case": "ABS", "number": "SINGULAR", "determiner": "DEF" }
  },
  {
    "type": "ACTION", "verb_lemma": "activate",
    "morphology": {
      "system": "NOR_NORK", "polarity": "NEG", "aspect": "PERF",
      "voice": "ACTIVE", "mood": "IND", "tense": "PAST",
      "arguments": {
        "subject": "HURA", "object": "HURA", "indirect_object": "NULL"
      }
    },
    "bound_modifiers": { "evidential": null, "fine_aspect": null }
  }
]
```

**Compiled tokens (assuming dict IDs: temperature=201, >=5, system=202, alert=203, activate=verb 100):**

Each token is 64 bits: `[LEMMA(32)][FEATURES(32)]`.

```
Token 0: MATH_EXPRESSION(0o23=19) START
  LEMMA:    (0<<31)|(19<<25)|0  = 0x26000000   discriminator=0, meta=MATH_EXPRESSION, concept=0
  FEATURES: (1<<30)|0           = 0x40000000   flow=START
  → [0x26000000][0x40000000]

Token 1: DICT_WORD(0) UNIT, concept=temperature(201), ERG, SINGULAR, DEF
  LEMMA:    (0<<31)|(0<<25)|201 = 0x000000C9
  FEATURES: (0<<30)|(2<<25)|(1<<22)|(1<<18) = 0x04440000
                                               flow=UNIT, case=ERG(2), num=SINGULAR(1), det=DEF(1)
  → [0x000000C9][0x04440000]

Token 2: MATH_OPERATOR(0o22=18) UNIT, concept=5 (operator ">")
  LEMMA:    (0<<31)|(18<<25)|5  = 0x24000005
  FEATURES: 0x00000000          flow=UNIT, no grammar
  → [0x24000005][0x00000000]

Token 3: NUM_NATURAL(0o10=8) UNIT, value=100, ABS, SINGULAR, NONE
  LEMMA:    (0<<31)|(8<<25)|100 = 0x10000064
  FEATURES: (0<<30)|(1<<25)|(1<<22)|(0<<18) = 0x02400000
                                               flow=UNIT, case=ABS(1), num=SINGULAR, det=NONE
  → [0x10000064][0x02400000]

Token 4: MATH_EXPRESSION(19) END, MOT, SINGULAR, NONE
  LEMMA:    (0<<31)|(19<<25)|0  = 0x26000000
  FEATURES: (2<<30)|(0x0E<<25)|(1<<22)|(0<<18) = 0x9C400000
                                                   flow=END, case=MOT(0x0E), num=SINGULAR, det=NONE
  → [0x26000000][0x9C400000]

Token 5: DICT_WORD(0) UNIT, concept=system(202), ERG, SINGULAR, DEF
  LEMMA:    (0<<31)|(0<<25)|202 = 0x000000CA
  FEATURES: 0x04440000           flow=UNIT, case=ERG, num=SINGULAR, det=DEF
  → [0x000000CA][0x04440000]

Token 6: DICT_WORD(0) UNIT, concept=alert(203), ABS, SINGULAR, DEF
  LEMMA:    (0<<31)|(0<<25)|203 = 0x000000CB
  FEATURES: (0<<30)|(1<<25)|(1<<22)|(1<<18) = 0x02440000
                                               flow=UNIT, case=ABS, num=SINGULAR, det=DEF
  → [0x000000CB][0x02440000]

Token 7: Action Token, verb=activate(100), NOR-NORK, NEG, PERF, ACTIVE, IND, PAST, S=HURA(3), O=HURA(3)
  LEMMA:    (1<<31)|(ACHIEVEMENT<<28)|(TRANSITIVE<<25)|100
            = 0x80000000|(2<<28)|(2<<25)|0x64
            = 0x80000000|0x20000000|0x04000000|0x64
            = 0xA4000064
            (Aktionsart=ACHIEVEMENT: punctual change of state; valency=TRANSITIVE)
  FEATURES: (0<<31)|(1<<28)|(1<<27)|(0<<24)|(0<<22)|(0<<19)|(0<<17)|(1<<15)|(3<<12)|(3<<9)
            = 0x10000000|0x08000000|0x00008000|0x00003000|0x00000600
            = 0x1800B600
            (reserved=0, system=NOR-NORK(1), polarity=NEG(1), aspect=PERF(0),
             voice=ACTIVE(0), mood=IND(0), tense=PAST(1), subj=HURA(3), obj=HURA(3))
  → [0xA4000064][0x1800B600]
```

**Bit-level decomposition of Token 7 FEATURES:**

```
0x1800B600
Bits: 0_001_1_000_00_000_00_01_011_011_000_0000_00
      │ │   │ │   │  │   │  │  │   │   │   │    └clusivity=0
      │ │   │ │   │  │   │  │  │   │   │   └evidentiality=0
      │ │   │ │   │  │   │  │  │   │   └indirect=NULL(0)
      │ │   │ │   │  │   │  │  │   └object=HURA(3)
      │ │   │ │   │  │   │  │  └subject=HURA(3)
      │ │   │ │   │  │   │  └tense=PAST(01)
      │ │   │ │   │  │   └mood=IND(00)
      │ │   │ │   │  └voice_ext=0
      │ │   │ │   └voice=ACTIVE(00)
      │ │   │ └aspect=PERF(000)
      │ │   └polarity=NEG(1)
      │ └system=NOR-NORK(001)
      └reserved=0
```

**Total: 8 tokens × 8 bytes = 64 bytes.** Original sentence ≈ 65 bytes UTF-8, ~12 BPE tokens. Attention cost: O(144) → O(64).

---

## 15. Implementation Roadmap

The implementation follows a **compiler bootstrapping** strategy: a world-class LLM serves as the Universal Teacher to generate a high-quality multilingual BBP corpus, which then trains the BBP-native Core Engine. Once the Core Engine exists, lightweight Frontier Translators are distilled from the corpus for production use. The Universal Teacher is a bootstrapping tool, not a permanent architecture component.

### 15.1 Guiding Principles

**Quality over breadth in early phases.** A smaller corpus with near-perfect structural and semantic accuracy is vastly more valuable than a large corpus with systematic errors. Errors in the training corpus propagate as biases in the Core Engine that are expensive to correct later.

**Typological coverage from day one.** The BBP feature space (ergativity, evidentiality, fine aspect, dual number, topic-comment, serial verbs) cannot be exercised by any single language. The initial corpus MUST span a typologically diverse set of languages so that the Core Engine learns all BBP features as natural, frequent phenomena — not as rare edge cases.

**Single Teacher with Cross-Validation.** A primary Universal Teacher LLM (with a comprehensive system prompt containing the full BBP specification) generates corpus for all languages. This guarantees that the same analytical criteria are applied uniformly across languages. However, a secondary Teacher (a different LLM) MUST independently process the human-reviewed test set (Section 15.4.1) to detect systematic biases. Divergences between the two Teachers are analyzed and resolved by human linguists, with decisions recorded in the **BBP Linguistic Conventions** companion document (a normative artefact separate from this specification that codifies all contested interpretive decisions).

**Validation is non-negotiable.** Every generated BBP-IR instance passes through the Axiomatic Validator (Section 13.2.1) before entering the corpus. Instances that fail CRITICAL axioms are discarded. Instances that trigger MAJOR warnings are flagged for review. Round-trip validation (NL → BBP-IR → binary → BBP-IR → NL) provides an additional semantic fidelity check.

### 15.2 Typological Core — The Seven Languages

The initial corpus spans seven languages selected to maximize coverage of BBP's feature space. Each language is chosen because it exercises specific BBP features as obligatory, natural grammar — not as theoretical possibilities.

| Language | Primary BBP features exercised | Corpus source candidates |
|----------|-------------------------------|--------------------------|
| **Spanish** | NOR/NOR-NORK/NOR-NORI-NORK (ditransitives with *le/lo/se*), subjunctive mood, perfective/imperfective aspect, middle/reflexive voice (*se rompió*), negation scope (*no todos* vs *todos no*), light verbs (*dar un paseo*) | Wikipedia ES, OpenSubtitles, UD AnCora, news corpora |
| **Basque** | Native ergativity (all four verb paradigms), agglutinative morphology, allocutive/hitano, SOV word order, Basque auxiliary system (the direct inspiration for the Basque Engine) | Wikipedia EU, UD BDT, Euskal Herriko Unibertsitatea corpora, EiTB media |
| **Turkish** | Obligatory evidentiality (*-mış* vs *-dı*), rich agglutination, causative/passive/middle voice chains, aorist habitual aspect, SOV order | Wikipedia TR, UD BOUN, OpenSubtitles TR, TDD corpora |
| **Mandarin Chinese** | Tenseless encoding (tense always PRESENT), aspect as primary verbal axis, fine aspect particles (了/过/着/起来 → ASPECT_FINE), serial verb constructions, classifier system, topic-comment tendencies | Wikipedia ZH, UD Chinese-GSD, OpenSubtitles ZH, news corpora |
| **Japanese** | Topic-comment (は → TOPICAL), explicit case particles (が/を/に → ABS-ERG/ABS/DAT), honorific register levels, causative-passive compounds, SOV order | Wikipedia JA, UD GSD, OpenSubtitles JA, NHK corpora |
| **Arabic** | Obligatory dual number, perfective/imperfective as primary verbal axis, triconsonantal root system (dictionary validation), VSO/SVO flexibility, broken plural patterns | Wikipedia AR, UD PADT, OpenSubtitles AR, Al Jazeera corpora |
| **English** | Baseline nominative-accusative → ergative mapping validation, passive voice frequency, phrasal verbs, complex noun phrases, relative clause nesting, the world's largest available corpus for volume | Wikipedia EN, UD EWT, OpenSubtitles EN, BNC, news corpora |

**Feature coverage matrix:**

```
Feature                  ES  EU  TR  ZH  JA  AR  EN
─────────────────────────────────────────────────────
NOR (intransitive)       ✓   ✓   ✓   ✓   ✓   ✓   ✓
NOR-NORK (transitive)    ✓   ✓   ✓   ✓   ✓   ✓   ✓
NOR-NORI (intr+dat)      ✓   ✓   ✓   ~   ✓   ✓   ✓
NOR-NORI-NORK (ditrans)  ✓   ✓   ✓   ~   ✓   ✓   ✓
Subjunctive              ✓   ✓   ~   —   —   ✓   ~
Potential mood           ~   ✓   ✓   ~   ✓   —   ✓
Imperative               ✓   ✓   ✓   ✓   ✓   ✓   ✓
Perfective/Imperfective  ✓   ✓   ✓   ✓   ✓   ✓   ✓
ASPECT_FINE              ~   ~   ✓   ✓   ✓   ~   ~
EVIDENTIAL               —   —   ✓   —   —   —   —
TOPICAL case             —   —   ~   ~   ✓   —   —
DUAL number              —   —   —   —   —   ✓   —
Middle/Reflexive voice   ✓   ✓   ✓   —   ~   ✓   —
Causative voice          ~   ✓   ✓   ~   ✓   —   —
Passive voice            ✓   ✓   ✓   ✓   ✓   ✓   ✓
Light verbs              ✓   ✓   ✓   ✓   ✓   —   ✓
Serial verb constr.      —   —   —   ✓   ~   —   —
Nominative→Ergative map  ✓   —   ✓   ✓   ✓   ✓   ✓
LITERAL blocks (names)   ✓   ✓   ✓   ✓   ✓   ✓   ✓
ILL / ELA / SUP / SUB    —   —   —   —   —   —   —   (Phase 1.5 add: Finnish)
DEL / ADE                —   —   —   —   —   —   —   (Phase 1.5 add: Finnish)
VOC                      ~   —   ~   —   —   ✓   ~
ESS / TRANS              —   —   —   —   —   —   —   (Phase 1.5 add: Finnish)
SEM                      ~   ✓   ✓   ~   ~   ~   ~
ABE                      —   ✓   —   —   —   —   —   (Basque: gabe postposition)

✓ = obligatory/natural  ~ = possible/marginal  — = absent

Note: Phase 1.5 SHOULD add Finnish as an 8th typological core language to provide
obligatory coverage of Finno-Ugric case features (ILL, ELA, SUP, SUB, DEL, ADE,
ESS, TRANS, ABE). Finnish exercises 9 of the 11 new cases as obligatory morphology.
```

No single language covers every feature. The seven together cover all defined BBP features with at least one language where the feature is obligatory natural grammar.

### 15.3 Phase 1 — Protocol Foundation (Months 1-3)

**Objective:** A fully functional, tested BBP toolchain that can compile, validate, and decompile BBP streams.

#### 15.3.0 Conformance Test Suite — Minimum Feature Coverage

Before declaring v1.0-final, the Conformance Test Suite (CTS) MUST contain fixtures covering the following 12 features for all 7 typological core languages (Section 15.2). Each feature MUST have a minimum of 2 test cases per language.

| # | Feature | Key sections | Minimum per language | Languages where obligatory |
|---|---------|-------------|---------------------|---------------------------|
| 1 | Negation (narrow + wide scope) | §6.4 | 2 | All 7 |
| 2 | TAM basic (4+ tense×aspect combinations) | §3.3.2–3.3.6 | 4 | All 7 |

> **Note on TAM minimum:** The TAM feature requires 4 cases (not 2) because the 2-bit tense × 1-bit aspect fields yield 8 core combinations, and meaningful coverage requires exercising at least one combination from each quadrant of the tense×aspect matrix (e.g., PRES+PERF, PRES+IMPERF, PAST+PERF, FUT+IMPERF). Testing only 2 would leave entire tense or aspect values unexercised.
| 3 | Transitivity alternation (NOR vs NOR-NORK, same entity) | §3.3.1, §12.1 | 2 | All 7 |
| 4 | Ditransitive (pair table + EXTENDED mode) | §3.3.7 | 2 | ES, EU, TR, JA, AR, EN |
| 5 | Local coreference (REFERENCE backpointer) | §8.1–8.2 | 2 | All 7 |
| 6 | Subordination (one embedded clause minimum) | §7.2 | 2 | All 7 |
| 7 | Numbers (inline + LITERAL block) | §10.1–10.2 | 2 | All 7 |
| 8 | Proper names (LITERAL block + coreference) | §4.3, §8.1 | 2 | All 7 |
| 10 | Evidentiality (EVIDENTIAL native verb feature field) | §6.7.2 | 2 | TR (mandatory), others optional |
| 11 | Fine aspect (TEMPORAL ASPECT_FINE) | §6.7.3 | 2 | ZH (mandatory), others optional |
| 12 | TOPICAL case | §4.5.1 (0x11), §12.3 | 2 | JA (mandatory), others optional |

**CTS directory structure:**

```
cts/
├── manifest.yaml                    # Feature manifest (this table as machine-readable)
├── es/                              # Spanish
│   ├── negation/
│   │   ├── narrow_scope_001.bbpir   # BBP-IR (JSON)
│   │   ├── narrow_scope_001.bbpb    # Expected binary (golden)
│   │   ├── narrow_scope_001.meta.json
│   │   └── ...
│   ├── tam_basic/
│   ├── transitivity/
│   └── ...
├── eu/                              # Basque
├── tr/                              # Turkish
├── zh/                              # Mandarin
├── ja/                              # Japanese
├── ar/                              # Arabic
├── en/                              # English
└── lite_degradation/                # Cross-language degradation tests (§2.8.3)
    ├── ext_fallback_001.bbpir
    ├── literal_split_001.bbpir
    ├── unknown_hash_001.bbpir
    └── ...
```

**Metadata format** (`*.meta.json`):

```json
{
  "feature": "negation",
  "sub_feature": "narrow_scope",
  "profile": "Full",
  "ontology_version": "1.0",
  "source_language": "es",
  "natural_language": "Juan no comió pan",
  "notes": "Narrow-scope verb negation; UAT polarity=NEG, entity determiners unchanged."
}
```

**CTS runner requirements:** The reference CTS runner MUST: (1) parse `.bbpir` files, (2) validate against the Axiomatic Validator, (3) compile to binary via the reference Compiler, (4) compare byte-for-byte against `.bbpb` golden files, (5) report per-feature, per-language pass/fail and aggregate metrics.

**Gating rule (normative):** v1.0-final MUST NOT be declared until CTS Sprint 1 passes on the reference implementation. The minimum fixture count is computed as: `total = Σ(min_cases_per_feature) × #languages`. With the current matrix (12 features × 7 languages × 2 cases each, except TAM which requires 4), this yields `(11 × 2 + 1 × 4) × 7 = 182` minimum fixtures. If features or languages are added, the formula scales accordingly.

**Fixture sizing tiers (informative):** The minimum counts above define the gating threshold. For planning purposes, the following tiers provide guidance on test depth:

| Tier | Cases per feature per language | Total fixtures (approx.) | Purpose |
|------|-------------------------------|--------------------------|---------|
| MVP (gating) | 2–4 | 168–336 | Minimum for declaring v1.0-final. Covers happy path and one edge case per feature. |
| Robust | 5–8 | 420–672 | Recommended for production-grade confidence. Covers edge cases, error paths, and cross-feature interactions. |
| Comprehensive | 10+ | 840+ | Long-term target. Includes adversarial inputs, performance regression baselines, and full cross-linguistic interaction coverage. |

Implementations SHOULD NOT interpret the MVP gating minimum as a target ceiling. The CTS is expected to grow incrementally beyond the gating threshold as the toolchain matures.

##### Gold Set (§15.3.0.1)

In addition to the CTS (which tests structural conformance), a Gold Set of human-reviewed annotations provides the semantic anchor against Teacher drift.

**Purpose clarification.** The Gold Set is designed for **BBP feature coverage validation**, not for exhaustive linguistic coverage. 500 sentences cannot cover all linguistic phenomena in a language, but they can cover all *BBP features* relevant to that language. The Gold Set MUST have guaranteed coverage of: every case in the case system (at least 5 instances each), every natural TAM combination for the language, every determiner value, every flow type (UNIT, START/END, LITERAL), every axiom from Groups A-F (at least one trigger per axiom), and every native verb feature field relevant to the language. With ~100 relevant features per language at ~5 instances each, 500 sentences is approximately the right order of magnitude for axiom coverage.

**Minimum for initial release:**

- 500 sentences per language in `gold/<lang>/`, each with: natural language text, BBP-IR (JSON), and reviewer notes.
- 100 sentences per language in `gold/<lang>/double_annot/` with independent annotations from two Teacher LLMs (e.g., Claude and GPT-4), plus a human-adjudicated result.
- Inter-annotator agreement metrics (per-slot accuracy) recorded and published.

**Gold Set growth targets:** 500 sentences is the minimum for initial release. Growth targets: 1,000 sentences for v1.0 release, 5,000 for v1.1, 20,000 for v2.0. Each growth phase focuses on phenomena that caused the most errors in the previous phase.

##### Silver Set (§15.3.0.2)

The Silver Set supplements the Gold Set with breadth. It consists of 50,000+ sentences per language, auto-generated by the Teacher LLM and validated by the Axiomatic Validator + round-trip testing, but NOT human-reviewed. The Silver Set's quality is measured by its agreement rate with the Gold Set on overlapping phenomena. If Silver-Gold agreement exceeds 95% per feature, the Silver Set is trustworthy for that feature. Features where agreement is < 95% are flagged for additional Gold Set coverage.

**Pipeline (pragmatic for initial phase):**

1. Teacher LLM generates BBP-IR from source text.
2. Human reviewer (Arkaitz + collaborators) corrects errors and annotates decisions.
3. For double-annotation subset: second Teacher independently generates BBP-IR; human arbitrates divergences; decisions are recorded in BBP Linguistic Conventions.

#### 15.3.1 Specification

- [ ] Finalize this specification document (address all open issues from review).
- [ ] Publish formal JSON Schema for BBP-IR (Section 14).
- [ ] Publish formal ABNF or CDDL grammar for the binary stream format.

#### 15.3.2 Core Toolchain

- [ ] Implement `bbp_validator`: BBP-IR JSON → validated BBP-IR JSON. Implements all axiom groups (A, B, C, D) from Section 13.2.1. Emits structured error/warning reports with severity levels.
- [ ] Implement `bbp_compiler`: validated BBP-IR JSON → binary stream. Deterministic bit packing. Handles LITERAL padding, direct lemma ID emission, PLACEHOLDER resolution, native verb feature field ordering.
- [ ] Implement `bbp_decoder`: binary stream → BBP-IR JSON. Inverse of compiler. Handles SYNC recovery, direct lemma decoding, logical token indexing.
- [ ] Implement `bbp_renderer`: BBP-IR JSON → natural language text. Language-specific rendering rules (gender, morphophonology, word order) driven by dictionary metadata. Initial support: Spanish and English.
- [ ] Implement reference test suite: all Appendix D vectors as automated tests. Compiler and decoder MUST pass all vectors.

- [ ] Implement BBP-AST normalizers for at least three source languages: Python (via `ast` module), JavaScript (via `acorn`), and Zig (via Zig's own AST). These are deterministic, non-LLM components.
- [ ] Implement BBP-AST Code Renderer for at least Python and Zig. Verify round-trip: source → AST → BBP-AST → AST → rendered source preserves semantics.
- [ ] Implement `bbp_validator` extensions: Axiom Groups E (Code) and F (Reasoning).
- [ ] Add Appendix E and F test vectors to automated test suite.

#### 15.3.3 Vocabulary Infrastructure

- [ ] Design vocabulary database schema (SQLite). Two-level resolution: synset level (broad, used by BBP tokens) and sense level (fine, used by Renderer). See Section 1.4.
- [ ] Populate initial vocabulary: map WordNet synsets → BBP FEATURES IDs for meta-types DICT_WORD, ADJECTIVE, ADVERB, PRONOUN. Target: 3,000 synsets covering 95% of general-purpose vocabulary.
- [ ] Populate verb root registry: map WordNet verb synsets → BBP verb root IDs (14-bit space). Target: 2,000 verb roots including all light verbs (IDs 0x0001–0x000F).
- [ ] Create BBP Language Registry: sequential numeric IDs (0-4095) mapped to ISO 639-3 codes, sorted by speaker count.

#### 15.3.4 Validation Milestone

- [ ] End-to-end pipeline test: manually authored BBP-IR (50 sentences, Spanish) → validator → compiler → decoder → renderer → Spanish text. Verify round-trip semantic fidelity by human review.
- [ ] All Appendix D test vectors pass compiler and decoder.
- [ ] Validator correctly detects all intentionally malformed test cases (one per axiom).

#### 15.3.5 Monolingual Bootstrap Strategy (English)

Before committing to a multilingual corpus, the pipeline MUST be validated end-to-end in a single language. English is the mandated bootstrap language for v1.0, for three reasons: (1) the seed dictionary resources (BabelNet/VerbNet/FrameNet) have strongest coverage for English; (2) current world-class Teacher LLMs operate with highest precision in English; (3) English corpus availability is effectively unbounded.

**Canonical lemma language.** During the bootstrap phase, all dictionary lemmas use English as the canonical form. The Compiler looks up lemmas by their English string + meta-type pair. This does not constrain later multilingual expansion: canonical lemma strings are internal identifiers, not part of the binary stream.

**Bootstrap Frontier Translator.** The first Frontier Translator trained is a monolingual English ↔ BBP-IR model. It is supervised exclusively by a world-class Teacher LLM operating on English input. Bilingual and multilingual Frontier Translators are introduced in Phase 4 (§15.6).

**Curriculum learning.** The bootstrap training corpus MUST follow a progressive complexity schedule, supervised at each stage by the Teacher LLM:

| Stage | Input complexity | Objective |
|-------|-----------------|-----------|
| 1 | Single-clause sentences, concrete nouns, basic TAM | Establish lemma ↔ ID mapping, basic case assignment |
| 2 | Multi-clause sentences, subordination, coreference | Structural nesting, REFERENCE backpointers |
| 3 | Paragraphs, discourse-level coherence, abstract concepts | DOC_START/END structure, cross-sentence reference |
| 4 | Full documents, technical and domain-specific text | Dictionary coverage saturation, LITERAL rate stabilisation |

**Dictionary growth during bootstrap.** The Compiler creates dictionary entries mechanically as new lemmas appear in Teacher-generated BBP-IR (§13.6.3). The dictionary grows in parallel with Frontier Translator training: no pre-built dictionary is required before training begins. The LITERAL rate in Teacher-generated output (excluding permanent rigid designators) is the primary indicator of dictionary maturity: it should approach zero by end of Stage 4.

### 15.4 Phase 2 — Universal Teacher and Corpus Generation (Months 3-8)

**Objective:** A high-quality, typologically diverse BBP corpus spanning seven languages, generated by a world-class Universal Teacher LLM, validated by the Axiomatic Validator, and verified by round-trip testing.

**Narrowing funnel approach.** Instead of generating 100K sentences × 7 languages simultaneously, Phase 2 adopts a depth-first strategy that produces validated intermediate results at each stage:

- **Month 3-4:** 10K sentences in Spanish, fully validated (Gold + Silver). This is the proof of concept. A 10K Spanish corpus that passes all axioms is more valuable than a 700K 7-language corpus with systematic errors.
- **Month 5-6:** Add Turkish (evidentiality stress test) and Japanese (topic-comment + case particles). 10K sentences each.
- **Month 7-8:** Scale to remaining languages (Basque, Mandarin, Arabic, English), 10K sentences each.
- **Month 9-12:** Scale corpus to 100K/language as toolchain matures and automation improves.

Each phase produces a **publishable intermediate milestone:**

| Milestone | Artifact | Blocking? |
|-----------|----------|-----------|
| Phase 1 | Specification (this document) + reference toolchain as open-source | v1.0-draft |
| Phase 1.5 | 1,000-sentence Spanish Gold Set with compiled binary streams and round-trip results | v1.0 release |
| Phase 2a | 10K-sentence Spanish corpus with measured error rates | Core Engine Stage 0 |
| Phase 2b | 10K-sentence trilingual corpus (Spanish, Turkish, Japanese) with measured error rates | Core Engine full training |
| Phase 2c | Small-scale Core Engine experiment (125M params, §15.5.5) with results | Phase 3 go/no-go |
| Phase 2d | Full corpus meeting §15.8 stopping criteria (BBP-TrC) | Core Engine full training |

**Additional r7 objectives for Phase 2:**

- [ ] Extend Universal Teacher system prompt with BBP-CoT meta-cognitive primitive instructions and examples.
- [ ] Generate BBP-CoT training corpus from FOLIO, LogiQA, GSM8K (10,000 reasoning chains minimum, validated by Axiom Group F).
- [ ] Generate BBP-AST corpus from curated open-source repositories: 50,000 functions across Python, JavaScript, Rust, Zig (mechanical conversion + LLM semantic enrichment).

#### 15.4.0 Frontier Translator Training Architecture — Inverted Ground Truth (Normative)

This section defines the normative training methodology for Frontier Translator LLMs. It supersedes any prior implicit assumption that Frontier Translators are trained by having a Teacher LLM analyze natural language text.

##### 15.4.0.1 The Core Principle: BBP-IR as Ground Truth Anchor

**The fundamental architectural decision in Frontier Translator training is the direction of ground truth.**

In a naive implementation, one might train a Frontier Translator by feeding it natural language sentences and asking it to predict the correct BBP-IR — with the "correct" BBP-IR produced by a Teacher LLM analyzing the input sentence. This approach has a fatal flaw: the Teacher's analysis of NL → BBP-IR is probabilistic, error-prone at the structural level (case misassignment, wrong evidentiality, incorrect argument roles), and its errors are structurally silent — a misassigned case bit is indistinguishable from a correct one in the binary stream. The training corpus would encode LLM analysis errors as ground truth.

BBP mandates the inverse approach: **BBP-IR is always the point of origin. Natural language is the generated artifact.**

```
                 CORRECT direction of ground truth

    ┌──────────────────────────────────────────────────────┐
    │ Combinatorial Generator                               │
    │  → produces valid (lemma, feature-config) pairs      │
    │  → correctness guaranteed by construction            │
    │  → BBP-IR is the ANCHOR                              │
    └─────────────────────────┬────────────────────────────┘
                              │  BBP-IR (ground truth)
                              ▼
    ┌──────────────────────────────────────────────────────┐
    │ World-class Teacher LLM  (e.g. Claude Opus)          │
    │  → receives BBP-IR                                   │
    │  → generates the best NL sentence that expresses it  │
    │  → filters / rejects absurd or inexpressible structs │
    │  → NL is the GENERATED ARTIFACT                      │
    └─────────────────────────┬────────────────────────────┘
                              │  (BBP-IR, NL) verified pair
                              ▼
    ┌──────────────────────────────────────────────────────┐
    │ Frontier Translator Training                          │
    │  input:  NL sentence                                 │
    │  label:  BBP-IR  ← ground truth, always correct      │
    │  learns: NL → BBP-IR  by supervised inversion        │
    └──────────────────────────────────────────────────────┘
```

The Frontier Translator is trained to map NL → BBP-IR, but the ground truth BBP-IR was never produced by analyzing NL — it was produced by construction. The model learns the analysis direction by inversion from a corpus where the structural labels are guaranteed correct. This is the same principle by which a student learns to translate into a foreign language by first studying high-quality translations produced by native speakers, rather than by having their own tentative translations graded by a fallible evaluator.

**This training pipeline is not monolithic.** It is executed as a three-phase progressive curriculum (§15.4.0.4): Phase A trains on individual tokens, starting from the simplest morphological configurations in widely-documented languages (English, Spanish) — cases so elementary that the margin for error is minimal; Phase B advances to full multi-token propositions with increasing syntactic complexity; Phase C introduces real-world text for idiomatic, discourse-level, and metaphorical coverage. The Frontier Translator is never exposed to difficult cases before it has mastered simple ones. The inverted ground truth principle applies throughout Phases A and B; Phase C introduces a supervised analysis direction under strict cross-verification conditions, but weights it lower than the structurally-anchored Phases A and B training signal to prevent analysis-mode errors from corrupting the structural priors already learned.

##### 15.4.0.2 Why This Inversion is Architecturally Sound

The asymmetry between the two directions is not merely practical — it is fundamental to the reliability properties of the system:

**BBP-IR → NL (synthesis): the easy direction.** Generating natural language from a fully specified structured representation is a task where world-class LLMs perform at near-human quality. The structure constrains the output space drastically: given `[RAIN, NOR, PAST, PERF, IND, AFF, S:3s]`, the space of valid sentences is small and well-defined. Errors in this direction are overt: the generated sentence is ungrammatical, unnatural, or obviously does not express the requested feature configuration — all detectable by a verification pass.

**NL → BBP-IR (analysis): the hard direction.** Assigning the correct case, evidentiality, and argument roles to an arbitrary natural language sentence requires disambiguation of surface ambiguity, cross-linguistic structural inference, and morphosyntactic analysis. LLMs make systematic errors in this direction (see Linguistic Challenge Registry, §12.0.1), and those errors are structurally covert — a sentence labeled with the wrong case value looks formally valid and passes the Axiomatic Validator.

By anchoring ground truth in BBP-IR and deriving NL from it, the training pipeline converts the hard direction (analysis) into a learned function trained against guaranteed-correct labels, rather than a generative process that produces the labels themselves.

##### 15.4.0.3 Absurdity Filtering and Cross-Linguistic Fallback

The combinatorial generator operates over the Cartesian product of (lemma × valid feature configurations). Some of these combinations, while grammatically valid in BBP, may have no natural expression in a given target language — or may appear semantically anomalous from a human perspective.

The Teacher LLM applies the following decision procedure for each generated (BBP-IR, language) pair:

```
1. Can this configuration be expressed naturally in language L?
   YES → generate the best possible NL sentence. Emit pair.
   NO  → goto 2.

2. Is there any language L' in the typological core (§15.2)
   where this configuration has a natural expression?
   YES → emit pair for language L', tag with source_lang=L'.
         Flag L as having no natural expression for this config;
         do not generate a forced/unnatural sentence.
   NO  → goto 3.

3. Is the configuration semantically coherent at all?
   (e.g., [RAIN, NOR-NORK, PAST, PERF, ERG=TABLE, ABS=ABSTRACT_CONCEPT])
   YES → store as rare/marked; defer to Phase C real-world corpus.
   NO  → DISCARD. Log discarded config for dictionary constraint refinement.
```

**Rationale for cross-linguistic fallback.** What appears semantically anomalous in a nominative-accusative language may be the natural expression of an ergative or evidential language. A configuration like `[FALL, NOR, PAST, IMPERF, EVIDENTIAL=REPORTED, S:1s]` ("I (reportedly) was falling") may be unmarked in Turkish but feel forced in English. The Teacher emits it for Turkish, tags it accordingly, and skips the English generation. The Frontier Translator for each language is trained only on pairs where the NL expression is natural for that language.

**Conservative discard policy.** It is always preferable to discard a (BBP-IR, NL) pair than to emit a forced, unnatural sentence. A Frontier Translator trained on unnatural NL will learn to produce unnatural translations. The combinatorial generator produces enough pairs that aggressive filtering does not create coverage gaps — it merely shifts rare configurations to the real-world Phase C corpus (§15.4.0.5).

##### 15.4.0.4 Progressive Training Curriculum

The Frontier Translator is trained in three phases of increasing complexity. Each phase uses the inverted ground truth principle throughout.

**Phase A — Token-level synthetic curriculum (Months 3-5)**

The generator produces individual UAT and UET tokens — single-token BBP-IR structures representing the minimal semantic unit. The Teacher generates a short phrase or sentence fragment that expresses that single token.

**Language sequencing.** Phase A begins with English and Spanish as the bootstrapping languages: both are morphologically simpler than Turkish, Japanese, or Arabic at the case/evidentiality level, both have large high-quality corpora, and both allow the earliest possible human validation (§15.4.4). The combinatorial generator for Phase A targets only the simplest configurations: base case/number/determiner values, no evidentiality, no extended voice, no classifier. The goal is to give the Frontier Translator a near-error-free foundation before introducing morphologically complex languages and constructions. Additional languages are introduced progressively as Phase A validation metrics stabilize: Turkish and Japanese enter in mid-Phase A, specifically to cover their obligatory features (evidentiality for Turkish, particle-to-case mapping for Japanese) at the token level; Arabic and Mandarin enter in Phase B, where multi-token propositions provide sufficient structural context for their aspect and agreement systems.

```
BBP-IR (ground truth):
  [EAT, UAT, NOR-NORK, PAST, PERF, IND, AFF, S:3s, O:3s]

Teacher generates (NL artifact):
  ES: "lo comió"          → [ate it]
  EN: "she ate it"
  TR: "yedi"              → [ate (evidential=direct, 3sg)]
  JA: "食べた"             → [ate]
```

At this phase, the Frontier Translator learns the core mapping between feature configurations and their surface realizations per language. The task is narrow and the output space is well-constrained. A model of 1-3B parameters can achieve high accuracy at this level.

**Phase B — Propositional synthetic curriculum (Months 5-7)**

The generator produces multi-token BBP-IR propositions: a full UAT with its argument UETs, including case, number, and determiner values. The Teacher generates a complete sentence.

```
BBP-IR (ground truth):
  [mesa, DICT_WORD, SUP, SINGULAR, DEF]
  [libro, DICT_WORD, ABS, SINGULAR, INDEF]
  [estar, UAT, NOR-NORK, PRES, IMPERF, IND, AFF, S:3s, O:3s]

Teacher generates (NL artifact):
  ES: "Un libro está sobre la mesa."
  EN: "A book is on the table."
  TR: "Bir kitap masanın üzerinde."
```

Complexity increases gradually: start with simple transitive/intransitive propositions, advance to ditransitives, subordinate clauses, modal and evidential constructions. Each tier is validated before the next is introduced.

**Phase C — Real-world fine-tuning: controlled reversal of ground truth direction (Months 7-9)**

After Phase B, the Frontier Translator has learned the structural core of NL ↔ BBP-IR. Phase C introduces real-world text to cover:
- Idiomatic constructions not reachable by enumeration
- Discourse-level phenomena (topic continuity, pragmatic register)
- Domain-specific vocabulary (legal, medical, technical)
- Metaphor and non-compositional meaning

**Note on the direction reversal.** This phase intentionally inverts the ground truth direction established in Phases A and B, and this is not a contradiction of the Inverted Ground Truth principle — it is its controlled extension. By this point, the Frontier Translator has a robust structural prior from Phases A and B. Phase C uses this prior as a filter: the Teacher's NL→BBP-IR analysis is cross-verified against the model's own Phase A/B-trained expectations (dual Teacher + Axiomatic Validator + human sampling), and pairs that diverge significantly from the structural priors are discarded. The result is that analysis-mode errors do not corrupt the training signal — they are rejected by a model that has already learned what correct BBP structure looks like.

For Phase C, the direction is reversed: real NL sentences are given to the Teacher in Analysis mode (NL → BBP-IR). The Teacher's output undergoes strict cross-verification (dual Teacher + Axiomatic Validator + human sampling). Only pairs with high verification confidence enter the training set. These pairs are tagged `origin: analysis` and weighted lower in the training loss to prevent analysis-mode errors from corrupting the structural priors learned in Phases A and B.

```
Phase C pair (lower weight, strictly verified):
  NL (input):   "El tiempo vuela."  ["Time flies"]
  BBP-IR (label, cross-verified):
    [tiempo, DICT_WORD, ERG, SINGULAR, DEF, META=ABSTRACT]  ← metaphorical ERG
    [volar, UAT, NOR, PRES, IMPERF, IND, AFF, S:3s]
    [DISAMBIGUATION, confidence=140, METAPHOR_FLAG=1]
```

##### 15.4.0.5 Frontier LLM Architecture and Scale

The Frontier Translator is a **small, specialized model** trained exclusively for the NL ↔ BBP-IR task for a specific language or language family. It is not a general-purpose LLM.

**Why small is sufficient.** The NL ↔ BBP-IR task has three properties that make it amenable to small specialized models:

1. **Narrow output space.** BBP-IR is a structured JSON with a finite set of field values. The model does not generate free text — it fills a typed structure. The combinatorial space is large but well-defined, and the Axiomatic Validator rejects invalid outputs at zero cost.

2. **High-quality training signal.** Phases A and B provide training pairs where the structural label is correct by construction. The model never has to learn from noisy labels. High-quality data at moderate scale outperforms noisy data at large scale for structured prediction tasks.

3. **No world-knowledge required.** The Frontier Translator does not need to reason about the world — it needs to map surface linguistic structure to BBP structure. World knowledge is encoded in the dictionary (synset IDs) and in the Core Engine. The Frontier Translator's job is purely structural.

**Target scale:** 1B-7B parameters per Frontier Translator, per language (or language family). A 7B model fine-tuned on 500K-1M high-quality (NL, BBP-IR) pairs is expected to outperform a 70B general-purpose model on this task, because the task is narrow and the training distribution is clean.

**Bidirectionality.** The same model is trained to perform both NL → BBP-IR and BBP-IR → NL, by training on both directions of the same pairs. A direction token (`[DIRECTION: ENCODE]` / `[DIRECTION: DECODE]`) is prepended to each training example. This makes the Frontier Translator a fully bidirectional translation model.

##### 15.4.0.6 Relationship to §15.4.3 and §15.6

The synthesis pipeline in §15.4.3 describes the **corpus generation infrastructure** — the combinatorial generator, the Teacher contextualization step, and the verification pass. Section §15.4.0 describes the **training methodology** that the corpus supports. The two sections are complementary: §15.4.3 answers "how is the corpus built?"; §15.4.0 answers "what is the corpus for, and why is it structured this way?"

Section §15.6 describes the **distillation of production-grade Frontier Translators** from the validated corpus. The distillation strategy in §15.6 is an application of the §15.4.0 methodology at production scale, using BBP-TrC (§15.4.5) as the training corpus.

#### 15.4.1 Universal Teacher System Prompt

The Universal Teacher is a world-class LLM (Claude or equivalent) operating with a comprehensive system prompt that serves as the single, authoritative interpreter of the BBP specification across all seven languages. The system prompt is the most critical engineering artifact in the entire project: every error or ambiguity in the prompt propagates systematically across the entire corpus.

- [ ] Design Universal Teacher system prompt. MUST include:
  - Complete BBP specification (Sections 1-14, or a condensed authoritative reference).
  - Explicit nominative-accusative → ergative-absolutive conversion rules with examples per language.
  - Per-language mapping guides:
    - Spanish: clitic analysis (*se lo di* → NOR-NORI-NORK), reflexive *se* → Middle voice, subjunctive triggers, *a personal* → no case change (already in patient role).
    - Basque: direct morphological decomposition → BBP fields (native mapping, minimal transformation).
    - Turkish: evidential suffix identification (*-mış/-dı/-yor/-ecek/-miş* combinations), agglutinative chain decomposition, causative/passive stacking.
    - Mandarin: particle → aspect mapping (了→PERF, 过→EXPERIENTIAL, 着→CONTINUATIVE, 起来→INCOATIVE), zero-tense rule, serial verb identification, topic detection.
    - Japanese: particle → case mapping (が→ABS/ERG, を→ABS, に→DAT, は→TOPICAL, で→INSTRUMENTAL), register detection (です/ます→FORMAL), causative-passive compound analysis.
    - Arabic: dual detection and agreement rules, root identification, broken plural handling, aspect-primary verb analysis, VSO→ergative mapping.
    - English: baseline mapping, phrasal verb decomposition, passive identification, relative clause nesting.
  - Instructions to NOT infer tense when source language does not encode it.
  - Instructions to emit EVIDENTIAL bound modifier tokens for Turkish (obligatory, every verb).
  - Instructions to use TEMPORAL ASPECT_FINE for Mandarin aspect particles and Russian prefixes.
  - Instructions to use TOPICAL case for Japanese は and Korean 은/는.
  - Criteria for PLACEHOLDER vs reordering for cataphoric structures.
  - Output format: strict BBP-IR JSON per Section 14 schema.
  - [ ] Full preposition/particle elimination rules (§12.0.2), including:
    - Complete preposition-to-case mapping table (§12.0.2.1).
    - List of prepositions that ARE emitted as tokens (§12.0.2.2, categories A–E).
    - Worked examples for all 7 core languages demonstrating elimination (§12.0.2.3).
    - Rules for ambiguous markers (Finnish -lla/-llä, Japanese に, Spanish de/para).
    - English phrasal verb decomposition procedure.
- [ ] Validate prompt on 50 manually selected test sentences per language (350 total). Sentences MUST cover every feature marked ✓ in the coverage matrix (Section 15.2).
- [ ] Iterate prompt until weighted error rate < 3% on the test set (as measured by Axiomatic Validator + human review for Spanish/Basque).

#### 15.4.2 Source Corpus Selection

- [ ] Curate source corpora for each language. Prioritize:
  - **Diversity of construction types** over volume. A sentence that exercises NOR-NORI-NORK + subjunctive + negation scope is worth a thousand simple SVO declaratives.
  - **Universal Dependencies treebanks** (gold-standard syntactic annotation provides cross-validation signal).
  - **OpenSubtitles** (natural spoken language, diverse register, good coverage of imperatives, questions, exclamations).
  - **Wikipedia** (formal register, complex sentence structure, domain vocabulary, factual content for reasoning training).
  - **News corpora** (evidential language in Turkish news, aspect usage in Mandarin news, formal Arabic).
- [ ] Design sentence sampling strategy:
  - Stratified sampling by construction type (transitive, intransitive, ditransitive, passive, causative, relative clause, conditional, question, imperative, negation, coordination, subordination).
  - Minimum quotas per BBP feature per language (e.g., at least 5,000 sentences with EVIDENTIAL for Turkish, at least 5,000 with ASPECT_FINE for Mandarin).
  - Length distribution: 60% short (5-15 words), 30% medium (15-30 words), 10% complex (30+ words, nested clauses).
- [ ] Target corpus size: determined by the quality stopping criteria in §15.8, not a fixed sentence count. An order-of-magnitude estimate of ~100,000 sentences per language (~700,000 total for 7 languages) was used in early design, but this figure is not a target — it is a rough planning baseline. The actual corpus will be larger or smaller depending on measured Frontier Translator error rates and coverage saturation (§15.8). The go/no-go criterion for advancing to Core Engine pre-training (§15.5) is defined in §15.8 (Frontier Translator error rate targets table). Use the conservative targets as the phase gate.

#### 15.4.3 Generation Pipeline

**Pipeline architecture: synthesis-first, not analysis-first.**

**Relationship to §15.4.0.** This section describes the infrastructure that implements the Inverted Ground Truth methodology defined in §15.4.0. The pipeline here produces the (BBP-IR, NL) pairs that are the training corpus for the Frontier Translator. BBP-IR is always produced first by deterministic construction; NL is always the Teacher-generated artifact. Refer to §15.4.0 for the normative rationale and the three-phase training curriculum that this pipeline feeds.

The pipeline described here inverts the conventional NLP corpus-generation approach. The conventional approach — natural text → LLM analysis → structured representation — makes the LLM's fallible linguistic analysis the origin of every token in the corpus. Errors propagate silently: a misassigned case or wrong evidentiality value is indistinguishable from a correct one in the binary stream, and round-trip evaluation catches only coarse semantic divergences, not fine structural errors.

BBP's 32+32 token architecture (LEMMA / FEATURES) enables a fundamentally different strategy: **generate the BBP token first by deterministic construction, then generate natural language context conditioned on it.** The LLM's role shifts from analyst (difficult, error-prone) to contextualized generator (reliable, directly evaluable). The token is correct by design; the only failure mode is an LLM that generates a sentence which does not actually exhibit the requested configuration — a detectable and discardable error.

```
┌──────────────────────────────────────────────────────────┐
│ Step 1 — Combinatorial generation (deterministic)         │
│                                                           │
│ For each lemma in the dictionary, enumerate all           │
│ grammatically valid (lemma, feature-configuration) pairs  │
│ based on dictionary constraints.                          │
│   e.g. RAIN → NOR system only, not NOR-NORK              │
│         TABLE → INESSIVE case valid; verb system invalid  │
│ Output: inventory of (lemma, config) pairs,               │
│         validity guaranteed by construction.              │
└────────────────────────┬─────────────────────────────────┘
                         │ (lemma, config) pairs
                         ▼
┌──────────────────────────────────────────────────────────┐
│ Step 2 — LLM contextualization                            │
│                                                           │
│ For each (lemma, config) pair, prompt a world-class LLM   │
│ to generate a natural sentence in which that concept      │
│ appears with those exact properties.                      │
│                                                           │
│ Prompt contract: "Generate a sentence where the verb EAT  │
│ appears with: aspect=perfective, tense=past, mood=indic,  │
│ polarity=affirmative, subject=3rd-singular,               │
│ object=3rd-singular."                                     │
│                                                           │
│ The LLM performs synthesis conditioned on a               │
│ specification — the task where LLMs are reliable.         │
│ It does NOT perform linguistic analysis.                  │
└────────────────────────┬─────────────────────────────────┘
                         │ (BBP token, NL sentence) pairs
                         ▼
┌──────────────────────────────────────────────────────────┐
│ Step 3 — Cross-verification (two directions)              │
│                                                           │
│ Direction A — Structural: Axiomatic Validator confirms    │
│   the token is well-formed. CRITICAL fail → discard.      │
│                                                           │
│ Direction B — Semantic: a second LLM (or the same with   │
│   a verification prompt) confirms that the generated      │
│   sentence actually exhibits the requested configuration. │
│   Failures in either direction discard the pair.          │
│   Successes produce a verified (token, context) pair.     │
└────────────────────────┬─────────────────────────────────┘
                         │ Verified (BBP token, NL sentence) pairs
                         ▼
┌──────────────────────────────────────────────────────────┐
│ Step 4 — Compiler + Decoder round-trip                    │
│                                                           │
│ Compile token → binary stream.                            │
│ Decode binary stream → BBP-IR.                            │
│ Verify decoded IR == compiled IR (bit-level identity).    │
└──────────────────────────────────────────────────────────┘
```

**Why this matters for corpus quality.** In the analysis pipeline, every token is produced by LLM inference over ambiguous natural text. In the synthesis pipeline, the token is produced by deterministic lookup against dictionary constraints; LLM inference only produces the natural-language wrapper. The structural signal — case, TAM, argument roles — is never at risk of LLM hallucination. The corpus quality ceiling is determined by dictionary completeness and constraint accuracy, not by LLM linguistic analysis reliability.

**Supplementary analysis pipeline.** For domains where the synthesis pipeline cannot provide sufficient coverage (idiomatic constructions, metaphor, discourse-level phenomena that resist enumeration), the conventional analysis pipeline (NL → Teacher LLM → BBP-IR) is used as a supplement. Supplementary pairs undergo the same Axiomatic Validator + cross-verification step and are tagged with provenance (`origin: analysis`) in the corpus metadata. The validated proportion from the synthesis pipeline serves as the quality anchor.

- [ ] Implement combinatorial pair generator: for each dictionary entry, enumerate valid feature configurations per grammatical category (verb: system × aspect × voice × mood × tense × person; noun: case × number × determiner × classifier × animacy × noun_class × register).
- [ ] Implement LLM contextualization batch system. Prompt templates per language per feature configuration. Target: 10 distinct sentence contexts per (lemma, config) pair for frequent lemmas; 3 for rare lemmas.
- [ ] Implement cross-verification: second-pass LLM prompt confirms configuration is exhibited. Discard pairs with verification failure rate > 20% — these indicate an underspecified or ambiguous prompt template requiring revision.
- [ ] Run supplementary analysis pipeline for idiomatic / discourse-level coverage. Tag all pairs with `origin: synthesis` or `origin: analysis`.
- [ ] Implement round-trip (Compiler → Decoder → verify).
- [ ] Run generation in batches of 10,000 pairs per language. After each batch:
  - Report: Validator pass rate, verification pass rate, feature coverage statistics.
  - If verification pass rate < 80% for a feature, revise prompt template for that feature.
  - If specific lemma classes show high discard rates, investigate dictionary constraint completeness.

#### 15.4.4 Quality Assurance

- [ ] **Human validation (Spanish, Basque):** Author manually reviews 500 randomly sampled instances per language. Records systematic error patterns. Feeds corrections back into system prompt. This is the ground truth calibration — the only point where human linguistic judgment directly validates the Teacher's output.
- [ ] **Cross-validation with UD treebanks:** For sentences sourced from Universal Dependencies, compare Teacher's case assignments against UD dependency labels. Ergative/absolutive assignments should be consistent with UD's agent/patient analysis. Systematic disagreements reveal Teacher bias.
- [ ] **Adversarial review:** Author designs 100 "trap" sentences per language — sentences with known ambiguities, scope interactions, or cross-linguistic pitfalls. Verifies Teacher handles them correctly. Examples:
  - Spanish: "No todos los estudiantes aprobaron" (negation scope).
  - Turkish: "Gelmiş olmalı" (evidential + potential mood stacking).
  - Mandarin: "他吃了饭了" (double 了 — perfective + sentence-final change-of-state).
  - Japanese: "象は鼻が長い" (double-subject, topic vs. subject).
  - Arabic: "الكتابان الكبيران" (dual + adjective agreement in dual).
  - Basque: "Liburuak eman dizkiot" (NOR-NORI-NORK, plural absolutive, 1sg ergative, 3sg dative).
  - English: "The chicken is ready to eat" (agent/patient ambiguity).

#### 15.4.5 Corpus Publication

- [ ] Package validated corpus as **BBP-TrC** (BBP Training Corpus — size determined by §15.8 quality criteria, not a fixed 700K target):
  - Format: JSONL (one BBP-IR instance per line) + parallel binary files + source sentences + round-trip scores + language tags + feature annotations.
  - Split: 90% train / 5% validation / 5% test. Stratified by language and feature coverage.
  - License: open (CC-BY-SA or equivalent).
- [ ] Publish corpus statistics report: feature frequency distributions per language, Validator pass rates, round-trip score distributions, known limitations.

### 15.5 Phase 3 — Core Cognitive Engine (Months 8-14)

**Objective:** Train a BBP-native LLM that operates exclusively on 64-bit BBP token streams — a model that has never seen natural language text and reasons entirely within BBP's typed semantic structures.

#### 15.5.0a Frontier Translator Error Rate as Epistemological Bottleneck (Normative)

The Core Engine is pre-trained exclusively on BBP streams produced by the Frontier Translator pipeline. The quality of the world-knowledge representations learned by the Core Engine is therefore a direct function of the Frontier Translator's error rate.

A Frontier Translator that misassigns case roles, mislabels evidentiality, or incorrectly maps argument structure produces BBP streams with structurally silent errors — errors indistinguishable from correct encodings by any downstream validator. These errors are embedded in the Core Engine's representations during pre-training and cannot be corrected without retraining. The lower the Frontier Translator error rate, the higher the fidelity of the world model acquired by the Core Engine.

**This relationship is the most critical quality dependency in the entire architecture.** The Prologue correctly identifies corpus generation methodology as "the most critical engineering artefact in the project"; this section makes explicit *why*: every percentage point of Frontier Translator error rate translates directly into a percentage of the Core Engine's pre-training corpus that encodes a distorted or incorrect view of the world.

**Normative consequence:** The go/no-go criterion for advancing to Core Engine pre-training MUST be defined in terms of measured Frontier Translator error rate on the held-out evaluation set, not in terms of corpus sentence count. Corpus generation continues until the error rate is sufficiently low. The specific targets for each phase and language group are defined normatively in §15.8 (Frontier Translator error rate targets table). Implementations MUST use the conservative targets from that table as the phase transition gate; the optimistic targets are quality aspirations.

#### 15.5.0b Reasoning Curriculum (Normative)

The Core Engine’s reasoning training is a distinct curriculum phase that MUST precede or run concurrently with the general linguistic pre-training. The Reasoning Curriculum encompasses two domains: **mathematical reasoning** and **formal logical reasoning**. Both share a critical architectural property: training data is **fully or partially deterministically generated**, with correctness verifiable without a Teacher LLM.

This is the purest instantiation of the Inverted Ground Truth principle in the entire project: both the structure (BBP-IR) and the ground truth result are deterministic or formally checkable. The probabilistic component of the training signal is minimal or zero.

**Mathematical Reasoning Track (M).** Unlike linguistic training — which depends on the Frontier Translator pipeline and is subject to translation error rates — mathematical training data is fully deterministically generated: a combinatorial generator produces MATH_EXPRESSION BBP structures, the runtime evaluates them, and the resulting (expression, result) pairs are correct by construction. No Teacher LLM is involved.

The Mathematical Reasoning Track (M) follows the three-phase progression specified in §10.3.4:

- **Phase M-A** — Concrete arithmetic: single operations over the full numeric range, building magnitude-aware representations.
- **Phase M-B** — Mathematical reasoning: MATH_EXPRESSION[EXECUTABLE] within BBP-CoT chains, building strategy-level reasoning over computation.
- **Phase M-C** — Quantitative science: domain-specific mathematical reasoning (physics, chemistry, economics) where the Core Engine learns to deploy exact computation as evidence within scientific argumentation.

**Formal Logical Reasoning Track (L).** Formal deductive reasoning can also be generated mechanically, extending the Inverted Ground Truth principle to the logical domain. The ground truth is the output of a formal inference engine, not a Teacher LLM.

- **Phase L-A** — Propositional logic: mechanically generated BBP-CoT chains for modus ponens, modus tollens, disjunctive syllogism, hypothetical syllogism, and De Morgan’s laws. The Compiler generates (premises, inference step, conclusion) triples; correctness is verified by a SAT solver. The BBP-CoT structure MUST use HYPOTHESIS, INFERENCE_STEP, and CONCLUSION tokens (§17) explicitly.
- **Phase L-B** — Predicate logic and syllogisms: first-order logic structures with universally and existentially quantified propositions. Ground truth verified by a first-order theorem prover.
- **Phase L-C** — Scientific and causal reasoning: formal causal chains over domain laws (kinematic equations, chemical stoichiometry, economic equilibrium). The Core Engine learns to structure multi-step scientific arguments as BBP-CoT chains with EVIDENCE[COMPUTATION] blocks.

**Shared gating policy.** The Reasoning Curriculum phases are gated independently of the linguistic curriculum and of each other:

- Phase M-A metrics MUST be validated before Phase M-B begins.
- Phase L-A metrics MUST be validated before Phase L-B begins.
- Phases M and L MAY run concurrently once their respective Phase-A gates are passed.
- See §10.3.4 for detailed Mathematical Reasoning Track (M) specification and go/no-go criteria.
- See §10.3.5 for detailed Formal Logical Reasoning Track (L) specification and go/no-go criteria.

**Rationale.** The extension of the curriculum to formal logical reasoning is architecturally motivated: if mathematical training produces magnitude-aware numeric representations by construction, then logical training should produce inference-structure-aware representations by the same principle. The Core Engine should not be required to discover modus ponens statistically from linguistic data — it should learn it from a corpus where every example is provably correct by construction.

#### 15.5.0 Cost-Effectiveness Analysis

The three-layer architecture (Frontier Translator → Core Engine → output) incurs a translation overhead. BBP's advantage only materializes if the Core Engine is significantly more efficient than a text LLM for downstream tasks.

**Break-even analysis.** Let C₁ be the cost of Frontier Translation (NL→BBP-IR→binary), C₂ be the cost of Core Engine inference on the BBP stream, and C_text be the cost of a text-based LLM processing the same query end-to-end. BBP is cost-effective when C₁ + C₂ < C_text. Given that BBP streams are ~40% shorter than BPE token sequences for the same content (§14.3), and dense O(N²) attention is the relevant baseline for pre-training, the Core Engine's attention cost is ~36% of the text model's for the same semantic content (0.6² = 0.36). At inference time, modern attention optimizations (Flash Attention, KV-cache, sliding window) alter absolute compute, but shorter sequences still yield proportionally lower KV-cache memory, faster prefill, and a larger effective context window. The approximate break-even condition is: C₁ < 0.64 × C_text.

**Frontier Translators as distilled specialists.** A Frontier Translator for Spanish→BBP does not need to be a general-purpose LLM. It can be a fine-tuned 3B-7B model (§15.6) trained specifically on NL→BBP-IR translation. The Frontier Translator's task is narrower than general text generation: it maps structured input (parsed sentences) to structured output (JSON). This is precisely the kind of task where small, specialized models excel. The break-even condition becomes achievable because C₁ is the cost of a small specialist, not a world-class LLM.

**Primary value proposition — translate-once, reason-many.** The Layer 1 bottleneck argument applies primarily to single-query latency. For scenarios where the same content is processed multiple times, the translation cost is amortized: translate once, query the Core Engine many times. BBP's strongest use cases are:

1. **Document indexing for RAG.** A document corpus is translated to BBP once, then the Core Engine answers queries over the BBP-encoded corpus repeatedly. Translation cost is amortized across all queries.
2. **Knowledge bases.** Domain knowledge (legal, medical, scientific) encoded in BBP once and queried by multiple agents indefinitely.
3. **Inter-agent communication.** Agents that think in BBP communicate without any translation cost. The Layer 1 bottleneck exists only at human-facing boundaries.
4. **Streaming dialogue.** In multi-turn conversations, the BBP context window grows more slowly than a text context window (due to density), compounding savings over many turns.

**Additional r7 objectives for Phase 3:**

- [ ] Add structural consistency loss terms for BBP-CoT (L_reasoning) to training objective.
- [ ] Evaluate reasoning benchmarks with BBP-CoT structured chains vs. unstructured BBP-NL chains. Key question: does explicit epistemic typing improve logical accuracy?
- [ ] Evaluate code generation benchmarks with BBP-AST representation vs. raw text BPE. Key question: does AST-level representation improve structural correctness of generated code?

#### 15.5.1 Tokenizer and Embedding Architecture

The Core Engine does not use a conventional text tokenizer (BPE, SentencePiece). Its input is a stream of 64-bit unsigned integers. A flat embedding lookup table over the full 64-bit token space is infeasible (2⁶⁴ entries). The tokenizer MUST exploit BBP's structured bit layout via compositional field-level embeddings.

**Compositional field-level embeddings (normative).** The Core Engine MUST implement compositional field-level embeddings. Each field of the 64-bit token (concept_id / verb_id, meta-type / Aktionsart+valency, case, number, determiner, aspect, tense, mood, voice, evidentiality, person fields, and remaining grammar fields) MUST have its own independently learned embedding vector. The final token representation fed to the transformer is produced by combining all field embeddings.

**Rationale.** Compositional field-level embeddings are the mechanism by which structural rigidity (§1.3) produces productive compositionality in practice. They enable the Core Engine to construct reasonable representations for field combinations never seen during training, by combining the independently learned meanings of each field. Two BBP tokens that share a case field share a case sub-embedding, enabling systematic generalization across tokens — an advantage unavailable with whole-token or unstructured BPE embeddings. Without this architecture, the claim that "any valid combination of field values produces an interpretable semantic unit — even if that combination was never seen during training" (§1.3) cannot hold computationally.

**Combination method (normative).** The field embeddings MUST be combined by **element-wise summation**:

```
embedding(token) = Σᵢ Eᵢ[fieldᵢ(token)]
```

where Eᵢ is the learned embedding matrix for field i (dimensions: vocabulary_size_i × D_model), and fieldᵢ(token) is the value of field i extracted from the token's bit layout. Each field Eᵢ is an independently trained lookup table of the same output dimension D_model. The token embedding is their element-wise sum.

**Rationale for summation over concatenation.** Concatenation of field embeddings followed by a learned linear projection to D_model is theoretically equivalent to summation in expressive power, but introduces an additional learned matrix that obscures the field structure during training — the projection layer must rediscover the field decomposition that BBP encodes by construction. Summation preserves field semantics directly in the shared embedding space and is consistent with established practice (positional encodings, segment embeddings in BERT-family architectures). Cross-field interactions are learned by the transformer's self-attention layers in subsequent processing — they do not need to be pre-encoded in the embedding combination.

The combination method cannot be changed after training begins without retraining the model from scratch.

**Fields with independent embedding tables (normative).** The following fields MUST each have their own independently trained embedding table:

*Action Tokens:* Aktionsart (3b), valency_class (3b), verb_id (25b, hashed for scale), system (3b), polarity (1b), aspect (3b), voice (2b), voice_ext (3b), mood (2b), tense (2b), subject_person (3b), object_person (3b), indirect_person (3b), evidentiality (4b), clusivity (2b).

*Entity Tokens:* meta-type (6b), concept_id (23b or 25b depending on meta-type variant), flow (2b), case (5b), number (3b), determiner (4b), noun_classifier (4b), animacy (2b), noun_class (4b), register (6b).

*Self-descriptive meta-types:* sign (1b), magnitude (5b), parity (1b) for NUM_* types; kingdom (3b), taxon_id (22b), vital_status (2b) for LIVING_ENTITY.

**Default and absent field values (normative).** Fields where the zero value is defined as **absent or inapplicable** for a given token (e.g., object_person=000 for intransitive verbs, indirect_person=000 when no indirect object exists) MUST contribute a **fixed zero-vector** to the token embedding — they are structurally absent and should not inject learned signal.

Fields where the zero value is defined as a **linguistically meaningful unmarked category** (e.g., EV_UNSPECIFIED, UNMARKED number, UNIT flow) MUST use a **learned embedding** initialized to zero but free to diverge during training.

The distinction between "absent" and "unmarked" is determined by the normative definition of each field value in its defining section. An implementation that applies the wrong policy (treating an unmarked category as absent, or vice versa) is non-conformant.

- [ ] Design and implement compositional embedding architecture:
  - Decompose each 64-bit token into its constituent fields (flag, meta-type/verb-root, flow/system, FEATURES/TAM, deklinabidea/arguments).
  - Each field has its own embedding table of dimension D_model (normative decision: summation, §15.5.1). The token embedding is the element-wise sum of all field embeddings.
  - This mirrors how BBP itself is compositional: the embedding of a token is composed from the embeddings of its structural components.
  - **This is the mechanism by which BBP's "structural clarity" converts into computational advantage**: each sub-embedding learns the semantics of its field independently, and the composed embedding inherits structure that BPE embeddings must discover from distributional context alone. Two BBP tokens that share a case field will share a case sub-embedding, enabling systematic generalization across tokens — an advantage unavailable with unstructured BPE token IDs.

  **Recommended field table (informative):**

  ```
  Field         │ Entries        │ Notes
  ──────────────┼────────────────┼──────────────────────────────────────
  flag (UAT/UET)│       2        │ absent fields → zero-vector (fixed)
  meta_type     │      64        │
  flow          │       4        │ UNIT = unmarked → learned embedding
  case          │      32        │
  number        │       8        │ UNMARKED → learned embedding
  determiner    │      16        │
  classifier    │      16        │
  animacy       │       4        │
  noun_class    │      16        │
  register      │      64        │
  verb_id       │  33,554,432    │ too large for full lookup; use hashing
  Aktionsart    │       6        │
  valency_class │       6        │
  system        │       8        │
  TAM fields    │     256        │ EV_UNSPECIFIED → learned embedding
  arguments     │     128        │
  ```

  Each row has its own D_model-wide embedding table. The final token embedding = Σᵢ Eᵢ[fieldᵢ(token)].

- [ ] Implement the absent/unmarked field policy (§15.5.1 default value policy): absent fields contribute zero-vector (fixed); unmarked-but-meaningful fields contribute a learned embedding initialized to zero.

**LITERAL block treatment by the Core Engine (normative).** The Core Engine MUST treat LITERAL block content as **semantically opaque**. Specifically:

- The embedding of a LITERAL concept is the embedding of its **opening 64-bit token** (flow=LITERAL, meta-type, FEATURES=byte_length). The raw byte content is not processed by the transformer directly.
- Grammatical context is supplied by the **closing token** (flow=END, FEATURES=grammatical fields), which is a normal 64-bit token and IS processed by the transformer.
- The Core Engine cannot reason about the internal content of a LITERAL block beyond what is encoded in the meta-type and the closing token’s grammatical fields. A LITERAL NAME_PERSON token is “a person whose name is not in the dictionary” — the Core Engine knows it is a person, knows its case and number, but cannot access semantic associations of the specific name.
- **Mitigation:** the primary mitigation for semantic opacity is maximizing dictionary coverage. A high out-of-dictionary rate on the evaluation corpus is a failure signal (Readiness Scorecard, §15.9). The target is that LITERAL blocks appear in less than 1% of tokens in the production stream after Phase 2 dictionary growth.
- **v1.1 candidates:** sub-word byte-level embeddings (Option B) or a BPE sub-tokenizer over LITERAL content (Option C) are documented as candidates for v1.1, conditional on Phase 3 results demonstrating that LITERAL opacity is a measurable performance bottleneck.

#### 15.5.2 Model Architecture

- [ ] Start with a standard Transformer decoder (GPT-style autoregressive) adapted for BBP tokens:
  - Input: sequence of 64-bit BBP tokens (via compositional embeddings).
  - Output: next-token prediction over the structured token space.
  - Output head: **multi-head factored prediction** (predict each field independently, then combine). This exploits the constraint structure: if system=NOR is predicted, the object-head can be suppressed. Mirrors Section 13.5 but at full scale.
- [ ] Implement **structural consistency loss** derived from Axiomatic Validator rules:
  - Cross-head constraint penalties: system=NOR predicted → penalize non-zero object prediction.
  - Case-system consistency: if ERG is predicted for a UET, penalize if the nearest UAT predicts system=NOR.
  - native verb feature field sequencing: if EVIDENTIAL is predicted, next token MUST be UAT or another native verb feature field.
- [ ] Model scale for initial experiments: 125M-350M parameters. BBP tokens are far denser than text tokens — a 350M parameter model processing BBP may have effective reasoning capacity comparable to a much larger text-based model, because each token position carries a complete semantic unit rather than a subword fragment. This hypothesis is testable and is a key research question of the project.

#### 15.5.3 Training Curriculum

- [ ] **Stage 1 — Language modeling (months 8-10):** Train on BBP-TrC with standard causal language modeling objective (next-token prediction). The model learns the statistical structure of valid BBP streams: which token types follow which, how case systems correlate with verb paradigms, how bound modifier tokens precede UATs, how blocks nest and close.

- [ ] **Stage 1b — Minimal contrast training (concurrent with Stage 1):** Exploit the LEMMA/FEATURES separation to construct training batches of token pairs that differ in exactly one feature dimension. The model receives two BBP streams that are identical except for one bit field (e.g., only aspect changes, or only polarity, or only the subject's case) and must predict which field changed and what the semantic consequence is.

  ```
  Stream A: [RAIN, NOR, PAST, PERF, INDIC, AFF, S:3s]  → "It rained"
  Stream B: [RAIN, NOR, PAST, IMPERF, INDIC, AFF, S:3s] → "It was raining"
  Contrast: aspect (perfective → imperfective)
  Consequence: completed event → event in progress / habitual
  ```

  This regime forces the model to develop sensitivity to each feature dimension independently — the experimental principle of controlled variation applied to training. It is directly enabled by the synthesis pipeline (§15.4.3): because BBP tokens were generated by combinatorial enumeration, minimal-contrast pairs are available by construction for every lemma in the dictionary.

- [ ] **Stage 1c — Paradigm completion (concurrent with Stage 1):** Given a lemma and a subset of its valid (lemma, config) pairs with their natural-language contexts, the model must generate plausible natural contexts for held-out configurations. This validates productive compositionality (§1.3) as a measurable training objective, not merely a theoretical claim.

  ```
  Given:
    [GO] + [NOR, PAST, PERF, INDIC, AFF, S:1s]   → "I went to the market"
    [GO] + [NOR, PRES, IMPERF, INDIC, AFF, S:1s]  → "I go to the market every day"
    [GO] + [NOR, FUT, PERF, INDIC, AFF, S:1s]     → "I will have gone before Monday"

  Complete:
    [GO] + [NOR, HYPO, IMPERF, SUBJ, NEG, S:1s]   → ?
  ```

  Success on held-out configurations provides empirical evidence that the model has learned the structure of the feature variation space, not merely token-to-token surface correlations. This is the compositional generalization benchmark defined in Appendix H.8, formulated as an explicit training task rather than a post-hoc evaluation.

- [ ] **Stage 2 — Reasoning tasks (months 10-12):** Fine-tune on BBP-encoded reasoning datasets:
  - Logical inference: premises encoded as BBP → conclusion as BBP. Source: FOLIO, LogiQA converted to BBP via synthesis pipeline.
  - Mathematical reasoning: word problems encoded as BBP → solution steps as BBP. Source: GSM8K, MATH converted to BBP.
  - Causal reasoning: "Because X (BBP), therefore Y (BBP)" chains. Source: generated from news articles and scientific abstracts.

- [ ] **Stage 3 — Dialogue and interaction (months 12-14):** Fine-tune on BBP-encoded multi-turn dialogues:
  - Question-answering: question in BBP → answer in BBP.
  - Task execution: instruction in BBP → plan in BBP.
  - Inter-agent simulation: two agents exchanging BBP messages to accomplish a shared goal.

#### 15.5.4 Evaluation

- [ ] **Structural validity:** What percentage of generated BBP streams pass the Axiomatic Validator? Target: > 98%.
- [ ] **Round-trip semantic fidelity:** Generated BBP → Renderer → NL text. Human evaluation of coherence and meaning preservation. Target: > 90% coherent.
- [ ] **Reasoning benchmarks (BBP-encoded):** FOLIO, LogiQA, GSM8K with inputs and expected outputs in BBP. Compare against text-based baselines of similar parameter count.
- [ ] **Key research question:** Does reasoning in BBP's typed, role-marked representation produce better logical accuracy than reasoning in natural language tokens, parameter count being equal? If yes, the core thesis of BBP is validated. If no, investigate whether the gap is in the representation, the training data, or the model architecture.

#### 15.5.5 Staged Validation — Go/No-Go Gates

Before investing months in corpus generation for a full Core Engine, a small-scale experiment provides a critical go/no-go signal:

**Stage 0 — Micro-experiment (weeks 1-2 of Phase 3):**

- Take a domain-restricted subset: 500 verb roots + 2,000 nouns, covering all 8 aspect values, 4 mood values, 4 tense values, both polarities, and the 6 core cases (ABS, ERG, DAT, GEN_POSS, INE, ABL).
- Generate 50K (token, NL context) pairs using the synthesis pipeline (§15.4.3): combinatorial enumeration of valid configurations → LLM contextualization → cross-verification. This corpus is structurally correct by construction.
- Generate an additional 10K minimal-contrast pair batches (Stage 1b format) from the same vocabulary subset.
- Train a small transformer (125M parameters) with two objectives: (a) standard next-token prediction on the 50K corpus; (b) minimal-contrast feature prediction on the 10K pair batches.
- Evaluate:
  - **Compositional generalization:** Hold out 20% of valid feature combinations from training. Does the model correctly process and generate these held-out combinations? Target: > 60% accuracy (§1.3 benchmark, Appendix H.8).
  - **Embedding disentanglement:** Run linear probes on the model's internal representations. Do independent sub-dimensions emerge aligned with case, aspect, tense, and polarity? (Verifiable without semantic judgment — purely geometric analysis.)
  - **Axiom constraint learning:** Does the model implicitly learn structural constraints (e.g., never generating ERG case with a NOR-system verb)?
  - **REFERENCE resolution:** Can it resolve coreference backpointers correctly?
- Compare against a text-trained model of the same size on equivalent semantic tasks.

**Go/no-go criteria:**

| Metric | Go threshold | Action if not met |
|--------|-------------|-------------------|
| Axiom violation rate (generated streams) | < 10% | Investigate embedding architecture; try binary-field alternative |
| Compositional generalization (held-out combos) | > 60% accuracy | Critical failure; revise tokenizer and embedding design |
| Embedding disentanglement (linear probe R²) | > 0.7 for ≥ 3 fields | Embedding architecture not reflecting structure; revise sub-embedding design |
| REFERENCE resolution accuracy | > 80% | Investigate positional encoding interaction |

If Stage 0 fails on all three metrics, the architecture MUST be revised before proceeding. Revision options: hybrid training (BBP + text parallel corpora, see §15.5.5.1), BBP as a fine-tuning format rather than a pre-training format, or fundamental redesign of the embedding layer.

##### 15.5.5.1 Fallback: Hybrid Training Regime

If BBP-exclusive training fails to produce adequate reasoning capabilities, a hybrid approach trains on a *parallel corpus* where each training example contains both natural language text and its BBP binary representation. The model learns to associate BBP structures with natural language semantics. After pre-training on parallel data, the model can be fine-tuned on BBP-only streams. This is analogous to how multilingual models learn cross-lingual representations: exposure to parallel data enables transfer.

**Success criteria for full-scale training (normative):**

```
The Core Engine is considered viable if it achieves ALL of:
  1. Axiom violation rate < 2% on generated streams
  2. Round-trip semantic fidelity > 85% (human eval, 1-5 scale)
  3. Reasoning benchmark accuracy ≥ 80% of text baseline
     at equivalent parameter count (on FOLIO, GSM8K)
  4. Compositional generalization > 90% on held-out
     field combinations (§1.3, Appendix H.8)

If these thresholds are not met after 3 training runs with
different hyperparameters, the architecture WILL be revised
to consider hybrid training (§15.5.5.1) or BBP as a
fine-tuning format rather than a pre-training format.
```

### 15.6 Phase 4 — Frontier Translator Distillation (Months 12-18)

**Objective:** Distill lightweight, fast, production-grade Frontier Translators from the validated corpus, replacing the expensive Universal Teacher for real-time operation.

#### 15.6.1 Distillation Strategy

The Universal Teacher (world-class LLM) was used to generate the corpus but is too large and expensive for production inference. Frontier Translators are small models (1B-7B parameters) fine-tuned exclusively on the NL ↔ BBP-IR task for a specific language or language family, trained using the Inverted Ground Truth methodology defined in §15.4.0. The distillation process here is the production-scale instantiation of §15.4.0: the same curriculum (Phases A, B, C), the same ground truth direction (BBP-IR as anchor), and the same quality guarantees — applied at BBP-TrC scale.

- [ ] **Per-language Frontier Translators (priority):**
  - Spanish NL → BBP-IR (and reverse).
  - Basque NL → BBP-IR (and reverse).
  - English NL → BBP-IR (and reverse).
  - Turkish NL → BBP-IR (and reverse).
- [ ] **Per-family Frontier Translators (second priority):**
  - CJK Translator: Mandarin + Japanese (shared writing system challenges, similar structural features for BBP purposes).
  - Semitic Translator: Arabic (+ future Hebrew, Amharic).
- [ ] Base model selection: evaluate Llama 3, Mistral, Qwen, Gemma in the 3B-7B range. Select based on multilingual capability in the target language.
- [ ] Training data: the language-specific partition of BBP-TrC. Each instance is a (NL sentence, BBP-IR JSON) pair used in both directions.
- [ ] Evaluation: compare against Universal Teacher on 1,000 held-out sentences per language. Target: < 5% relative degradation in Validator pass rate and round-trip fidelity.

#### 15.6.2 Production Deployment Architecture

```
                    ┌──────────────────────────────┐
                    │       Human Interface         │
                    │     (text, voice, UI)         │
                    └──────────────┬───────────────┘
                                   │ NL text
                    ┌──────────────▼───────────────┐
                    │    Frontier Translator        │
                    │    3B-7B, per-language         │
                    │    Latency: < 200ms           │
                    └──────────────┬───────────────┘
                                   │ BBP-IR JSON
                    ┌──────────────▼───────────────┐
                    │    Validator + Compiler       │
                    │    Deterministic, < 1ms       │
                    └──────────────┬───────────────┘
                                   │ BBP binary
               ┌───────────────────┼───────────────────┐
               │                   │                   │
    ┌──────────▼──────────┐  ┌─────▼─────┐  ┌─────────▼─────────┐
    │   Core Engine       │  │  Agent A  │  │  Agent B          │
    │   BBP-native LLM    │  │           │  │                   │
    │                     │  │◄───BBP───►│  │                   │
    └──────────┬──────────┘  └───────────┘  └───────────────────┘
               │ BBP binary
    ┌──────────▼───────────────┐
    │ Decoder + Renderer       │
    │ Deterministic + Frontier │
    └──────────┬───────────────┘
               │ NL text
    ┌──────────▼───────────────┐
    │     Human Interface      │
    └──────────────────────────┘
```

### 15.7 Phase 5 — Expansion and Ecosystem (Month 18+)

#### 15.7.1 Language Expansion

- [ ] Add new Frontier Translators for additional languages. Priority by speaker count and typological diversity:
  - Tier 1: Hindi (light verbs, SOV, ergative split), Korean (topic-comment, honorifics, agglutination), Russian (aspect pairs, fine aspect prefixes).
  - Tier 2: Swahili (Bantu noun classes, applicative voice), Quechua (evidentiality + ergativity, agglutination), Finnish (15 grammatical cases, agglutination).
  - Tier 3: community-contributed Frontier Translators for additional languages.
- [ ] Each new language generates additional BBP corpus that incrementally refines the Core Engine via continued training.
- [ ] The Core Engine serves as a **semantic validation oracle** for new Frontier Translators: if a translator produces BBP that the Core Engine processes incoherently (low confidence, inconsistent outputs), it signals a translator quality issue.

#### 15.7.2 Core Engine Evolution

- [ ] Scale Core Engine from 350M to larger parameter counts based on Phase 3 research findings.
- [ ] If the BBP-native reasoning hypothesis is validated, investigate:
  - Multi-agent architectures where specialized BBP agents (logic, math, planning, retrieval) communicate via BBP streams.
  - Hybrid architectures where a BBP Core Engine handles reasoning and a text-based model handles creative/social tasks.
  - BBP as a shared memory format for agent ensembles (persistent BBP state that multiple agents read and write).

#### 15.7.3 Protocol Evolution

- [ ] v1.1: Use reserved NOR extended bits (0-3) for evidentiality/allocutive.
- [x] ~~v1.1: Add inclusive/exclusive distinction for first person plural.~~ **Resolved in r16:** CLUSIVITY native verb feature field (§6.7.5).
- [x] ~~v1.1: Add antipassive and applicative voice.~~ **Resolved in r16:** VOICE_EXT native verb feature field (§6.7.4).
- [x] ~~v1.1: Add stream version header for forward compatibility.~~ **Resolved in r16:** Protocol version in DOC_START Deklinabidea (§11.3).
- [ ] v1.2+: Meta-cognitive primitives for Core Engine reasoning (HYPOTHESIS, RETRACTION, INFERENCE_STEP) based on research findings from Phase 3.
- [ ] Ongoing: Maintain the BBP specification, vocabulary database, Language Registry, and Axiomatic Validator axiom set as living documents with semantic versioning.

### 15.8 Success Criteria

| Phase | Milestone | Measurable criterion |
|-------|-----------|---------------------|
| 1 | Toolchain functional | All Appendix D test vectors pass; 50-sentence end-to-end round-trip verified by human review |
| 2 | Corpus generated | Corpus quality stopping criteria met (see note below); Validator pass rate > 90% per language; round-trip score > 3.0 mean per language |
| 2 | Teacher prompt stable | Frontier Translator weighted error rate at or below conservative target for all Phase 2 languages (see Frontier Translator error rate targets below) |
| 3 | Core Engine trained | > 98% structural validity on generated streams; > 90% round-trip semantic coherence |
| 3 | Reasoning hypothesis | BBP-native model outperforms text-based model of same parameter count on FOLIO/LogiQA/GSM8K (BBP-encoded) |
| 4 | Frontier distillation | < 5% quality degradation vs Universal Teacher; inference latency < 200ms |
| 5 | Ecosystem | ≥ 10 languages supported; community contributions active; Core Engine processing coherent BBP from all supported languages |
| 1 | BBP-AST toolchain | Round-trip (source → BBP-AST → source) preserves semantics for 500 test functions across 3 languages |
| 2 | CoT corpus | 10,000 validated reasoning chains; Axiom Group F pass rate > 95% |
| 3 | Code hypothesis | BBP-AST representation enables Core Engine to generate structurally valid code (> 95% parse-valid) at same parameter count where text-based model achieves < 80% |
| 3 | Reasoning hypothesis (CoT) | BBP-CoT chains show > 10% relative improvement on FOLIO/LogiQA vs. unstructured BBP-NL reasoning at same parameter count |

**Corpus quality stopping criteria (normative).** No fixed volume target is specified. The corpus grows until both stopping criteria are met:

1. **Quality criterion:** Frontier Translator weighted error rate at or below the conservative target for all languages in the current phase (see table below).
2. **Coverage saturation criterion:** Fewer than 1% new BBP feature combinations per additional 10K validated instances across all core languages for the current phase.

Volume is a consequence of quality, not a target in itself. A corpus that reaches any previously estimated volume figure but does not meet both criteria is incomplete. A corpus that meets both criteria before reaching any estimated volume figure MAY proceed to the next phase immediately.

**Frontier Translator error rate targets (normative).** Error rate is defined as the weighted error rate by linguistic feature slot on the human-reviewed test set (§15.3.0.1). A "slot error" is any field (case, TAM, evidentiality, number, etc.) whose value in the Teacher output diverges from the human-adjudicated Gold Set annotation. Phase transition is gated on reaching the **conservative** target, not the optimistic target. The optimistic target is a quality aspiration. Failure to reach the conservative target after exhausting documented prompt engineering iterations MUST trigger a root cause analysis before proceeding.

| Phase | Languages | Optimistic | Conservative | Blocking? |
|-------|-----------|------------|--------------|-----------|
| Phase 2 — corpus generation | ES, EN | < 3% | < 8% | Phase 3 entry |
| Phase 2 — corpus generation | EU, TR, JA, ZH, AR | < 5% | < 12% | Phase 3 entry |
| Phase 3 — Core Engine pre-training | All 7 | < 2% | < 5% | Core Engine quality floor |
| Phase 4 — Frontier distillation | All 7 | < 3% | < 8% | Production release |

### 15.9 BBP Readiness Scorecard

The specification SHOULD NOT move from "Release Candidate" to "Released" until the following mandatory benchmarks are populated with measured values. This scorecard provides the empirical grounding that separates a validated protocol from a theoretical exercise.

| Benchmark | Target | Measured | Blocking? |
|-----------|--------|----------|-----------|
| Teacher LLM weighted error rate (Spanish) | < 5% | TBD | v1.0 release |
| Teacher LLM weighted error rate (Turkish) | < 8% | TBD | v1.0 release |
| Round-trip semantic preservation (human eval, 1-5) | > 3.5 | TBD | v1.0 release |
| Compilation throughput (sentences/sec) | > 100 | TBD | v1.0 release |
| out-of-dictionary rate (general prose) | < 10% | TBD | v1.0 release |
| out-of-dictionary rate (medical domain) | < 15% | TBD | Domain profile release |
| EXTENDED mode frequency (prose) | < 5% | TBD | v1.0 release |
| EXTENDED mode frequency (dialogue) | < 15% | TBD | Informational |
| Axiom violation rate (after auto-correct) | < 2% | TBD | v1.0 release |
| Gold Set inter-annotator agreement | > 85% | TBD | v1.0 release |
| AST_OPAQUE density (Python) | < 5% | TBD | BBP-AST release |
| AST_OPAQUE density (Rust) | < 20% | TBD | BBP-AST release |
| Core Engine axiom violation rate | < 2% | TBD | Phase 3 |
| Compositional generalization (held-out combos) | > 90% | TBD | Phase 3 |
| Teacher accuracy on ILL/ELA/SUP/DEL/ADE (Finnish) | < 8% error | TBD | Phase 1.5 |
| Teacher accuracy on VOC (Arabic, Latin) | < 5% error | TBD | v1.0 release |
| Teacher accuracy on ESS vs TRANS (Finnish) | < 10% error | TBD | Phase 1.5 |
| Round-trip VOC preservation (Latin 50 sentences) | > 3.0 mean | TBD | v1.0 release |
| Finnish case disambiguation (SUP vs ADE) | > 85% precision | TBD | Phase 1.5 |
| Preposition leak rate (Teacher emitting relational words as tokens) | < 3% of obl phrases | TBD | v1.0 release |
| BPE-to-BBP token ratio (English, general prose) | < 0.45 | TBD | v1.0 release |
| BPE-to-BBP token ratio (Spanish, general prose) | < 0.50 | TBD | v1.0 release |
| BPE-to-BBP token ratio (Finnish, general prose) | < 0.55 | TBD | Phase 1.5 |
| Ambiguous preposition resolution accuracy (Spanish de/para, Japanese に) | > 90% | TBD | v1.0 release |
| Round-trip fidelity after preposition reconstruction (Renderer) | > 3.5 mean | TBD | v1.0 release |

**Immediate action.** Run a minimal end-to-end benchmark: 50 manually authored Spanish sentences through the Teacher LLM → Validator → Compiler → Decoder → Renderer pipeline, reporting error rates. This can be done in days, not months, and provides the first empirical data point for the entire project.

---


## 16. BBP-AST — Code Representation

### 16.1 Philosophy: Code as Typed Structure, Not Text

LLMs that process code as text — sequences of BPE tokens that happen to be syntactically valid in some programming language — are performing an inherently lossy operation. The token `func` carries no intrinsic knowledge that it is a keyword; the model must infer this from positional context. Indentation, punctuation, and syntactic boilerplate consume tokens without contributing semantic information. And the representation is tied to a specific language's surface syntax: the same algorithm looks entirely different in Python, Rust, and Haskell at the token level.

BBP-AST represents code at the level of the **Abstract Syntax Tree** (AST): the universal structural skeleton that all programming languages share beneath their surface syntax. Every programming language has functions, variables, conditionals, loops, assignments, and expressions — they differ only in how these constructs are spelled in text. BBP-AST encodes the constructs directly, using the same 64-bit token format and the same nesting mechanics (START/END, Section 7) that BBP uses for natural language.

**Structural skeleton, not full AST replacement.** BBP-AST's value is not replacing language-specific ASTs but providing a *navigable structural skeleton* that enables cross-language reasoning about program structure. A Core Engine that sees `FUNC_DEF → CONDITIONAL → LOOP_FOR → FUNC_CALL` understands the structural nesting of a program even if some language-specific details are encoded as AST_OPAQUE nodes. This is analogous to how a programmer can understand the structure of code in an unfamiliar language by recognizing function definitions, loops, and conditionals — without understanding every language-specific keyword.

**What BBP-AST enables:** Code search, refactoring, structural diff, dead code detection, complexity analysis, and cross-language structural comparison. These tasks depend on structure, not evaluation semantics. BBP-AST enables these tasks across languages. Full semantic analysis (type checking, formal verification) requires language-specific tooling regardless of representation format.

**What BBP-AST does NOT provide:** Evaluation semantics (lazy vs. strict evaluation), ownership semantics (Rust borrows), or language-specific runtime behavior. These are preserved in AST_OPAQUE nodes for language-aware renderers but are not interpreted by the protocol.

**Code semantic annotations (v1.1 candidate).** To bridge the gap between pure syntax and operational semantics, v1.1 MAY define a small set of code-semantic annotations as optional bound modifier tokens for BBP-AST constructs:

| Annotation | Values | Languages |
|-----------|--------|-----------|
| EVAL_STRATEGY | lazy / strict / by-name / by-reference | Haskell, Scala, Rust |
| OWNERSHIP | owned / borrowed / shared / moved | Rust, C++ |
| MUTABILITY | mutable / immutable / interior-mutable | Rust, Kotlin, Swift |
| PURITY | pure / side-effecting / IO | Haskell, PureScript |

These are optional (zero cost if absent), language-specific (a Rust encoder emits OWNERSHIP; a Python encoder does not), and encoded as UET modifiers preceding the relevant construct. They enrich the representation beyond pure syntax toward operational semantics without changing the 64-bit token format.

**Key properties:**

- **Language-agnostic.** The same BBP-AST stream can be rendered as Python, Rust, JavaScript, Zig, or any target language by a language-specific Code Renderer. The Core Engine reasons about program structure without being bound to any particular syntax.

- **Typed at the token level.** Each token in a BBP-AST stream carries its structural role in the bits: a FUNC_DEF is always a function definition, a LOOP_WHILE is always a while-loop, a MATH_OPERATOR is always an operator. The model does not need to infer structural roles from context.

- **Composable with natural language.** A BBP-AST block can appear inside a natural language BBP stream (e.g., inside an EVIDENCE block in a reasoning chain), and vice versa (e.g., a comment or docstring inside a function definition is a natural language BBP sub-stream). The token format is uniform; the distinction is structural, not format-level.

- **Mechanically derivable from source code.** Unlike natural language → BBP-IR (which requires an LLM for semantic analysis), code → BBP-AST can be largely automated: existing language parsers (Python `ast`, Rust `syn`, JavaScript `acorn`, etc.) produce ASTs that are mechanically convertible to BBP-AST. The Teacher LLM is only needed for semantic enrichment (meaningful names → dictionary IDs, pattern recognition, intent annotation).

### 16.2 CONTROL Payload Registry — Programming Constructs

All programming constructs are encoded as CONTROL UETs (meta-type 0o66) with FEATURES values in the range 0x060–0x07F. This provides 32 slots, of which 24 are defined in v1.0-r7.

Constructs that delimit a scope or body use flow=START/END. Constructs that are atomic use flow=UNIT.

| Payload | Hex | Name | Flow | Description |
|---------|-----|------|------|-------------|
| 96 | 0x060 | FUNC_DEF | START/END | Function or method definition |
| 97 | 0x061 | FUNC_CALL | START/END | Function or method invocation |
| 98 | 0x062 | RETURN | UNIT | Return statement (value follows as next token/block) |
| 99 | 0x063 | YIELD | UNIT | Yield expression (generators, coroutines) |
| 100 | 0x064 | LOOP_FOR | START/END | For / for-each loop |
| 101 | 0x065 | LOOP_WHILE | START/END | While loop |
| 102 | 0x066 | LOOP_BREAK | UNIT | Break from enclosing loop |
| 103 | 0x067 | LOOP_CONTINUE | UNIT | Continue to next iteration |
| 104 | 0x068 | CONDITIONAL | START/END | If / else-if / else block |
| 105 | 0x069 | COND_BRANCH | START/END | A single branch within a CONDITIONAL (condition + body) |
| 106 | 0x06A | COND_ELSE | START/END | The else branch (no condition, body only) |
| 107 | 0x06B | MATCH | START/END | Pattern matching / switch-case block |
| 108 | 0x06C | MATCH_ARM | START/END | A single arm within a MATCH (pattern + body) |
| 109 | 0x06D | ASSIGN | UNIT | Assignment (target precedes, value follows) |
| 110 | 0x06E | VAR_DECL | START/END | Variable declaration (may include type annotation, initializer) |
| 111 | 0x06F | CONST_DECL | START/END | Constant declaration |
| 112 | 0x070 | TYPE_DEF | START/END | Type / struct / record definition |
| 113 | 0x071 | ENUM_DEF | START/END | Enum / union type definition |
| 114 | 0x072 | CLASS_DEF | START/END | Class / trait / interface definition |
| 115 | 0x073 | IMPL_BLOCK | START/END | Implementation block (Rust `impl`, Go methods, etc.) |
| 116 | 0x074 | TRY_CATCH | START/END | Exception handling block |
| 117 | 0x075 | TRY_BODY | START/END | The try body within TRY_CATCH |
| 118 | 0x076 | CATCH_BODY | START/END | A catch/except handler within TRY_CATCH |
| 119 | 0x077 | FINALLY_BODY | START/END | Finally/defer block within TRY_CATCH |
| 120 | 0x078 | IMPORT | UNIT | Import / use / require statement |
| 121 | 0x079 | MODULE | START/END | Module / package / namespace boundary |
| 122 | 0x07A | ASSERT | UNIT | Assertion statement |
| 123 | 0x07B | COMMENT | START/END | Code comment (body is NL BBP sub-stream) |
| 124 | 0x07C | AST_OPAQUE | START/END | Opaque node for non-universal constructs (§16.8) |
| 125 | 0x07D | LAMBDA | START/END | Anonymous function / closure (distinct from FUNC_DEF) |
| 126 | 0x07E | DECORATOR | UNIT | Decorator / annotation (Python `@`, Java `@`, TypeScript) |
| 127 | 0x07F | COMPREHENSION | START/END | List / dict / set / generator comprehension |
| 128 | 0x080 | PATTERN_MATCH_GUARD | START/END | Guard clause in pattern matching arm |
| 129 | 0x081 | PIPE_CHAIN | START/END | Function composition / pipe operator (`|>`, `>>`, `%>%`) |
| 130 | 0x082 | TYPE_CONSTRAINT | START/END | Typeclass constraint / where clause / generic bound |
| 131 | 0x083 | GENERIC_PARAM | START/END | Generic / type parameter declaration |
| 132 | 0x084 | LIFETIME | UNIT | Lifetime annotation (Rust `'a`, Zig comptime) |
| 133 | 0x085 | ASYNC_BLOCK | START/END | Async / await scope (coroutine boundary) |
| 134 | 0x086 | UNSAFE_BLOCK | START/END | Unsafe scope (Rust `unsafe`, C# `unsafe`, Zig `@intCast`) |
| 135 | 0x087 | PROPERTY_ACCESS | UNIT | Property / field access (`.` operator, structural) |
| 136 | 0x088 | INDEX_ACCESS | START/END | Array / map indexing (subscript operator) |
| 137–143 | 0x089–0x08F | *Reserved* | — | Future programming constructs |

**Design note — universality of constructs:** These 38 constructs cover the structural primitives shared across imperative, functional, and object-oriented paradigms. The expansion from r15 (24 constructs) to r16 (38 constructs) significantly reduces AST_OPAQUE usage for functional languages (Haskell, Rust, Scala) and decorator-heavy languages (Python, TypeScript, Java). See §16.8 for AST_OPAQUE density thresholds and Appendix H.6 for mandatory benchmarks.

Language-specific constructs that do not map cleanly to these universals (e.g., Rust's borrow annotations beyond lifetimes, Haskell's do-notation, Python's with-statement as distinct from TRY_CATCH) are encoded as:

- **Annotations:** A MODIFIER UET (0o07) or ADJECTIVE UET (0o02) immediately preceding the construct it annotates. E.g., `@staticmethod` → `[MODIFIER, FEATURES=STATIC_METHOD, UNIT]` before a FUNC_DEF.
- **Type constraints:** UETs within a VAR_DECL or FUNC_DEF block, using LITERAL blocks for complex type expressions.
- **Language-specific keywords:** LANG_* UETs (Group 5) that identify the source/target language, allowing the Code Renderer to apply language-specific semantics during output.

### 16.3 Identifier Representation

Program identifiers (function names, variable names, type names, module names) are represented using **structural context inference** rather than dedicated meta-types. This follows BBP's compositionality principle: the same LITERAL block mechanism used for proper names in natural language serves for program identifiers, with the enclosing CONTROL construct providing the type information.

**Rule:** A LITERAL block (or NAME_WORK UET with inline FEATURES) immediately inside a programming CONTROL construct is interpreted as:

| Enclosing construct | Identifier role |
|---|---|
| FUNC_DEF (first identifier) | Function name |
| FUNC_DEF (subsequent identifiers) | Parameter names |
| FUNC_CALL (first identifier) | Called function name |
| FUNC_CALL (subsequent elements) | Arguments |
| VAR_DECL / CONST_DECL (first identifier) | Variable / constant name |
| TYPE_DEF / ENUM_DEF (first identifier) | Type name |
| CLASS_DEF (first identifier) | Class name |
| IMPL_BLOCK (first identifier) | Implemented type name |
| MODULE (first identifier) | Module name |
| IMPORT (identifier) | Imported name |

**Identifier encoding options:**

1. **LITERAL block** (for arbitrary-length names):
   ```
   [NAME_WORK, flow=LITERAL, FEATURES=9]       → 9 bytes raw
   [raw: "factorial" + 3 bytes padding]        → (12 bytes on wire)
   [NAME_WORK, flow=END, case=ABS]
   ```

2. **Dictionary ID** (for common standard library names):
   A DICT_WORD or NAME_WORK with flow=UNIT and a FEATURES pointing to a code vocabulary (using lemma IDs if needed). Standard library functions, common variable names (`i`, `n`, `result`, `self`, `err`), and language keywords can be assigned stable dictionary IDs via VOCAB_PAGE, enabling inline encoding without LITERAL overhead.

3. **REFERENCE** (for subsequent uses of an already-defined name):
   After the first occurrence of an identifier (in its defining construct), all subsequent uses SHOULD use a REFERENCE UET (0o61) pointing back to the definition. This mirrors coreference in natural language and provides O(1) identifier resolution.

**Scope tracking:** The parser maintains a scope stack (parallel to the nesting depth stack from Section 7). Each FUNC_DEF, CLASS_DEF, MODULE, LOOP_*, and CONDITIONAL START pushes a new scope. The corresponding END pops it. REFERENCE targets are resolved within the current scope chain, walking outward to enclosing scopes — identical to lexical scoping in programming languages.

### 16.4 Type Annotations

Type annotations in statically typed languages are encoded as UETs within the construct they annotate. BBP-AST provides two mechanisms:

**Inline primitive types:** A CODE_TYPE identifier using a small set of FEATURES values for universal primitive types:

| Payload | Type | Languages |
|---------|------|-----------|
| 0 | void / unit / None | C, Rust, Python, Zig |
| 1 | bool / boolean | Universal |
| 2 | i8 / int8 / byte | C, Rust, Zig, Java |
| 3 | i16 / int16 / short | C, Rust, Zig, Java |
| 4 | i32 / int / i32 | C, Rust, Zig, Python |
| 5 | i64 / long / i64 | C, Rust, Zig, Java |
| 6 | u8 / uint8 / unsigned char | C, Rust, Zig |
| 7 | u16 / uint16 / unsigned short | C, Rust, Zig |
| 8 | u32 / uint32 / unsigned int | C, Rust, Zig |
| 9 | u64 / uint64 / unsigned long | C, Rust, Zig |
| 10 | f32 / float | C, Rust, Zig, Python |
| 11 | f64 / double | C, Rust, Zig, Python |
| 12 | char / rune | C, Rust, Go, Zig |
| 13 | string / str / String | Universal |
| 14 | array / list / slice | Universal (parameterized) |
| 15 | map / dict / HashMap | Universal (parameterized) |
| 16 | optional / Option / Maybe | Rust, Haskell, Swift, Zig |
| 17 | result / Result / Either | Rust, Haskell, Zig |
| 18 | pointer / reference | C, Rust, Zig, Go |
| 19 | function / closure / Fn | Universal (higher-order) |

These are encoded as MODIFIER UETs (0o07) with FEATURES values from a code-type vocabulary page, immediately preceding or following the identifier they annotate.

**Complex parameterized types:** Use TYPE_EXPR blocks (0o26, flow=START/END) to compose parameterized types, with TYPE_PARAM (0o27) children for type arguments. This replaces the former MATH_EXPRESSION workaround, which is deprecated for type annotation positions (see Axiom E13).

```
— "Vec<Option<i32>>" —

Correct encoding:
[TYPE_EXPR, flow=START]           → Vec<…>
  [MODIFIER, FEATURES=ARRAY_TYPE]
  [TYPE_EXPR, flow=START]         → Option<…>
    [MODIFIER, FEATURES=OPTIONAL_TYPE]
    [TYPE_PARAM, FEATURES=I32_TYPE, flow=UNIT]    → i32
  [TYPE_EXPR, flow=END]
[TYPE_EXPR, flow=END]

Deprecated encoding (MINOR warning per Axiom E13):
[MATH_EXPRESSION, flow=START]
  [MODIFIER, FEATURES=ARRAY_TYPE]
  [MATH_EXPRESSION, flow=START]
    [MODIFIER, FEATURES=OPTIONAL_TYPE]
    [MODIFIER, FEATURES=I32_TYPE]
  [MATH_EXPRESSION, flow=END]
[MATH_EXPRESSION, flow=END]
```

**Semantics of TYPE_EXPR (0o26):** A block (flow=START/END) that wraps a compound parameterized type expression. A TYPE_EXPR block contains a base type (MODIFIER UET with a code-type FEATURES value) followed by zero or more TYPE_PARAM or nested TYPE_EXPR tokens for type arguments. Type argument ordering follows source-language declaration order (left-to-right for most languages, consistent with generic bracket notation `F<A, B>`).

**Semantics of TYPE_PARAM (0o27):** An atomic type parameter or type variable. In the context of a TYPE_EXPR, it represents a concrete type argument (e.g., `i32` in `Vec<i32>`). In the context of a FUNC_DEF or TYPE_DEF with a GENERIC_PARAM block, it represents an abstract type variable (e.g., `T` in `fn identity<T>(x: T) -> T`).

TYPE_PARAM uses flow=UNIT for concrete types (FEATURES points to the primitive type table above, or to a lemma ID for named types). For abstract type variables (generic parameters), TYPE_PARAM uses flow=LITERAL to carry the variable name.

**Generic type variable declaration** in a GENERIC_PARAM block:

```
— fn identity<T>(x: T) -> T  (Rust/Zig/Haskell) —
[CONTROL, FUNC_DEF, flow=START]
  [NAME_WORK, LITERAL, "identity", ...]             → function name

  [CONTROL, GENERIC_PARAM, flow=START]              → <T>
    [TYPE_PARAM, flow=LITERAL, FEATURES=1]          → 1-byte literal: "T"
    [raw: 0x54 0x00 0x00 0x00 0x00 0x00 0x00 0x00]
    [TYPE_PARAM, flow=END]
  [CONTROL, GENERIC_PARAM, flow=END]

  [NAME_WORK, LITERAL, "x", UNIT]                   → parameter name
  [TYPE_PARAM, flow=LITERAL, FEATURES=1]            → param type: T (ref to generic)
  [raw: 0x54 ...]
  [TYPE_PARAM, flow=END]

  [TYPE_PARAM, flow=LITERAL, FEATURES=1]            → return type: T
  [raw: 0x54 ...]
  [TYPE_PARAM, flow=END]
  — body —
[CONTROL, FUNC_DEF, flow=END]
```

### 16.5 Expression Representation

Expressions in BBP-AST reuse the existing Mathematics meta-types (Group 2) extensively. Arithmetic, comparison, and logical operations use MATH_OPERATOR (0o22). Numeric literals use NUM_* (Group 1). The nesting mechanics of MATH_EXPRESSION (0o23) represent operator precedence and grouping.

**Operator FEATURES registry extension for programming:**

| Payload | Operator | Description |
|---------|----------|-------------|
| 0–15 | (existing) | +, −, ×, ÷, =, <, >, ≤, ≥, ≠, ... |
| 16 | `==` | Equality comparison (distinct from assignment) |
| 17 | `!=` | Inequality |
| 18 | `&&` / `and` | Logical AND |
| 19 | `\|\|` / `or` | Logical OR |
| 20 | `!` / `not` | Logical NOT |
| 21 | `&` | Bitwise AND |
| 22 | `\|` | Bitwise OR |
| 23 | `^` | Bitwise XOR |
| 24 | `~` | Bitwise NOT |
| 25 | `<<` | Left shift |
| 26 | `>>` | Right shift |
| 27 | `%` / `mod` | Modulo |
| 28 | `**` / `pow` | Exponentiation |
| 29 | `..` | Range |
| 30 | `.` | Member access |
| 31 | `::` | Scope resolution / namespace |
| 32 | `->` | Arrow (return type, lambda) |
| 33 | `=>` | Fat arrow (match arm, lambda) |
| 34 | `?` | Error propagation (Rust `?`, Zig `try`) |
| 35 | `[]` | Index access |
| 36 | `as` | Type cast |

### 16.6 Nesting Conventions and Scope

BBP-AST uses the same START/END stack mechanics defined in Section 7. Programming constructs nest naturally:

```
[CONTROL, MODULE, flow=START]                              depth 0→1
  [NAME_WORK, LITERAL, "math_utils", ...]                  depth 1

  [CONTROL, FUNC_DEF, flow=START]                          depth 1→2
    [NAME_WORK, LITERAL, "factorial", ...]                 depth 2  (func name)
    [NAME_WORK, LITERAL, "n", UNIT]                        depth 2  (param)
    [MODIFIER, FEATURES=U64_TYPE]                           depth 2  (param type)

    [CONTROL, CONDITIONAL, flow=START]                     depth 2→3
      [CONTROL, COND_BRANCH, flow=START]                   depth 3→4
        — condition —
        [REFERENCE, FEATURES→"n"]                           depth 4
        [MATH_OPERATOR, FEATURES=≤]                         depth 4
        [NUM_NATURAL, FEATURES=1]                           depth 4
        — body —
        [CONTROL, RETURN, flow=UNIT]                       depth 4
        [NUM_NATURAL, FEATURES=1]                           depth 4
      [CONTROL, COND_BRANCH, flow=END]                     depth 4→3
    [CONTROL, CONDITIONAL, flow=END]                       depth 3→2

    [CONTROL, RETURN, flow=UNIT]                           depth 2
    [MATH_EXPRESSION, flow=START]                          depth 2→3
      [REFERENCE, FEATURES→"n"]                             depth 3
      [MATH_OPERATOR, FEATURES=×]                           depth 3
      [CONTROL, FUNC_CALL, flow=START]                     depth 3→4
        [REFERENCE, FEATURES→"factorial"]                   depth 4
        [MATH_EXPRESSION, flow=START]                      depth 4→5
          [REFERENCE, FEATURES→"n"]                         depth 5
          [MATH_OPERATOR, FEATURES=−]                       depth 5
          [NUM_NATURAL, FEATURES=1]                         depth 5
        [MATH_EXPRESSION, flow=END]                        depth 5→4
      [CONTROL, FUNC_CALL, flow=END]                       depth 4→3
    [MATH_EXPRESSION, flow=END]                            depth 3→2

  [CONTROL, FUNC_DEF, flow=END]                            depth 2→1
[CONTROL, MODULE, flow=END]                                depth 1→0
```

**Rendering:** A Code Renderer targeting Python would produce:

```python
# module: math_utils
def factorial(n: int) -> int:
    if n <= 1:
        return 1
    return n * factorial(n - 1)
```

The same BBP-AST targeting Zig would produce:

```zig
// module: math_utils
fn factorial(n: u64) u64 {
    if (n <= 1) return 1;
    return n * factorial(n - 1);
}
```

The same BBP-AST targeting Rust would produce:

```rust
// module: math_utils
fn factorial(n: u64) -> u64 {
    if n <= 1 {
        return 1;
    }
    n * factorial(n - 1)
}
```

### 16.7 Paradigm Coverage

BBP-AST is designed to cover three major programming paradigms through composition of the 24 defined constructs.

#### 16.7.1 Imperative / Procedural

The core constructs (FUNC_DEF, ASSIGN, VAR_DECL, LOOP_*, CONDITIONAL, RETURN) directly model imperative programming. Mutable state is represented by ASSIGN tokens that target previously declared variables via REFERENCE.

```
— "x = x + 1" —
[CONTROL, ASSIGN, flow=UNIT]
[REFERENCE, FEATURES→"x"]                  → target (left-hand side)
[MATH_EXPRESSION, flow=START]
  [REFERENCE, FEATURES→"x"]                → current value
  [MATH_OPERATOR, FEATURES=+]
  [NUM_NATURAL, FEATURES=1]
[MATH_EXPRESSION, flow=END]
```

#### 16.7.2 Object-Oriented

CLASS_DEF delimits a class body. Methods are FUNC_DEF blocks inside a CLASS_DEF. IMPL_BLOCK handles the Rust/Go pattern of separate implementation. Member access uses MATH_OPERATOR with FEATURES=`.` (member access).

```
— Class definition with method —
[CONTROL, CLASS_DEF, flow=START]
  [NAME_WORK, LITERAL, "Point", ...]                → class name

  [CONTROL, VAR_DECL, flow=START]                    → field
    [NAME_WORK, LITERAL, "x", UNIT]
    [MODIFIER, FEATURES=F64_TYPE]
  [CONTROL, VAR_DECL, flow=END]

  [CONTROL, VAR_DECL, flow=START]                    → field
    [NAME_WORK, LITERAL, "y", UNIT]
    [MODIFIER, FEATURES=F64_TYPE]
  [CONTROL, VAR_DECL, flow=END]

  [CONTROL, FUNC_DEF, flow=START]                    → method
    [NAME_WORK, LITERAL, "distance_to", ...]
    [NAME_WORK, LITERAL, "other", UNIT]              → parameter
    [REFERENCE, FEATURES→"Point"]                     → parameter type
    [MODIFIER, FEATURES=F64_TYPE]                     → return type
    — body —
    [CONTROL, RETURN, flow=UNIT]
    [CONTROL, FUNC_CALL, flow=START]
      [NAME_WORK, LITERAL, "sqrt", UNIT]
      [MATH_EXPRESSION, flow=START]
        — (self.x - other.x)² + (self.y - other.y)² —
        — ... (composed from MATH_OPERATOR, REFERENCE, member access) —
      [MATH_EXPRESSION, flow=END]
    [CONTROL, FUNC_CALL, flow=END]
  [CONTROL, FUNC_DEF, flow=END]

[CONTROL, CLASS_DEF, flow=END]
```

#### 16.7.3 Functional

Functional patterns are naturally expressed through BBP-AST's existing composition mechanics:

- **Higher-order functions:** FUNC_CALL where an argument is itself a FUNC_DEF (lambda/closure). The FUNC_DEF has no name identifier — an anonymous FUNC_DEF inside a FUNC_CALL is a lambda.
- **Pattern matching:** MATCH / MATCH_ARM constructs.
- **Immutability:** CONST_DECL instead of VAR_DECL.
- **Recursion:** FUNC_CALL with REFERENCE pointing to the enclosing FUNC_DEF (self-reference).
- **Pipe / composition:** Sequential FUNC_CALL blocks where each uses the result of the previous as input.

```
— "list.map(|x| x * 2).filter(|x| x > 10)" —
[CONTROL, FUNC_CALL, flow=START]                     → .filter(...)
  [MATH_OPERATOR, FEATURES=. (member)]
  [CONTROL, FUNC_CALL, flow=START]                   → .map(...)
    [MATH_OPERATOR, FEATURES=. (member)]
    [REFERENCE, FEATURES→"list"]
    [NAME_WORK, LITERAL, "map", UNIT]
    [CONTROL, FUNC_DEF, flow=START]                  → lambda |x| x * 2
      [NAME_WORK, LITERAL, "x", UNIT]               → param (no func name = lambda)
      [MATH_EXPRESSION, flow=START]
        [REFERENCE, FEATURES→"x"]
        [MATH_OPERATOR, FEATURES=×]
        [NUM_NATURAL, FEATURES=2]
      [MATH_EXPRESSION, flow=END]
    [CONTROL, FUNC_DEF, flow=END]
  [CONTROL, FUNC_CALL, flow=END]
  [NAME_WORK, LITERAL, "filter", UNIT]
  [CONTROL, FUNC_DEF, flow=START]                    → lambda |x| x > 10
    [NAME_WORK, LITERAL, "x", UNIT]
    [MATH_EXPRESSION, flow=START]
      [REFERENCE, FEATURES→"x"]
      [MATH_OPERATOR, FEATURES=>]
      [NUM_NATURAL, FEATURES=10]
    [MATH_EXPRESSION, flow=END]
  [CONTROL, FUNC_DEF, flow=END]
[CONTROL, FUNC_CALL, flow=END]
```

### 16.8 Opaque Nodes for Non-Universal Constructs

BBP-AST's 24 universal programming constructs (Section 16.2) cover structural primitives shared across paradigms. However, some language-specific features resist decomposition into universal constructs without significant semantic loss. Examples include Rust lifetime annotations, Haskell type classes, Lisp macros, advanced Python decorators with arguments, and C++ template metaprogramming.

For these cases, BBP-AST provides the **AST_OPAQUE** mechanism: a typed container that preserves AST navigability while encapsulating language-specific content that the protocol does not interpret.

#### 16.8.1 CONTROL Payload Assignment

| Payload | Name | Description |
|---------|------|-------------|
| 0x07C | AST_OPAQUE | Opaque node wrapper for non-universal constructs |

*This consumes one of the four reserved slots (0x07C–0x07F) in the programming construct range.*

#### 16.8.2 Token Format

```
Token N:   [CONTROL, AST_OPAQUE(0x07C), flow=START]
Token N+1: [LANG_*, flow=UNIT, FEATURES=language_id]        → source language
Token N+2: [DICT_WORD or LITERAL, ...]                     → node_kind identifier
  ... opaque content: valid BBP tokens or LITERAL blocks ...
Token M:   [CONTROL, AST_OPAQUE(0x07C), flow=END]
```

**Structure rules:**

- The FIRST content token inside an AST_OPAQUE block MUST be a LANG_* UET (Group 5) identifying the source language. This allows renderers to select language-specific handling even when the node content is opaque.
- The SECOND content token SHOULD identify the `node_kind` — the language-specific construct type. This may be a DICT_WORD with a FEATURES from a code-specific vocabulary (e.g., "lifetime", "decorator", "macro"), or a LITERAL block for arbitrary identifiers.
- Subsequent content MAY be any valid BBP tokens. If the internal structure of the construct is partially representable in universal BBP-AST, it SHOULD be encoded as such. Only the truly non-universal portion should be opaque.
- An optional `payload_hash` (a NUM_NATURAL with the hash of the original source fragment) MAY follow the node_kind token, enabling external systems to retrieve the original source.

#### 16.8.3 Rendering Rule

A Code Renderer that does not recognize the `node_kind` of an AST_OPAQUE block MUST produce a degraded but readable output:

1. Emit a comment identifying the language and node_kind.
2. Render the opaque content as plain text (LITERAL blocks as UTF-8 strings, other BBP tokens via default rendering).
3. MUST NOT silently discard the opaque block.

**Example — Rust lifetime annotation `'a`:**

```
[CONTROL, AST_OPAQUE(0x07C), flow=START]
  [LANG_COMPILED, flow=UNIT, FEATURES=3]                → Rust (ID=3)
  [DICT_WORD, flow=UNIT, FEATURES=LIFETIME_ID]          → node_kind: "lifetime"
  [NAME_WORK, flow=LITERAL, FEATURES=2]
  [raw: "'a" + 2 pad]
  [NAME_WORK, flow=END, case=UNMARKED]
[CONTROL, AST_OPAQUE(0x07C), flow=END]
```

A Rust-aware renderer would produce `'a`. A generic renderer would produce `/* Rust:lifetime */ 'a`.

**Example — Python decorator with arguments:**

```
[CONTROL, AST_OPAQUE(0x07C), flow=START]
  [LANG_INTERPRETED, flow=UNIT, FEATURES=1]             → Python (ID=1)
  [DICT_WORD, flow=UNIT, FEATURES=DECORATOR_ID]         → node_kind: "decorator"
  [CONTROL, FUNC_CALL, flow=START]                     → @app.route("/api")
    [NAME_WORK, LITERAL, "app", ...]
    [MATH_OPERATOR, FEATURES=. (member)]
    [NAME_WORK, LITERAL, "route", ...]
    [NAME_WORK, LITERAL, "/api", ...]
  [CONTROL, FUNC_CALL, flow=END]
[CONTROL, AST_OPAQUE(0x07C), flow=END]
[CONTROL, FUNC_DEF, flow=START]                        → the decorated function
  ...
[CONTROL, FUNC_DEF, flow=END]
```

Note that the decorator's internal structure (a FUNC_CALL) IS representable in universal BBP-AST, so it is encoded as such inside the AST_OPAQUE wrapper. Only the *semantic relationship* between the decorator and the function (which is language-specific) requires the opaque container.

#### 16.8.4 Validation

Validation rules for AST_OPAQUE are defined as Axioms E11–E12 in Section 13.2.1 (Axiom Group E).

### 16.9 Error Handling Patterns

The TRY_CATCH / TRY_BODY / CATCH_BODY / FINALLY_BODY constructs cover both exception-based (Python, Java, JavaScript) and value-based (Rust `Result`, Zig `errdefer`, Go `if err != nil`) error handling patterns.

**Exception-based:**

```
[CONTROL, TRY_CATCH, flow=START]
  [CONTROL, TRY_BODY, flow=START]
    — code that may throw —
  [CONTROL, TRY_BODY, flow=END]
  [CONTROL, CATCH_BODY, flow=START]
    [NAME_WORK, LITERAL, "ValueError", ...]          → exception type
    [NAME_WORK, LITERAL, "e", UNIT]                  → bound name
    — handler body —
  [CONTROL, CATCH_BODY, flow=END]
  [CONTROL, FINALLY_BODY, flow=START]
    — cleanup —
  [CONTROL, FINALLY_BODY, flow=END]
[CONTROL, TRY_CATCH, flow=END]
```

**Value-based (Rust/Zig style):** The `?` operator (MATH_OPERATOR FEATURES=34) propagates errors inline. For explicit matching on Result/Option, use MATCH/MATCH_ARM:

```
— "let value = try_parse(input)?" —
[CONTROL, VAR_DECL, flow=START]
  [NAME_WORK, LITERAL, "value", UNIT]
  [CONTROL, FUNC_CALL, flow=START]
    [NAME_WORK, LITERAL, "try_parse", UNIT]
    [REFERENCE, FEATURES→"input"]
  [CONTROL, FUNC_CALL, flow=END]
  [MATH_OPERATOR, FEATURES=? (error_propagation)]
[CONTROL, VAR_DECL, flow=END]
```

### 16.10 Embedded Natural Language

Code comments and documentation strings are natural language content embedded within BBP-AST. The COMMENT construct (0x07B) delimits this boundary:

```
[CONTROL, COMMENT, flow=START]
  — Standard BBP natural language tokens —
  [DICT_WORD, "calculate", ERG, SINGULAR, DEF]
  [DICT_WORD, "factorial", ABS, SINGULAR, INDEF]
  [CONTROL, SENTENCE_END, flow=UNIT]
[CONTROL, COMMENT, flow=END]
```

This means the Core Engine can reason about code documentation using the same mechanisms it uses for natural language — and a Frontier Translator can render comments in any human language from the same BBP representation.

### 16.11 Code Frontier Translator Pipeline

The conversion pipeline from source code to BBP-AST is substantially more mechanical than the natural language pipeline:

```
Source code (text)
        │
        ▼
┌──────────────────┐
│ Language Parser   │   Standard parser (Python ast, Rust syn,
│ (deterministic)   │   JS acorn, Zig AST, etc.)
│                   │   Output: language-specific AST
└────────┬─────────┘
         │  Language AST (JSON/struct)
         ▼
┌──────────────────┐
│ AST Normalizer    │   Deterministic mapping:
│ (deterministic)   │   lang-AST nodes → BBP CONTROL constructs
│                   │   Identifier extraction → LITERAL blocks
│                   │   Type annotations → MODIFIER UETs
│                   │   Expressions → MATH_* composition
└────────┬─────────┘
         │  BBP-AST IR (JSON, Section 14 format)
         ▼
┌──────────────────┐
│ Semantic Enricher │   Teacher LLM (optional, for training corpus):
│ (LLM-assisted)    │   - Map identifier names to vocabulary IDs
│                   │   - Detect design patterns
│                   │   - Generate NL descriptions for COMMENT blocks
│                   │   - Annotate intent / purpose metadata
└────────┬─────────┘
         │  Enriched BBP-AST IR
         ▼
┌──────────────────┐
│ Validator +       │   Same pipeline as NL BBP:
│ Compiler          │   axiom checking → bit packing
└──────────────────┘
```

**Key difference from NL pipeline:** Steps 1 and 2 are fully deterministic — no LLM required. Step 3 is optional and only needed for training corpus generation (to provide semantic annotations). For production use (code → BBP-AST for Core Engine consumption), the pipeline is entirely mechanical.

#### 16.11.1 Verbosity Expectations

BBP-AST trades token count for structural explicitness. For code with minimal type annotations (e.g., Python scripts), BBP-AST is typically 0.7–0.9× the source token count. For heavily-decorated modern code (e.g., Rust with async, pub, lifetime annotations, derive macros), BBP-AST can be 1.2–1.5× the source token count. The Core Engine does not benefit from compression per se — it benefits from knowing that token 47 is a LOOP_WHILE without parsing context.

Estimated ratios (to be validated by Appendix H benchmarks):

| Language style | Source tokens | BBP-AST tokens | Ratio |
|----------------|--------------|----------------|-------|
| Python (simple scripts) | 100 | ~80 | 0.8× |
| Python (with decorators) | 100 | ~110 | 1.1× |
| Rust (idiomatic, typed) | 100 | ~140 | 1.4× |
| TypeScript (with generics) | 100 | ~130 | 1.3× |
| C (legacy, minimal types) | 100 | ~70 | 0.7× |

### 16.12 BBP-AST Validation Rules

Added to Section 13.2.1 as **Axiom Group E — Code Structural Consistency:**

```
E1: Every FUNC_DEF block MUST contain at least one identifier
    (the function name), except for anonymous functions (lambdas)
    inside FUNC_CALL arguments.
    SEVERITY: MAJOR (warning)

E2: Every FUNC_CALL block MUST contain at least one identifier
    or REFERENCE as its first element (the called function).
    SEVERITY: CRITICAL (error)

E3: A RETURN or YIELD token MUST be inside a FUNC_DEF block
    (at any nesting depth).
    SEVERITY: CRITICAL (error)

E4: A LOOP_BREAK or LOOP_CONTINUE token MUST be inside a
    LOOP_FOR or LOOP_WHILE block (at any nesting depth).
    SEVERITY: CRITICAL (error)

E5: A CONDITIONAL block MUST contain at least one COND_BRANCH.
    SEVERITY: CRITICAL (error)

E6: A COND_ELSE block MUST be the last child of a CONDITIONAL
    block and MUST NOT have a condition expression.
    SEVERITY: MAJOR (warning)

E7: A MATCH block MUST contain at least one MATCH_ARM.
    SEVERITY: CRITICAL (error)

E8: A TRY_CATCH block MUST contain exactly one TRY_BODY and
    at least one CATCH_BODY or FINALLY_BODY.
    SEVERITY: CRITICAL (error)

E9: A REFERENCE inside a BBP-AST block SHOULD point to an
    identifier defined in the current scope chain.
    SEVERITY: MINOR (warning — may reference external symbols)

E10: ASSIGN MUST be followed by exactly two elements: a target
     (REFERENCE or member-access expression) and a value
     (expression, literal, or FUNC_CALL).
     SEVERITY: MAJOR (warning)

E11: An AST_OPAQUE block (CONTROL 0x07C) MUST contain at least
     one LANG_* UET (Group 5) as its first content token.
     An AST_OPAQUE without a language identifier is invalid.
     SEVERITY: MAJOR (error)

E12: An AST_OPAQUE block SHOULD contain a node_kind identifier
     as its second content token (DICT_WORD or LITERAL).
     Absence triggers a warning but is not an error (the block
     is still navigable, just less informative).
     SEVERITY: MINOR (warning)
```

---

## 17. BBP-CoT — Structured Chain of Thought

### 17.1 Philosophy: Explicit Epistemic Status

When a conventional LLM "reasons," it produces a chain of natural language sentences where each step is superficially identical: plain text. The model must infer the epistemic status of each step (hypothesis? conclusion? retraction? evidence?) from contextual clues in the prose. This inference is itself subject to error, and the lack of explicit structure makes it impossible for external systems to formally verify the reasoning chain.

BBP-CoT (Chain of Thought) makes the epistemic status of every reasoning step **explicit in the token structure**. Each step is wrapped in a typed meta-cognitive block — HYPOTHESIS, CONCLUSION, EVIDENCE, RETRACTION — that declares its role in the argument. The relationships between steps (this CONCLUSION follows from that EVIDENCE; this RETRACTION invalidates that HYPOTHESIS) are encoded as structural references, not as prose to be parsed.

**Core insight:** This is the same move BBP makes for natural language grammar. Just as BBP makes "who does what to whom" explicit in the case system rather than leaving it to word-order inference, BBP-CoT makes "what is being claimed, on what basis, and with what certainty" explicit in the meta-cognitive type system rather than leaving it to textual inference.

**Scope of verifiability.** BBP-CoT provides **structural transparency**, not **logical verification**. The typed block structure (HYPOTHESIS → EVIDENCE → INFERENCE_STEP → CONCLUSION) enables external tools to reconstruct the topology of a reasoning chain and verify its *structural* consistency: every CONCLUSION has precedent EVIDENCE, every RETRACTION points to a valid target, every CONFIDENCE is attached to a block. It does NOT guarantee that the *content* of EVIDENCE logically entails the *content* of CONCLUSION. Semantic validity remains a property of the model, not the protocol. The protocol's contribution is making the reasoning structure machine-parseable for auditing, debugging, and partial formal verification.

**Key properties:**

- **Structurally verifiable.** If a CONCLUSION claims to follow from EVIDENCE blocks, an external verifier can check whether the evidence *structurally* supports the conclusion — the blocks are correctly linked, the types match, the references resolve — because both are typed, structured BBP, not prose. Semantic entailment remains a model property.

- **Retraction is a first-class operation.** LLMs that reason in text sometimes "take back" a claim, but there is no structural signal — the reader must notice a contradiction. In BBP-CoT, RETRACTION is an explicit token that points (via REFERENCE) to the retracted block. The model's reasoning graph is acyclic and auditable.

- **Confidence is quantified, not described.** Instead of "I'm fairly sure that..." (what does "fairly" mean?), a CONFIDENCE UET carries a numeric value that downstream systems can threshold, aggregate, or propagate through inference chains.

- **Homogeneous with BBP-NL and BBP-AST.** A reasoning chain can contain natural language propositions (BBP-NL), code (BBP-AST), mathematical expressions, or any combination — all in the same 64-bit token stream, all structurally navigable. There is no mode-switching between "thinking" and "programming."

- **Mathematical evidence is exact, not probabilistic.** When a reasoning chain includes EVIDENCE[COMPUTATION] blocks containing MATH_EXPRESSION[EXECUTABLE] tokens (§10.3), the runtime evaluates those expressions deterministically and inserts guaranteed-correct results. This means the *numerical* component of a reasoning chain can be formally verified — the model learns *when* and *why* to compute, while the runtime guarantees *that* the computation is correct. See §10.3 and §17.4 for the normative specification of this interaction.

### 17.2 Meta-Cognitive Primitives — Detailed Specification

All meta-cognitive primitives are UETs from Group 7 (0o7x). They use the standard 64-bit Entity Token layout (LEMMA 32b + FEATURES 32b). Within FEATURES: bits 30–31=flow (2b), bits 25–29=case (5b), and bits 0–24 (the standard Deklinabidea fields: number, determiner, classifier, animacy, noun_class, register) are **reinterpreted** as the meta-cognitive payload specific to each primitive. When not reinterpreted, Deklinabidea fields SHOULD be UNMARKED (all zeros).

#### 17.2.1 HYPOTHESIS (0o70) — Tentative Proposition

A proposition that the model puts forward as a candidate for truth, pending evaluation.

**Payload encoding (12 bits):**

```
┌──────────────────────────────────────────────────┐
│          HYPOTHESIS Payload (12 bits)              │
├──────────────┬───────────────┬────────────────────┤
│  Bits 8-11   │  Bits 4-7     │  Bits 0-3          │
│  Sub-type(4b)│  Priority (4b)│  Local ID (4b)     │
└──────────────┴───────────────┴────────────────────┘
```

**Hypothesis sub-type (4 bits, bits 8-11):**

| Code | Sub-type | Description |
|------|----------|-------------|
| 0 | PROPOSITION | General claim ("X is true") |
| 1 | ASSUMPTION | Assumed for sake of argument ("Suppose X") |
| 2 | PREDICTION | Expected future outcome ("X will happen") |
| 3 | ALTERNATIVE | One option among several ("Or perhaps X") |
| 4 | COUNTERFACTUAL | Contrary-to-fact ("If X were true...") |
| 5–15 | *Reserved* | Future hypothesis types |

**Priority (4 bits, bits 4-7):** Relative importance in a set of competing hypotheses (0=lowest, 15=highest). Default 0 when not ranking.

**Local ID (4 bits, bits 0-3):** A local identifier (0-15) for this hypothesis within the current reasoning scope. Used by RETRACTION and EVIDENCE to reference specific hypotheses without a full REFERENCE backpointer (for short-range references). For long-range references, use a REFERENCE UET.

**Usage:**

```
[HYPOTHESIS, flow=START, FEATURES=0x001]    → PROPOSITION, priority=0, id=1
  — BBP content: the hypothesized proposition —
[HYPOTHESIS, flow=END]
```

#### 17.2.2 CONCLUSION (0o71) — Derived Proposition

A proposition that the model asserts as following from prior evidence or inference.

**Payload encoding (12 bits):**

```
┌──────────────────────────────────────────────────┐
│          CONCLUSION Payload (12 bits)              │
├──────────────┬───────────────┬────────────────────┤
│  Bits 8-11   │  Bits 4-7     │  Bits 0-3          │
│  Strength(4b)│  Basis ct (4b)│  Local ID (4b)     │
└──────────────┴───────────────┴────────────────────┘
```

**Strength (4 bits, bits 8-11):**

| Code | Strength | Description |
|------|----------|-------------|
| 0 | TENTATIVE | Preliminary conclusion, may be revised |
| 1 | MODERATE | Supported but not fully established |
| 2 | STRONG | Well-supported by multiple evidence |
| 3 | DEFINITIVE | Logically necessary / proven |
| 4–15 | *Reserved* | Future strength levels |

**Basis count (4 bits, bits 4-7):** Number of supporting EVIDENCE / INFERENCE_STEP blocks that precede this CONCLUSION (0-15). Informational; the Validator cross-checks against actual preceding evidence.

**Local ID (4 bits, bits 0-3):** Local identifier for reference by subsequent RETRACTION or EVIDENCE.

#### 17.2.3 RETRACTION (0o72) — Invalidation

Explicitly invalidates a prior HYPOTHESIS or CONCLUSION. RETRACTION is always followed by a REFERENCE UET (0o61) that points to the retracted block's opening token.

**Payload encoding (12 bits):**

```
┌──────────────────────────────────────────────────┐
│          RETRACTION Payload (12 bits)              │
├──────────────┬────────────────────────────────────┤
│  Bits 8-11   │  Bits 0-7                          │
│  Reason (4b) │  Target local ID (8b)              │
└──────────────┴────────────────────────────────────┘
```

**Retraction reason (4 bits, bits 8-11):**

| Code | Reason | Description |
|------|--------|-------------|
| 0 | CONTRADICTED | New evidence contradicts the claim |
| 1 | SUPERSEDED | A better explanation replaces it |
| 2 | INSUFFICIENT | The supporting evidence was inadequate |
| 3 | ERROR | The reasoning contained a logical error |
| 4 | SCOPE_CHANGE | The claim was valid in a different context |
| 5–15 | *Reserved* | Future retraction reasons |

**Target local ID (8 bits, bits 0-7):** The local ID of the HYPOTHESIS or CONCLUSION being retracted (expanded to 8 bits for wider range than emission IDs). For targets beyond local ID range, a REFERENCE UET MUST follow the RETRACTION.

**Structural rule:** A RETRACTION MUST be followed by a REFERENCE UET whose FEATURES points to the opening token of the retracted block. This provides both human-readable local ID matching and machine-precise backpointer resolution.

```
[RETRACTION, FEATURES=0x003, flow=UNIT]     → reason=CONTRADICTED, target_id=3
[REFERENCE, FEATURES=<token_index>, flow=UNIT]
```

**A RETRACTION does not delete the retracted block from the stream.** It marks it as invalidated. The retracted block remains structurally present for audit trail purposes. A BBP-CoT processor builds a reasoning graph where retracted nodes are flagged but preserved.

**Declarative annotation, not imperative operation.** RETRACTION is a *declarative annotation*, not an imperative operation. Transformers generate tokens left-to-right; a RETRACTION that invalidates a prior HYPOTHESIS does not — and cannot — modify the generating model's hidden states, which already conditioned subsequent generation on the retracted hypothesis. Its value is threefold:

1. **Downstream signaling.** It signals downstream consumers (other agents, reasoning auditors, RAG systems) to exclude the retracted block from the active reasoning context.
2. **Training signal.** During training, the model learns the pattern "when evidence contradicts a hypothesis, emit RETRACTION before proceeding." Over time, this creates a self-correction capability: the model learns to explicitly mark abandoned hypotheses rather than silently diverging.
3. **Transparency.** It enables external inspection of the model's full reasoning trajectory, including dead ends — critical for debugging, auditing, and building trust in AI reasoning.

**Reasoning revision (v1.1 candidate).** A future REASONING_REVISION mode MAY define a two-pass generation mechanism: the first pass produces the raw reasoning chain, the second pass re-processes the chain with retracted blocks masked from attention. This would make RETRACTION genuinely functional at the computational level, at the cost of doubled inference for reasoning-heavy tasks. For v1.0, the declarative annotation is sufficient and honest about its limitations.

#### 17.2.4 EVIDENCE (0o73) — Supporting Material

Marks a block of content as evidential support for a subsequent or enclosing CONCLUSION.

**Payload encoding (12 bits):**

```
┌──────────────────────────────────────────────────┐
│          EVIDENCE Payload (12 bits)                │
├──────────────┬───────────────┬────────────────────┤
│  Bits 8-11   │  Bits 4-7     │  Bits 0-3          │
│  Type (4b)   │  Weight (4b)  │  Local ID (4b)     │
└──────────────┴───────────────┴────────────────────┘
```

**Evidence type (4 bits, bits 8-11):**

| Code | Type | Description |
|------|------|-------------|
| 0 | OBSERVATION | Empirical fact or data point |
| 1 | PREMISE | Assumed true for the argument |
| 2 | CITATION | Reference to external source |
| 3 | COMPUTATION | Result of a calculation or simulation |
| 4 | ANALOGY | Structural similarity to a known case |
| 5 | TESTIMONY | Reported by another agent or source |
| 6 | CONSTRAINT | A rule or invariant that must hold |
| 7 | COUNTEREXAMPLE | Evidence against an alternative |
| 8–15 | *Reserved* | Future evidence types |

**Weight (4 bits, bits 4-7):** Relative evidential weight (0=minimal, 15=decisive). Default 0 when not ranking.

**Content:** The EVIDENCE block (flow=START/END) can contain any valid BBP content: natural language propositions, BBP-AST code blocks, mathematical expressions, REFERENCE to prior blocks, or combinations thereof.

```
[EVIDENCE, flow=START, FEATURES=0x302]    → COMPUTATION, weight=0, id=2
  — BBP content: the evidence —
  [MATH_EXPRESSION, flow=START]
    [NUM_NATURAL, FEATURES=42]
    [MATH_OPERATOR, FEATURES=%]
    [NUM_NATURAL, FEATURES=2]
    [MATH_OPERATOR, FEATURES==]
    [NUM_NATURAL, FEATURES=0]
  [MATH_EXPRESSION, flow=END]
[EVIDENCE, flow=END]
```

#### 17.2.5 QUERY (0o74) — Identified Unknown

The model explicitly identifies something it needs to know but does not currently know. This is valuable for inter-agent communication: an agent can emit a QUERY that another agent or a retrieval system can answer.

**Payload encoding (12 bits):**

```
┌──────────────────────────────────────────────────┐
│          QUERY Payload (12 bits)                   │
├──────────────┬───────────────┬────────────────────┤
│  Bits 8-11   │  Bits 4-7     │  Bits 0-3          │
│  Type (4b)   │  Urgency (4b) │  Local ID (4b)     │
└──────────────┴───────────────┴────────────────────┘
```

**Query type (4 bits, bits 8-11):**

| Code | Type | Description |
|------|------|-------------|
| 0 | FACTUAL | "What is X?" |
| 1 | CONDITIONAL | "What would happen if X?" |
| 2 | PROCEDURAL | "How to do X?" |
| 3 | CLARIFICATION | "What does X mean in this context?" |
| 4 | VERIFICATION | "Is X true?" |
| 5 | RETRIEVAL | "Find information about X" |
| 6–15 | *Reserved* | Future query types |

**Content:** The QUERY block contains the question as BBP content, typically including a PRONOUN with determiner=INTERROGATIVE.

#### 17.2.6 INFERENCE_STEP (0o75) — Explicit Reasoning Rule

Marks the application of a specific inference rule. MUST immediately precede or be contained within a CONCLUSION block.

**Payload encoding (12 bits):**

```
┌──────────────────────────────────────────────────┐
│      INFERENCE_STEP Payload (12 bits)              │
├──────────────┬────────────────────────────────────┤
│  Bits 8-11   │  Bits 0-7                          │
│  Category(4b)│  Rule ID (8b)                      │
└──────────────┴────────────────────────────────────┘
```

**Category (4 bits, bits 8-11):**

| Code | Category | Description |
|------|----------|-------------|
| 0 | DEDUCTIVE | Logical deduction (truth-preserving) |
| 1 | INDUCTIVE | Generalization from instances |
| 2 | ABDUCTIVE | Inference to best explanation |
| 3 | ANALOGICAL | Reasoning by structural similarity |
| 4 | CAUSAL | Cause-effect reasoning |
| 5 | MATHEMATICAL | Mathematical proof step |
| 6 | HEURISTIC | Rule of thumb / approximation |
| 7–15 | *Reserved* | Future categories |

**Rule ID (8 bits, bits 0-7) — Deductive rules (category=0):**

| ID | Rule | Description |
|----|------|-------------|
| 0 | MODUS_PONENS | P, P→Q ⊢ Q |
| 1 | MODUS_TOLLENS | ¬Q, P→Q ⊢ ¬P |
| 2 | DISJUNCTIVE_SYLLOGISM | P∨Q, ¬P ⊢ Q |
| 3 | HYPOTHETICAL_SYLLOGISM | P→Q, Q→R ⊢ P→R |
| 4 | CONJUNCTION | P, Q ⊢ P∧Q |
| 5 | SIMPLIFICATION | P∧Q ⊢ P |
| 6 | ADDITION | P ⊢ P∨Q |
| 7 | RESOLUTION | P∨Q, ¬P∨R ⊢ Q∨R |
| 8 | UNIVERSAL_INSTANTIATION | ∀x.P(x) ⊢ P(a) |
| 9 | EXISTENTIAL_GENERALIZATION | P(a) ⊢ ∃x.P(x) |
| 10 | REDUCTIO_AD_ABSURDUM | Assume ¬P, derive contradiction ⊢ P |
| 11 | CONSTRUCTIVE_DILEMMA | P→Q, R→S, P∨R ⊢ Q∨S |
| 12–255 | *Reserved* | Future rules |

**Rule ID (8 bits) — Mathematical rules (category=5):**

| ID | Rule | Description |
|----|------|-------------|
| 0 | SUBSTITUTION | Replace equals with equals |
| 1 | ALGEBRAIC_SIMPLIFICATION | Apply algebraic identity |
| 2 | INDUCTION_BASE | Base case of mathematical induction |
| 3 | INDUCTION_STEP | Inductive step |
| 4 | CASE_ANALYSIS | Proof by cases |
| 5 | PIGEONHOLE | Pigeonhole principle |
| 6–255 | *Reserved* | Future rules |

Other categories (INDUCTIVE, ABDUCTIVE, etc.) define their own rule ID registries as needed; Rule ID=0 is always "UNSPECIFIED" for categories without a detailed registry.

#### 17.2.7 CONFIDENCE (0o76) — Certainty Annotation

Annotates the immediately preceding or enclosing block with a quantified certainty level and a calibration status that allows downstream systems to assess the reliability of the confidence value.

**Payload encoding (12 bits):**

```
┌──────────────────────────────────────────────────────────┐
│          CONFIDENCE Payload (12 bits)                      │
├──────────────┬───────────────────────────────────────────┤
│  Bits 10-11  │  Bits 0-9                                 │
│  Calib (2b)  │  Confidence value (10b, 0-1023)           │
└──────────────┴───────────────────────────────────────────┘
```

**Calibration status (2 bits, bits 10-11):**

| Code | Status | Description |
|------|--------|-------------|
| `00` | UNCALIBRATED | Model's raw output; interpret with skepticism. Not suitable for automated decision-making. |
| `01` | SELF_ASSESSED | Model's own estimate of certainty; quality unknown. Informational only. |
| `10` | EXTERNALLY_CALIBRATED | Validated against ground truth (held-out evaluation set with published calibration report). |
| `11` | FORMALLY_VERIFIED | Machine-checked proof or tautological derivation. Highest trust level. |

**Confidence value (10 bits, bits 0-9):** A scaled certainty where:
- 0 = unknown / not assessed
- 1–250 = low confidence (exploratory, speculative)
- 251–600 = moderate confidence (some evidence, uncertain)
- 601–850 = high confidence (well-supported)
- 851–1000 = very high confidence (strongly verified)
- 1001–1023 = near-certain (reserved for formal proofs, tautologies)

**Trust policy (normative):** Downstream systems that make automated decisions based on CONFIDENCE values MUST check the calibration status first:
- UNCALIBRATED and SELF_ASSESSED confidence SHOULD be treated as informational noise, not actionable signal.
- Only EXTERNALLY_CALIBRATED or FORMALLY_VERIFIED values are trusted for automated thresholding.
- A system producing BBP-CoT streams with EXTERNALLY_CALIBRATED confidence MUST publish a calibration report documenting the empirical relationship between confidence values and actual correctness rates. This report SHOULD be referenced in the stream's metadata.

CONFIDENCE always uses flow=UNIT and modifies the immediately preceding block. It does not create its own START/END scope.

```
[CONCLUSION, flow=START, ...]
  — content —
[CONCLUSION, flow=END]
[CONFIDENCE, FEATURES=0b10_1010010000, flow=UNIT]
  → calibration=EXTERNALLY_CALIBRATED(10), value=656 → high confidence
```

### 17.3 Reasoning Scope and Structure

#### 17.3.1 Reasoning Blocks

A complete reasoning chain is enclosed in a top-level CONTROL block using a new structural marker:

| Payload | Hex | Name |
|---------|-----|------|
| 22 | 0x016 | REASONING_START |
| 23 | 0x017 | REASONING_END |

*(Added to the Structure sub-range 0x010–0x01F of the CONTROL FEATURES registry.)*

```
[CONTROL, REASONING_START, flow=START]     → reasoning chain begins
  — sequence of HYPOTHESIS, EVIDENCE, INFERENCE_STEP,
    CONCLUSION, RETRACTION, QUERY, CONFIDENCE blocks —
[CONTROL, REASONING_END, flow=END]         → reasoning chain ends
```

A REASONING block may appear:
- At the top level of a document (standalone reasoning).
- Inside a COMMENT block within BBP-AST (reasoning about code).
- Inside an EVIDENCE block (nested reasoning as evidence for a higher-level conclusion).
- Inside a HYPOTHESIS block (exploring the consequences of a supposition).

#### 17.3.2 Reasoning Graph

The sequence of meta-cognitive blocks within a REASONING scope defines a **directed acyclic graph** (DAG):

- **Nodes:** HYPOTHESIS, CONCLUSION, EVIDENCE, QUERY blocks.
- **Edges:** REFERENCE backpointers from EVIDENCE → HYPOTHESIS (this evidence supports/undermines this hypothesis); INFERENCE_STEP → preceding EVIDENCE (this rule applies to this evidence); RETRACTION → retracted node (this node is invalidated).
- **Acyclicity:** Guaranteed by the REFERENCE constraint that payloads must point to tokens with lower indices than the current token (Section 8.2).

A BBP-CoT processor can reconstruct this graph from the token stream and perform:
- **Completeness checking:** Does every CONCLUSION have sufficient EVIDENCE?
- **Consistency checking:** Are any non-retracted blocks contradictory?
- **Dependency tracing:** What is the full evidence chain supporting a given CONCLUSION?
- **Confidence propagation:** Aggregate CONFIDENCE values through the inference chain.

### 17.4 Interaction with BBP-NL and BBP-AST

The power of BBP-CoT comes from the fact that reasoning, natural language, and code all share the same token format. This enables three critical interaction patterns:

#### 17.4.1 Reasoning About Code

A BBP-CoT chain can contain BBP-AST blocks as evidence, hypotheses, or conclusions:

```
[CONTROL, REASONING_START, flow=START]

  [HYPOTHESIS, flow=START, FEATURES=0x001]       → "This loop terminates"
    [REFERENCE, FEATURES→LOOP_WHILE token]       → points to the loop in question
    [DICT_WORD, "terminate", ABS, SINGULAR]
    [UAT, NOR, PRES, IMPERF, S:HURA]
  [HYPOTHESIS, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x301]         → COMPUTATION evidence
    — BBP-AST showing the decrement —
    [CONTROL, ASSIGN, flow=UNIT]
    [REFERENCE, FEATURES→"counter"]
    [MATH_EXPRESSION, flow=START]
      [REFERENCE, FEATURES→"counter"]
      [MATH_OPERATOR, FEATURES=−]
      [NUM_NATURAL, FEATURES=1]
    [MATH_EXPRESSION, flow=END]
  [EVIDENCE, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x601]         → CONSTRAINT evidence
    — The counter is bounded below by 0 —
    [REFERENCE, FEATURES→"counter"]
    [MATH_OPERATOR, FEATURES=≥]
    [NUM_NATURAL, FEATURES=0]
  [EVIDENCE, flow=END]

  [INFERENCE_STEP, FEATURES=0x500, flow=UNIT]    → MATHEMATICAL: INDUCTION
  [CONCLUSION, flow=START, FEATURES=0x301]       → STRONG, basis=0, id=1
    [REFERENCE, FEATURES→HYPOTHESIS token]
    [DICT_WORD, "confirm", ABS]
    [UAT, NOR-NORK, PRES, PERF, AFF, S:HURA, O:HURA]
  [CONCLUSION, flow=END]
  [CONFIDENCE, FEATURES=3400, flow=UNIT]

[CONTROL, REASONING_END, flow=END]
```

An external verifier can check: the EVIDENCE blocks reference a decrementing counter bounded below, the INFERENCE_STEP claims mathematical induction, and the CONCLUSION claims termination. The structural links make this formally auditable.

#### 17.4.2 Reasoning About Natural Language

Standard BBP-NL content inside meta-cognitive blocks:

```
[HYPOTHESIS, flow=START, FEATURES=0x001]
  [DICT_WORD, "user", ERG, SINGULAR, DEF]
  [DICT_WORD, "factorial", ABS, SINGULAR, INDEF]
  [UAT, NOR-NORK, PRES, IMPERF, AFF, POT, S:HURA, O:HURA]
    → "The user may want [to compute] a factorial"
[HYPOTHESIS, flow=END]
```

#### 17.4.3 Mixed Reasoning

A single reasoning chain can alternate between NL propositions, code analysis, and mathematical proof — all in the same stream:

```
[CONTROL, REASONING_START, flow=START]

  [QUERY, flow=START, FEATURES=0x004]            → VERIFICATION query
    — "Is this function O(n)?" —
  [QUERY, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x001]         → PREMISE
    — NL: "Recursive call reduces n by 1 each time" —
  [EVIDENCE, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x301]         → COMPUTATION
    — BBP-AST: the recursive call structure —
  [EVIDENCE, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x001]         → PREMISE
    — MATH: T(n) = T(n-1) + O(1) —
    [MATH_EXPRESSION, flow=START]
      — ... mathematical recurrence relation ... —
    [MATH_EXPRESSION, flow=END]
  [EVIDENCE, flow=END]

  [INFERENCE_STEP, FEATURES=0x503, flow=UNIT]    → MATH: INDUCTION_STEP
  [CONCLUSION, flow=START, FEATURES=0x201]
    — "The function is O(n)" —
  [CONCLUSION, flow=END]
  [CONFIDENCE, FEATURES=3600, flow=UNIT]

[CONTROL, REASONING_END, flow=END]
```

### 17.5 Fundamental Limits of BBP-CoT: Structural Validity vs. Semantic Soundness

#### 17.5.0 Protocol Limit Declaration (Normative)

**BBP-CoT specifies the structure of reasoning; it cannot specify its validity.**

The BBP-CoT axioms (Group F, §17.7) define what constitutes a structurally well-formed reasoning chain: every CONCLUSION must be preceded by at least one EVIDENCE or INFERENCE_STEP block, RETRACTION must reference a prior HYPOTHESIS or CONCLUSION via REFERENCE, CONFIDENCE values must be within range, and so on. These properties are verifiable mechanically by the Validator without any semantic understanding.

What the protocol **cannot** specify, and **cannot enforce**, is whether the *content* of a structurally valid reasoning chain constitutes genuine logical inference. A model can learn to produce chains that:

- Are structurally perfect (pass all Group F axioms without error).
- Contain INFERENCE_STEP tokens with correct syntax.
- Reference prior tokens with valid REFERENCE pointers.
- Reach CONCLUSION tokens with matching basis_count.

Yet produce reasoning that is **semantically vacuous**, **logically circular**, or **factually incorrect** — because the structural axioms are satisfied while the semantic content is not. This is an instance of Goodhart’s Law applied to the reasoning structure: when the structural metric becomes the optimization target, it ceases to correlate with the goal.

**This is a fundamental limit of the protocol, not a correctable specification gap.** No addition to the wire format can detect cargo-cult reasoning — reasoning that mimics the structural pattern of inference without performing genuine inference. The validity of reasoning is a property of the trained model, not of the token stream.

**Normative consequence.** Implementations MUST NOT rely on BBP-CoT structural conformance as a proxy for reasoning quality. Implementations MUST evaluate reasoning quality using task-specific external benchmarks (accuracy on FOLIO, GSM8K, LogiQA, or domain-specific held-out evaluation sets) *independently* of Group F axiom compliance. A model that achieves 100% Group F compliance but performs at chance on task-specific benchmarks is not reasoning — it is structurally mimicking reasoning.

**Mitigation status.** The mitigations described in §17.6.3 (novelty scoring, grounding requirements, calibration, structural diversity incentives) and the training-time mechanisms in §17.6.4 (outcome verification, perturbation probes, cross-prompt consistency, step ablation) are **heuristic training strategies suggested to reduce the incidence of cargo-cult reasoning**. They are not normative protocol requirements. Their effectiveness is an empirical question. They are documented here to inform training teams; they are not part of the wire protocol specification.

#### 17.5.1 Mandatory Anti-Gaming Measure (Normative)

Implementations that train the Core Engine on BBP-CoT data MUST implement at least one of the following verification mechanisms. Implementing both is RECOMMENDED. The implemented mechanism(s) MUST be documented in the training report. Implementing neither is non-conformant for any implementation that claims BBP-CoT support.

**Mechanism A — Outcome verification.** For reasoning chains that produce a CONCLUSION over a domain with verifiable ground truth (mathematical proofs, code correctness, factual queries with known answers), the training pipeline MUST include a verification pass that checks the conclusion against the ground truth. CONCLUSION tokens whose propositional content is factually incorrect despite structurally valid chains MUST be down-weighted in the training loss.

**Mechanism B — Perturbation probes.** For a randomly sampled subset of training examples (minimum 10% of the BBP-CoT corpus), the training pipeline MUST include structurally invalid variants (e.g., CONCLUSION without supporting EVIDENCE, RETRACTION without a following REFERENCE, INFERENCE_STEP outside any reasoning context) and verify that the model assigns lower probability to invalid structures than to valid ones. Any training run where this property does not hold after the warmup phase MUST be investigated before continuing.

---

### 17.6 Training Considerations: Mitigating Structural Goodhart Effects (Informative)

**Status: INFORMATIVE.** This section provides guidance for model training and evaluation. It does not introduce normative requirements to the wire protocol.

#### 17.6.1 The Risk

BBP-CoT's typed structure (HYPOTHESIS → EVIDENCE → INFERENCE_STEP → CONCLUSION) enables structural auditing. However, if a model's training loss rewards structural completeness (e.g., every CONCLUSION preceded by EVIDENCE, all references resolving), the model may learn to produce chains that are *structurally perfect but semantically vacuous* — a Goodhart effect where the metric (structural validity) ceases to correlate with the goal (sound reasoning).

**Concrete example — Evidence spam:**

```
[REASONING_START]
  [HYPOTHESIS, flow=START, FEATURES=0x001]
    "The function has a bug in the base case"
  [HYPOTHESIS, flow=END]

  [EVIDENCE, flow=START, type=OBSERVATION, weight=8, id=1]
    "The function returns incorrect values"
  [EVIDENCE, flow=END]
  [EVIDENCE, flow=START, type=OBSERVATION, weight=7, id=2]
    [REFERENCE, FEATURES→evidence_1]                    ← circular: cites itself
    "The return values are wrong"                       ← paraphrase of evidence 1
  [EVIDENCE, flow=END]
  [EVIDENCE, flow=START, type=OBSERVATION, weight=6, id=3]
    [REFERENCE, FEATURES→evidence_1]                    ← circular again
    "Incorrect outputs observed"                        ← another paraphrase
  [EVIDENCE, flow=END]

  [CONCLUSION, flow=START, FEATURES=0x231]              → STRONG, basis=3
    "The base case is buggy"
  [CONCLUSION, flow=END]
  [CONFIDENCE, FEATURES=3500]
[REASONING_END]
```

This chain is structurally valid (3 EVIDENCE blocks, all with type and weight, CONCLUSION with basis=3 matching the count, CONFIDENCE attached). But evidences 2 and 3 are paraphrases of evidence 1 with circular REFERENCE — no new information is contributed. The high CONFIDENCE is unearned.

#### 17.6.2 Structural Detection Heuristics

The following heuristics can be applied mechanically to BBP-CoT streams (no semantic understanding required):

1. **Self-referential evidence.** An EVIDENCE block that contains a REFERENCE pointing to another EVIDENCE block within the same reasoning scope (circular citation). Detection: graph cycle check on REFERENCE edges within REASONING scope.

2. **Redundant evidence.** Multiple EVIDENCE blocks within the same scope that share >80% of their non-REFERENCE, non-CONTROL tokens (approximate content overlap). Detection: Jaccard similarity on token payloads within EVIDENCE blocks.

3. **Unsupported high confidence.** A CONFIDENCE token with value > 2500 (62.5%) attached to a CONCLUSION whose basis_count is 0 or whose supporting EVIDENCE blocks are flagged as redundant/circular by heuristics 1–2. Detection: threshold check with evidence quality filter.

4. **Conclusion without novel content.** A CONCLUSION whose content tokens are a strict subset of its supporting EVIDENCE tokens (the conclusion merely restates the evidence, contributing no inference step). Detection: token-set containment check.

#### 17.6.3 Recommended Mitigations

For teams training models that produce BBP-CoT output:

- **Novelty scoring.** In the training loss, weight EVIDENCE blocks by their information novelty relative to prior EVIDENCE in the same scope. Penalty for high semantic overlap (measured via embedding cosine similarity or token overlap).

- **Grounding requirement.** For EVIDENCE of type OBSERVATION or COMPUTATION, require an attached source reference: a REFERENCE to a BBP-AST fragment, a LITERAL block with a source identifier, or a REFERENCE to an external retrieval result. Ungrounded OBSERVATION evidence should receive a training penalty.

- **Calibration.** Periodically evaluate CONFIDENCE values against external ground truth (unit test results, retrieval verification, human judgment). Penalize systematic over-confidence.

- **Structural diversity.** Reward reasoning chains that include RETRACTION (self-correction), QUERY (identifying unknowns), and ALTERNATIVE hypotheses, not only straight-line HYPOTHESIS → EVIDENCE → CONCLUSION paths.

> **See §17.5.0** for the normative declaration of this fundamental protocol limit. The mitigations above reduce the incidence of cargo-cult reasoning chains but cannot eliminate the underlying problem, which is a property of the trained model, not of the wire format.

#### 17.6.4 Training-Time Semantic Validation (Normative for Training, Not Applicable in Production)

**Scope.** The mechanisms in this section apply exclusively during Core Engine training. They MUST NOT be applied at inference time: the computational cost is incompatible with production latency requirements, and their purpose — detecting structural mimicry before the model is fixed — is irrelevant once training has converged.

The goal is to ensure that the training signal rewards genuine reasoning, not structural validity alone. Four mechanisms are specified:

---

**Mechanism 1 — Outcome verification for computable domains.**

For training examples where the conclusion is objectively verifiable (mathematical results, logical entailments, code correctness, formally checkable facts), the training reward MUST include the correctness of the conclusion independently of structural conformance. A structurally perfect chain that reaches a wrong conclusion MUST receive a negative outcome signal. A structurally imperfect chain that reaches a correct conclusion MUST still receive a positive outcome signal (weighted appropriately).

Implementation: the training corpus MUST include a minimum proportion of examples with verifiable conclusions. Recommended minimum: 30% of BBP-CoT training examples across domains where verification is mechanically possible (arithmetic, formal logic, unit-testable code).

---

**Mechanism 2 — Deliberate perturbation probes.**

For a sampled subset of training examples, the Teacher LLM generates one or more **perturbed variants**: versions of the same problem with a modified premise (a false claim substituted, a numerical value changed, a logical polarity inverted). The Core Engine must produce a BBP-CoT chain that reflects the perturbation — detecting the altered premise, adjusting the EVIDENCE weight or CONFIDENCE accordingly, or reaching a different conclusion.

A model that produces structurally identical chains for the original and the perturbed variant is demonstrably not processing semantic content. Such outputs MUST receive a strong negative training signal regardless of structural validity.

Recommended perturbation rate: 20% of training examples in curriculum stages 3 and 4 (§15.3.5).

---

**Mechanism 3 — Cross-prompt consistency evaluation.**

Periodically between training epochs (not per-batch), the same reasoning problem is presented to the Core Engine in multiple reformulations: paraphrased premises, different surface orderings, alternative framings that preserve logical content. A model reasoning genuinely should produce conclusions of equivalent semantic content across reformulations.

Consistency is measured at the BBP token level, not the natural language level: equivalent conclusions should map to the same or aliased LEMMA IDs in the CONCLUSION block. Systematic inconsistency across reformulations is a signal that the model is pattern-matching surface structure rather than reasoning over content.

This mechanism is an **evaluation signal** between epochs, not a per-example loss term. It informs decisions about training continuation, curriculum progression, and go/no-go gates (§15.5.5).

---

**Mechanism 4 — Step ablation probes.**

For a sampled subset of training examples with multi-step reasoning chains (two or more INFERENCE_STEP blocks), the Teacher LLM generates **ablated variants** with one or more intermediate steps removed. If the intermediate steps contribute genuine inferential content, their removal should degrade the quality of the conclusion. If conclusion quality does not degrade, the removed steps were structural decoration.

Ablation results feed into the novelty scoring mitigation (§17.6.3): INFERENCE_STEP blocks whose ablation produces no measurable conclusion degradation are penalised in the training loss.

---

**Integration with curriculum learning.** These mechanisms are introduced progressively across the four training stages defined in §15.3.5:

| Stage | Mechanisms active |
|-------|------------------|
| 1 (single-clause) | Mechanism 1 only (verifiable outcomes on simple facts) |
| 2 (multi-clause) | Mechanisms 1 + 2 (perturbation probes introduced) |
| 3 (paragraphs) | Mechanisms 1 + 2 + 3 (cross-prompt consistency evaluation) |
| 4 (full documents) | All four mechanisms active |

### 17.7 BBP-CoT Validation Rules

Added to Section 13.2.1 as **Axiom Group F — Reasoning Structural Consistency:**

```
F1: A RETRACTION UET MUST be immediately followed by a REFERENCE UET
    whose FEATURES points to a HYPOTHESIS or CONCLUSION token.
    SEVERITY: CRITICAL (error)

F2: A RETRACTION's REFERENCE target MUST point to a token with
    meta-type HYPOTHESIS (0o70) or CONCLUSION (0o71).
    Retracting EVIDENCE, QUERY, or other types is invalid.
    SEVERITY: CRITICAL (error)

F3: A RETRACTION MUST NOT target an already-retracted block.
    (No double retraction — retract once.)
    SEVERITY: MAJOR (warning)

F4: An INFERENCE_STEP SHOULD be inside or immediately before
    a CONCLUSION block. An orphaned INFERENCE_STEP triggers a warning.
    SEVERITY: MINOR (warning)

F5: A CONCLUSION with strength ≥ STRONG (code ≥ 2) SHOULD be
    preceded by at least one EVIDENCE or INFERENCE_STEP in the
    same reasoning scope.
    SEVERITY: MINOR (warning)

F6: A CONCLUSION's basis_count field SHOULD match the actual count
    of EVIDENCE and INFERENCE_STEP blocks that precede it in the
    current reasoning scope and reference it (or are unreferenced
    and thus implicitly supporting the nearest subsequent CONCLUSION).
    SEVERITY: TRIVIAL (warning, auto-correct if unambiguous)

F7: A CONFIDENCE UET MUST be preceded by a block-closing token
    (HYPOTHESIS END, CONCLUSION END, EVIDENCE END) or another
    CONFIDENCE UET. CONFIDENCE not following a block is invalid.
    SEVERITY: MAJOR (warning)

F8: Within a REASONING scope, at least one CONCLUSION or QUERY
    SHOULD be present. A reasoning block with only HYPOTHESIS
    and EVIDENCE but no CONCLUSION or QUERY is structurally
    complete but semantically incomplete — flagged for review.
    SEVERITY: TRIVIAL (warning)

F9: REASONING_START and REASONING_END MUST be balanced (matching
    START/END). Nested REASONING blocks are permitted (reasoning
    within reasoning, e.g., exploring a sub-problem).
    SEVERITY: CRITICAL (error)

F10: Meta-cognitive UETs (Group 7, 0o70-0o76) SHOULD only appear
     inside a REASONING scope. Meta-cognitive tokens outside any
     REASONING block trigger a warning (valid but semantically
     ambiguous — implicit reasoning scope assumed).
     SEVERITY: MINOR (warning)
```

### 17.8 Training Considerations

#### 17.8.1 Corpus Generation for BBP-CoT

Generating training data for BBP-CoT requires the Universal Teacher LLM to produce explicitly typed reasoning chains — a qualitatively different task from grammatical analysis. The system prompt extensions for Phase 2 (Section 15.4) MUST include:

- **Explicit instructions** to wrap each reasoning step in the appropriate meta-cognitive primitive (HYPOTHESIS, EVIDENCE, CONCLUSION, etc.), not to produce free-form reasoning text.
- **Structural constraint examples:** showing valid and invalid reasoning chains (e.g., CONCLUSION without EVIDENCE → warning; RETRACTION without REFERENCE → error).
- **Multi-modal examples:** reasoning chains that mix NL propositions, BBP-AST code blocks, and mathematical expressions within the same REASONING scope.
- **Retraction examples:** chains where the model proposes a HYPOTHESIS, finds contradicting EVIDENCE, emits a RETRACTION, and reaches a different CONCLUSION. This pattern is rare in conventional LLM training data but essential for robust reasoning.

**Source datasets:**

| Dataset | BBP-CoT features exercised |
|---------|----------------------------|
| FOLIO | Deductive reasoning, MODUS_PONENS / MODUS_TOLLENS |
| LogiQA | Multi-step inference, EVIDENCE chains |
| GSM8K | Mathematical reasoning, COMPUTATION evidence, ALGEBRAIC_SIMPLIFICATION |
| MATH | Proof steps, INDUCTION_BASE / INDUCTION_STEP |
| HumanEval / MBPP | Code reasoning, BBP-AST inside EVIDENCE blocks |
| Natural language inference (SNLI, MultiNLI) | HYPOTHESIS / CONCLUSION / CONTRADICTION |
| Theorem proving (Lean, Coq exports) | Formal INFERENCE_STEP chains |

#### 17.8.2 Structural Consistency Loss for BBP-CoT

The Core Engine's training loss function (Section 15.5.2) is extended with BBP-CoT constraints:

```
L_reasoning = λ_retract · L_retract_target +
              λ_evidence · L_evidence_coverage +
              λ_inference · L_inference_placement +
              λ_confidence · L_confidence_calibration
```

Where:
- **L_retract_target:** Penalizes RETRACTION followed by non-REFERENCE or REFERENCE to non-HYPOTHESIS/CONCLUSION.
- **L_evidence_coverage:** Penalizes CONCLUSION with strength ≥ STRONG that has no preceding EVIDENCE in scope.
- **L_inference_placement:** Penalizes INFERENCE_STEP outside CONCLUSION context.
- **L_confidence_calibration:** Penalizes CONFIDENCE values that diverge from the empirical accuracy of the model's conclusions on the validation set (encourages well-calibrated confidence).

---

## Appendix A: Glossary

| Term | Definition |
|------|-----------|
| **Aditz laguntzailea** | Basque auxiliary verb; the morphological engine that encodes person, number, tense, and transitivity |
| **Allocutive (Hitano)** | Basque verb form that marks the gender of the listener, even when they are not a participant |
| **native verb feature field** | A UET that must immediately precede a UAT, forming a logical unit that extends the UAT's grammatical frame (Section 6.7) |
| **Deklinabidea** | The Basque declension system; the set of case suffixes applied to nouns |
| **Ergative** | The grammatical case marking the agent of a transitive verb |
| **Absolutive** | The grammatical case marking the patient of a transitive verb OR the subject of an intransitive verb |
| **TAM** | Tense-Aspect-Mood: the three (plus voice and polarity) grammatical categories encoded in the UAT |
| **BBP-IR** | BBP Intermediate Representation; the JSON bridge between LLM analysis and binary compilation |
| **MCP** | Model Context Protocol; the tool-use interface allowing an LLM to invoke the BBP compiler |
| **UAT** | Universal Action Token; the 64-bit token encoding a verbal predicate |
| **UET** | Universal Entity Token; the 64-bit token encoding a non-verbal element |
| **Round-trip** | The process of encoding text to BBP and decoding back to text to verify fidelity |
| **SYNC** | A reserved token pattern used for stream error recovery |
| **EXTENDED** | Pair table index 14 in NOR-NORI-NORK, signaling that ABS/DAT should be read from UETs |
| **LITERAL** | Flow control value (11) indicating raw byte data follows, with FEATURES = byte length |
| **PLACEHOLDER** | Determiner value (0xA) for cataphoric pronouns awaiting forward resolution (Section 8.4) |
| **Axiomatic Validator** | Deterministic rule engine between Teacher LLM and Compiler that verifies IR consistency (Section 13.2.1) |
| **AMR** | Abstract Meaning Representation; a text-based semantic formalism using PENMAN/S-expression notation. BBP shares its motivation but diverges in execution (Section 1.7) |
| **Extended Reference** | A REFERENCE UET with `flow=LITERAL` that provides 32-bit or 64-bit absolute token indexing for long-range coreference beyond the 4,094-token direct backpointer limit (Section 8.5) |
| **STATE_CHECKPOINT** | A CONTROL LITERAL block (FEATURES 0x050) that snapshots the current nesting stack state, enabling O(256) random access in Seekable streams (Section 11.7) |
| **KEYFRAME** | A heavy checkpoint (CONTROL FEATURES 0x051) that includes both nesting state and an entity table, providing complete self-contained recovery for RAG and long streams (Section 11.8) |
| **BBP-Full** | Informal term (deprecated as a protocol profile) for BBP streams produced by a Frontier Translator (Teacher LLM encoder) with full ergative-absolutive analysis, bound modifier tokens, and rich evidentiality. The distinction is a property of the encoder, not the wire protocol. See §2.8 and §13.1.1. |
| **BBP-Lite** | Informal term (deprecated as a protocol profile) for BBP streams produced by a deterministic parser encoder (§13.1.1), typically with UNMARKED case, no evidentiality, and no DISAMBIGUATION. The resulting stream is valid BBP-64; receivers MUST NOT treat it as a distinct protocol variant. See §2.8 and §13.1.1. |
| **Seekable** | A stream capability flag (DOC_START bit 0) indicating that STATE_CHECKPOINTs are present at regular intervals, enabling efficient random access (Section 11.3) |
| **BBP Linguistic Conventions** | A normative companion document that records contested linguistic interpretive decisions resolved by human adjudication during Cross-Teacher validation (Section 15.4) |

---

## Appendix B: Design Decisions and Trade-offs

| Decision | Rationale | Trade-off |
|----------|-----------|-----------|
| 64-bit fixed-width tokens | Constant-time field extraction; GPU-friendly; 8 tokens per AVX-512 | Cannot represent large inline values without LITERAL blocks |
| MSB as type discriminator | O(1) classification | Halves address space per type |
| Union-discriminated arguments | Recovers 4 bits in NOR, 1 bit in NOR-NORK/NOR-NORI | Parser must branch on System tag |
| Pair table for NOR-NORI-NORK | Encodes 3 args in 7 bits with EXTENDED fallback | Non-3rd-person ABS requires UET lookup |
| Subject always at bits 4-6 | O(1) agent extraction regardless of paradigm | Constrains argument layout |
| 8-bit TAM (Pol+Asp+Voice+Mood+Tense) | 256 TAM combinations; covers Mandarin, Arabic, Slavic, Turkish, Japanese | Fine aspect and evidentiality deferred to bound modifier tokens |
| Allocutive removed from UAT | Freed 2 bits for Voice; allocutive is Basque-only | Hitano deferred to NOR extended bits (v1.1) |
| 6-bit meta-type (64 categories) | Freed 1 bit for FEATURES (4x vocabulary) | Max 64 ontological categories |
| FEATURES payload field (4,096/category) | ~98% inline coverage; 262K total concepts; addressed directly via lemma IDs | Rare domain vocab needs page switch token |
| 11-bit Deklinabidea (5+2+4) | Minimum for 18 cases + Dual + 11 determiners (incl. PLACEHOLDER) | 14 reserved case slots, 5 reserved det slots |
| DUAL replaces COLLECTIVE | Dual is obligatory morphology in Arabic/Hebrew/Slovenian | Collective via MODIFIER UET |
| TOPICAL case added | Covers Japanese は, Korean 은/는 | Uses 1 reserved case slot |
| flow=11 as LITERAL | Separates semantic blocks (START/END) from raw data blocks; solves FEATURES dual-meaning bug | All 4 flow values now used (no reserved) |
| 8-byte padding for raw data | Guarantees stream alignment for SIMD | Up to 7 wasted bytes per literal block |
| Logical token indexing | REFERENCE backpointers are stable regardless of raw data length | Parser needs separate token counter |
| CONTROL FEATURES registry | Prevents collision between punctuation, structure, register, compositionality, and extensions | Must maintain registry as spec evolves |
| Big-endian wire format | Matches network byte order | Little-endian hosts need conversion |
| SYNC token (`[0x6DFFFFFF][0xFFFFFFFF]`) | Enables error recovery in corrupted streams | Uses one reserved 64-bit pattern |
| No gender in bits | Gender is lexical, not logical; resolved by dictionary | Renderer must look up gender per language |
| Narrow negation scope | UAT polarity negates verb; NEGATIVE determiner negates entity | Requires Teacher LLM to encode scope correctly |
| Bidirectional modifier binding | Accepts adverb/adjective before or after head | Teacher LLM normalizes to head-first |
| Forward references prohibited | Enables single-pass streaming | Cataphora via PLACEHOLDER mechanism |
| PLACEHOLDER cataphora | Preserves discourse structure without breaking single-pass | 2 extra tokens per cataphoric reference |
| BBP Language Registry | Maps sequential numeric IDs to ISO 639-3 codes | Requires maintaining external registry file |
| Teacher-Student training | Leverages existing LLM capabilities | Inherits LLM parsing errors (mitigated by Axiomatic Validator) |
| native verb feature field protocol | Extends UAT grammar without touching the fixed 64-bit token format; zero cost for languages that don't need it | 1 extra token per feature per verb when needed |
| 64-bit Limit | Prevents DoS/overflow attacks via infinite prefix chaining. | Hard limit on ID space (though 2^64 is effectively infinite) |
| Axiomatic Validator | Catches Teacher LLM errors deterministically; enables auto-correction | Added pipeline complexity; must maintain axiom set |
| Coarse+fine aspect split | 1-bit UAT covers universal axis; native verb feature field covers language-specific fine aspects | Fine aspect costs 1 extra token |
| long-range REFERENCE via LITERAL block (§8.5) | Reuses existing LITERAL block mechanics; no new token types; backward-compatible degradation; absolute 32-bit indexing covers documents up to ~4 billion tokens | 3× overhead per extended reference (12 bytes vs. 4 bytes); parser must detect REFERENCE + LITERAL combination |
| Encoder-type distinction vs. protocol profiles (§2.8, §13.1.1) | Single wire protocol (BBP-64) with two encoder classes: Frontier Translator (Teacher LLM, rich analysis) and deterministic parser (structured input, fully well-formed). PROFILE provenance tag (§11.5) is informative only; receivers handle both without special-casing. | Sparse streams (UNMARKED case, no evidentiality) are valid but carry less semantic information; downstream consumers must handle partial FEATURES gracefully. |
| STATE_CHECKPOINT (§11.7) | Enables O(256) random access; bounds memory for RAG; ~2.5% overhead; composable with SYNC | Adds tokens to stream; Seekable flag required; encoder must track nesting state |
| KEYFRAME (§11.8) | Complete self-contained recovery point; resolves long-range REFERENCE memory issue; H.264-style architecture | Higher overhead per KEYFRAME (~50-200 bytes); entity table maintenance; recommended but not mandatory |
| Cross-Teacher validation (§15.4) | Detects systematic Teacher biases invisible to round-trip testing; produces documented Linguistic Conventions | Doubles Teacher compute for test set; requires human linguist adjudication for divergences |
| BBP-AST value = navigability (§16.1) | Honest positioning prevents false expectations; Core Engine benefits from structural typing, not compression | BBP-AST may be more verbose than source for decorated languages (Rust, TypeScript) |
| LEMMA as typed semantic identifier (§2.3, r21) | Moving the type discriminator (bit 31) and meta-type (bits 30–25) from FEATURES to LEMMA makes every token self-describing: its ontological category is recoverable from the LEMMA word alone, without loading FEATURES. This frees all 32 FEATURES bits for grammatical encoding, resolving documented coverage gaps in honorifics, noun class, number, and animacy. Conceptual coherence improves: concept identity and grammatical context are cleanly separated at the word boundary. | Concept ID space per meta-type narrows from 32b (4B IDs, flat) to 25b (33M IDs, partitioned by meta-type). Migration from r20 is lossless if BBP-IR is available; binary-only migration requires concept_id ≤ 25b (no production deployments existed). BREAKING CHANGE for all parsers. |
| CLF auxiliary token pattern (§1.6, §4.12, r21) | Encoding the universal semantic category of classifier in the 4-bit noun_classifier field, with language-specific classifiers delegated to a preceding CLF auxiliary token (CLF_EXTENDED = 0xE), makes the base token parseable in O(1) for the vast majority of cases. The pattern is infinitely extensible — no format change is required to add new language-specific classifiers — and has zero cost for languages that do not use classifiers (no auxiliary token emitted). The same auxiliary token pattern is available as a general extension mechanism for other fields. | One additional token per language-specific classifier use (when CLF_EXTENDED is needed). Parser must handle the positional dependency (CLF aux immediately precedes the nominal). Axioms A_CLF1–A_CLF2 enforce structural validity. |
| Three-dimensional honorific encoding (§4.11, r21) | Replacing the flat 4-bit (16-value) register field with a 3D 6-bit field (speech_level × respect_direction × domain) covers the structural requirements of Japanese keigo (sonkeigo, kenjōgo, teineigo), the Korean speech level system, and European T/V distinctions within a single inline field, without auxiliary tokens. The HONORIFIC_CONTEXT CONTROL token (0x030) optionally provides speaker/addressee/referent role-assignment metadata for Renderers that require it for phonologically-conditioned lexical selection (e.g., いらっしゃる vs. いる). | 2 additional FEATURES bits consumed (4b → 6b). The 3D structure adds encoder complexity: the Teacher must decompose a language's honorific system into three orthogonal dimensions. For languages with no honorific morphology, all sub-fields default to 0 at zero cost. |

---

## Appendix C: Quick Reference — Bit Maps

### C.1 Action Token (64b)

```
LEMMA (bits 63–32):
  Bit  63     : 1 (Action discriminator)
  Bits 62–60  : Aktionsart         (3b)  STATE | ACTIVITY | ACHIEVEMENT |
                                          ACCOMPLISHMENT | SEMELFACTIVE | CAUSATIVE_BASE
  Bits 59–57  : Valency class      (3b)  INTRANS_UNACCUSATIVE | INTRANS_UNERGATIVE |
                                          TRANSITIVE | DITRANSITIVE | COPULATIVE | AMBITRANSITIVE
  Bits 56–32  : Verb ID            (25b) Dictionary index

FEATURES (bits 31–0):
  Bits 31–30  : (reserved, must be 0 for Action Tokens)
  Bits 29–28  : System             (2b)  NOR | NOR-NORK | NOR-NORI | NOR-NORI-NORK
  Bit  27     : Polarity           (1b)  AFF | NEG
  Bits 26–24  : Aspect             (3b)  PERF | IMPERF | [extended]
  Bits 23–22  : Voice              (2b)  ACTIVE | PASSIVE | MIDDLE | CAUSATIVE
  Bits 21–19  : Voice_Ext          (3b)  extended voice (see §3.5)
  Bits 18–17  : Mood               (2b)  IND | SUB | POT | IMP
  Bits 16–15  : Tense              (2b)  PRES | PAST | FUT | HYPO
  Bits 14–12  : Subject            (3b)  argument index (see §3.3)
  Bits 11– 9  : Object             (3b)  argument index
  Bits  8– 6  : Indirect           (3b)  argument index
  Bits  5– 2  : Evidentiality      (4b)  16-value normative inventory (see §3.8)
  Bits  1– 0  : Clusivity          (2b)  see §3.9
```

### C.2 Entity Token (64b)

```
LEMMA (bits 63–32):
  Bit  63     : 0 (Entity discriminator)
  Bits 62–57  : Meta-type          (6b)  see §2.4 meta-type registry
  Bits 56–32  : Concept ID         (25b) Dictionary index

FEATURES (bits 31–0):
  Bits 31–30  : Flow control       (2b)  UNIT | START | END | LITERAL
  Bits 29–25  : Case               (5b)  29 defined values + 3 reserved (see §4.5)
  Bits 24–22  : Number             (3b)  UNMARKED | SINGULAR | DUAL | TRIAL |
                                          PAUCAL | PLURAL | COLLECTIVE | DISTRIBUTIVE
  Bits 21–18  : Determiner         (4b)  NONE | DEF | INDEF | PROX | MED | DIST |
                                          INTER | UNIV | EXIST | NEG | PLACEHOLDER
  Bits 17–14  : Noun classifier    (4b)  14 semantic categories + CLF_EXTENDED + CLF_OTHER_LITERAL
  Bits 13–12  : Animacy            (2b)  INANIMATE | ANIMATE | HUMAN | DIVINE
  Bits 11– 8  : Noun class         (4b)  NC_NONE | NC_1–NC_14 | NC_EXTENDED | NC_ANIMATE_AGREE
  Bits  7– 2  : Register/Honorific (6b)  bits 7–6: speech_level | bits 5–4: respect_direction |
                                          bits 3–2: domain (see §4.11)
  Bits  1– 0  : Reserved           (2b)
```

## Appendix D: Reference Test Vectors and Bit Decomposition

This appendix provides annotated bit-level decompositions for all examples in the specification, serving as both documentation and machine-verifiable test vectors.

### D.1 Decomposition Methodology

Every hex value in the spec is decomposed using this format:

```
0xHHHHHHHH
Bits: [bit31]_[field1]_[field2]_..._[fieldN]
      │       └──annotation──┘
      └annotation
```

Fields are separated by underscores and annotated with their decoded values. The MSB (bit 31) is always shown first as a single bit.

### D.2 UAT Test Vectors

**Vector UAT-1:** Section 3.6 — "Yo no le di los libros" (I didn't give him the books)
verb=dar(74), NOR-NORI-NORK, NEG, PERF, ACTIVE, IND, PAST, S=NI(1), O=HURA(3), IO=HURA(3)

```
LEMMA:
  bit  31     = 1              Action discriminator
  bits 30–28  = 011            Aktionsart=ACCOMPLISHMENT (dar: telic, durative transfer)
  bits 27–25  = 011            valency_class=DITRANSITIVE
  bits 24– 0  = 74=0x4A        verb_id

  (1  << 31) = 0x80000000
  | (3 << 28)= 0x03000000   Aktionsart=ACCOMPLISHMENT(3)
  | (3 << 25)= 0x01800000   valency=DITRANSITIVE(3)
  | 74       = 0x0000004A
  ──────────────────────────
  LEMMA      = 0x8480004A

FEATURES:
  bit  31     = 0              reserved
  bits 30–28  = 011            system=NOR-NORI-NORK(3)
  bit  27     = 1              polarity=NEG
  bits 26–24  = 000            aspect=PERF(0)
  bits 23–22  = 00             voice=ACTIVE(0)
  bits 21–19  = 000            voice_ext=0
  bits 18–17  = 00             mood=IND(0)
  bits 16–15  = 01             tense=PAST(1)
  bits 14–12  = 001            subject=NI(1)
  bits 11– 9  = 011            object=HURA(3)
  bits  8– 6  = 011            indirect=HURA(3)
  bits  5– 2  = 0000           evidentiality=EV_UNSPECIFIED(0)
  bits  1– 0  = 00             clusivity=0

  (3 << 28)  = 0x30000000   system=NOR-NORI-NORK
  | (1 << 27)= 0x08000000   polarity=NEG
  | (1 << 15)= 0x00008000   tense=PAST
  | (1 << 12)= 0x00001000   subject=NI
  | (3 <<  9)= 0x00000600   object=HURA
  | (3 <<  6)= 0x000000C0   indirect=HURA
  ──────────────────────────
  FEATURES   = 0x380097C0

Wire: [LEMMA=0x8480004A][FEATURES=0x380097C0]

Bits (FEATURES): 0_011_1_000_00_000_00_01_001_011_011_0000_00
                 │ │   │ │   │  │   │  │  │   │   │   │    └clusivity
                 │ │   │ │   │  │   │  │  │   │   │   └evidentiality=0
                 │ │   │ │   │  │   │  │  │   │   └indirect=HURA(3)
                 │ │   │ │   │  │   │  │  │   └object=HURA(3)
                 │ │   │ │   │  │   │  │  └subject=NI(1)
                 │ │   │ │   │  │   │  └tense=PAST(01)
                 │ │   │ │   │  │   └mood=IND(00)
                 │ │   │ │   │  └voice_ext=0
                 │ │   │ │   └voice=ACTIVE(00)
                 │ │   │ └aspect=PERF(000)
                 │ │   └polarity=NEG(1)
                 │ └system=NOR-NORI-NORK(011)
                 └reserved=0
```

**Vector UAT-2:** Section 14.3 Token 7 — "activate" (NOR-NORK, NEG, PERF, ACTIVE, IND, PAST, S=HURA, O=HURA)
verb=activate(100)

```
LEMMA:
  bit  31    = 1               Action discriminator
  bits 30–28 = 010             Aktionsart=ACHIEVEMENT (punctual change of state)
  bits 27–25 = 010             valency_class=TRANSITIVE
  bits 24– 0 = 100=0x64        verb_id

  (1  << 31) = 0x80000000
  | (2 << 28)= 0x20000000   Aktionsart=ACHIEVEMENT(2)
  | (2 << 25)= 0x04000000   valency=TRANSITIVE(2)
  | 100      = 0x00000064
  ──────────────────────────
  LEMMA      = 0xA4000064

FEATURES:
  bits 30–28 = 001             system=NOR-NORK(1)
  bit  27    = 1               polarity=NEG
  bits 16–15 = 01              tense=PAST
  bits 14–12 = 011             subject=HURA(3)
  bits 11– 9 = 011             object=HURA(3)
  (all other fields = 0)

  (1 << 28)  = 0x10000000   system=NOR-NORK
  | (1 << 27)= 0x08000000   polarity=NEG
  | (1 << 15)= 0x00008000   tense=PAST
  | (3 << 12)= 0x00003000   subject=HURA
  | (3 <<  9)= 0x00000600   object=HURA
  ──────────────────────────
  FEATURES   = 0x1800B600

Wire: [LEMMA=0xA4000064][FEATURES=0x1800B600]
```

### D.3 UET Test Vectors

**Vector UET-1:** Section 14.3 Token 1 — DICT_WORD "temperature" (ERG, SINGULAR, DEF, concept_id=201)

```
LEMMA:
  bit  31    = 0             Entity discriminator
  bits 30–25 = 000000        meta_type=DICT_WORD(0)
  bits 24–0  = 201=0xC9      concept_id

  (0 << 31)  = 0x00000000
  | (0 << 25)= 0x00000000   meta_type=DICT_WORD
  | 201      = 0x000000C9   concept_id
  ──────────────────────────
  LEMMA      = 0x000000C9

FEATURES:
  bits 31–30 = 00            flow=UNIT
  bits 29–25 = 00010         case=ERG(2)
  bits 24–22 = 001           number=SINGULAR(1)
  bits 21–18 = 0001          determiner=DEF(1)
  bits 17– 0 = 000…0         classifier, animacy, noun_class, register = UNMARKED

  (0b00 << 30)               = 0x00000000   flow=UNIT
  | (2  << 25)               = 0x04000000   case=ERG
  | (1  << 22)               = 0x00400000   number=SINGULAR
  | (1  << 18)               = 0x00040000   determiner=DEF
  ──────────────────────────
  FEATURES   = 0x04440000

Wire: [LEMMA=0x000000C9][FEATURES=0x04440000]

Bits (FEATURES): 00_00010_001_0001_0000_00_0000_000000_00
                 ││ │     │   │    │    │  │     │      └reserved
                 ││ │     │   │    │    │  │     └register=0
                 ││ │     │   │    │    │  └noun_class=0
                 ││ │     │   │    │    └animacy=0
                 ││ │     │   │    └classifier=0
                 ││ │     │   └determiner=DEF
                 ││ │     └number=SINGULAR
                 ││ └case=ERG
                 │└(reserved in Entity FEATURES, must be 0)
                 └flow=UNIT
```

**Vector UET-2:** SYNC token

```
LEMMA:
  bit  31    = 0             Entity discriminator
  bits 30–25 = 110110        meta_type=CONTROL(54=0o66)
  bits 24–0  = 0x1FFFFFF     concept_id=all-ones

  (0  << 31) = 0x00000000
  | (54 << 25)= 0x6C000000
  | 0x1FFFFFF= 0x01FFFFFF
  ──────────────────────────
  LEMMA      = 0x6DFFFFFF

FEATURES:
  All bits set to 1 → FEATURES = 0xFFFFFFFF

Wire: [LEMMA=0x6DFFFFFF][FEATURES=0xFFFFFFFF]

This pattern is structurally impossible for any valid operational token:
LITERAL flow (0b11) on a CONTROL token is not a legal operational encoding,
and all grammar fields simultaneously at reserved/maximum values is excluded
by the grammar field constraints of §4.
```

### D.4 Bound Evidential Token Test Vector

**Vector EV-1:** Turkish "gelmiş" (reportedly came) — EVIDENTIAL auxiliary token

```
Token 0: EVIDENTIAL Entity Token
  meta_type=EVIDENTIAL(53=0o65), flow=UNIT, evidential_payload=EV_REPORT_GENERAL(0x7)

  LEMMA:
    bit  31    = 0               Entity discriminator
    bits 30–25 = 110101          meta_type=EVIDENTIAL(53)
    bits 24–0  = 0               concept_id=0 (not used for EVIDENTIAL tokens)

    (0  << 31) = 0x00000000
    | (53 << 25)= 0x6A000000
    | 0        = 0x00000000
    ──────────────────────────
    LEMMA      = 0x6A000000

  FEATURES (reinterpreted as evidential payload — see §3.8):
    flow=UNIT=00, evidential_value=EV_REPORT_GENERAL(0x7) in appropriate payload bits
    FEATURES   = 0x00000700    (illustrative; exact payload layout per §3.8)

  Wire: [LEMMA=0x6A000000][FEATURES=0x00000700]

Token 1: Action Token "gel" (come), NOR, PAST, PERF, ACTIVE, IND, S=HURA(3)
  (verb_id depends on dictionary entry for "gel")
  LEMMA = (1<<31) | (ACTIVITY<<28) | (INTRANS_UNACCUSATIVE<<25) | verb_id
  FEATURES = (0b00<<30)|(0b01<<7)|(0b011<<4)|...  [system=NOR, tense=PAST, subj=HURA]
  → [verb-specific; depends on gel's dictionary ID and full TAM]
```

### D.5 Long-Range REFERENCE Test Vector

**Vector ER-1:** Extended Reference to token 0 from position 8,742

```
Token 8742: REFERENCE Entity Token (LITERAL — byte payload follows)
  meta_type=REFERENCE(49=0o61), flow=LITERAL(3), byte_length=4, grammar=UNMARKED

  LEMMA:
    (0  << 31) = 0x00000000   Entity discriminator
    | (49 << 25)= 0x62000000   meta_type=REFERENCE(49)
    | 0        = 0x00000000   concept_id=0
    ──────────────────────────
    LEMMA      = 0x62000000

  FEATURES (LITERAL flow — remaining bits = byte_length):
    (3 << 30)  = 0xC0000000   flow=LITERAL
    | 4        = 0x00000004   byte_length=4
    ──────────────────────────
    FEATURES   = 0xC0000004

  Wire: [LEMMA=0x62000000][FEATURES=0xC0000004]
  [raw bytes: 00 00 00 00 00 00 00 00]   → absolute token index 0 (4 bytes) + 4 bytes 0x00 padding
  [total: 8 bytes, 8-byte aligned per §4.4]

Token 8743: REFERENCE Entity Token (END — carries coreference grammar role)
  meta_type=REFERENCE(49=0o61), flow=END(2), case=ERG(2), number=SINGULAR(1), det=DEF(1)

  LEMMA:
    (0  << 31) = 0x00000000
    | (49 << 25)= 0x62000000
    | 0        = 0x00000000
    ──────────────────────────
    LEMMA      = 0x62000000

  FEATURES:
    (2 << 30)  = 0x80000000   flow=END
    | (2 << 25)= 0x04000000   case=ERG
    | (1 << 22)= 0x00400000   number=SINGULAR
    | (1 << 18)= 0x00040000   determiner=DEF
    ──────────────────────────
    FEATURES   = 0x84440000

  Wire: [LEMMA=0x62000000][FEATURES=0x84440000]

Decoded: Extended backpointer to token 0, ergative singular definite.
Wire cost: 24 bytes (8 LEMMA+FEATURES + 8 raw+padding + 8 END token) vs. 8 bytes for a
standard inline REFERENCE. Extended form is used when logical index > 22 bits.
```

---


## Appendix E: BBP-AST Test Vectors and Examples

### E.1 Factorial Function (Python-origin)

**Source:**

```python
def factorial(n):
    if n <= 1:
        return 1
    return n * factorial(n - 1)
```

**BBP-AST IR (JSON):**

```json
[
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "FUNC_DEF", "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "NAME_WORK",
    "value": "factorial", "flow": "LITERAL",
    "byte_length": 9,
    "raw_content": "factorial"
  },
  {
    "type": "ENTITY", "meta_type": "NAME_WORK",
    "flow": "END",
    "declension": { "case": "ABS", "number": "SINGULAR", "determiner": "NONE" }
  },
  {
    "type": "ENTITY", "meta_type": "NAME_WORK",
    "value": "n", "flow": "LITERAL",
    "byte_length": 1,
    "raw_content": "n"
  },
  {
    "type": "ENTITY", "meta_type": "NAME_WORK",
    "flow": "END",
    "declension": { "case": "ABS", "number": "SINGULAR", "determiner": "NONE" }
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "CONDITIONAL", "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "COND_BRANCH", "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "REFERENCE",
    "flow": "UNIT", "reference_target": 3
  },
  {
    "type": "ENTITY", "meta_type": "MATH_OPERATOR",
    "value": "<=", "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "NUM_NATURAL",
    "value": 1, "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "flow": "END"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "RETURN", "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "NUM_NATURAL",
    "value": 1, "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "COND_BRANCH", "flow": "END"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "CONDITIONAL", "flow": "END"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "RETURN", "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "REFERENCE",
    "flow": "UNIT", "reference_target": 3
  },
  {
    "type": "ENTITY", "meta_type": "MATH_OPERATOR",
    "value": "*", "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "FUNC_CALL", "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "REFERENCE",
    "flow": "UNIT", "reference_target": 1
  },
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "flow": "START"
  },
  {
    "type": "ENTITY", "meta_type": "REFERENCE",
    "flow": "UNIT", "reference_target": 3
  },
  {
    "type": "ENTITY", "meta_type": "MATH_OPERATOR",
    "value": "-", "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "NUM_NATURAL",
    "value": 1, "flow": "UNIT"
  },
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "flow": "END"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "FUNC_CALL", "flow": "END"
  },
  {
    "type": "ENTITY", "meta_type": "MATH_EXPRESSION",
    "flow": "END"
  },
  {
    "type": "ENTITY", "meta_type": "CONTROL",
    "value": "FUNC_DEF", "flow": "END"
  }
]
```

### E.2 FizzBuzz with Pattern Matching (Multi-paradigm)

**BBP-AST token stream (annotated):**

```
Token 0:  [CONTROL, FUNC_DEF, flow=START]
Token 1:  [NAME_WORK, LITERAL, "fizzbuzz", 8 bytes]
          [raw: 66 69 7A 7A 62 75 7A 7A]
Token 2:  [NAME_WORK, flow=END, ABS, SINGULAR, NONE]
Token 3:  [NAME_WORK, LITERAL, "n", 1 byte]
          [raw: 6E 00 00 00]
Token 4:  [NAME_WORK, flow=END, ABS, SINGULAR, NONE]

Token 5:  [CONTROL, MATCH, flow=START]

Token 6:    [CONTROL, MATCH_ARM, flow=START]
              — pattern: n % 15 == 0 —
Token 7:      [MATH_EXPRESSION, flow=START]
Token 8:        [REFERENCE, FEATURES=3, UNIT]          → n
Token 9:        [MATH_OPERATOR, FEATURES=% (mod)]
Token 10:       [NUM_NATURAL, FEATURES=15]
Token 11:       [MATH_OPERATOR, FEATURES=== (eq)]
Token 12:       [NUM_NATURAL, FEATURES=0]
Token 13:     [MATH_EXPRESSION, flow=END]
              — body: return "FizzBuzz" —
Token 14:     [CONTROL, RETURN, flow=UNIT]
Token 15:     [NAME_WORK, LITERAL, "FizzBuzz", 8 bytes]
              [raw: 46 69 7A 7A 42 75 7A 7A]
Token 16:     [NAME_WORK, flow=END, ABS]
Token 17:   [CONTROL, MATCH_ARM, flow=END]

Token 18:   [CONTROL, MATCH_ARM, flow=START]
              — pattern: n % 3 == 0 —
Token 19:     [MATH_EXPRESSION, flow=START]
Token 20:       [REFERENCE, FEATURES=3, UNIT]
Token 21:       [MATH_OPERATOR, FEATURES=%]
Token 22:       [NUM_NATURAL, FEATURES=3]
Token 23:       [MATH_OPERATOR, FEATURES===]
Token 24:       [NUM_NATURAL, FEATURES=0]
Token 25:     [MATH_EXPRESSION, flow=END]
Token 26:     [CONTROL, RETURN, flow=UNIT]
Token 27:     [NAME_WORK, LITERAL, "Fizz", 4 bytes]
              [raw: 46 69 7A 7A]
Token 28:     [NAME_WORK, flow=END, ABS]
Token 29:   [CONTROL, MATCH_ARM, flow=END]

Token 30:   [CONTROL, MATCH_ARM, flow=START]
              — pattern: n % 5 == 0 —
              — ... (similar structure) ... —
Token 39:   [CONTROL, MATCH_ARM, flow=END]

Token 40:   [CONTROL, COND_ELSE, flow=START]          → default arm
Token 41:     [CONTROL, RETURN, flow=UNIT]
Token 42:     [REFERENCE, FEATURES=3, UNIT]             → return n
Token 43:   [CONTROL, COND_ELSE, flow=END]

Token 44: [CONTROL, MATCH, flow=END]
Token 45: [CONTROL, FUNC_DEF, flow=END]
```

---

## Appendix F: BBP-CoT Test Vectors and Examples

### F.1 Simple Deductive Reasoning

**Problem:** "Is 42 prime?"

```
[CONTROL, REASONING_START, flow=START]

  [HYPOTHESIS, flow=START, FEATURES=0x001]           → PROPOSITION, priority=0, id=1
    [NUM_NATURAL, FEATURES=42, ABS, SINGULAR, DEF]
    [ADJECTIVE, "prime", ABS, SINGULAR, DEF]            → FEATURES = dict_id for "prime"
    [UAT: "be", NOR, PRES, IMPERF, AFF, IND, S:HURA]
  [HYPOTHESIS, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x301]             → COMPUTATION, weight=0, id=1
    [MATH_EXPRESSION, flow=START]
      [NUM_NATURAL, FEATURES=42, ABS]
      [MATH_OPERATOR, FEATURES=%]
      [NUM_NATURAL, FEATURES=2, INST]
      [MATH_OPERATOR, FEATURES===]
      [NUM_NATURAL, FEATURES=0, ABS]
    [MATH_EXPRESSION, flow=END]
  [EVIDENCE, flow=END]

  [RETRACTION, FEATURES=0x001, flow=UNIT]            → reason=CONTRADICTED, target=id 1
  [REFERENCE, FEATURES=<HYPOTHESIS token index>, flow=UNIT]

  [INFERENCE_STEP, FEATURES=0x001, flow=UNIT]        → DEDUCTIVE: MODUS_TOLLENS
  [CONCLUSION, flow=START, FEATURES=0x311]           → STRONG, basis=1, id=1
    [NUM_NATURAL, FEATURES=42, ABS, SINGULAR, DEF]
    [ADJECTIVE, "prime", ABS, SINGULAR, DEF]
    [UAT: "be", NOR, PRES, IMPERF, NEG, IND, S:HURA]
      → "42 is not prime"
  [CONCLUSION, flow=END]
  [CONFIDENCE, FEATURES=4000, flow=UNIT]             → very high confidence

[CONTROL, REASONING_END, flow=END]
```

### F.2 Reasoning About Code (Loop Termination)

**Context:** Given a BBP-AST while-loop, prove it terminates.

```
[CONTROL, REASONING_START, flow=START]

  [QUERY, flow=START, FEATURES=0x400]                → VERIFICATION, urgency=0, id=0
    [REFERENCE, FEATURES→LOOP_WHILE token]
    [DICT_WORD, "terminate", ABS, SINGULAR]
    [UAT: "do", NOR, PRES, IMPERF, AFF, POT, S:HURA]
      → "Can [this loop] terminate?"
  [QUERY, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x601]             → CONSTRAINT, weight=0, id=1
    — BBP-NL: "The counter variable is decremented by 1 each iteration" —
    [DICT_WORD, "counter", ERG, SINGULAR, DEF]
    [NUM_NATURAL, FEATURES=1, INST]
    [DICT_WORD, "iteration", INE, SINGULAR, INDEF]
    [UAT: "decrement", NOR-NORK, PRES, IMPERF, AFF, PASSIVE, IND, S:HURA, O:HURA]
  [EVIDENCE, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x302]             → COMPUTATION, weight=0, id=2
    — BBP-AST: the decrement assignment —
    [CONTROL, ASSIGN, flow=UNIT]
    [REFERENCE, FEATURES→"counter"]
    [MATH_EXPRESSION, flow=START]
      [REFERENCE, FEATURES→"counter"]
      [MATH_OPERATOR, FEATURES=−]
      [NUM_NATURAL, FEATURES=1]
    [MATH_EXPRESSION, flow=END]
  [EVIDENCE, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x603]             → CONSTRAINT, weight=0, id=3
    — BBP-NL: "The loop condition is counter > 0" —
    [MATH_EXPRESSION, flow=START]
      [REFERENCE, FEATURES→"counter"]
      [MATH_OPERATOR, FEATURES=>]
      [NUM_NATURAL, FEATURES=0]
    [MATH_EXPRESSION, flow=END]
  [EVIDENCE, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x014]             → PREMISE, weight=0, id=4
    — BBP-NL: "Natural numbers bounded below by 0 and decremented by 1
       must eventually reach 0" —
    [DICT_WORD, "number", ABS, PLURAL, DEF]
    [ADJECTIVE, "natural", ABS]
    [NUM_NATURAL, FEATURES=0, ALL_TERM]
    [UAT: "reach", NOR-NORK, FUT, PERF, AFF, IND, S:HAIEK, O:HURA]
  [EVIDENCE, flow=END]

  [INFERENCE_STEP, FEATURES=0x502, flow=UNIT]        → MATHEMATICAL: INDUCTION_BASE
  [INFERENCE_STEP, FEATURES=0x503, flow=UNIT]        → MATHEMATICAL: INDUCTION_STEP
  [CONCLUSION, flow=START, FEATURES=0x341]           → DEFINITIVE, basis=4, id=1
    [REFERENCE, FEATURES→LOOP_WHILE token]
    [UAT: "terminate", NOR, FUT, PERF, AFF, IND, S:HURA]
      → "[The loop] will terminate"
  [CONCLUSION, flow=END]
  [CONFIDENCE, FEATURES=4050, flow=UNIT]             → near-certain (mathematical proof)

[CONTROL, REASONING_END, flow=END]
```

### F.3 Reasoning with Retraction (Debugging)

**Scenario:** Model hypothesizes a bug cause, finds contradicting evidence, retracts, and reaches the correct conclusion.

```
[CONTROL, REASONING_START, flow=START]

  [QUERY, flow=START, FEATURES=0x000]                → FACTUAL
    — "Why does the function return wrong results for negative input?" —
  [QUERY, flow=END]

  [HYPOTHESIS, flow=START, FEATURES=0x001]           → PROPOSITION, id=1
    — "The comparison operator is wrong (< instead of <=)" —
    [DICT_WORD, "operator", ABS, SINGULAR, DEF]
    [ADJECTIVE, "comparison", ABS]
    [ADJECTIVE, "wrong", ABS]
    [UAT: "be", NOR, PRES, IMPERF, AFF, IND, S:HURA]
  [HYPOTHESIS, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x302]             → COMPUTATION, id=2
    — BBP-AST: the actual comparison in the code —
    [MATH_EXPRESSION, flow=START]
      [REFERENCE, FEATURES→"n"]
      [MATH_OPERATOR, FEATURES=≤]
      [NUM_NATURAL, FEATURES=1]
    [MATH_EXPRESSION, flow=END]
  [EVIDENCE, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x003]             → OBSERVATION, id=3
    — "The operator is ≤ (correct), not < (wrong)" —
    [DICT_WORD, "operator", ABS, SINGULAR, DEF]
    [ADJECTIVE, "correct", ABS]
    [UAT: "be", NOR, PRES, IMPERF, AFF, IND, S:HURA]
  [EVIDENCE, flow=END]

  [RETRACTION, FEATURES=0x001, flow=UNIT]            → CONTRADICTED, target=id 1
  [REFERENCE, FEATURES→HYPOTHESIS 1 token, flow=UNIT]

  [HYPOTHESIS, flow=START, FEATURES=0x002]           → PROPOSITION, id=2
    — "The function doesn't handle negative numbers" —
    [DICT_WORD, "function", ERG, SINGULAR, DEF]
    [DICT_WORD, "number", ABS, PLURAL, INDEF]
    [ADJECTIVE, "negative", ABS]
    [UAT: "handle", NOR-NORK, PRES, IMPERF, NEG, IND, S:HURA, O:HAIEK]
  [HYPOTHESIS, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x304]             → COMPUTATION, id=4
    — BBP-AST: trace showing factorial(-1) → infinite recursion —
    [CONTROL, FUNC_CALL, flow=START]
      [REFERENCE, FEATURES→"factorial"]
      [NUM_INTEGER, FEATURES=-1, ABS]                → negative input
    [CONTROL, FUNC_CALL, flow=END]
    — n=-1, n≤1 is true, returns 1... wait —
  [EVIDENCE, flow=END]

  — The model realizes n=-1 ≤ 1 IS true, so it returns 1, not infinite recursion.
     But factorial(-1) should be undefined, not 1. —

  [RETRACTION, FEATURES=0x102, flow=UNIT]            → SUPERSEDED, target=id 2
  [REFERENCE, FEATURES→HYPOTHESIS 2 token, flow=UNIT]

  [HYPOTHESIS, flow=START, FEATURES=0x003]           → PROPOSITION, id=3
    — "The base case catches negatives but returns 1 instead of error" —
  [HYPOTHESIS, flow=END]

  [EVIDENCE, flow=START, FEATURES=0x305]             → COMPUTATION, id=5
    — BBP-AST: showing n=-3, -3 ≤ 1 → returns 1 —
  [EVIDENCE, flow=END]

  [INFERENCE_STEP, FEATURES=0x000, flow=UNIT]        → DEDUCTIVE: MODUS_PONENS
  [CONCLUSION, flow=START, FEATURES=0x231]           → STRONG, basis=3, id=1
    — "The function should validate n ≥ 0 before the base case" —
    [DICT_WORD, "function", ERG, SINGULAR, DEF]
    [DICT_WORD, "input", ABS, SINGULAR, DEF]
    [UAT: "validate", NOR-NORK, PRES, IMPERF, AFF, IMP, S:HURA, O:HURA]
    [MATH_EXPRESSION, flow=START]
      [REFERENCE, FEATURES→"n"]
      [MATH_OPERATOR, FEATURES=≥]
      [NUM_NATURAL, FEATURES=0]
    [MATH_EXPRESSION, flow=END]
  [CONCLUSION, flow=END]
  [CONFIDENCE, FEATURES=3500, flow=UNIT]

[CONTROL, REASONING_END, flow=END]
```

This example demonstrates the distinctive power of BBP-CoT: the reasoning trace includes two retractions, each with explicit structural links to the invalidated hypothesis, code evidence via BBP-AST references, and a final conclusion with rated confidence. An external verifier can reconstruct the entire reasoning graph and validate its logical structure.

---

## Appendix G: Updated Design Decisions

| Decision | Rationale | Trade-off |
|----------|-----------|-----------|
| Code constructs as CONTROL payloads, not new meta-types | Reuses existing CONTROL mechanics; 32 slots in 0x060-0x07F suffice; no meta-type budget consumed | Code tokens require 2-step decoding (CONTROL → FEATURES → construct) |
| Structural context for identifiers, not dedicated meta-types | Follows compositionality principle; avoids consuming Group 3 reserved slots; consistent with BBP philosophy | Parser must track enclosing construct for identifier typing |
| Group 7 for meta-cognition, not new CONTROL payloads | Meta-cognitive blocks need START/END nesting, FEATURES sub-fields, and are ontologically distinct from control flow | Consumes 7 of 14 remaining meta-type slots |
| RETRACTION requires REFERENCE | Machine-precise target identification; structural integrity | 2 tokens per retraction (RETRACTION + REFERENCE) |
| CONFIDENCE as separate UET, not Deklinabidea reuse | Full FEATURES payload field for fine-grained confidence; clear separation | 1 extra token per annotated block |
| REASONING_START/END as CONTROL structure payloads | Consistent with SENT_START, PARA_START pattern; reasoning is structural scope | 2 tokens overhead per reasoning chain |
| Mechanical code pipeline (parser → normalizer) | Deterministic, LLM-free for production; leverages existing language tooling | LLM still needed for semantic enrichment in training |
| 24 programming constructs (not more) | Covers universal patterns; language-specific constructs via MODIFIER annotations | Very exotic constructs need composition |
| **r16: Extended voice and clusivity as bound modifier tokens** | **Zero format change; zero cost for languages that don't need them; backward compatible** | **One extra token per verb for languages that use extended voice or clusivity** |
| **r16: Forbidden aspect combinations (C5)** | **Eliminates semantically incoherent Teacher outputs at validation time** | **Loses some theoretical expressiveness for edge cases** |
| **r16: Domain-specific profiles** | **Same 32-bit format, same axioms; only vocabulary assignments change** | **Multiple vocabularies to maintain per domain** |
| **r16: CONFIDENCE dual encoding (calibration + value)** | **Makes confidence values honest; systems can filter by calibration status** | **Loses 2 bits of precision (1024 levels vs 4096; still more than sufficient)** |
| **r16: Protocol version in DOC_START** | **Forward compatibility from v1.0; no extra tokens** | **Limited to major 0-15, minor 0-63** |
| **r16: Checkpoint continuation for depth > 15** | **Simple parser logic (concatenate segments); supports depth up to 255** | **Two checkpoints needed for deep nesting** |
| **r16: Compact long-range REFERENCE (23-bit)** | **Single token, 8M range; covers virtually all documents** | **Loses inline declension (inherited from referent)** |
| **r16: Expanded programming constructs (38 total)** | **Reduces AST_OPAQUE for functional/modern languages significantly** | **Expands range into 0x080+; more constructs to implement** |
| **r16: START/END MUST match (promoted from SHOULD)** | **Eliminates structural ambiguity; deterministic parsing guaranteed** | **Breaking for implementations that relied on SHOULD** |
| **r16: Transport-layer integrity delegation** | **No format change; standard practice for binary protocols** | **Does not protect against encoder/decoder software bugs** |

---



---

## Appendix H: Mandatory Benchmarks for v1.0 Validation

Before declaring v1.0-final, the following benchmarks MUST be generated and published. These benchmarks resolve open questions identified during the specification process and provide empirical grounding for design decisions that were previously justified only by theoretical estimates.

### H.1 Token Format Validation (r21 bit layout)

**Purpose:** Verify that all parsers, compilers, and validators correctly extract fields from the current bit layout (§2.3), in which the type discriminator (bit 31) and meta-type (bits 30–25) reside in LEMMA and all 32 FEATURES bits are available for grammatical encoding. All test vectors MUST be validated against the reference implementation before v1.0-final is declared.

**Scope:** The following field extractions MUST be tested for both Action Tokens and Entity Tokens across a corpus of at least 10,000 synthetic tokens with randomized field values.

#### H.1.1 Required extraction tests — Action Token

| Field | LEMMA bits | Extraction expression | Test coverage |
|---|---|---|---|
| Type discriminator | 31 | `(lemma >> 31) & 0x1` | All 0 values MUST yield Entity; all 1 values MUST yield Action |
| Aktionsart | 30–28 | `(lemma >> 28) & 0x7` | All 7 defined values + reserved (0x7) correctly round-tripped |
| Valency class | 27–25 | `(lemma >> 25) & 0x7` | All 7 defined values + reserved (0x7) correctly round-tripped |
| Verb ID | 24–0 | `lemma & 0x01FFFFFF` | Boundary values: 0x0, 0x1, 0x00FFFFFF, 0x01FFFFFF |

#### H.1.2 Required extraction tests — Entity Token

| Field | FEATURES bits | Extraction expression | Test coverage |
|---|---|---|---|
| Flow control | 31–30 | `(features >> 30) & 0x3` | UNIT(0), START(1), END(2), LITERAL(3) |
| Case | 29–25 | `(features >> 25) & 0x1F` | All 29 defined values; reserved slots 0x1D–0x1F MUST be rejected as MAJOR |
| Number | 24–22 | `(features >> 22) & 0x7` | All 8 values: UNMARKED(0)–DISTRIBUTIVE(7) |
| Determiner | 21–18 | `(features >> 18) & 0xF` | All 16 values |
| Noun classifier | 17–14 | `(features >> 14) & 0xF` | CLF_NONE(0)–CLF_OTHER_LITERAL(0xF); CLF_EXTENDED(0xE) MUST trigger A_CLF1 check |
| Animacy | 13–12 | `(features >> 12) & 0x3` | INANIMATE(0)–DIVINE(3) |
| Noun class | 11–8 | `(features >> 8) & 0xF` | NC_NONE(0)–NC_ANIMATE_AGREE(0xF) |
| Register/Honorific | 7–2 | `(features >> 2) & 0x3F` | speech_level(bits 7–6), respect_direction(bits 5–4), domain(bits 3–2); all 64 combinations |
| Reserved | 1–0 | `features & 0x3` | MUST be 0x0; non-zero MUST trigger MINOR warning |

#### H.1.3 Migration regression tests (r20 → r21)

The following vector pairs MUST be included to verify that the r20→r21 migration tool produces correct output for all boundary cases:

| Test case | r20 token (hex) | Expected r21 token (hex) | Notes |
|---|---|---|---|
| Entity, meta=0, concept=0 | `00000000_00000000` | `00000000_00000000` | Identity case |
| Entity, meta=0x3F, concept=0x1FFFFFF | `00000000_FFFFFFFF` (FEATURES only) | `7FE00000_...` LEMMA | Max concept_id, max meta-type |
| Action, verb_id=1 | `00000001_80000000` (old FEATURES disc=1) | `80000001_00000000` | Discriminator moves from FEATURES[31] to LEMMA[31] |
| Entity, concept_id > 0x1FFFFFF | — | MIGRATION_CONFLICT error | concept_id exceeds 25-bit r21 space |

**Blocking:** This benchmark MUST pass at 100% for all non-reserved field combinations before v1.0-final is declared. Reserved field handling (graceful rejection vs. pass-through) is SHOULD, not MUST.




**Metrics:**

| Metric | Threshold | Action if exceeded |
|--------|-----------|-------------------|
| Throughput degradation at 10% EXT density vs. 0% | < 20% | If exceeded, document SIMD-friendly EXT handling strategies |
| Branch misprediction rate increase at 10% EXT | < 5% absolute | If exceeded, investigate branchless EXT detection |

**Profiled benchmarks (mandatory):** The SIMD benchmark MUST be run on three data profiles to capture domain-dependent variation:

| Profile | Description | Expected EXT density | Source |
|---------|-------------|---------------------|--------|
| General prose | Wikipedia articles, mixed topics | 5–10% | CTS general corpus |
| Dialogue | Conversational text, chat logs, subtitles | 3–8% | CTS dialogue corpus |
| Technical/entities | Scientific papers, legal documents, medical records | 10–20% | CTS domain corpus |

**Additional reporting requirements:**

| Metric | Description |
|--------|-------------|
| P99 streak per window (256 tokens) | Longest streak at 99th percentile, per profile |
| Throughput-vs-streak correlation | Scatter plot or regression of throughput against per-window streak length. SHOULD be included; a tabular summary with correlation coefficient is an acceptable alternative for CI pipelines. |
| Branch miss proxy | If available on the benchmarking platform, report branch misprediction rate per profile |

### H.3 BBP-AST Verbosity

**Methodology:** Convert 500 functions per language (Python, Rust, TypeScript, C, Zig) from source code to BBP-AST. Measure the ratio of BBP-AST tokens to source text tokens.

**Metrics:**

| Metric | Threshold | Action |
|--------|-----------|--------|
| Mean ratio for Python | — | Document honestly (expected: 0.7–1.1×) |
| Mean ratio for Rust (idiomatic) | — | Document honestly (expected: 1.2–1.5×) |
| Mean ratio for TypeScript | — | Document honestly (expected: 1.1–1.4×) |
| Modifier-to-structure ratio | — | Document (expected: <40% for typical code) |

No hard thresholds — the value proposition of BBP-AST is navigability, not compression. Results are documented for transparency.

### H.4 Round-Trip Semantic Fidelity

**Methodology:** Process 350 sentences (50 per language × 7 languages) through the full pipeline: NL → Teacher → BBP-IR → Validator → Compiler → Binary → Decoder → BBP-IR → Renderer → NL. Human evaluators score semantic equivalence on a 1–5 scale.

**Metrics:**

| Metric | Threshold | Action if not met |
|--------|-----------|-------------------|
| Mean semantic score per language | > 3.0 / 5.0 | If not met, iterate Teacher prompt for that language |
| Mean semantic score across all languages | > 3.5 / 5.0 | If not met, investigate systematic Teacher failures |
| Validator pass rate (all languages) | > 90% | If not met, iterate Teacher prompt and axiom calibration |

### H.5 Cross-Teacher Divergence

**Methodology:** Process the same 350-sentence test set through two independent Teacher LLMs (e.g., Claude and GPT-4). Compare the resulting BBP-IRs token by token.

**Metrics:**

| Metric | Threshold | Action |
|--------|-----------|--------|
| Structural divergence rate (different system tags, case assignments) | < 10% | Divergences adjudicated by human linguist; decisions recorded in BBP Linguistic Conventions |
| Stylistic divergence rate (modifier ordering, DISAMBIGUATION thresholds) | — | Resolved by convention; documented |
| Languages with divergence > 15% | 0 | If any language exceeds 15%, investigate and resolve before v1.0-final |

### H.6 AST_OPAQUE Density

**Methodology:** Convert 500 functions/modules per language from source code to BBP-AST using the Code Frontier Translator pipeline (§16.11). Measure the percentage of BBP-AST tokens that fall inside AST_OPAQUE blocks.

**Metrics:**

| Source Language | Max AST_OPAQUE % | Action if exceeded |
|----------------|-------------------|---------------------------------|
| Python | < 5% | Investigate converter quality; likely missing construct mapping |
| JavaScript/TS | < 10% | Investigate converter quality |
| C | < 5% | Investigate converter quality |
| Java | < 10% | Investigate; decorators/annotations should use DECORATOR (0x07E) |
| Rust | < 20% | Document which constructs remain opaque; prioritize for r17 |
| Haskell | < 30% | Document; consider Group 5 extensions for functional patterns |
| Zig | < 15% | Investigate; comptime should map to TYPE_CONSTRAINT/GENERIC_PARAM |

**Critical threshold:** If any language exceeds 40% AST_OPAQUE density, the BBP-AST claim of "language-agnostic navigability" is NOT met for that language. The language SHOULD be excluded from BBP-AST support claims until the converter is improved or additional CONTROL payloads are defined.

**Per-construct breakdown:** For each language, publish the top 10 source-language constructs that map to AST_OPAQUE, ranked by frequency. This data drives prioritization of new CONTROL payloads in future revisions.

## Appendix I: Companion Document Stubs

The following companion documents are scoped for future development. They do NOT block v1.0-final but are required for specific deployment milestones.

### I.1 BBP Entity Table Protocol

**Purpose:** Define the contract for KEYFRAME entity tables in multi-agent environments where multiple agents produce and consume BBP streams with overlapping entity references.

**Scope:**

| Topic | Description |
|-------|-------------|
| Namespaces | Entity identity scoped by `{agent_id, session_id, stream_id}` |
| Collision resolution | Deterministic strategy when two agents assign the same local_alias to different entities (priority by scope, timestamp, or trust tier) |
| TTL and eviction | Rules for expiring entity table entries in long-lived or infinite streams |
| Merge protocol | Deterministic merge of entity tables from different agents (CRDT-simple or authority-based) |
| ONTOLOGY_VERSION interaction | Behavior when a KEYFRAME references entities from a different ontology version: compatible major → apply migration mappings; incompatible major → reject or mark as opaque (per Axiom H2 / ONTOLOGY-2, §13.2.1: a MAJOR version mismatch is a CRITICAL error; the receiving agent MUST reject the KEYFRAME entity table and MAY fall back to in-stream REFERENCE resolution) |
| Backward compatibility | Agents that do not implement the Entity Table Protocol MUST still be able to decode KEYFRAME tokens (they simply ignore the entity table and rely on in-stream REFERENCE resolution) |

**Blocking:** This document blocks the "multi-agent interoperability" claim (Hito C in the implementation roadmap). It does NOT block v1.0-final.

### I.2 BBP Observability Guide

**Purpose:** Define standard metrics, thresholds, and export interfaces for monitoring BBP encoding/decoding health in production systems.

**Minimum Metrics:**

| Metric | Type | Export |
|--------|------|--------|
| Streak max | uint | Per-stream |
| Streak P95/P99 per window | uint | Per-stream |
| Max nesting depth | uint | Per-stream |
| KEYFRAME count / STATE_CHECKPOINT count | uint / uint | Per-stream |
| Validator errors (by axiom group) | count map | Per-stream |
| Teacher divergence (slot-level) | float per slot | Per CTS/Gold run |

**Export Interface:** The reference library MUST return metrics as a structured object (e.g., `EncodeMetrics`, `DecodeMetrics`) at the end of encode/decode operations — not only as log output. This enables programmatic consumption by CI pipelines and monitoring systems.

**Blocking:** This document blocks production deployment recommendations. It does NOT block v1.0-final.

---

## Appendix J: Changelog History

### Changelog (r38 → r39)

Incoherence resolution pass. All 10 incoherencies identified in the r38 review have been corrected. No new normative content added.

| ID | Change | Section | Description |
|----|--------|---------|-------------|
| I-1 | §11.5 CONTROL registry: 0x054 registrado | §11.5 | CRÍTICA: La fila `0x054–0x05F Reserved` reemplazada por dos filas: `0x054 NCBI_TAXONOMY_VERSION` (con descripción normativa completa y referencia a §11.3 y V-NCBI-VERSION-1) y `0x055–0x05F Reserved`. Elimina la contradicción entre §11.3 (0x054 asignado) y §11.5 (0x054 Reserved). |
| I-2 | §10.2: definición del bloque extended NUM_FRACTION | §10.2 | CRÍTICA: Añadida subsección «Large fractions — compositional START/END block» con token stream example, descripción de campos (NUM_INTEGER numerador signed + NUM_NATURAL denominador unsigned), invariante de denominador ≠ 0, regla de preferencia inline, y referencias cruzadas a J6/J7. Los axiomas J6/J7 ya existían pero su objeto no estaba definido en §10.2. |
| I-3a | §5.2: V-LIVING-3, V-LIVING-4, política de estabilidad de taxon_id | §5.2 | CRÍTICA: Añadidas V-LIVING-3 (consistencia kingdom vs. NCBI_TAXONOMY_VERSION, MAJOR auto-correctable), V-LIVING-4 (taxon_id deprecated: MAJOR, auto-correct solo en 1:1, no en 1:N) y la política normativa de estabilidad de taxon_id relativa a la versión NCBI declarada. Estas adiciones estaban declaradas en el changelog de r38 pero no aplicadas al body. |
| I-3b | §5.2.3: ADJECTIVE(FUNCTIONALLY_EXTINCT), ADJECTIVE(ENDANGERED), ADJECTIVE(INVASIVE), V-LIVING-5 | §5.2.3 | CRÍTICA: Añadidas definiciones de los tres ADJECTIVE de estado de conservación con semántica precisa y orden canónico (categoría 4). Añadido ejemplo del rinoceronte blanco del norte (taxon=57771). Añadida V-LIVING-5 (FUNCTIONALLY_EXTINCT + vital_status=EXTINCT = contradicción semántica, MAJOR). Estas adiciones estaban declaradas en el changelog de r38 pero no aplicadas al body. |
| I-4 | §13.4: nota de alcance sobre §13.4.1 Renderer | §13.4 | MAYOR: Añadida nota al final de §13.4 aclarando que el Renderer y el Decoder son componentes arquitecturalmente distintos y que §13.4.1 especifica el Renderer de producción. Justifica el placement bajo §13.4 (el test suite del Decoder ejercita la misma lógica de gender agreement). Se documenta que §13.7 sería la ubicación correcta en una revisión futura con sección Renderer completa. |
| I-5 | §15.5.0a: referencia a tabla normativa de §15.8 | §15.5.0a | MAYOR: La frase «what constitutes "sufficiently low" is a project-level empirical decision that MUST be documented and published before pre-training begins» reemplazada por referencia directa a la tabla de targets de §15.8. Elimina la contradicción entre §15.5.0a (targets indefinidos) y §15.8 (targets especificados normativamente). |
| I-6 | §15.5.1 todo-list: corregido para reflejar summation como decisión tomada | §15.5.1 | MAYOR: El todo-list que listaba «sum, concatenation + projection, or cross-attention» como opciones equivalentes, describía concatenación en la nota de arquitectura recomendada, y proponía «Evaluate alternative: binary-field encoding» y «Benchmark on BBP-700K» — todo ello contradictorio con la decisión normativa de summation adoptada en r38. Reemplazado por todo-list coherente que confirma summation, actualiza la tabla de campos, e incluye únicamente tareas de implementación consecuentes con la decisión tomada. |
| I-7 | §5.3 ↔ §13.6.6: referencias cruzadas y SHOULD→MUST | §5.3.6, §5.3.7, §13.6.6 | MAYOR: Añadida nota al final de §5.3.7 remitiendo a §13.6.6 para el pipeline completo. §5.3.6 actualizado: SHOULD→MUST para el uso del índice por el Frontier Translator (alineado con el MUST de §13.6.6). Añadida nota de cross-reference al inicio de §13.6.6 remitiendo a §5.3 para la especificación normativa de estructura y ordering. |
| I-8 | §5.2.3: eliminada referencia residual a «PROPOSAL-1» | §5.2.3 | MENOR: «The natural_gender field in PROPOSAL-1 (§2.3)» → «The natural_gender field (§2.3)». PROPOSAL-1 es el nombre de diseño interno; la decisión es normativa desde r31. |
| I-9 | §15.4.2 / §15.4.5 / §15.5 / §15.6: BBP-700K → BBP-TrC | §15.4.2, §15.4.5, §15.5, §15.6 | MENOR: El nombre «BBP-700K» implica un corpus de tamaño fijo, contradictorio con los criterios de calidad de §15.8. Renombrado a «BBP-TrC» (BBP Training Corpus) en todas las referencias de §15.4.5, §15.5 y §15.6. El valor 700K permanece únicamente donde aparece explícitamente como estimación histórica de planificación (§15.4.2). |
| I-10 | §15.4.2: actualizado todo item de tamaño de corpus | §15.4.2 | MENOR: El todo item que decía «100,000 sentences per language, 700,000 total (estimated)» como target reemplazado por texto que referencia §15.8 como criterio de parada y retiene el 700K sólo como baseline de planificación. Elimina el duplicado parcial con §15.5.0a. |

### Changelog (r37 → r38)

| Change | Section | Description |
|--------|---------|-------------|
| §2.3: taxonomía normativa referential/self-descriptive ampliada | §2.3 | C-1 (CRÍTICO): La sección referential/self-descriptive reescrita con lista canónica completa de miembros por clase, regla normativa explícita para Compiler y parser, y nueva regla de Validator V-METATYPE-CLASS (meta-type sin clase declarada → tratar como UNKNOWN 0o77, CRITICAL). Sustituye el bloque anterior con ejemplos sueltos por una taxonomía formal. |
| §5.0: nueva sección preámbulo de clases ontológicas | §5.0 (nuevo) | C-1 (CRÍTICO): Inserción de §5.0 antes del catálogo §5.1. Declara que toda sección de meta-type DEBE especificar su clase explícitamente (omisión = error de spec). Tabla de referencia rápida de asignaciones de clase. |
| §5.3 Lexicalization Index: especificación completa | §5.3 (nuevo) | M-1 (MAYOR): Nueva sección §5.3 con especificación normativa del Lexicalization Index: estructura de datos (canonical_pattern → {lang_id → surface_form}), ordenamiento canónico de modificadores (normativo, 5 categorías con desempate por concept_id), umbral de inclusión (frecuencia ≥ N + forma idiomática ≠ salida composicional), integración con Renderer (DEBE consultar antes de generación composicional), integración bidireccional con Frontier Translator, crecimiento automático durante entrenamiento. Nueva regla V-MOD-ORDER (orden incorrecto → MAJOR auto-correctable). El antiguo §5.3 pasa a §5.4. |
| §5.2.3: estados de conservación y V-LIVING-5 | §5.2.3 | m-1 (MENOR): Añadidos ADJECTIVE(FUNCTIONALLY_EXTINCT), ADJECTIVE(ENDANGERED), ADJECTIVE(INVASIVE) con definiciones semánticas y orden canónico (categoría 4). Nueva regla V-LIVING-5: FUNCTIONALLY_EXTINCT + vital_status=EXTINCT es contradicción semántica (MAJOR). Nuevo ejemplo: rinoceronte blanco del norte (taxon=57771). |
| §5.2: V-LIVING-3, V-LIVING-4 y política de estabilidad | §5.2 | M-4 (MAYOR): Nuevas reglas V-LIVING-3 (consistencia kingdom vs. NCBI_TAXONOMY_VERSION declarado, MAJOR), V-LIVING-4 (taxon_id deprecated → MAJOR, auto-correct solo en redireccionamientos 1:1, no en splits 1:N). Añadida política de estabilidad de taxon_id. |
| §10.2: ordering normativo de NUM_COMPLEX | §10.2 | C-3 (CRÍTICO): Declaración explícita de que el PRIMER token NUM_REAL en bloque NUM_COMPLEX es la parte real y el SEGUNDO es la imaginaria. Orden fijo. Invertir produce el conjugado complejo — stream semánticamente incorrecto aunque estructuralmente válido. |
| §10.2: extended form de NUM_FRACTION | §10.2 | M-2 (MAYOR): Especificación de bloque composicional START/END para fracciones con componentes > 16 bits: NUM_INTEGER (numerador, int32, signed) + NUM_NATURAL (denominador, uint32, > 0). La forma inline (16+16b) sigue siendo canónica cuando los componentes caben en 16b. |
| Axiomas J5, J6, J7 añadidos al Axiom Group J | §10.3.3 | C-3+M-2: J5 — ordering NUM_COMPLEX (CRITICAL). J6 — estructura de bloque extended NUM_FRACTION: exactamente dos tokens, NUM_INTEGER + NUM_NATURAL, denominador ≠ 0 (CRITICAL). J7 — uso de extended form cuando inline es suficiente (MAJOR, auto-correctable). |
| §10.4: BBP Runtime Minimum Specification (nueva sección) | §10.4 (nuevo) | C-2 (CRÍTICO): Nueva sección §10.4 con tres subsecciones. §10.4.1: alcance v1.0 limitado a aritmética concreta; expresiones con variables no ligadas → MATH_ERROR(UNDEFINED); álgebra simbólica diferida a v1.1. §10.4.2: jerarquía de promoción de tipos (NUM_NATURAL → NUM_INTEGER → NUM_FRACTION → NUM_REAL); forma canónica normativa para NUM_FRACTION output del Runtime (denominador positivo, signo en numerador, gcd=1). §10.4.3: modelo de integración post-generación; Core Engine MUST NOT generar token resultado dentro de bloque EXECUTABLE (CRITICAL si lo hace). |
| §11.3: Stream Identity Principle + NCBI_TAXONOMY_VERSION | §11.3 | M-4 (MAYOR): Stream Identity Principle declarado como normativo: formato de stream en entrenamiento y producción DEBEN ser idénticos. Nuevo token NCBI_TAXONOMY_VERSION (FEATURES=0x054): MUST estar en DOC_START de cualquier stream con LIVING_ENTITY (ausencia = MAJOR error); formato LITERAL de 3 bytes (year uint16 big-endian + month uint8) + 5 bytes padding; ejemplo: `07 EA 03` para marzo 2026. Regla V-NCBI-VERSION-1 (payload incorrecto = CRITICAL). Orden de emisión DOC_START actualizado: PROFILE → ONTOLOGY_VERSION → NCBI_TAXONOMY_VERSION → STATE_CHECKPOINT. |
| §13.4.1: Renderer — resolución de concordancia de género verbal | §13.4.1 (nuevo) | m-2 (MENOR): Algoritmo normativo de resolución de género verbal para lenguas con morfología verbal de género (árabe, hebreo, amhárico, bantú). Para DICT_WORD/NAME_PERSON/PRONOUN/ADJECTIVE: usar natural_gender de LEMMA bits 24–23. Para LIVING_ENTITY: usar noun_class de FEATURES; fallback a ADJECTIVE(BIOLOGICAL_MALE/FEMALE); fallback a default de lengua. Regla V-RENDER-GENDER-1 (MAJOR, verificable via Gold Set). Nota: LIVING_ENTITY no puede tener natural_gender en LEMMA por conflicto de bits con kingdom+taxon_id. |
| §15.5.1: combinación de embeddings por suma (decisión normativa) | §15.5.1 | M-3 (MAYOR): La sección "Combination method (normative decision required)" —que dejaba la elección abierta entre suma y concatenación— reemplazada por decisión normativa: **summation** (suma element-wise) es el mecanismo normativo. Fórmula explícita: embedding(token) = Σᵢ Eᵢ[fieldᵢ(token)]. Lista completa de campos con tabla de embedding independiente (Action Tokens, Entity Tokens, meta-tipos auto-descriptivos). Política normativa para valores cero (campo genuinamente ausente → vector cero fijo; categoría no marcada significativa → embedding aprendido). Rationale de elección de suma sobre concatenación. |
| §15.8: criterio de volumen de corpus reemplazado por criterios de calidad | §15.8 | m-4 (MENOR): El criterion "700K validated instances" eliminado como criterio primario. Reemplazado por dos criterios de parada: (1) error rate del Frontier Translator en o por debajo del objetivo conservador para todos los idiomas de la fase; (2) saturación de cobertura (< 1% combinaciones nuevas por 10K instancias adicionales). Tabla de targets de error rate por fase (optimista/conservador). Política go/no-go: la transición de fase está condicionada al objetivo conservador, no al optimista. El volumen es una consecuencia de la calidad, no un objetivo en sí mismo. |
| §17.5.1: medida anti-gaming normativa mínima (nueva sección) | §17.5.1 (nuevo) | m-3 (MENOR): Nueva §17.5.1 que promueve al menos un mecanismo anti-gaming a normativo. Implementaciones que entrenan Core Engine con datos BBP-CoT DEBEN implementar al menos uno de: Mecanismo A (outcome verification: verificación de conclusiones contra ground truth, down-weighting de conclusiones incorrectas) o Mecanismo B (perturbation probes: mínimo 10% de corpus con variantes estructuralmente inválidas, verificación de que modelo asigna menor probabilidad a estructuras inválidas). Ninguno = no-conformante para cualquier implementación que declare soporte BBP-CoT. |

### Changelog (r35 → r36)

| Change | Section | Description |
|--------|---------|-------------|
| Eliminación de referencias residuales a BBP-Lite en campos gramaticales | §4.5, §4.6, §4.9 | Sustituidas referencias a BBP-Lite en tablas de Case (UNMARKED), Number (UNMARKED) y Animacy por terminología encoder-neutral: "sparse encoders", "encoders that omit number", "source language or encoder". La distinción es del encoder, no del protocolo. |
| Token PROFILE (§11.5): redefinido como provenance tag | §11.5 | El CONTROL token 0x052 PROFILE deja de declarar BBP-Full (0) / BBP-Lite (1) y pasa a ser un encoder provenance tag informativo: 0x000 = Frontier Translator (Teacher LLM); 0x001 = deterministic parser (§13.1.1). MUST NOT usarse para declarar subconjuntos del protocolo. Actualizado el example DOC_START y el registry de §11.5. |
| §12 case assignment: etiquetas de reglas actualizadas | §12.1 | "Rules for BBP-Full (Teacher LLM)" → "Rules for Frontier Translator (Teacher LLM encoder)". "Rules for BBP-Lite (deterministic encoder)" → "Rules for deterministic parser encoder (§13.1.1)". Añadida nota sobre uso del PROFILE provenance tag (0x001) en encoders deterministas. |
| Glosario Appendix A: definiciones BBP-Full/Lite actualizadas | Appendix A | BBP-Full y BBP-Lite marcados como términos informales deprecated como perfiles de protocolo. Redefinidos en términos de clase de encoder con referencias a §2.8 y §13.1.1. |
| Appendix B design decisions: fila BBP-Full/Lite actualizada | Appendix B | Fila renombrada a "Encoder-type distinction vs. protocol profiles". Rationale actualizado: protocolo único BBP-64 con dos clases de encoder. Trade-off documentado: streams dispersos son válidos pero con menor información semántica. |
| §2.3: MATH_STRUCTURE (pending) → deferred to v1.1 | §2.3 | En la lista de meta-types auto-descriptivos, MATH_STRUCTURE actualizado de "(pending)" a "deferred to v1.1 (§5.1)" para consistencia con la tabla de §5.1. |
| §17.6.3: párrafo "Fundamental limitation" sustituido por cross-reference | §17.6.3 | El párrafo normativo "Fundamental limitation" de §17.6.3 era duplicado de §17.5.0 (nueva sección normativa de r35). Sustituido por bloque citando §17.5.0 para eliminar la redundancia y la ambigüedad de status (normativo vs. informativo). |
| §10.3.4: título actualizado a Mathematical Reasoning Track (M) | §10.3.4 | Título renombrado de "Mathematical Curriculum" a "Mathematical Reasoning Track (M)". Párrafo introductorio actualizado para referenciar el Reasoning Curriculum de §15.5.0b del que forma parte como Track M. |

### Changelog (r34 → r35)

| Change | Section | Description |
|--------|---------|-------------|
| Nota de estado de implementación y génesis | Prólogo | INFORMATIVO: Añadida nota al final del Prólogo declarando el estado teórico del spec (sin implementación, sin datos empíricos), documentando el origen del proyecto (hipótesis del autor desarrollada iterativamente con asistentes LLM: Claude, Gemini, ChatGPT) y declarando que la participación de IAs no valida ni invalida la arquitectura. |
| Corrección de cita EMNLP 2025 | §1.11.3 | CORRECCIóN CRÍTICA: Sustituidos números no verificados (98.7%, 53.5%, 69.7%) por datos reales del abstract verificado: 88.6% de reducción media de cross-entropy loss y 15.4× más problemas resueltos correctamente respecto a CoT y LoRA (Llama 3.1 8B, Symbolic-Math Dataset). Cita completa: Dhanraj & Eliasmith, EMNLP 2025, pp. 30589–30608. DOI: https://doi.org/10.18653/v1/2025.emnlp-main.1556. |
| Eliminación de BBP-Lite / BBP-Full como perfiles de protocolo | §2.8 | NORMATIVO: Los perfiles BBP-Lite y BBP-Full eliminados del protocolo. La distinción es una propiedad del encoder, no del stream. §2.8 degradado a INFORMATIVE. Se preserva el mecanismo PROFILE en CONTROL tokens (§11.5) únicamente para etiquetado de provenance, no para declarar subconjuntos del protocolo. |
| LITERAL blocks — especificación normativa completa | §4.4 | NORMATIVO: Especificación completa de LITERAL blocks: (1) Invariante de alineación: todo contenido raw DEBE ser extendido con bytes 0x00 hasta múltiplo de 8 bytes. (2) FEATURES del token de apertura = longitud real en bytes (sin padding). Parser lee ceil(FEATURES/8)×8 bytes. (3) Axiom Group K, regla K.1: bytes de padding MUST be 0x00 (violación CRITICAL). (4) Opción B normativa: FEATURES del token de cierre (flow=END) son los campos gramaticales del concepto; FEATURES de apertura es exclusivamente longitud. |
| MATH_STRUCTURE diferido a v1.1 | §5.1 (Grupo 4) | NORMATIVO: 0o47 etiquetado como Reserved — deferred to v1.1. Documentadas las tres condiciones desbloqueantes: (1) mecanismo de codificación del contenido estructural; (2) extensión de MATH_OPERATOR para operaciones estructurales; (3) extensión del flag EXECUTABLE a álgebra simbólica. |
| NUM_COMPLEX redefinido como bloque composicional | §10.1, §10.2 | NORMATIVO: NUM_COMPLEX ya no usa LITERAL block. Se codifica como bloque START/END con dos tokens NUM_REAL interiores (float32 × 2). Axioma de Validator: un bloque NUM_COMPLEX START DEBE contener exactamente dos tokens NUM_REAL. Preserva precisión float32, facilita extensión a float64 en v1.1 sin cambiar la estructura exterior. |
| Parser determinista como alternativa al Frontier Translator | §13.1.1 (nuevo) | NORMATIVO: Cuando el input es estructurado y completamente tipado (sensores, eventos tipados, ASTs, resultados de BD), el Frontier Translator PUEDE ser reemplazado por un parser determinista. El stream resultante es BBP-64 válido indistinguible. La elección de encoder es una decisión de deployment, no una distinción de protocolo. |
| Jerarquía de decisión NL→BBP del Frontier Translator | §13.6.6 | NORMATIVO: Añadida jerarquía de decisión de cuatro pasos para dirección NL→BBP: (1) lookup de diccionario directo; (2) consulta del índice de lexicalización; (3) descomposición composicional; (4) LITERAL block + DISAMBIGUATION. Los pasos 1–3 DEBEN intentarse en orden. Emitir LITERAL para un concepto con entrada en el diccionario es no-conformante. |
| Curriculum de Razonamiento (extensión de Curriculum Matemático) | §15.5.0b | NORMATIVO: §15.5.0b renombrado de “Mathematical Curriculum” a “Reasoning Curriculum”. Añadido Formal Logical Reasoning Track (L): Fase L-A (lógica proposicional verificada por SAT solver), Fase L-B (lógica de predicados verificada por theorem prover), Fase L-C (razonamiento científico y causal). Política de gating compartida. Racionale: el mismo principio Inverted Ground Truth que justifica el curriculum matemático justifica el curriculum lógico. |
| LITERAL blocks en Core Engine — declaración normativa | §15.5.1 | NORMATIVO: El Core Engine trata el contenido de LITERAL blocks como semánticamente opaco. El embedding del concepto LITERAL es el del token de apertura de 64 bits. Contexto gramatical aportado por el token de cierre. Mitigación principal: maximizar cobertura del diccionario (target: <1% de tokens en producción). Opciones B y C (byte-level embeddings, sub-tokenizador BPE) documentadas como candidatos para v1.1 condicionadas a resultados de Phase 3. |
| Límite del protocolo BBP-CoT — declaración normativa | §17.5.0 (nuevo) | NORMATIVO: Añadida sección §17.5.0 declarando explícitamente que BBP-CoT especifica la estructura del razonamiento pero no puede especificar su validez semántica. La detección de razonamiento cargo-cult es un límite fundamental del protocolo, no un gap corregible por especificación. Las implementaciones DEBEN evaluar calidad de razonamiento con métricas externas independientemente de la conformancia del Axiom Group F. Las mitigaciones de §17.6.3 y §17.6.4 son estrategias heurísticas de entrenamiento sugeridas, no requisitos normativos del protocolo. |

### Changelog (r33 → r34)

| Change | Section | Description |
|--------|---------|-------------|
| Principio referencial vs. auto-descriptivo | §2.3 | NORMATIVO: Distinción explícita entre meta-types referenciales (concept_id apunta al diccionario: DICT_WORD, NAME_*, SCI_*) y auto-descriptivos (concept_id IS el descriptor: NUM_*, LIVING_ENTITY). Los parsers NO DEBEN intentar lookup de diccionario para meta-types auto-descriptivos. El Compiler NUNCA crea entradas de diccionario para ellos. |
| LIVING_ENTITY (0o46) — meta-type nuevo | §5.1, §5.2 | PROPOSAL-5 (normativo): Meta-type auto-descriptivo para entidades biológicas. LEMMA: kingdom(3b)+taxon_id(22b, NCBI ID). El campo kingdom es redundante con taxon_id pero permite razonamiento O(1) por reino via bit masking. FEATURES: layout gramatical estándar completamente preservado. Bits 1-0 (antes reservados) repropuestos como vital_status: UNSPECIFIED/ALIVE/DEAD/EXTINCT. Etapa de desarrollo y sexo biológico expresados composicionalmente como tokens ADJECTIVE modificadores. |
| vital_status en FEATURES bits 1-0 | §4.1 | NORMATIVO: Los bits 1-0 de FEATURES, anteriormente reservados, quedan asignados como vital_status para LIVING_ENTITY (0o46). Para todos los demás meta-types permanecen como UNSPECIFIED (00). |
| Relación DICT_WORD / LIVING_ENTITY / SCI_TAXONOMY | §5.2.4 | CLARIFICATION: Documentadas las tres capas como representaciones no alternativas sino con propósitos distintos: léxico-cultural (DICT_WORD), ontológico-biológico (LIVING_ENTITY), nomenclatura formal (SCI_TAXONOMY). El Frontier Translator selecciona según contexto. |
| Índice de lexicalización | §13.6.6 (nuevo) | NORMATIVO: Tabla por lengua que mapea patrones BBP multi-token frecuentes a formas lexicalizadas. Jerarquía de decisión del Renderer: índice → diccionario → generación composicional. Crecimiento automático desde anotaciones del corpus durante entrenamiento del Frontier Translator (umbral mínimo de frecuencia: 50 ocurrencias/lengua/ciclo). Bidireccional: guía también al Frontier Translator en dirección NL→BBP. Versionado y publicado en el mismo ciclo trimestral que el diccionario. |

### Changelog (r32 → r33)

| Change | Section | Description |
|--------|---------|-------------|
| Rediseño de tokens numéricos | §10.1, §10.2 | PROPOSAL-2 (normativo): Inversión del layout numérico. LEMMA codifica clasificación matemática (meta-type + sign + magnitude + parity + reserved). FEATURES codifica el valor como 32 bits de dato puro (unsigned para NUM_NATURAL, two's complement para NUM_INTEGER, IEEE 754 float32 para NUM_REAL, 16b+16b para NUM_FRACTION). Elimina el límite inline de 4.095, hace innecesarios los bloques LITERAL para la mayoría de números. Los embeddings composicionales sobre los campos del LEMMA producen geometría de recta numérica por construcción. |
| MATH_EXPRESSION ejecutable | §10.3 (nuevo) | PROPOSAL-3 (normativo): Arquitectura matemática de dos capas. Flag EXECUTABLE en MATH_EXPRESSION (bit 0 de FEATURES del token START). Cuando activo, el runtime BBP evalúa la expresión determinísticamente e inserta el token resultado garantizado correcto después del END. En caso de error emite MATH_ERROR. Sin flag: comportamiento declarativo actual preservado. Token MATH_ERROR especificado con 4b de código de error y backpointer al token origen. Axiom Group J añadido al Validator. |
| Curriculum matemático del Core Engine | §15.5.0b (nuevo) | NORMATIVO: Fase de entrenamiento matemático independiente del pipeline lingüístico. Corpus generado completamente de forma determinista sin Teacher LLM (instancia más pura de Inverted Ground Truth). Tres fases: M-A aritmética concreta, M-B razonamiento matemático en BBP-CoT, M-C ciencia cuantitativa. Referencia cruzada con §10.3.4. |
| BBP-CoT + EXECUTABLE integración | §17.1 | CLARIFICATION: Añadida propiedad explícita en §17.1 documentando que EVIDENCE[COMPUTATION] + MATH_EXPRESSION[EXECUTABLE] proporciona evidencia matemática exacta y formalmente verificable dentro de cadenas de razonamiento BBP-CoT. |

### Changelog (r31 → r32)

| Change | Section | Description |
|--------|---------|-------------|
| Wikidata QID anchoring eliminado | 1.4, 13.6.2, 13.6.5 | CORRECTION: Eliminadas todas las referencias a Wikidata QIDs como anclas de identidad del diccionario. El anclaje a QIDs externos es incoherente con el mecanismo real de crecimiento del diccionario (§13.6.3), en el cual el Compiler crea entradas mecánicamente durante el entrenamiento sin referencia a ningún recurso externo. La métrica de orden de asignación de IDs se reemplaza por frecuencia en los treebanks de Universal Dependencies. La tabla de referencias cruzadas en §13.6.5 elimina la columna de QIDs. |
| Scope de recursos seed explicitado | 13.6.2 | CLARIFICATION (normativa): Añadido párrafo normativo "Scope of seed resources" que declara explícitamente que BabelNet, VerbNet y FrameNet son exclusivamente el seed de fase 1 y no constituyen la fuente primaria del diccionario de producción. El mecanismo dominante de crecimiento post-fase-1 es el proceso Compiler-driven de §13.6.3. Los implementadores NO DEBEN tratar la ausencia de entrada en estos recursos como evidencia de ausencia en el diccionario BBP. |
| Frontier Translator error rate como cuello de botella | 15.4.2, 15.5 | CLARIFICATION (normativa): El criterio de parada para la generación del corpus es el error rate del Frontier Translator, no un número fijo de frases. Los 700K son una estimación de orden de magnitud. Añadida sección normativa §15.5.0a "Frontier Translator Error Rate as Epistemological Bottleneck": el error rate del Frontier determina directamente la fidelidad del modelo del mundo adquirido por el Core Engine durante el preentrenamiento. Los errores del Frontier son estructuralmente silenciosos y se incrustan en las representaciones del Core Engine sin posibilidad de corrección sin reentrenamiento. |
| Embeddings composicionales normativos | 15.5.1 | GAP RESUELTO: §15.5.1 elevado de lista de tareas pendientes a especificación normativa. El Core Engine DEBE implementar embeddings composicionales por campo. Cada campo del token de 64 bits tiene su propio embedding independiente. El método de combinación (suma o concatenación+proyección) DEBE elegirse y documentarse antes del inicio del entrenamiento. Añadida justificación normativa: sin embeddings composicionales, la propiedad de composicionalidad productiva (§1.3) no puede sostenerse computacionalmente. |
| Natural gender field en LEMMA | 2.3, 4.1 | PROPOSAL (normativo): Para los meta-types DICT_WORD, NAME_PERSON, PRONOUN y ADJECTIVE, los bits 24–23 del LEMMA se reproponen como campo natural_gender de 2 bits (GENDER_UNSPECIFIED/MASCULINE/FEMININE/NEUTER), reduciendo concept_id a 23 bits (8M conceptos) para esos meta-types. El parser aplica una máscara condicional basada en el meta-type, sin cambio estructural en la máquina de estados. El campo codifica el sexo biológico o social del referente, no el género gramatical arbitrario de la palabra. El Frontier Translator emite el valor cuando el idioma fuente lexicaliza la distinción; GENDER_UNSPECIFIED en caso contrario. |

### Changelog (r30 → r31)

| Change | Section | Description |
|--------|---------|-------------|
| §17.6.4 Training-Time Semantic Validation | 17.5.4 (NEW) | NEW (normative for training): Cuatro mecanismos de validación semántica aplicables exclusivamente durante el entrenamiento del Core Engine, no en producción. (1) Outcome verification: para dominios computables, la recompensa de entrenamiento debe incluir corrección del resultado independientemente de la validez estructural; mínimo 30% de ejemplos con conclusión verificable. (2) Deliberate perturbation probes: variantes con premisas modificadas para detectar mimicry estructural; tasa recomendada 20% en etapas 3-4 del curriculum. (3) Cross-prompt consistency evaluation: el mismo problema en múltiples reformulaciones entre épocas, como señal de evaluación (no término de loss por batch). (4) Step ablation probes: eliminación de INFERENCE_STEPs intermedios para verificar que contribuyen contenido inferencial genuino; resultados alimentan el novelty scoring de §17.6.3. Los cuatro mecanismos se integran progresivamente con el curriculum learning de §15.3.5. |

### Changelog (r29 → r30)

| Change | Section | Description |
|--------|---------|-------------|
| Dictionary Proposal Pipeline eliminado | 13.6.3 | NORMATIVE CHANGE: El Dictionary Proposal Pipeline (Stage A clustering, Stage B promotion scoring, gate conditions, Goodhart mitigation, Promotion Queue) ha sido eliminado. Era maquinaria diseñada para un modelo en el que LITERAL era una categoría transitoria, que ya no existe en la arquitectura. El crecimiento del diccionario ocurre exclusivamente mediante ciclos de entrenamiento supervisado: el Compiler crea entradas mecánicamente durante el entrenamiento, y las incidencias en producción se capturan en el Unknown Primitive Incident Log para su revisión humana en el siguiente ciclo. |
| Unknown Primitive Incident Log | 13.6.3 | NEW: Mecanismo minimalista de log para producción. Cuando un Frontier Translator emite un LITERAL que clasifica como posible primitivo irreducible (no como designador rígido), registra un incidente con: valor del LITERAL, lengua fuente, meta-type inferido, contexto BBP-IR y timestamp. El log se revisa en cada ciclo de entrenamiento: los primitivos genuinos se incorporan al corpus del Teacher LLM para la siguiente iteración; los falsos positivos descomponibles via lemma + FEATURES se usan como ejemplos negativos de entrenamiento. |
| §13.6.5 Promotion Queue eliminada | 13.6.5 | EDITORIAL: La referencia a "New ID assignments from the Promotion Queue" reemplazada por "New ID assignments from entries created during the most recent training cycle", coherente con la eliminación del pipeline automatizado. |

### Changelog (r28 → r29)

| Change | Section | Description |
|--------|---------|-------------|
| Bias audit — scope redefinition | 13.6.2 | NORMATIVE CHANGE: The bias audit requirement is redefined. It no longer measures lexical coverage by language family. It now measures exclusively the presence of irreducible conceptual primitives absent from the seed — concepts where no existing lemma serves as semantic root and no lemma + FEATURES combination approximates the meaning. Lexical gaps expressible via existing lemma + FEATURES do not constitute dictionary gaps. Each identified gap must be documented with concept, affected languages, justification of irreducibility, and remediation plan. |
| Bias audit — format vs. pipeline distinction | 13.6.2 | NORMATIVE CHANGE: The bias audit must explicitly distinguish (1) format coverage (~7,000 languages, token format capacity) from (2) pipeline coverage (~20–50 languages, validated Frontier Translator + sufficient written corpus). Languages outside class (2) have format coverage but not pipeline coverage. The audit must list which languages fall in each class at v1.0 release time. |
| LITERAL semantics and Compiler entry creation | 13.6.3 | NORMATIVE CHANGE: LITERAL is redefined as exclusively for permanent rigid designators (brand names, product identifiers, model numbers, proper names of concrete entities). LITERAL is not a transitional state. The semantic decision (lemma vs. LITERAL, meta-type) is made by the Teacher LLM; the Frontier LLM learns it by supervised training. The Compiler creates dictionary entries mechanically when it encounters a lemma absent from the dictionary; LITERAL blocks never trigger entry creation. |
| §15.3.5 Monolingual Bootstrap Strategy | 15.3.5 (NEW) | NEW (normative): Full specification of the English monolingual bootstrap strategy: English as mandatory bootstrap language, English canonical lemma form, monolingual English ↔ BBP-IR Frontier Translator as first training target, four-stage curriculum learning schedule (single-clause → multi-clause → paragraphs → full documents), dictionary growth in parallel with training via Compiler-driven entry creation, and LITERAL rate in Teacher output as the primary dictionary maturity indicator. |
| Core Engine unknown ID behaviour | 13.6.5 | NORMATIVE CHANGE: Replaces "behaviour for unknown IDs is implementation-defined" with a mandatory normative rule: unknown IDs MUST be treated as LITERAL blocks carrying the numeric ID as raw value, and MUST NOT be mapped to any known ID. Defines the degradation model for cross-version inter-agent communication: shared dictionary subset operates normally; concepts outside that subset degrade to transparent opacity and are flagged in the decode log. |

### Changelog (r27 → r28)

| Change | Section | Description |
|--------|---------|-------------|
| §1.4 dictionary reference | 1.4 | EDITORIAL: Replaced informal forward-reference "(Section 13.3)" with an explicit summary of the dictionary construction plan (BabelNet/VerbNet/FrameNet bootstrap, Wikidata QID anchoring, quarterly publication pipeline, immutable-ID versioning) and a normative cross-reference to **§13.6**, where the full specification resides. §13.3 was already superseded by §13.6 in r27; this change removes the last pointer to the obsolete reference in the introductory sections. |
| §1.5.1 C3 dictionary reference | 1.5.1 | EDITORIAL: Extended the closing sentence of the C3 (ontological commitment) paragraph to explicitly reference **§13.6** as the section that operationalises the dictionary construction methodology (bootstrap sources, promotion criteria, bias audit requirements, publication cycle). Prevents a reader encountering the ontological-commitment caveat without a pointer to its resolution. |

### Changelog (r26 → r27)

| Change | Section | Description |
|--------|---------|-------------|
| PRESUPPOSITION meta-type | 5.1 | NEW (normative): Slot 0o67 activated as PRESUPPOSITION, replacing the previous Reserved entry. Full typed encoding of presupposed background propositions with 4-bit `presupposition_type` (EXISTENTIAL, FACTIVE, LEXICAL, STRUCTURAL, COUNTERFACTUAL, ITERATIVE, OTHER) and 2-bit `projection_behavior` (PROJECTS, HOLES, PLUGS, FILTERS). The projection behavior field enables formal representation of the classical linguistic test that distinguishes presuppositions from entailments and implicatures. |
| PRESUPPOSITION validation rules | 5.2 | NEW (normative): Validation rules 17–19 added for PRESUPPOSITION UETs: flow=UNIT is invalid; content must include at least one UET and one UAT; ordering and projection/context consistency rules. |
| §6.8 PRESUPPOSITION — full specification | 6.8 (NEW) | NEW (normative): Complete specification of PRESUPPOSITION encoding: FEATURES layout, token stream structure, four worked examples (LEXICAL/question, EXISTENTIAL/Russell, PLUGS/reported speech), BBP-IR JSON representation, and Frontier Translator guidance (Tier 3 task). |
| SPEECH_ACT CONTROL token | 11.5 | NEW (normative): CONTROL FEATURES code 0x090 allocated to SPEECH_ACT. Reserved range updated to 0x091–0xFFE. SPEECH_ACT encodes illocutionary force (4b: ASSERTION, PROMISE, DECLARATION, REQUEST, WARNING, APOLOGY, GREETING, COMMISSIVE, EXPRESSIVE), institutional domain (4b: GENERAL, LEGAL, RELIGIOUS, BUREAUCRATIC, SOCIAL_RITUAL, MILITARY), and uptake requirements (2b: NONE, ADDRESSEE, INSTITUTIONAL, BILATERAL). |
| §11.5.2 SPEECH_ACT payload | 11.5.2 (NEW) | NEW (normative): Full FEATURES payload layout for SPEECH_ACT CONTROL token with normative inventory for all three dimensions (illocutionary force, institutional domain, uptake). |
| §11.6 SPEECH_ACT — full specification | 11.6 (NEW) | NEW (normative): Philosophy (Austin/Searle speech act theory grounding), block structure with felicity conditions as HYPOTHESIS sub-blocks with CONFIDENCE=1.0, three worked examples (DECLARATION/wedding, PROMISE/commitment, indirect REQUEST/salt), BBP-IR JSON representation, and Frontier Translator guidance (Tier 3 for non-ASSERTION forces, Tier 1 for ASSERTION default). |
| Axiom Group I | 13.2.1 | NEW (normative): Eleven axioms (I1–I11) governing PRESUPPOSITION and SPEECH_ACT structural consistency. Covers flow constraints, content requirements, ordering guidance, projection/context interaction, nesting depth, DECLARATION felicity condition requirements, and PRESUPPOSITION-inside-SPEECH_ACT interaction. All new axioms follow the existing severity classification (CRITICAL / MAJOR / MINOR / TRIVIAL). |
| §1.5.1 Category B reclassification | 1.5.1 | NORMATIVE CHANGE: B3 (Presupposition) and B4 (Non-propositional performativity) promoted from Category B (partially mitigable with documented residual loss) to Category A (pipeline-mitigable with residual approaching zero). Residual loss is now a Frontier Translator quality problem — identical in kind to any other missed semantic feature — not an architectural limitation. The original B3/B4 text replaced with descriptions of the native encoding mechanisms now available. Category B now contains only B1 (grammatical tone) and B2 (idiomatic classifiers). |

### Changelog (r25 → r26)

| Change | Section | Description |
|--------|---------|-------------|
| Translator analogy | Prologue | AMENDED (editorial): Added paragraph introducing the professional translator analogy. Grounds the BBP architecture in an intuitive model: Frontier LLMs as translators at the boundary, Core Engine as a thinker in BBP, Layer 3 agents as thinkers sharing a common language with no translator needed. Explicitly identifies the Frontier Translator's training quality as the critical system bottleneck, reinforcing §15.4. No normative content changed. |
| Core Engine separation clarification | 1.2 | AMENDED (editorial): Rewritten Core Engine description to clarify the Layer 1/Layer 2 separation as structural rather than ontological. The Core Engine's primitives are human concepts encoded in the dictionary; what BBP eliminates is dependence on any particular human language and its expressive constraints. Removed the phrase "has never been trained on natural language text" as the primary descriptor (accurate but misleading in isolation). Added translator analogy as guiding intuition. Extended the Key architectural invariant to cover the Layer 3 direct-communication case. No normative content changed. |
| Cognitive Capacity Hypothesis | 1.11 (NEW) | NEW (informative): New section grounding the Core Engine's expected cognitive capacity in four convergent research traditions: (1) cognitive psychology — vocabulary as cognitive amplification mechanism (Jensen 2001; Crawford et al. 1989); (2) weak linguistic relativity — the grammar of thought has measurable cognitive consequences (Levinson 1997; Brown & Lenneberg 1954); (3) neuro-symbolic AI — structured representations quantifiably amplify reasoning over natural language baselines (EMNLP 2025; PNAS 2025); (4) BBP as meta-language — the fundamental reframing that BBP is not the absence of language but the language with the highest density of explicit grammatical structure ever formally specified, being the formal union of what all human languages can express. Synthesised as: cognitive capacity ≈ f(conceptual richness × explicit compositional grammar). Epistemic status explicitly declared as working hypothesis, falsifiable by §15.5.5 benchmarks and Appendix H. |

### Changelog (r24 → r25)

| Change | Section | Description |
|--------|---------|-------------|
| Basque framing clarification | 1.1 | AMENDED (editorial): Removed comparativist framing of the ERG/ABS design choice. The ergative-absolutive system is now presented as a pragmatic starting point drawn from the author's familiarity with Basque — not as a claim of superiority over nominative-accusative or any other alignment. Georgian, Tibetan, Quechua, Mayan, or any ergative language would have served equally as an initial anchor. Section title changed from "The Basque Insight: Origin, Evolution, and What Was Retained" to "Basque as Starting Point: Origin, Evolution, and What Was Retained". The paragraph on ergativity rewritten to lead with the engineering requirement (explicit role-marking in a binary protocol) rather than with a contrast against nom-acc languages. Subheading "The two core Basque properties that drove the design" rephrased to "The two properties borrowed from Basque as initial design anchors". The "most defensible as an engineering choice" phrase replaced with "most direct engineering application" plus explicit acknowledgement that the same principle was available from any of the hundreds of ergative languages. No normative content changed. |

### Changelog (r23 → r24)

| Change | Section | Description |
|--------|---------|-------------|
| Dictionary Strategy | 13.6 | NEW (normative): Full specification of dictionary bootstrap, automatic growth, and publication. Covers: (1) Phase 1 bootstrapping from BabelNet + VerbNet + FrameNet with Wikidata QID anchors; (2) automatic growth via Dictionary Proposal Log, cross-lingual clustering, and composite promotion scoring with gate conditions; (3) immutable ID annotation system (DEPRECATED, ALIAS, CANONICAL, ERRATA, HISTORICAL) replacing deletion; (4) fixed quarterly publication cycle decoupled from Frontier LLM releases; (5) Frontier LLM `trained_on_dict_version` synchronisation rule; (6) Core Engine cross-version compatibility requirements. Supersedes the informal "vocabulary database" reference in §13.3. |
| Immutability rationale | 13.6.1 | NEW (normative): Explicit statement that IDs are permanent and the dictionary is append-only, with architectural rationale grounded in stream longevity. No-deletion policy modelled on Unicode codepoint permanence. |
| Bias audit requirement | 13.6.2 | NEW (normative): Bias audit of seed dictionary (coverage by linguistic family and semantic domain) declared a REQUIRED v1.0 release artefact. Acknowledges known gaps in BabelNet/VerbNet/FrameNet for low-resource languages. |

### Changelog (r22 → r23)

| Change | Section | Description |
|--------|---------|-------------|
| Tense normative inventory | 3.6 | NEW (normative): Defined 4-value Tense encoding — PRES (00), PAST (01), FUT (10), HYPO (11). Replaces the underspecified "compact inventory" placeholder present since the 32-bit era. No bit layout change. |
| Mood normative inventory | 3.6 | NEW (normative): Defined 4-value Mood encoding — IND (00), SUB (01), POT (10), IMP (11). Replaces the underspecified "compact inventory" placeholder. No bit layout change. |
| Cross-field TAM note | 3.6 | NEW (informative): Documented orthogonality of Tense × Mood fields and overflow strategy via TEMPORAL/MODAL UET tokens for fine-grained distinctions. |
| Prologue amendment | Prologue | AMENDED (editorial): Expanded "who does what to whom" to "who does what to whom, when, how, why, and under what epistemic conditions" — consistent with §1.9 formulation — to better convey the full semantic scope of the bit layout. |

### Changelog (r21 → r22)

| Change | Section | Description |
|--------|---------|-------------|
| Prologue | Prologue | NEW: Two-paragraph historical prologue added before §1. Positions BBP against 70 years of prior attempts — interlingua MT, KQML/FIPA-ACL, and current NL-based inter-agent protocols (MCP, A2A, ACP) — and states the core architectural bet concisely. Intended as the reader's first contact with the document. |

### Changelog (r20 → r21)

| Change | Section | Description |
|--------|---------|-------------|
| LEMMA typed identifier | 2.2, 2.3 | Discriminator bit and meta-type relocated to LEMMA[31] and LEMMA[30:25]. LEMMA is now a self-describing typed semantic identifier: its ontological category (Entity vs. Action, and meta-type or Aktionsart/valency) is encoded in LEMMA without loading FEATURES. |
| Verb class in Action LEMMA | 2.2, 2.3 | Bits 30:25 of Action Token LEMMA encode Aktionsart (3b, per Vendler/Comrie taxonomy) and valency_class (3b). Normative inventories defined. These are lexical properties of the verb lemma, enabling validator cross-checks without dictionary lookup. |
| FEATURES full 32b for grammar | 4.1 | Entity Token FEATURES redesigned with all 32 bits available for grammatical encoding. flow_control occupies bits 30–31 (most-significant grammatical field). All sub-fields renumbered accordingly. |
| Number expanded to 3b | 4.6 | 8 values: UNMARKED, SINGULAR, DUAL, TRIAL, PAUCAL, PLURAL, COLLECTIVE, DISTRIBUTIVE. Covers documented grammatical number systems across ~7,000 known languages. |
| Animacy separated to 2-bit field | 4.9 | 2-bit independent field (INANIMATE, ANIMATE, HUMAN, DIVINE). Encodes the Silverstein animacy hierarchy. |
| Noun class separated and expanded | 4.10 | 4-bit independent field (16 values). Covers core Bantu classes (NC_1–NC_14) plus NC_EXTENDED and NC_ANIMATE_AGREE. Directly encodes Swahili and the majority of Bantu languages without auxiliary tokens. |
| Noun classifier inventory redefined | 4.8 | 14 semantic categories plus CLF_EXTENDED (0xE) and CLF_OTHER_LITERAL (0xF). Field encodes universal semantic category of classifier, not language-specific instance. |
| CLF auxiliary token mechanism | 1.6, 4.12 | General auxiliary token pattern formalized in §1.6. CLF auxiliary token mechanism specified in §4.12. Axioms A_CLF1, A_CLF2 added to Axiom Group A. |
| Honorific expanded to 6b / 3D | 4.11 | Three-dimensional encoding: speech_level (2b) × respect_direction (2b) × domain (2b). Covers keigo (Japanese), Korean speech level system, and T/V distinctions without auxiliary tokens. |
| HONORIFIC_CONTEXT CONTROL | 11.5, 11.5.1 | New CONTROL payload 0x030. Optional metadata for Frontier Renderer keigo inference. Provides speaker/addressee/referent role assignment and target-language ID. Compositionality entries occupy 0x031–0x03F. |
| Evidentiality normative inventory | 3.8 | 16-value normative inventory defined (EV_UNSPECIFIED through EV_OTHER_LITERAL). Cross-linguistic mapping table for Turkish, Bulgarian, Quechua, Tuyuca, Tibetan, Japanese. |
| Axiom Group H_EXT | 13.2.1 | New axiom group for evidentiality normative mapping. H_EXT1–H_EXT3 enforce conformance to the cross-linguistic mapping table for languages with grammaticalized evidential systems. |
| §1.5 scope amendment | 1.5, 1.5.1 | §1.5.1 added, documenting known boundaries of semantic coverage in three categories: A (pipeline-mitigable), B (partially mitigable with documented residual loss), C (structural limits). |
| New §1.9 — Semantic coverage scope | 1.9 | Formal statement of what r21 achieves at the token format level, what the complete pipeline achieves beyond it, and what no finite representational system achieves. |
| New §1.10 — Emergent semantic potential | 1.10 | Documents four architectural consequences of the Core Engine design: cross-linguistic semantic geometry (§1.10.1), concepts without lexemes (§1.10.2), asymmetric lexical density as a resource (§1.10.3), and formal detectability of semantic operations (§1.10.4). Epistemic status: architectural predictions conditional on §1.8, falsifiable by Phase 3 benchmarks. |
| Axiom Group A: A_CLF1, A_CLF2 | 13.2.1 | A_CLF1 and A_CLF2 added enforcing structural validity of CLF auxiliary token pairs. |

### Changelog (r19 → r20)

| Change | Section | Description |
|--------|---------|-------------|
| Inverted Ground Truth — Normative Frontier Training Methodology | §15.4.0 (NEW), §13.2, §1.2, §15.4.3, §15.6.1 | **NEW (normative, r20):** Defines the central training architecture for Frontier Translator LLMs. BBP-IR is always the ground truth anchor; NL is the Teacher-generated artifact. The combinatorial generator produces valid BBP-IR structures; a world-class Teacher LLM synthesizes the best natural-language expression from each structure. The Frontier LLM is trained with BBP-IR as the supervised label, learning NL→BBP-IR by inversion from guaranteed-correct structural labels. Includes: three-phase curriculum (token-level synthetic → propositional synthetic → real-world fine-tuning), absurdity filtering with cross-linguistic fallback, rationale for small specialized model architecture (1B-7B), bidirectionality via direction token. Supersedes any implicit assumption that Frontier Translators are trained by having a Teacher analyze natural text. |
| §13.2 Teacher LLM role clarification | §13.2 | **AMENDED:** Section retitled "Teacher LLM — Dual Role". Primary role (synthesis: BBP-IR→NL) and secondary role (analysis: NL→BBP-IR) are now formally distinguished. Synthesis is the dominant role feeding the Frontier training pipeline. Analysis is supplementary, used only for phenomena that resist enumeration, and tagged `origin: analysis` with lower training weight. The architectural safety property of the inversion (covert vs. overt errors) is stated explicitly. |
| §1.2 Layer 1 description | §1.2 | **AMENDED:** Frontier Translators description updated to reference §15.4.0 training methodology. Inverted Ground Truth property stated at architectural overview level. |
| §15.4.3 pipeline framing | §15.4.3 | **AMENDED:** Opening paragraph updated to explicitly position §15.4.3 as the infrastructure implementation of the §15.4.0 methodology. |
| §15.6.1 distillation strategy | §15.6.1 | **AMENDED:** Cross-reference to §15.4.0 added. Parameter scale updated from "3B-7B" to "1B-7B" to reflect that Phase A+B curriculum may enable smaller models. |

### Changelog (r18 → r19)

| Change | Section | Description |
|--------|---------|-------------|
| TYPE_EXPR / TYPE_PARAM | §5.1, §16.4, Axiom Group E | NEW: Two new meta-types (0o26 TYPE_EXPR, 0o27 TYPE_PARAM) replace reserved slots in Group 2, providing dedicated type-level constructs for parameterized types and type variables. MATH_EXPRESSION in type annotation positions is deprecated (Axiom E13, MINOR). New axioms E13–E16 enforce type encoding consistency. |
| Complete Case Inventory | §4.5, §14.1, Axiom Group A | NEW: 11 new cases added (ILL 0x12, ELA 0x13, SUP 0x14, SUB 0x15, DEL 0x16, ADE 0x17, VOC 0x18, ESS 0x19, TRANS 0x1A, SEM 0x1B, ABE 0x1C), bringing the total to 29. Non-breaking: no existing valid stream uses reserved slots. New axioms A5 (VOC), A6 (VOC), A7 (TRANS), A8 (ESS-TRANS), A9 (ILL-INE), A10 (ELA-ABL), A11 (SUB-DEL). |
| Preposition Elimination Principle | §1.1, §1.8, §12.0.2, §13.2, §15.4.1 | NEW: Formal statement of the synthetic model design property. BBP absorbs all prepositional/postpositional/particle relations into case fields; no relational words are emitted as tokens. New §12.0.2 provides normative preposition-to-case mapping for all 29 cases × 5 core languages, exhaustive exception list, worked examples for 5 languages, and Renderer inverse-mapping guidance. Axiom C6 (PREPOSITION_LEAK) added. |
| §1.8 item 3 expanded | §1.8 | AMENDED: Item 3 "Reduced sequence length" rewritten to ground the compression claim in the concrete mechanism of preposition elimination (3a lexical compression, 3b preposition elimination). Added quantitative estimates and comparison table. |
| §15.2 Feature Coverage Matrix | §15.2 | AMENDED: Added 5 new rows covering ILL/ELA/SUP/DEL/ADE, VOC, ESS/TRANS, SEM, ABE coverage across the 7 core languages. Added Phase 1.5 note for Finnish. |
| §15.9 BBP Readiness Scorecard | §15.9 | AMENDED: Added 12 new benchmark rows covering new case accuracy, preposition leak rate, BPE-to-BBP ratios, and Renderer round-trip fidelity. |
| §12.0.1 Linguistic Challenge Registry | §12.0.1 | AMENDED: Added 15 new entries covering Finnish case disambiguation (SUP/ADE, ESS/TRANS), VOC edge cases, new case interactions (ABE/NEG+COM), and preposition ambiguity (Spanish de/para, Japanese に, Finnish -lla/-llä, German two-way prepositions, Arabic في). |
| §13.2 UD Mapping | §13.2.0 | AMENDED: Extended UD → BBP mapping table for oblique prepositions, covering 18 new (obl, preposition) → BBP case mappings for deterministic Component 2 resolution. |

### Changelog (r17 → r18)

| Change | Section(s) | Description |
|---|---:|---|
| Token format migrates to **BBP-64 (32+32)** | 2–4, Appendix C | BBP tokens are now 64-bit: **LEMMA(32)** + **FEATURES(32)**. Type discrimination introduced in LEMMA bit 31. |
| Removed **Bound Modifiers** | 6 | Evidentiality, fine aspect, extended voice and clusivity are represented as native Action FEATURES fields. |
| Removed ditransitive **pair table / EXTENDED mode** | 3 | Ditransitive roles are explicit (Subject/Object/Indirect) in Action FEATURES. |
| Removed **Extended REFERENCE** | 8 | Long-range coreference uses native REFERENCE mechanisms without r17-era index limits. |
| Profiles re-scoped | 2.8 | Domain profiles no longer remap vocabulary; profiles are conformance / provenance only. |
| Degradation policy simplified | 2.8 | Prefer lemma IDs; use LITERAL only for raw strings or not-yet-dictionary concepts. |

### Changelog (r16 → r17)

| Change | Section | Description |
|--------|---------|-------------|
| Vision reframe | 1 | Reframed "machine code for cognition" analogy → "structured semantic encoding optimized for transformer ingestion." Clarified that bit fields become structured statistical signals, not deterministic instructions. |
| Generative Rigidity | 1.3 | Redefined "creativity" as "productive compositionality" — ability to generate/process unseen field combinations. Added compositional generalization benchmark reference (Appendix H.8). |
| Synset ambiguity | 1.4 | Acknowledged residual lexical ambiguity (2-3 interpretations per synset). Introduced training-time sense annotation via multi-task objective and optional `sense_distribution` field in BBP-IR. |
| Empirical Validation Status | 1.8 | NEW: Explicitly states Core Engine hypothesis is untested. Lists theoretical motivations as arguments, not facts. Defines success criteria and fallback strategies (hybrid training, BBP as fine-tuning format). |
| Pair Table revision policy | 3.3.7 | Added benchmark-driven revision policy: if EXTENDED mode exceeds 15% in dialogue corpora, pair table SHOULD be revised using data-driven analysis. |
| EVIDENTIAL justification | 6.7.2 | Added explicit design justification for native verb feature field approach. Asymmetry (Turkish pays 1 token/verb, English pays 0) instantiates Compositionality Principle. Added v1.1 fast-path optimization candidate. |
| Case Assignment Decision Tree | 12.0 | NEW: 6-step normative decision tree for Teacher LLM case assignment. Covers volitional transitive, split intransitivity, experiencer+stimulus, impersonal, ditransitive, and residual cases. |
| Linguistic Challenge Registry | 12.0.1 | NEW (informative): Living document of known hard cases across languages. Records current encoding and open questions. Drives future spec revisions. |
| Section 12 scope | 12 | Per-language detailed guides (20-30pp each) moved to normative companion "BBP Linguistic Conventions." Section 12 provides universal rules and summaries. |
| Hybrid Teacher Architecture | 13.2.0 | NEW: Three-component architecture — Morphological Analyzer (rule-based), Syntactic Parser (UD-based neural), LLM Semantic Enricher (handles only what parsers cannot). |
| Teacher Difficulty Rating | 13.2.0.1 | NEW: Three tiers with different human review requirements (Tier 1 >95% auto; Tier 2 80-95% 10% review; Tier 3 <80% 100% review). |
| UD Bootstrapping | 13.2.0.2 | NEW: Start from Universal Dependencies treebanks. UD→BBP mapping is largely rule-based. LLM fills only gaps UD doesn't provide. |
| Gold Set redefinition | 15.3.0.1 | Redefined purpose as "BBP feature coverage validation" not "exhaustive linguistic coverage." Growth targets: 1K→5K→20K. |
| Silver Set | 15.3.0.2 | NEW: 50K+ auto-generated sentences/language. Quality measured by agreement with Gold Set (>95% threshold per feature). |
| Narrowing Funnel | 15.4 | NEW: Depth-first timeline. Month 3-4: 10K Spanish; Month 5-6: +Turkish/Japanese; Month 7-8: remaining languages; Month 9-12: scale to 100K/lang. |
| Cost-Effectiveness Analysis | 15.5.0 | NEW: Break-even condition C₁+C₂ < C_text. Primary value: "translate-once, reason-many" scenarios (RAG, knowledge bases, inter-agent). |
| Compositional embeddings | 15.5.1 | Fully specified parameter allocation (2.65M vs 419M flat). Sub-embeddings learn field semantics independently → systematic generalization. |
| Staged Validation | 15.5.5 | NEW: Stage 0 micro-experiment (125M params, 50K streams). Go/no-go gates. Success criteria for full-scale training (4 normative thresholds). Fallback if criteria not met after 3 runs. |
| Readiness Scorecard | 15.8 | NEW: 14 mandatory benchmarks with targets. Spec SHOULD NOT move from RC to Released until populated. Immediate action: 50-sentence Spanish end-to-end benchmark. |
| BBP-AST reposition | 16.1 | Reframed as "navigable structural skeleton" not "full AST replacement." Added v1.1 code semantic annotations (EVAL_STRATEGY, OWNERSHIP, MUTABILITY, PURITY). |
| RETRACTION reframe | 17.2.3 | NEW: Explicit statement that RETRACTION is a declarative annotation, not imperative operation. Threefold value (downstream signaling, training signal, transparency). v1.1 REASONING_REVISION candidate. |
| Anti-Goodharting strengthening | 17.5 | AMENDED: Added "necessary but not sufficient" statement. Structural axiom compliance alone does not demonstrate genuine reasoning. Implementations MUST evaluate task-specific metrics independently. |
| Compositional generalization benchmark | H.8 | NEW: Benchmark for held-out field combinations to validate productive compositionality claim. |

### Changelog (r15 → r16)

| Change | Section | Description |
|--------|---------|-------------|
| Forbidden aspect combinations | 3.3.3, 6.7.3, 13.2.1 | NEW (normative): Added axiom C5 defining INVALID UAT Aspect × ASPECT_FINE combinations (TP-01). |
| VOICE_EXT native verb feature field | 6.7.4, 13.2.1 | NEW (normative): Added VOICE_EXT native verb feature field for antipassive, applicative, reciprocal, inverse, cooperative voices. Causative remains inline (TP-02). |
| CLUSIVITY native verb feature field | 6.7.5, 13.2.1 | NEW (normative): Added CLUSIVITY native verb feature field for inclusive/exclusive "we" distinction. Zero cost for non-clusivity languages (TP-03). |
| Domain-specific profiles | 2.8.4, 11.5 | NEW (normative): Defined domain-specific BBP profiles (BBP-Medical, BBP-Legal, etc.) with optimized vocabularies per meta-type (TP-04). |
| Pair table O(1) documentation | 3.3.7 | AMENDED (informative): Documented amortized O(1) decoding complexity. Added benchmark to Appendix H (TP-05). |
| Checkpoint overflow continuation | 11.7.2 | AMENDED (normative): Added continuation flag (bit 0) for STATE_CHECKPOINT depth > 15. Max depth 255 via chaining (TP-06). |
| Compact long-range REFERENCE | 8.5.9 | NEW (normative): Single-token long-range REFERENCE using Deklinabidea reinterpretation. 23-bit absolute index (8M tokens). Declension inherited from referent (TP-07). |
| AST_OPAQUE density + expanded constructs | 16.2, 16.8, H.6 | AMENDED (normative): Expanded programming constructs (LAMBDA, DECORATOR, COMPREHENSION, etc.). Added AST_OPAQUE density benchmark H.6 (TP-08). |
| CONFIDENCE dual encoding | 17.2.7 | AMENDED (normative): Split CONFIDENCE FEATURES into 2-bit calibration status + 10-bit confidence value (TP-09). |
| Quality provenance in PROFILE | 2.8.2, 2.8.5 | NEW (normative): PROFILE Deklinabidea reinterpreted with provenance and quality tier. Defined Lite→Full elevation pipeline (TP-10). |
| START/END meta-type MUST match | 7.4, 13.2.1 | AMENDED (normative): Promoted START/END meta-type matching from SHOULD to MUST. Added axiom A6 (TP-11). |
| Transport-layer integrity note | 11.9 | NEW (informative): Normative note on transport-layer integrity for unreliable channels. BBP does not include per-token error detection (TP-13). |
| Consolidated Deklinabidea reinterpretation | 4.5.4 | NEW (normative): Consolidated lookup table for all CONTROL payloads that reinterpret Deklinabidea as extended data (TP-14). |
| Protocol version in DOC_START | 11.3 | AMENDED (normative): DOC_START Deklinabidea encodes protocol version (major 0-15, minor 0-63) alongside Seekable flag (TP-15). |

### Changelog (r14 → r15)

| Change | Section | Description |
|--------|---------|-------------|
| Canonical form definition | 2.8.3 | AMENDED (normative): Added DEGRAD-5 defining canonical_form as `NFC(trim(input))` with deterministic whitespace collapsing. Closes last ambiguity for cross-encoder byte-identity. |
| LITERAL meta-type in Lite | 2.8.3 | AMENDED (normative): Tightened LITERAL meta-type selection from SHOULD to MUST for BBP-Lite encoders without explicit ontological typing. Default: DICT_WORD (0o00). |
| TAM minimum justification | 15.3.0 | AMENDED (informative): Added inline rationale for the 4-case minimum in TAM feature (minimum coverage of tense×aspect matrix). |

### Changelog (r13 → r14)

| Change | Section | Description |
|--------|---------|-------------|
| H3 FEATURES preservation clarification | 13.2.1 | AMENDED (editorial): Clarified that "original FEATURES preserved" means the token is kept unmodified in the binary stream; semantic resolution to UNKNOWN occurs in higher layers. |
| CTS gating formula | 15.3.0 | AMENDED (editorial): Replaced approximate fixture count with derivable formula to prevent staleness if the feature/language matrix evolves. |

### Changelog (r12 → r13)

| Change | Section | Description |
|--------|---------|-------------|
| CTS sizing guidance | 15.3.0 | NEW (informative): Added fixture sizing tiers (MVP vs Robust) to prevent misinterpretation of minimum fixture counts. |

### Changelog (r11 → r12)

| Change | Section | Description |
|--------|---------|-------------|
| ONTOLOGY_VERSION Deklinabidea clarification | 11.3 | AMENDED: Clarified that "Deklinabidea SHOULD be all zeros" refers to the CONTROL UET declension fields (case/number/det), not to a linguistic case. Added decoder MUST-ignore rule. |
| SIMD benchmark reporting | H.2 | AMENDED: Relaxed scatter plot requirement from mandatory to SHOULD; tabular correlation accepted as alternative for CI pipelines. |
| Entity Table Protocol stub | Appendix I.1 | AMENDED: Anchored explicit cross-reference to Axiom H2 (ONTOLOGY-2) for KEYFRAME MAJOR version mismatch behavior. |

### Changelog (r10 → r11)

| Change | Section | Description |
|--------|---------|-------------|
| Degradation Policy | 2.8.3 | NEW: Normative fallback chain for BBP-Lite when concepts exceed inline range. Deterministic FNV-1a hash for UNKNOWN tokens. NFC normalization requirements. |
| ONTOLOGY_VERSION | 11.3, 11.5 | NEW: Mandatory ontology version declaration in DOC_START (CONTROL FEATURES 0x053). Compatibility axioms H1–H4 (ONTOLOGY-1/2/3/4). Migration table format. |
| CTS Feature Manifest | 15.3.0 | NEW: 12 mandatory CTS features for r11 conformance gating. Gold Set definition (500 sentences/lang + 100 double-annotated). |
| AST_OPAQUE | 16.8, 11.5, 13.2.1 | NEW: Opaque node mechanism (CONTROL 0x07C) for non-universal programming constructs. Preserves AST navigability. Axioms E11–E12. |
| CoT Anti-Gaming | 17.5 | NEW (informative): Training considerations for mitigating structural Goodhart effects in BBP-CoT. Detection heuristics and recommended mitigations. |
| UAT-Arguments Clarification | 3.3.7 | AMENDED (informative): Explicit documentation of UAT person encoding as fast-path optimization. |
| Companion Stubs | Appendix I | NEW: Scope definitions for "BBP Entity Table Protocol" and "BBP Observability Guide". |

### Changelog (r9 → r10)

| Change | Section | Description |
|--------|---------|-------------|
| BBP Profiles | 2.8 | NEW: Defined BBP-Full and BBP-Lite conformance profiles. BBP-Lite enables deterministic encoding without Teacher LLM for Edge AI. |
| STATE_CHECKPOINT | 11.7 | NEW: Periodic checkpoints encoding nesting stack state. Enables O(256) random access for RAG. |
| KEYFRAME | 11.8 | NEW: Heavy checkpoints with entity table. Enables RAG without full stream history. Resolves long-range REFERENCE memory issue. |
| CONTROL Registry Update | 11.5 | New payloads: STATE_CHECKPOINT (0x050), KEYFRAME (0x051), PROFILE (0x052). Removed "Reserved" label from 0x050–0x05F range. |
| Seekable Streams | 11.3 | DOC_START gains capability flag for Seekable streams. |
| Unaccusativity | 12.1.1 | NEW: Documented strategy for split-intransitivity across profiles. |
| Cross-Teacher Validation | 15.4 | NEW: Mandatory dual-Teacher validation for corpus generation. BBP Linguistic Conventions companion document. |
| BBP-AST Reframe | 16.1 | Repositioned value proposition: navigability over compression. Added honest verbosity estimates. |
| BBP-CoT Scope | 17.1 | NEW: Explicit statement that BBP-CoT provides structural transparency, not logical verification. |
| Axiom Group G | 13.2.1 | NEW: Checkpoint consistency axioms G1–G4. |
| Appendix H | H | NEW: Mandatory benchmarks (EXT overhead, SIMD divergence, AST ratio, round-trip, cross-Teacher). |
| Cleanup | Multiple | Removed VOCAB_PAGE deprecation notices (no production deployments). Streamlined legacy references. |

### Changelog (r8 → r9)

| Change | Section | Description |
|--------|---------|-------------|
| 32-bit Rationale Update | 2.6 | Updated argument: 32-bit is now stateless and RAG-friendly thanks to atomic prefixes. |
| Control Registry | 11.5 | 0x040 moved from VOCAB_PAGE to RESERVED (to prevent legacy confusion). |
| Safe Seek Algorithm | 11.6.5 | Added normative algorithm for finding atomic unit boundaries in random access (RAG). |
| Appendix Update | C | Added Type-E (Extension) bit layout. |

### Changelog (r7 → r8)

| Change | Section | Description |
|--------|---------|-------------|
| Semantic formalism positioning | 1.7 | New section: Relationship to AMR/UMR/MRS — positions BBP-IR as the functional analogue; BBP's contribution is the compilation and execution pipeline downstream |
| 32-bit rationale | 2.6 | New section: Formal evaluation of alternatives (variable-width, 32+16 extension, 64-bit) and argument for fixed 32-bit as the physical manifestation of Generative Rigidity |
| long-range REFERENCE | 8.5 | New section: Formal specification for long-range coreference via REFERENCE + LITERAL block; resolves the underspecified "absolute offset" mechanism from §8.2 |
| Axiom Group B update | 13.2.1 | New axioms B8–B11 for long-range REFERENCE structural validation |
| Appendix B update | B | Additional design decision rows for 32-bit rationale and long-range REFERENCE |

### Changelog (r6 → r7)


| Change | Section | Description |
|--------|---------|-------------|
| BBP-AST | 16 | New section: Code Representation as typed AST within the 64-bit token format |
| BBP-CoT | 17 | New section: Structured Chain of Thought with meta-cognitive primitives |
| Group 7 redesign | 5.1 | Redefined Group 7 (0o7x) from Reserved/UNKNOWN to Meta-Cognition & Reasoning |
| CONTROL code range | 11.5 | New CONTROL FEATURES range 0x060–0x07F for programming constructs |
| CONTROL structure | 11.5 | New Structure payloads: REASONING_START (0x016), REASONING_END (0x017) |
| Axiom Groups E, F | 13.2.1 | New validator axiom groups for code and reasoning structural consistency |
| Appendix C update | C | Updated CONTROL Quick Reference with programming range |
| Appendix E | E | BBP-AST test vectors and examples |
| Appendix F | F | BBP-CoT test vectors and examples |
| Appendix G | G | Updated design decisions table for r7 |
| Roadmap update | 15 | Additional corpus requirements for code and reasoning training data |

### Changelog (r5 → r6)

| Change | Section | Description |
|--------|---------|-------------|
| Vision rewrite | 1 | Repositioned BBP as a Language of Thought for native AI cognition and inter-agent communication, not primarily as a compression format |
| Layered Architecture | 1.2 | Defined the three-layer model: Frontier (NL↔BBP), Core (BBP-native LLM), Inter-Agent (BBP wire protocol) |
| Generative Rigidity | 1.3 | New principle: structural constraints enable semantic creativity, grounded in agglutinative morphology |
| Semantic Granularity | 1.4 | New guidance on dictionary ID resolution: broad synsets for core cognition, fine senses for frontier rendering |

*(All r4 → r5 changes preserved; see previous changelog.)*

---
