# Agentic RAG with Self-Critique Loop for Software Engineering KB
Agentic RAG with Self-Critique Loop for Software Engineering KB

**Overview**
This project demonstrates a lightweight but robust implementation of a Retrieval-Augmented Generation (RAG) pipeline enhanced with an Agentic self-critique loop, enabling intelligent responses grounded in a knowledge base of software engineering best practices.

**Key Components**
Component	Details
_Embeddings:_	Azure OpenAI text-embedding-ada-002 for vectorizing knowledge base
_Vector Store_	ChromaDB: (DuckDB + Parquet backend, persistent mode)
_Language Model_:	Azure OpenAI gpt-4o-mini for answer generation and critique
_LangGraph Nodes	_
1. Retrieve KB
2. Generate Answer
3. Critique Answer
4. Refine Answer
_Data Format:_	Input KB in JSON with fields like doc_id, answer_snippet, source
_Query Interface:	_Notebook prompt; accepts user queries interactively
_Environment	_ :Google Colab + Python + .env for secure key management

**Core Insights**
_1. Separation of Embedding and Chat Models Is Crucial_
Using two different deployments (for embedding vs. chat) avoids dimension mismatch and model capability errors.

This allows modular tuning and cost control.

_2. Embedding Dimension Mismatch Was a Common Pitfall_
Initial InvalidArgumentError: expected 1536 got 384 revealed that Chroma’s default retriever uses MiniLM if embeddings aren't explicitly provided.

_3. Self-Critique Improves Contextual Accuracy_
Adding critique (REFINE: vs. COMPLETE) ensures that initial hallucinations or missed citations are caught early.

It creates a pseudo-agent behavior without requiring heavy orchestration frameworks.

_4. Citation Format Enforcement ([KBxxx]) Is Key to Traceability_
Enabling downstream auditing and KB linkage.

LLM must be prompted explicitly to preserve this format for reliable critique.

_5. User-Controlled Refinement Increases Precision_
Refinement happens only when needed (i.e., based on critique).

It prevents over-generation, reducing token usage and cost.

**Observations from Testing**
Query	Initial Hit(s)	Critique	Final Snippet(s) Used
Best practices for caching	KB003	REFINE: invalidation	KB003, KB013
Setting up CI/CD pipelines	KB007, KB017	COMPLETE	KB007, KB017
Performance tuning tips	KB002	REFINE: profiling	KB002, KB022
API versioning	KB005	REFINE: semantic versioning	KB005, KB015
Error handling	KB009	COMPLETE	KB009

**Benefits**
Faster iteration: Avoids full retraining by using a retrieval-first architecture.

Interpretable outputs: Citations like [KB017] help trace answer lineage.

Scalable: Easily plug into enterprise knowledge systems with just JSON updates.

Reusable: Modular LangGraph nodes allow quick adjustments to new domains.

**Challenges & Fixes**
Challenge	Fix Applied
Embedding mismatch error (384 vs. 1536)	Explicitly used embedding model during indexing and querying
Chat model error with embedding deployment	Separated .env variables for chat vs. embedding
Citation missing or malformed	Prompt engineered to enforce [KBxxx] format
LangGraph function missing error	Ensured all functions like generate_initial_answer() are defined before usage

**Deliverables**
index_kb.py: Upserts KB to Chroma

agentic_rag_simplified.py: RAG pipeline logic with critique

Interactive CLI/Notebook cell for testing

All outputs traceable with logs for:

Retrieved KBs

Initial Answer

Critique

Final Answer

**Future Enhancements**
Add support for LangChain RunnableGraph or AgentExecutor

Enable semantic scoring for snippet selection

Deploy as a FastAPI REST endpoint

Enable streamed responses in chat UI

Include explanation generation per cited snippet
