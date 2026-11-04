# HexCoach: Grill Decisions

**Status: ONGOING.** The grilling session is paused (bad connection) and will resume later.
Source plan: [The Plan.md](The%20Plan.md). Nothing has been implemented yet.

## Settled decisions

### Product
- **Hosting:** public demo on free tiers. README with screenshots and Langfuse trace links.
- **v1 core:** rules and card-text Q&A with cited sources (judge-style). The other use cases (sequence of plays, deckbuilding/strategy, best play) come later.
- **Grounding:** always cite rule numbers or sources. If evidence is weak, say so or ask a clarifying question (abstain), never guess confidently.
- **Accounts:** real accounts (login), not anonymous sessions.

### Evaluation and observability
- **Evals:** hand-curated golden set (about 30-50 rulings with expected rule numbers), run as a Langfuse dataset and experiment, scores shown in the README.
- **User feedback:** thumbs up/down sent as a Langfuse score on the message's trace. Requirement: every chat response returns its trace id to the frontend.
- **No LLM-as-judge** for now.

### Cards
- **Card mentions in answers:** the model wraps cards as `[[Card Name]]`. The backend validates each against the card database, and the frontend only renders hover previews for validated ones.
- **Card context for the LLM:** `@`-mentioned cards are injected with full text. A decklist is injected as card names only. The LLM gets a `lookup_card` tool for any other card, and each call is traced in Langfuse.
- **Card data source:** snapshot of a community card API. Which one is still to be chosen. Ask the user before doing any research.

### Decklists
- **Format:** sectioned plain text, parsed deterministically in code (not by the LLM). Card names can contain commas.

  ```
  Legend:
  1 Viktor, Herald of the Arcane

  Champion:
  1 Viktor, Innovator

  MainDeck:
  3 Consult the Past
  ...

  Battlefields:
  1 Ravenbloom Conservatory
  ...

  Runes:
  6 Mind Rune
  6 Order Rune

  Sideboard:
  3 Salvage
  ...
  ```
- **Validation at import:** resolve each line to a real card (suggest fixes for unknown names) plus basic structural checks (section counts, copy limits) shown as warnings, not blocking. The numbers must be configurable constants filled in from the official rules. They are **not verified yet**.
- Saved decklists are stored per account.

### Agent and retrieval
- **Providers:** Gemini free tier as primary, Groq second, OpenRouter third, as a configurable fallback chain. Per-user rate limits to protect the shared quota.
- **LangGraph v1 (corrective-RAG-lite):** resolve card references -> retrieve rules -> grade evidence -> answer with citations, or abstain/ask a clarifying question. One retry with a rewritten query if evidence is weak. No router yet (added when other use cases arrive).
- **Retrieval:** chunk by rule number and section (keep rule id and parent section as metadata), hybrid search (keyword + vector) in Postgres/pgvector. Blog posts chunked separately and tagged by source.
- **Embeddings:** small embedding model (e.g. bge-small via an ONNX runtime) running inside the backend. Documents embedded once at ingestion, queries embedded at runtime with the same model. This is not the chat LLM.

### Data and storage
- **Database and auth:** Supabase (Auth plus Postgres with pgvector). Note: free projects pause after inactivity, so plan a keep-alive ping.
- **Conversations:** own `conversations` and `messages` tables only. No LangGraph checkpointer in v1. Each request loads message history and runs the graph fresh.
- **Source content:** the rules text, blog posts and card data are committed to the repo. Must add Riot's required fan-content disclaimer to the README and record an ADR. The exact policy wording is **not verified**.

### Deferred
- **Backend hosting:** decide at deployment time. The code must be container-based and portable. Memory matters because of the embedding model. Frontend goes on a free static host.

## Open questions (next round, interrupted)
- **Q19 Language:** answer in the user's language but keep card names and rule text in English, with the query translated to English for retrieval (recommended), or English only?
- **Q20 Source precedence:** tag chunks with source type and date, with a fixed precedence stated in the prompt (e.g. card text > rules > blog posts, exact order to be confirmed by the user), and say when a blog post is the only support (recommended); or treat all equal; or only the rules document can be cited as authority.
- **Q21 Prompts:** managed in Langfuse with a repo copy as automatic fallback (recommended); or repo only; or Langfuse only.

## Branches not yet explored
- Frontend stack (React tooling, UI library, chat component).
- API shape and streaming (likely SSE) between React and the Python backend.
- `@` card autocomplete mechanics (client-side index vs server search) and card image handling (hotlink vs store).
- Per-user rate limiting details and abuse protection.
- Testing and CI (golden set in CI?), repo layout, Python tooling.
- Trace tagging (user and session ids, privacy).
- "Not an official judge" disclaimer wording.
- Milestones and ordering of work.
- Glossary and ADRs (the `domain-modeling` skill is not installed, so write these by hand into `docs/`).

## Unverified facts (do not rely on them)
- Riot's fan-content / Riftbound digital-tools policy and what it allows.
- Deck-construction numbers (section sizes, copy limits).
- Exact free-tier limits of Gemini, Groq, OpenRouter, Supabase and the hosting options.
- Which community card API to use and its terms.
