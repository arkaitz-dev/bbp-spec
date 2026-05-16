# BBP: Basque Binary Protocol
### A Neuro-Symbolic Interlingua for Machine Cognition and Inter-Agent Communication
**Status:** Working Draft (v1.0-draft-r40) — Theoretical Specification. No implementation exists yet.

Natural language was not designed for machines. It is ambiguous, context-dependent, and relies heavily on positional syntax. Yet, modern AI architecture forces models to use human text as their internal representation for everything: prompts, JSON APIs, inter-agent communication, and Chain-of-Thought reasoning.

This creates a structural problem. Every time an agent receives a message, it must fragment it via BPE tokenizers and resolve syntactic ambiguity before it can process the semantics. The structure of the message is not in the message — it must be inferred. **BBP (Basque Binary Protocol)** proposes a different foundation: a machine-native, strictly deterministic representation of meaning where structure is in the token itself, not reconstructed from context.

---

## What is BBP?

BBP is not a new language model; it is a **binary semantic substrate**. It replaces text with a deterministic stream of **64-bit typed tokens**, strictly separating *what* is being talked about from *how* it participates in the proposition.

Each 64-bit token is divided into:

- **LEMMA (32 bits):** A typed semantic identifier — concept ID plus ontological category information encoded in the bit layout itself. For verbs, the LEMMA also carries lexical aspect class (Aktionsart) and valency, making structural validation possible without dictionary lookup.
- **FEATURES (32 bits):** The grammatical and pragmatic context. It encodes semantic roles (29 explicit cases, including ergative-absolutive), tense, aspect, mood, polarity, evidentiality, animacy, honorific register, and more.

By combining these tokens, systems exchange **structured semantic primitives** instead of ambiguous character strings. It is, in the specification's own words, the language of thought that sits at the centre — a typed substrate designed for machine cognition, not human reading.

---

## The 3-Layer Architecture

BBP does not eliminate natural language; it confines it to the human interface layer. The protocol defines a three-tier ecosystem:

1. **Frontier Translators (NL ↔ BBP):** Specialized LLMs trained exclusively to translate human language into BBP-IR (a JSON intermediate representation) and back. They perform the hard disambiguation work once, at the boundary. They are the *only* components that ever touch natural language text.
2. **Core Cognitive Engine:** A purpose-built LLM that ingests and emits only 64-bit BBP streams. Its tokenizer consumes typed semantic tokens; its vocabulary is the structured space of valid token combinations. The hypothesis is that reasoning over explicit, role-marked semantic structure produces measurable advantages over reasoning in natural language tokens — a claim the specification frames as falsifiable and testable, not assumed.
3. **Inter-Agent Protocol:** Agents that share BBP communicate by transmitting binary streams directly over the wire. No natural language involved, no parsing uncertainty, no pronoun resolution ambiguity.

---

## The Core Engine as Pre-Verbal Substrate

A complementary architectural reading of the same design: the Core Engine, by manipulating LEMMAs and their compositional relations directly, occupies a functional position analogous to what cognitive science calls **pre-verbal thought** — the substrate where concepts are handled before they are clothed in any particular language. The Frontier Translator then plays the role of the verbal-articulation layer, rendering Core Engine output into natural language and making it accessible to human interlocutors.

This reading suggests that several BBP design decisions — cross-linguistic LEMMA deduplication, preposition elimination, broad synsets rather than fine senses — implement what Quian Quiroga has called *productive forgetting*: the architectural enforcement of sparseness that the cognitive-science literature identifies as the precondition of abstraction, not a deficit of representational power. Following this principle, the Core Engine should be **just large enough** to internalise the compositional regularities of the BBP space, and no larger: optimal at the elbow of the compositional generalisation curve, not at its ceiling. The scaling-law intuition "*larger is better*" applies to the LLM layers; it does not apply unconditionally to the Core Engine.

---

## The Asymmetric LLM↔Core Engine Dialogue

The same architecture suggests an operating mode beyond the linear NL→BBP→Core Engine→BBP→NL pipeline: an **asymmetric iterative dialogue** between a natural-language LLM and a Core Engine, mediated by BBP. The LLM proposes hypotheses in natural language; the Core Engine validates them structurally against axiomatic constraints and emits typed critiques; the LLM ingests the critique and refines. The loop continues until convergence.

This differs from current chain-of-thought in a critical way. In current systems the generator and the verifier are the same model, sharing systematic errors that self-consistency methods can attenuate but not eliminate. In the asymmetric dialogue, the verifier has **architecturally different failure modes** from the generator: each layer detects what the other layer cannot detect by construction. The closest natural analogue is Kahneman's System 1 / System 2 distinction, with one refinement — the two systems are *architecturally* separate, integrated by protocol rather than by mechanism.

These claims are architectural readings, not phenomenological claims. They do not commit the specification to any thesis about consciousness, agency, or subjective experience. They commit it to one structural claim: the BBP architecture is not merely an efficient representation; it is *structurally homologous* to the leading functional decomposition of human cognition into pre-verbal substrate and verbal articulation. See §1.12 of the specification for the full treatment, falsifiability conditions, and explicit epistemic status.

---

## Beyond Conversation: Reasoning and Code

BBP extends beyond standard communication to formalize complex AI tasks natively:

- **Native mathematical precision:** Numeric types (integers, reals, fractions, complex numbers) are encoded directly in the token — not as digit sequences parsed from text. A `NUM_REAL` token carries an IEEE 754 float32 value in its FEATURES field; its LEMMA encodes sign, logarithmic magnitude, and parity as structured sub-fields, so the model receives mathematical geometry by construction rather than having to infer it statistically from surface forms. For expressions that require exact results, a `MATH_EXPRESSION[EXECUTABLE]` flag delegates arithmetic to a deterministic runtime layer — the Core Engine generates the expression and the runtime evaluates it, guaranteeing correctness without relying on the model's probabilistic output.
- **BBP-CoT (Chain of Thought):** Instead of generating free-form text, the engine emits typed, structurally auditable blocks: `HYPOTHESIS`, `EVIDENCE`, `INFERENCE_STEP`, `CONCLUSION`, and `RETRACTION` when correcting an error. The specification is explicit that this provides structural transparency, not logical verification — the blocks make reasoning inspectable, not provably correct.
- **BBP-AST (Abstract Syntax Tree):** Code is represented as typed token streams using 24 defined constructs (functions, conditionals, loops, classes, etc.). The same BBP-AST can be rendered to Python, Rust, Zig, or any other target language — the representation is language-agnostic at the semantic level.

---

## Why "Basque"?

The protocol draws its initial inspiration from the morphology of **Basque (Euskara)**, specifically its **ergative-absolutive** case alignment. In BBP, each token explicitly marks its role in the proposition — who the agent is, who the patient is — through internal bit fields, independently of position in the stream. This eliminates a class of structural ambiguity that positional languages like English leave implicit.

The other property initially borrowed from Basque — agglutination — was ultimately **abandoned** as the token-level mechanism. An earlier 32-bit design chained auxiliary tokens to express features, directly mirroring Basque's affix-stacking strategy. The resulting specification accumulated layers of compensatory complexity that a computational consumer gains nothing from. The current BBP-64 format is architecturally **fusional**: each token carries all its grammatical information internally, without requiring adjacent tokens. The irony is productive: BBP was born from agglutinative inspiration, but optimizing for its actual consumer led to a design closer to Latin or Sanskrit.

What BBP retains from Euskera is not the mechanism but the insight: explicit role-marking makes semantic structure transparent in ways that positional languages do not.

BBP-64 extends this universally, incorporating typological coverage from languages worldwide: evidentiality systems from Quechua and Tibetan (16 values), Bantu noun class systems covering Swahili and Zulu, three-dimensional honorific register from Japanese keigo and Korean speech levels, the Silverstein animacy hierarchy, and 29 explicit cases covering semantic distinctions absent from any individual natural language.

---

## Epistemic Status

BBP v1.0-draft-r39 is a **theoretical specification and falsifiable hypothesis**. No toolchain has been implemented. No Frontier Translator has been trained. No Core Engine exists. No empirical data supports or refutes the architecture's core claims.

The specification defines a micro-experiment (Stage 0) as the first empirical test — a 125M-parameter model trained on a domain-restricted corpus of 50K BBP token pairs, with explicit go/no-go criteria and documented fallback paths if the architecture fails to validate.

The hypothesis is: a neuro-symbolic system built on this specific combination will exhibit measurable advantages in reasoning accuracy and semantic consistency over natural-language-native architectures of equivalent parameter count. Every architectural claim is either a formal specification or an empirical prediction. Neither class has been validated.

## Explore Further

Read the [full specification (draft-r39)](https://github.com/arkaitz-dev/bbp-spec/blob/master/bbp-spec.md) to explore the complete token format, the 29-case inventory, the *Inverted Ground Truth* corpus generation methodology, the axiomatic validation framework, and the staged training roadmap.

