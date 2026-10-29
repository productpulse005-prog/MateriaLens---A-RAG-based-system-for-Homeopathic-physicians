# MateriaLens---A-RAG-based-system-for-Homeopathic-physicians

**A source-grounded research assistant for homeopathy reference texts.**

MateriaLens is an AI Product Management case exploring how Retrieval-Augmented Generation (RAG) can help homeopathic physicians find, compare, and verify passages from classical Materia Medica and repertory/reference texts.

> **Scope:** MateriaLens is intended for reference research only. It does not diagnose conditions, prescribe remedies, establish treatment effectiveness, or replace advice from a qualified healthcare professional.

## The problem

Practitioners may need to consult several books and reference tools to locate information about symptoms and modalities. Relevant passages can be scattered across sources, terminology may differ, and comparing citations manually takes time.

MateriaLens explores whether a focused search workflow can reduce the effort required to find relevant passages while preserving the original source context and making verification easier.

## Proposed solution

A practitioner enters a natural-language research question. MateriaLens retrieves relevant passages from a defined, rights-checked corpus and presents a concise summary alongside citations and an Evidence Matrix. When sources disagree or the corpus does not contain enough evidence, the system should make that limitation visible rather than guessing.

## Core capabilities in the MVP scope

- Natural-language search across an indexed reference corpus.
- Hybrid retrieval using keyword matching and semantic similarity.
- Re-ranking to prioritize relevant passages.
- Source-grounded summaries with source title, author, edition, and page or passage identifiers where available.
- An Evidence Matrix that organizes relevant passages by queried concept or symptom.
- Side-by-side evidence comparison, including visible disagreements and missing evidence.
- Clarifying questions for ambiguous queries and abstention when evidence is insufficient.
- Structured feedback on evidence relevance, citation accuracy, and missing passages.

## Proposed architecture

### Indexing pipeline

1. **Source corpus:** 3–5 public-domain or explicitly licensed Materia Medica texts and one repertory/reference text.
2. **Ingestion and OCR:** Import clean digital text or extract text from PDFs; apply OCR to scanned documents and sample-check its quality.
3. **Chunking:** Use structure-aware chunks that preserve book, author, chapter, symptom, and nearby context. The current design proposes approximately 400-token chunks with 80-token overlap; validate these settings against retrieval quality.
4. **Indexing:** Generate embeddings and maintain a keyword index, with source metadata attached to each passage.
5. **Storage:** Chroma is the proposed vector store for the prototype; confirm the final store and deployment approach during implementation.

### Query pipeline

1. Practitioner submits a research question.
2. The system identifies useful search concepts and retrieves candidates using keyword and semantic search.
3. Candidate results are combined and ranked; the initial design assumes a small result set, starting with Top-5 passages.
4. A language model synthesizes an answer using retrieved passages only.
5. The interface returns a concise synthesis, an Evidence Matrix, source passages, citations, and any evidence gaps or conflicts.

**Prototype choices:** Google AI Studio and Gemini 2.5 Flash are proposed for initial interaction testing. The embedding model and current model availability/pricing must be verified before implementation. These are design choices, not a claim that a production system has been deployed.

## Safety and trust principles

- Use only retrieved passages from the indexed corpus to support factual claims about those sources.
- Never invent quotations, citations, editions, or page numbers.
- Show disagreement between sources instead of deciding which is clinically correct.
- State clearly when evidence is missing or retrieval is weak.
- Decline diagnosis, prescribing, cure claims, and individualized medical advice.
- Index only documents whose reuse rights have been checked and recorded.
- Keep case text within the active session unless the user explicitly chooses to save it; protect saved searches with account-level access controls.

## Evaluation approach

The PRD proposes a small replay set before each release, followed by a larger expert-reviewed evaluation set. Evaluation should focus on the retrieved evidence—not just whether the final answer sounds plausible.

Key measures include:

- **Retrieval quality:** Precision@5 and Recall@5 on labeled queries.
- **Citation accuracy:** Percentage of citations that support the associated claim; proposed target of at least 95% before beta.
- **Grounded generation:** Faithfulness of factual claims to retrieved passages; proposed target of at least 95%, subject to human audit.
- **North-star metric:** Median time to locate relevant, verifiable passages, compared with the existing workflow.
- **Abstention quality:** Whether the system asks for clarification or declines to answer when evidence is ambiguous, absent, or out of scope.

Targets are proposed acceptance criteria, not measured results. Actual performance requires testing against a verified corpus with domain-expert review.

## Data and privacy assumptions

The MVP corpus should contain only public-domain or explicitly licensed documents. Each source should have a register containing its title, author, edition/year, rights basis, format, location metadata, ingestion date, and version. Scanned documents require OCR quality checks. The corpus is planned for quarterly review and re-indexing when authorized material changes.

Case text is not retained beyond the active session unless the user explicitly saves it. The PRD proposes deleting saved searches and profile data within 30 days after account deletion, subject to legally required retention and the normal backup-expiry cycle.

## Current status

MateriaLens is currently documented as a **product concept and PRD / prototype plan**. The PRD defines the proposed experience, architecture, safety boundaries, and evaluation approach. A real indexed corpus, verified citations, prototype test output, and performance results still need to be validated before claiming that the system works end to end.

## Planned next steps

1. Select the exact source texts and verify their reuse rights.
2. Build the source register and prepare a small, quality-checked corpus.
3. Implement ingestion, chunking, hybrid retrieval, and citation metadata.
4. Test the six initial evaluation queries and record expected passages from the actual source texts.
5. Run the prototype, capture real outputs, and replace response templates with observed results.
6. Review retrieval quality, citation accuracy, abstention behavior, and cost with domain experts before expanding the corpus.

## Disclaimer

MateriaLens is an exploratory product case study for reference retrieval. Historical or reference-text descriptions are not evidence that a remedy is safe or effective. Users should consult qualified healthcare professionals for diagnosis and treatment decisions.
