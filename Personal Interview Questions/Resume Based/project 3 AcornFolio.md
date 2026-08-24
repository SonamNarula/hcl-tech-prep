# Project Questions — AcornFolio (AI-Powered Mutual Fund Assistant: Next.js/Prisma/LanceDB/RAG Pipeline)

---

### Question 26 — Tell me about this project.
**Difficulty:** Easy | **Category:** Project Overview

**Interview Answer**
"AcornFolio is an AI-powered assistant for mutual fund information — a user can ask natural-language questions about funds and get grounded answers, rather than digging through fund fact sheets manually. It's built on Next.js for the full-stack framework, Prisma as the ORM over a relational database, and LanceDB as a vector database powering a RAG — Retrieval-Augmented Generation — pipeline so answers are grounded in actual fund data rather than the model just hallucinating numbers."

**Follow-up Questions**
- What is RAG, in your own words?
- Why does grounding matter specifically for financial data?
- What data source feeds the vector database — is it real fund data or sample data?

**Common Mistake**
Being able to say "RAG" but not being able to explain, step by step, what actually happens when a query comes in — interviewers will absolutely dig here since this is your most technically ambitious project.

---

### Question 27 — Explain RAG in your own words, specifically as used in this project.
**Difficulty:** Medium | **Category:** Core Concept

**Answer**
RAG means the LLM doesn't answer purely from its trained knowledge — instead, relevant documents (or chunks of fund data) are retrieved from a vector database based on similarity to the user's query, and those retrieved chunks are injected into the prompt as context before the LLM generates its answer. This grounds the response in actual, current data rather than the model's static training knowledge, which is especially important for something like fund NAVs or returns that change over time and where the LLM should never be "guessing."

**Interview Answer**
"When a user asks something like 'which fund has the lowest expense ratio in this category,' the query first gets converted into an embedding — a vector — and I search LanceDB for the most similar chunks of fund data I've indexed. Those retrieved chunks get inserted into the prompt as context, and only then does the LLM generate an answer — so it's answering from actual retrieved data, not just recalling facts from training, which matters a lot for financial numbers that need to be accurate and current."

**Follow-up Questions**
- What is an embedding?
- Why LanceDB specifically — what makes it different from Pinecone or Chroma?
- How do you chunk the fund data before embedding it — what's your chunking strategy?
- What happens if the retrieval step returns irrelevant chunks?

**Strong Candidate Point**
Mention chunk size trade-offs: too large and you waste context/dilute relevance, too small and you lose surrounding context — and that you had to think about this when preparing the fund data for embedding.

---

### Question 28 — Why LanceDB instead of Pinecone, Chroma, or FAISS?
**Difficulty:** Medium | **Category:** Tech Choice

**Interview Answer**
"LanceDB is embedded and file-based — it doesn't need a separate hosted service like Pinecone does, which made it simpler to set up for a project of this scale, and it's fast for the vector similarity search I needed. Pinecone is a good managed option but adds hosting/cost overhead I didn't need yet. Chroma is similar to LanceDB in being lightweight, but I found LanceDB's performance and columnar storage approach (built on Lance format, similar to Parquet) appealing for this use case."

**Follow-up Questions**
- What similarity metric does your vector search use — cosine similarity, dot product, Euclidean?
- How many vectors/documents are in your database currently?
- What would change about this choice if the dataset grew to millions of documents?

---

### Question 29 — What's the role of Prisma in this project?
**Difficulty:** Easy | **Category:** Backend / Data

**Interview Answer**
"Prisma is my ORM for the relational side of the data — things like user accounts, saved queries, or structured fund metadata that fits a relational model better than a vector store. It gives me type-safe database queries and handles migrations, which is a big win when the schema changes as the project evolves."

**Follow-up Questions**
- What database is Prisma actually talking to underneath — Postgres, MySQL, SQLite?
- Why split data between a relational DB (via Prisma) and a vector DB (LanceDB) instead of using one for everything?
- What does a Prisma migration actually do?

**Common Mistake**
Not being able to clearly separate "what lives in the relational DB" vs. "what lives in the vector DB" — this project explicitly uses two different data paradigms and interviewers will test if you understand *why*.

---

### Question 30 — Why Next.js instead of plain React or Node/Express?
**Difficulty:** Medium | **Category:** Framework Choice

**Interview Answer**
"Next.js gives me both frontend and backend (API routes) in one framework, which simplified the architecture for a full-stack RAG app — I don't need a separate Express server just to expose an endpoint that runs the retrieval-and-generation pipeline. It also gives server-side rendering and good developer experience out of the box."

**Follow-up Questions**
- Are you using the App Router or Pages Router, and why?
- Where does the actual RAG pipeline run — in an API route, a server action, or an edge function?
- How do you handle streaming the LLM's response back to the frontend, if at all?

---

### Question 31 — Walk me through the full pipeline when a user asks a question.
**Difficulty:** Hard | **Category:** End-to-End Architecture

**Interview Answer**
"User submits a query on the frontend → it hits a Next.js API route → the query text gets embedded into a vector → LanceDB is searched for the top-k most similar chunks of fund data → those chunks are formatted into a prompt template along with the user's original question → that combined prompt is sent to the LLM → the LLM's response streams (or returns) back to the frontend and is displayed to the user."

**Follow-up Questions**
- What value of k (number of retrieved chunks) do you use, and how did you decide on it?
- How do you handle a query where no relevant chunks are found?
- What LLM/API are you calling for generation, and how is that call made securely (API key handling)?
- What's your average response latency, and where's the bottleneck — embedding, retrieval, or generation?

**Strong Candidate Point**
Being able to name a real trade-off you hit — e.g., "increasing k improved answer completeness but also increased latency and cost, so I had to tune it."

---

### Question 32 — How do you prevent the AI from hallucinating fund numbers?
**Difficulty:** Hard | **Category:** Core Technical Concern

**Interview Answer**
"That's the whole point of the RAG grounding — by injecting actual retrieved fund data into the prompt and instructing the model to answer only from the provided context, I reduce hallucination risk significantly compared to a raw LLM call. [If you have prompt-engineering specifics, e.g. explicit instructions like 'answer only using the provided context; if the answer isn't in the context, say so'] I also explicitly instruct the model not to answer if the retrieved context doesn't contain the needed information."

**Common Mistake**
Claiming hallucination is "completely solved" — it isn't, even with RAG; the honest answer is that RAG significantly *reduces* hallucination risk, not eliminates it.

**Follow-up Questions**
- Can RAG still hallucinate? Why?
- How would you evaluate whether your system's answers are actually accurate?

---

### Question 33 — What happens if the vector database or LLM API is down?
**Difficulty:** Medium | **Category:** Resilience

**Interview Answer**
"[Be honest about current state.] Ideally there's a graceful failure — a clear error message to the user rather than a crash or silent wrong answer. If this isn't currently handled robustly, that's a known gap I'd address with proper try/catch around the API calls and a fallback UI state."

---

### Question 34 — What's the most difficult part of building AcornFolio?
**Difficulty:** Medium | **Category:** Challenges

**Interview Answer**
"[Have something specific and real ready — likely candidates: getting chunking strategy right so retrieved context was actually relevant, tuning the prompt so the LLM reliably stayed grounded in retrieved data instead of drifting into general knowledge, or handling the two-database architecture (Prisma + LanceDB) cleanly.]"

**Common Mistake**
Saying the hardest part was "learning RAG" in the abstract rather than naming a specific implementation challenge you actually hit and solved.

---

### Question 35 — What would you improve if you continued this project?
**Difficulty:** Medium | **Category:** Reflection

**Interview Answer**
"I'd add proper evaluation — a way to systematically test whether retrieved chunks are actually relevant and whether answers are accurate, maybe with a small labeled test set. I'd also look at hybrid search — combining vector similarity with keyword/metadata filtering — since pure semantic search can sometimes miss exact numeric queries like 'expense ratio under 1%.'"

**Follow-up Questions**
- What is hybrid search, and why would it help here specifically?
- How would you measure retrieval quality?

**Strong Candidate Point**
Mentioning that exact numeric/filter queries (like "expense ratio < 1%") are actually a weak spot for pure vector similarity search, and that metadata filtering or structured queries (via the Prisma/relational side) would complement RAG well — shows genuine understanding of RAG's limitations, not just its buzzwords.

---

### Question 36 — Is this project connected to real, live mutual fund data, or static/sample data?
**Difficulty:** Easy | **Category:** Data Source (be ready — this gets asked directly)

**Interview Answer**
"[Answer honestly based on actual project state — if it's a static/sample dataset, say so clearly.] Right now it runs on [a static dataset of fund data / sample data I curated] rather than a live feed — connecting to a real-time fund data API would be a natural next step for production use."

**Common Mistake**
Implying real-time live financial data integration if the project actually uses a static or sample dataset — this is an easy thing for an interviewer to catch with one follow-up question, and it damages trust in everything else you've said about the project.
