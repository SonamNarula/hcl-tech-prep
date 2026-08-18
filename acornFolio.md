# AcornFolio — Deep Grilling Edition
### Every question, with full cross-questioning chains (3–6 layers each)
### Next.js · React · TypeScript · Prisma + SQLite · LanceDB · Zustand · Tailwind/shadcn

**Built from your actual README (github.com/SonamNarula/AcornFolio) — grounded, not guessed.**

**What it is (from your README):** *AcornFolio is an AI-powered mutual fund dashboard for Indian retail investors. It helps users understand whether they are losing money through Regular mutual fund plans, compare them with Direct plans, analyze portfolio risk, calculate tax and exit-load tradeoffs, and ask a portfolio-aware AI assistant for explanations. Built for a hackathon/demo setting.*

**Core architecture claim (from your README):** A hybrid system — deterministic code handles the actual financial math (SIP/XIRR/tax/exit-load calculations), while a RAG-style AI layer (LanceDB semantic search feeding an LLM) handles explanation in plain language, grounded in portfolio + fund context.

**Confirmed gaps, straight from your own README's roadmap (use these honestly, don't hide them):**
- No authentication — portfolios are session-based, don't persist across devices
- Sample/seeded fund data, not a live production data pipeline
- No live AMFI NAV sync yet (fund prices aren't automatically kept current)
- Weak/no test coverage specifically flagged for tax, exit-load, and compounding calculations
- RAG evaluation for AI answer correctness isn't yet measured
- Not yet set up for a production-grade database (currently local SQLite file)

> ⚠️ **🚨 VERIFY tags** below mean the detail isn't in the README and needs confirming in your actual code (exact prompt structure, embedding model, specific formula implementations). Everything else is grounded directly in what you've documented — use it with confidence.

---

# SECTION A — TECHNOLOGY CHOICE GRILLING

## A1. Why Next.js (App Router)?

**Q:** Why Next.js instead of a separate React frontend + Express/Node backend, like your other projects?
**A:** "Next.js's App Router lets me write frontend UI and backend API routes in the same project and deployment unit — for a hackathon-scoped project needing both a dashboard and several data/AI endpoints, that meant less infrastructure to stand up and coordinate compared to running two separate services."

**Cross-Q1:** What's actually different about the App Router vs. the older Pages Router in Next.js?
**A:** "The App Router uses React Server Components by default, nested layouts, and a file-system-based routing convention built around folders with `page.tsx`/`route.ts` files — it also unifies data fetching patterns (server components can fetch data directly) in a way the older Pages Router didn't."

**Cross-Q2:** Where do your API routes actually live, and what do they handle?
**A:** 🚨 VERIFY exact route structure, but conceptually: routes under `app/api/` handling things like fund search, portfolio CRUD, and the AI chat endpoint — each acting as a small backend handler colocated with the frontend code.

**Cross-Q3 (grilling):** Isn't mixing frontend and backend in one project a scalability problem long-term?
**A:** "For a project at this scope, no — it's actually the standard Next.js pattern (a 'full-stack framework'), and plenty of production apps run this way successfully. It would become a real constraint if the backend needed genuinely independent scaling from the frontend, or a different deployment cadence — neither of which applies here."

## A2. Why Prisma + SQLite (not Postgres/MySQL)?

**Q:** Why SQLite instead of a proper server-based database like PostgreSQL?
**A:** "For a hackathon/demo-scoped project, SQLite meant zero infrastructure setup — it's a local file, no separate database server to run, provision, or connect to. That let me focus development time on the actual product logic rather than database ops."

**Cross-Q1:** What are you giving up by using SQLite instead of Postgres?
**A:** "Concurrent write performance at scale, built-in support for more advanced data types and features Postgres has, and critically — it doesn't work well for a real multi-user deployed product, since it's a single local file rather than a networked service multiple app instances could share."

**Cross-Q2:** Your own README roadmap says you need 'deployment-ready environment handling for production databases' — what does that actually mean you'd have to change?
**A:** "Migrate from SQLite to a hosted database like PostgreSQL, since SQLite's local-file model doesn't work well for a deployed app running on serverless/cloud infrastructure where the filesystem isn't reliably persistent across instances or restarts — I'd need to update the Prisma schema's datasource and handle a real connection string via environment variables."

**Cross-Q3 (grilling):** Why Prisma specifically as the ORM, rather than writing raw SQL?
**A:** "Prisma gives type-safe database access — the generated client's types are derived directly from my schema, so a mismatch between what I query and what the schema defines gets caught at compile time, not at runtime. It also makes schema migrations and switching databases later (SQLite to Postgres) much more straightforward than hand-writing and maintaining raw SQL across that change."

## A3. Why LanceDB for vector search?

**Q:** Why LanceDB specifically, over something like Pinecone, Chroma, or Weaviate?
**A:** 🚨 **VERIFY your actual reasoning** — a defensible honest answer if it was largely a practical/hackathon-scope choice: "LanceDB runs embedded/locally without needing a separate hosted vector database service or API keys for a third-party platform — for a hackathon timeline, that meant one less piece of external infrastructure to provision and manage, while still getting real vector similarity search."

**Cross-Q1:** What's the actual trade-off of an embedded/local vector store vs. a hosted one like Pinecone?
**A:** "A hosted service like Pinecone handles scaling, replication, and availability for you, and is built for production-scale multi-user workloads. LanceDB embedded is simpler to set up and has no ongoing service cost, but scaling it to a large, multi-user, concurrently-written dataset would need more infrastructure thought than a managed service would."

**Cross-Q2:** What's actually stored in your LanceDB fund_vectors table — what are you embedding?
**A:** 🚨 **VERIFY** — likely fund metadata/descriptions (fund name, category, objective, key stats) converted into embeddings, so a user's natural-language question can be matched against semantically similar fund records rather than requiring exact keyword matches.

**Cross-Q3:** What embedding model are you using to generate those vectors?
**A:** 🚨 **VERIFY THIS — very likely question, know it cold.** Whatever model you actually used (a local embedding model, or one via Ollama/an API) — be ready to name it specifically rather than saying "an embedding model."

**Final grilling Q:** How do you know your semantic search is actually returning relevant results and not just plausible-looking noise?
**A:** 🚨 **VERIFY honestly** — this maps directly to a gap your own README names: "🚨 Honestly, this is exactly what my README's roadmap flags as unfinished — I haven't built a formal RAG evaluation process to measure retrieval/answer correctness yet. Right now it's validated by manual spot-checking during development, not a systematic evaluation, which is a real gap for anything beyond a demo."

## A4. Why Zustand (not Redux/Context)?

**Q:** Why Zustand for state management instead of Redux or plain Context API?
**A:** "Zustand has a much smaller API surface and less boilerplate than Redux — no action types, reducers, or dispatch ceremony — while still giving a global store outside the component tree, which Context alone doesn't cleanly provide without extra work for things like avoiding unnecessary re-renders."

**Cross-Q1:** What specifically does Zustand hold in this app — what's actually global state here?
**A:** 🚨 VERIFY — likely candidates: the user's current portfolio/holdings, session ID, and possibly UI state shared across dashboard sections (selected filters, active view).

**Cross-Q2 (grilling):** Given Next.js Server Components can fetch data directly, why do you need client-side global state at all for some of this?
**A:** "Server Components are great for initial data fetching, but a lot of AcornFolio's interactivity — adding/removing holdings, live-updating calculators as a user adjusts inputs — needs to happen client-side without a full page round-trip, which is exactly where client state like Zustand earns its place alongside server-fetched data."

## A5. Why Tailwind + shadcn/ui + Framer Motion?

**Q:** Why this specific frontend styling stack?
**A:** "Tailwind gives fast, consistent utility-based styling without hand-writing CSS files per component. shadcn/ui provides accessible, pre-built component primitives (not a full component library dependency, but copyable, customizable components) that saved significant time building a dashboard with a lot of surface area — tables, cards, modals, forms. Framer Motion handled the interactive animation/transition polish on top."

**Cross-Q1:** What's the actual difference between shadcn/ui and a normal component library like Material UI?
**A:** "shadcn/ui isn't installed as a black-box dependency — the actual component code gets copied into your project, so you own and can directly modify it, rather than being constrained to whatever customization API a packaged library exposes. That fit well with wanting a fully custom dashboard look rather than an obviously 'off-the-shelf' UI kit feel."

---
*Section A complete. Continuing next: Section B — Architecture & Data Flow.*

---

# SECTION B — ARCHITECTURE & DATA FLOW

*Your README's own flowchart: User → Next.js React UI → Zustand Store → Next.js API Routes → Prisma ORM → SQLite DB, with API Routes also branching to LanceDB Vector Search and the AI Chat layer, before responses flow back to the UI.*

## B1. Session-based, no-login flow

**Q:** Your README says users can try the product "without login," with holdings linked to a session ID. Walk me through exactly how that works.
**A:** 🚨 VERIFY exact mechanism, but conceptually: "When a user first interacts with the app, a session ID is generated and stored (likely in a cookie or local storage), and any holdings/portfolio data the user adds gets tagged with that session ID in the SQLite database via Prisma — so their portfolio is retrievable on subsequent visits within that same session, without requiring an account."

**Cross-Q1:** How is that session ID generated — could two users end up with the same one?
**A:** 🚨 **VERIFY** — if it's a proper UUID or cryptographically random token, collision risk is negligible; if it's something simpler, that's worth knowing honestly.

**Cross-Q2:** What happens if a user clears their cookies, or switches to a different browser?
**A:** "They'd lose access to that session's portfolio, since there's no account tying it to their identity across devices/browsers — the session ID itself is the only link. This is directly the 'no authentication' gap my own README's roadmap names — adding auth so portfolios persist across devices is explicitly the next step."

**Cross-Q3 (grilling):** Is session-based, no-login access actually a good design choice, or just the fastest path for a hackathon demo?
**A:** "Both, honestly — it's genuinely good UX for letting someone try the product frictionlessly without a signup wall, which matters for a demo/hackathon context specifically. But it's not a substitute for real accounts in a production version — I'd want to eventually let a session optionally 'upgrade' into a real account, preserving whatever portfolio data the user had already built up."

## B2. Prisma schema & data model

**Q:** What does your data model actually look like — what are the core Prisma models/tables?
**A:** 🚨 **VERIFY YOUR ACTUAL SCHEMA** — likely candidates based on the README's feature set: a `Fund` model (name, category, Direct/Regular variant, expense ratio, NAV, etc.), a `Holding` or `Portfolio` model (linking a session to specific funds and quantities/units), and possibly a `Session` model.

**Cross-Q1:** How do you represent the Direct vs. Regular plan relationship in your schema — are they two separate fund records, or one record with variant fields?
**A:** 🚨 **VERIFY — this is a genuinely important modeling decision, know your actual answer.** If they're linked records: "Each fund likely has a Direct and Regular variant as related records — probably sharing a common identifier so I can pull both variants of the same underlying fund to compare expense ratios directly."

**Cross-Q2:** Where does the actual fund data (names, expense ratios, NAVs) come from right now?
**A:** 🚨 **VERIFY, and this is a real, disclosed gap from your own README:** "Currently it's sample/seeded data, not a live production data feed — my README's own roadmap flags replacing this with production-grade data pipelines and live AMFI NAV sync as future work. Right now the numbers are realistic but not necessarily current market data."

**Cross-Q3 (grilling):** So if a user relies on AcornFolio's numbers to make a real investment decision today, could they be wrong?
**A:** "Yes, potentially — since the underlying fund data isn't live-synced yet, the specific expense ratios or NAVs shown could be stale or based on sample data rather than the actual current market figures. That's exactly why the README includes a disclaimer that it's an educational/decision-support tool, not certified financial advice, and why live NAV sync is on the roadmap rather than already solved."

## B3. The hybrid deterministic + AI architecture

**Q:** Your README describes a "hybrid architecture" — deterministic calculations plus AI explanation. Walk me through why you split it that way instead of just having the AI do everything.
**A:** "Financial numbers — savings from switching plans, tax owed, exit load, SIP future value, XIRR — need to be exactly correct, and LLMs are known to make arithmetic and logical errors, especially on multi-step calculations. So those are computed with actual code, deterministically, and the AI's role is purely to explain the already-correct result in plain language, using the user's portfolio and relevant fund context to make that explanation specific rather than generic."

**Cross-Q1:** How do you actually guarantee the AI doesn't just make up its own numbers instead of using your calculated ones?
**A:** 🚨 **VERIFY your actual prompt construction** — conceptually: "The calculated results are included directly in the context/prompt sent to the AI, and the prompting is structured so the AI is instructed to explain and reference those given numbers rather than compute its own. It's not a hard technical guarantee against hallucination — it's a prompting and context-design choice — which is an honest limitation, not a bulletproof one."

**Cross-Q2 (grilling):** So there's no actual technical mechanism stopping the AI from stating a wrong number if it decides to hallucinate one anyway?
**A:** "🚨 Correct, honestly — without output validation (like parsing the AI's response and checking any numbers it states against the actual calculated values), there's no hard guarantee. That would be a meaningful robustness improvement — validating or even programmatically inserting the calculated figures into a templated response rather than fully trusting free-form generation to relay them faithfully."

**Cross-Q3:** Why not have the AI do the math too, given modern LLMs are reasonably good at arithmetic?
**A:** "Even 'reasonably good' isn't good enough for financial figures where being wrong has real consequences — a compounding calculation over 20 years, or a tax computation, needs to be exactly right every single time, not right most of the time. Delegating that to deterministic code removes an entire class of risk that isn't worth taking just because an LLM often gets it right."

---
*Section B complete. Continuing next: Section C — RAG & AI Grounding Grilling (deepest section — the core technical claim).*

---

# SECTION C — 🔥🔥🔥 RAG & AI GROUNDING — CORE CHAIN

*This is AcornFolio's equivalent of CodeFusion's conflict-resolution chain and Trackify's "real-time" chain: the core technical claim most likely to get pulled apart layer by layer. This is also your strongest section if handled well — RAG is a genuinely sophisticated thing to have built as a fresher.*

**Q1:** Your README describes "RAG-style fund context" using LanceDB. Explain, precisely, what RAG actually is and how yours works.
**A:** "RAG — Retrieval-Augmented Generation — means grounding an AI's response in retrieved, relevant data rather than relying purely on what the model already knows from training. In AcornFolio, when a user asks the AI something, the query is used to search LanceDB's vector index for semantically similar fund records, and that retrieved fund data is attached to the prompt sent to the LLM, so the response is grounded in actual fund data rather than the model's general, possibly outdated or generic knowledge."

**Cross-Q2:** Walk me through the pipeline step by step — from a user typing a question to getting an answer.
**A:** 🚨 VERIFY exact implementation, but conceptually:
1. User submits a question in the AI chat interface.
2. The query text (and likely the user's portfolio context) is sent to the chat API route.
3. The query is converted into an embedding vector using the same embedding model used to index the fund data.
4. LanceDB performs a similarity search, returning the most semantically relevant fund records.
5. Those retrieved records, plus the user's portfolio context and conversation history, are assembled into a prompt.
6. That prompt is sent to the LLM (Ollama local or ZAI SDK fallback, per your README).
7. The LLM's response is returned to the user.

**Cross-Q3:** What's actually being compared during "similarity search" — what makes two things "similar" in vector space?
**A:** "Both the user's query and the stored fund records are converted into embedding vectors — numerical representations positioned in a high-dimensional space such that semantically similar text ends up close together. LanceDB then finds the fund vectors with the smallest distance (e.g., cosine similarity) to the query vector, which corresponds to the funds most semantically relevant to what was asked, not just funds sharing exact keywords."

**Cross-Q4:** What embedding model are you actually using to generate these vectors?
**A:** 🚨 **VERIFY — know this cold, do not guess it live.**

**Cross-Q5 (grilling — the hard one):** What happens if the retrieval step returns irrelevant or wrong fund data — does the AI just confidently explain garbage?
**A:** "🚨 Honestly, yes, that's a real risk with the current setup — if retrieval returns poor matches, the AI would still generate a fluent-sounding explanation grounded in the wrong context, which could be worse than no context at all, since it would sound authoritative. This is exactly what my README flags as unfinished: 'improve RAG evaluation so AI answers can be measured for correctness' — I don't currently have a systematic way to measure retrieval quality or catch this class of failure."

**Cross-Q6:** How would you actually build that evaluation — what would you measure?
**A:** "I'd want a test set of representative questions with known-correct expected fund matches, and measure retrieval precision/recall against that set — whether the system consistently retrieves the right funds for a given question. Separately, I'd want to evaluate the generated answers themselves — checking whether stated numbers match the deterministically-calculated values, and whether the explanation is factually consistent with the retrieved context, not just fluent."

**Cross-Q7:** Why did you use two different AI backends — Ollama locally and a ZAI SDK fallback? Walk me through that decision.
**A:** 🚨 **VERIFY your actual reasoning** — a reasonable, defensible answer if this was a practical choice: "Running a model locally via Ollama avoids API costs and keeps things working without external dependency during development — but local models can be less capable or slower depending on hardware. The ZAI SDK fallback likely exists for when local inference isn't available or isn't good enough, giving a more capable hosted model as a backup path."

**Cross-Q8 (grilling):** Doesn't switching between two different model backends risk inconsistent answer quality or behavior?
**A:** "🚨 Yes, potentially — a local model and a hosted model can have meaningfully different capabilities, so the same question could get a noticeably better or worse answer depending on which backend actually served the request. If I were hardening this for production, I'd want consistent behavior between fallback paths to be an explicit, tested requirement, not just 'both technically work.'"

**Cross-Q9:** How is the user's portfolio context actually incorporated — is their full portfolio always sent to the AI, or just relevant parts?
**A:** 🚨 VERIFY — likely their full current holdings are included as context alongside the retrieved fund data, so the AI can reference "your Fund X holding" specifically rather than speaking generically.

**Cross-Q10 (final grilling — the honest close):** Given everything we've just discussed, is calling this "RAG" an accurate technical claim, or are you overselling a chatbot with some extra context stuffed in?
**A:** "I'd defend it as genuinely RAG — the defining characteristic is retrieval via vector similarity search feeding relevant, external context into the generation step, which is exactly what's happening with LanceDB here, not just static context stuffing. What I'd be honest about is that it's an early-stage, unevaluated implementation of that pattern — the core mechanism is real and correctly built, but the maturity and correctness-guarantees around it (evaluation, hallucination-guarding) are not yet where a production RAG system would need to be."

### 🚨 Interview trap
Don't claim you've measured or validated retrieval/answer accuracy if you haven't — your own README explicitly names this as unfinished ("Improve RAG evaluation so AI answers can be measured for correctness"). This is one of the most sophisticated things on your resume when explained honestly; don't undercut it by overclaiming rigor you don't actually have yet.

---
*Section C complete — rehearse this the most, it's your strongest technical differentiator. Continuing next: Section D — Financial Calculators Grilling.*

---

# SECTION D — FINANCIAL CALCULATORS GRILLING

*Your README lists calculators for SIP, SWP, STP, XIRR, tax, exit load, savings, rebalancing, and stress testing — all explicitly deterministic/code-based, not AI-generated. Your own roadmap also explicitly flags weak test coverage here, so expect this to be probed.*

## D1. Core calculation logic

**Q:** Explain the core Direct-vs-Regular savings calculation — what's actually being computed?
**A:** "The expense ratio difference between a fund's Direct and Regular plan is applied against the invested amount over time, compounding annually, to show the cumulative rupee cost of staying in the higher-cost Regular plan versus switching to Direct — essentially projecting the future value difference caused purely by that expense ratio gap."

**Cross-Q1:** Walk me through the actual formula you're using for that future value calculation.
**A:** 🚨 **VERIFY YOUR ACTUAL FORMULA — know this cold, it's a very natural "prove you understand the math" follow-up.** Likely a standard compound growth formula applied twice (once at each plan's effective return, i.e., fund return minus each variant's expense ratio) and the difference between the two outcomes.

**Cross-Q2:** What is XIRR, and why is it needed instead of a simpler return calculation?
**A:** "XIRR — Extended Internal Rate of Return — calculates the annualized return for a series of cash flows that happen at irregular dates and amounts, like SIP investments made monthly rather than a single lump sum. A simple return calculation assumes one investment date; XIRR properly accounts for the actual timing of each individual contribution, which matters a lot for accurately representing SIP performance."

**Cross-Q3 (grilling):** How did you implement XIRR — did you write the root-finding logic yourself, or use a library?
**A:** 🚨 **VERIFY — know this specifically.** XIRR typically requires an iterative numerical method (like Newton-Raphson) to solve for the rate, since there's no closed-form formula — be ready to say either "I implemented it myself using [method]" or "I used [library name]" honestly, not vaguely.

## D2. Tax & exit load edge cases

**Q:** Walk me through your tax calculation — how do you determine capital gains tax on a mutual fund switch?
**A:** 🚨 **VERIFY YOUR ACTUAL LOGIC.** Conceptually: needs to account for holding period (determining short-term vs. long-term capital gains treatment under Indian tax rules), the applicable tax rate for each category, and the actual gain amount (redemption value minus cost basis).

**Cross-Q1:** What happens at the exact boundary — say, a holding period right at the line between short-term and long-term classification?
**A:** 🚨 VERIFY — this is a classic off-by-one-style edge case. Be ready to explain how your logic actually handles the boundary date, or honestly flag it as untested if you're not certain: "🚨 I'd want to explicitly test that exact boundary case — it's precisely the kind of edge case my README flags as needing stronger test coverage."

**Cross-Q2:** How do you calculate exit load, and does it interact with the tax calculation, or are they independent?
**A:** 🚨 VERIFY — likely independent calculations (exit load is a percentage fee charged by the fund on early redemption, separate from tax owed to the government), both subtracted from the switching benefit to determine whether switching is actually worthwhile after all costs.

**Cross-Q3 (grilling):** If exit load and tax together outweigh the expense-ratio savings from switching, does the app actually tell the user "don't switch," or does it just show numbers and leave interpretation to them?
**A:** 🚨 **VERIFY — genuinely important product-logic question.** "🚨 Need to confirm — the calculators should be computing both sides (savings vs. switching cost) so the app can surface a net recommendation, but I'd want to verify the actual UI presents that comparison clearly rather than just showing raw numbers the user has to manually compare themselves."

## D3. Testing gap (own it directly)

**Q:** Your own README says you need "stronger test coverage for tax, exit load, and compounding calculations." Why is that specifically flagged as a priority, and what does its current absence actually risk?
**A:** "Because these are exactly the calculations where a subtle bug — an off-by-one in a date comparison, a wrong compounding period, a misapplied rate — wouldn't necessarily be visually obvious in the UI, but would mean the app is telling a user something factually wrong about their money. Financial calculations are a category where 'looks right on manual testing' isn't good enough; they need dedicated unit tests with known-correct expected outputs for a range of realistic and edge-case inputs."

**Cross-Q1:** If you had to write the first test right now, which calculation would you test first, and why?
**A:** "The Direct-vs-Regular compounding savings calculation, since it's the core value proposition of the entire product — if that number is wrong, everything downstream (the recommendation to switch or not) is built on a false premise. I'd test it against manually-verified expected values for a range of investment amounts, time horizons, and expense-ratio gaps."

**Cross-Q2 (grilling):** Isn't it a bit concerning to have shipped calculators handling real financial decisions without that testing already in place?
**A:** "For a hackathon/demo-stage project explicitly disclaimed as an educational, non-certified tool — which the README states outright — it's a reasonable, honestly-disclosed limitation rather than something hidden. It would be genuinely concerning if this were presented as production-ready financial advice software without that disclaimer and without closing this gap first, which is exactly why I wouldn't present it that way."

---
*Section D complete. Continuing next: Section E — Alternatives & Comparisons Grilling.*

---

# SECTION E — ALTERNATIVES & COMPARISONS GRILLING

## E1. Why not just build a simple calculator app, no AI at all?

**Q:** Given the AI layer has real, disclosed limitations (unevaluated retrieval, hallucination risk), why include it at all — why not ship a purely deterministic calculator tool?
**A:** "The calculators alone answer 'what are the numbers,' but a lot of the real value for a non-expert investor is in 'what do these numbers mean for me' — explaining tradeoffs in plain language, answering follow-up questions, personalizing to their actual portfolio. That's a fundamentally different, harder-to-hardcode problem that an AI layer is well-suited to, even with its current limitations, as long as those limitations are understood and disclosed rather than hidden."

**Cross-Q1 (grilling):** Isn't there real risk in an AI explaining financial decisions to a non-expert user, given it could hallucinate?
**A:** "Yes, genuinely — which is exactly why the deterministic-calculation-plus-AI-explanation split matters: the numbers themselves are guaranteed correct by code, and the AI's job is explanation, not computation. It reduces but doesn't eliminate risk — an AI could still mischaracterize a correct number in its explanation, which is why the disclaimer exists and why RAG evaluation is future work, not a solved problem."

## E2. Why LanceDB over a simpler keyword search?

**Q:** Given the fund dataset is likely small (sample/seeded data), was vector search actually necessary, or would simple keyword/SQL filtering have worked just as well?
**A:** "At the current small, seeded-data scale, honestly, keyword search on fund names/categories might work reasonably well too. The value of semantic vector search shows up more as the dataset and the range of natural-language questions grow — someone asking 'low-risk funds for retirement' benefits from semantic matching against fund descriptions/objectives in a way exact keyword matching wouldn't reliably catch. I chose the more scalable, semantically richer approach even at small scale, partly as a genuine technical exercise in building RAG correctly."

**Cross-Q1 (grilling):** So is using LanceDB here slightly over-engineered for the current data size?
**A:** "A fair characterization at the current seeded-data scale — but it's the right foundation for where the README's own roadmap says this is headed (production-grade data pipelines, more funds, more varied questions), so I'd frame it as building for where the product needs to go, not purely for where it is today."

## E3. Why Next.js API routes instead of a separate backend service?

**Q:** Given the AI/RAG logic is fairly involved, would a separate dedicated backend service (like your CodeFusion's Node/Express setup) have made more sense than Next.js API routes?
**A:** "For this project's scope, Next.js API routes kept everything in one deployable unit, which mattered for hackathon-timeline simplicity. A separate backend would make more sense if the AI/RAG workload needed independent scaling or a different runtime/infrastructure than the frontend — which isn't a real constraint yet at this project's current scale."

---

# SECTION F — RELIABILITY, SECURITY & SCALING GRILLING

## F1. Data currency & accuracy

**Q:** You've said the fund data is sample/seeded rather than live. How would you actually implement live AMFI NAV sync, which your README lists as a roadmap item?
**A:** "AMFI (Association of Mutual Funds in India) publishes NAV data that could be pulled via a scheduled background job — fetching updated NAVs on some regular interval (e.g., daily, since NAVs update once per day for most funds) and updating the corresponding records in the database, rather than the app querying a live source on every single user request."

**Cross-Q1:** Why not fetch live on every request instead of a background sync job?
**A:** "NAVs for mutual funds typically only update once per day anyway, so querying an external source on every user request would add latency and unnecessary load for data that isn't actually changing that frequently — a scheduled sync job matches the real update cadence of the underlying data."

## F2. Security & data sensitivity

**Q:** Is there any sensitive user financial data being handled here, and if so, how is it protected?
**A:** 🚨 VERIFY the actual sensitivity of what's stored — likely just portfolio holdings (fund names/quantities) tied to an anonymous session ID, not actual bank/brokerage credentials or PII, given the no-login design. "Since there's no login/account system, there's no PII or credentials stored — just portfolio holdings tied to an anonymous session identifier. That's actually a reasonable privacy-by-design choice for a demo-stage tool, though it would need a real security review (encryption at rest, access controls) the moment authentication and real user data are added, per the roadmap."

**Cross-Q1 (grilling):** What about the AI chat — is there any risk of sensitive info being sent to an external AI provider (the ZAI SDK fallback)?
**A:** "🚨 Worth being honest about — if the ZAI fallback is a hosted third-party API, whatever portfolio context is included in the prompt would be sent to that external service. Since there's no highly sensitive PII in the current session-based model, the practical risk is limited, but it's the kind of data-flow detail I'd want to explicitly document and get comfortable with before treating this as production-ready."

## F3. Scaling the AI layer

**Q:** If AcornFolio had many concurrent users, what would break first in the AI/RAG pipeline?
**A:** "Likely the local Ollama inference path first — a locally-run model has real hardware/throughput limits and isn't designed for many concurrent requests the way a properly scaled hosted inference service is. The ZAI SDK fallback would handle concurrency better if it's a managed hosted service, but then cost and rate limits become the real constraint instead."

---

# SECTION G — TESTING, DEPLOYMENT & REFLECTION

## G1. Testing

**Q:** How did you test AcornFolio overall — automated or manual?
**A:** 🚨 VERIFY, likely honest for a hackathon-scoped project: "Mostly manual — testing calculator outputs against manually-computed expected values, and testing the AI chat with representative questions during development. My own README explicitly flags stronger automated test coverage for the financial calculations as unfinished work, which I'd prioritize first if I continued building this."

## G2. Reflection

**Q:** If you were to take AcornFolio from hackathon-stage to something you'd actually ship, what are the top 3 things you'd do, in order?
**A:** "First, replace sample data with a real data pipeline and live NAV sync — the product's core value depends on trustworthy numbers. Second, add test coverage for the financial calculations, since correctness there is non-negotiable for anything beyond a demo. Third, build the RAG evaluation process my README flags as missing, so I can actually measure and improve AI answer quality rather than relying on manual spot-checks."

**Cross-Q1 (grilling — the honest close):** Given everything we've discussed, is AcornFolio's core pitch — 'data-backed AI guidance for mutual fund decisions' — something you'd stand behind for a real user's real money today?
**A:** "Not yet, and the README's own disclaimer says exactly that — it's an educational, decision-support tool, not certified financial advice. The deterministic math is sound where it's implemented and tested manually, but between seeded (not live) data, unevaluated RAG retrieval, and thin test coverage on the financial logic, I wouldn't call it production-ready for real financial decisions today. What I would stand behind is the architecture and the problem framing — the hybrid deterministic-plus-AI approach is the right shape for this problem, and the path to closing the remaining gaps is clear and already documented."

---

# 🧠 MASTER MEMORY SHEET — ACORNFOLIO

1. **Hybrid architecture is the core pitch:** deterministic code for financial math (never trust the LLM with numbers) + RAG-grounded AI purely for explanation. Say this distinction immediately and confidently — it's genuinely sophisticated for a fresher project.
2. **RAG = query embedded → LanceDB similarity search → retrieved fund context → assembled into prompt → LLM (Ollama local / ZAI SDK fallback) → response.** Know this pipeline cold.
3. **Own the gaps — they're already documented by you, not hidden:** no auth (session-only), seeded/sample fund data (not live), no live NAV sync, thin test coverage on tax/exit-load/compounding math, RAG retrieval unevaluated.
4. **🚨 Non-negotiable — personally verify before any interview where this comes up:**
   - Exact embedding model used for LanceDB vectors
   - Exact Prisma schema (Fund/Holding/Session models, how Direct/Regular variants are linked)
   - How session IDs are generated
   - Your actual formula implementations for XIRR, tax boundary handling, exit load
   - Whether the app currently produces a clear "switch or don't switch" net recommendation, or just raw numbers
5. **The winning move on every gap question:** name the gap plainly (your own README already does this for you) → name the specific correct fix → frame it as a documented roadmap, not something you're hiding. This project's honesty about its own limitations is a genuine asset — use it.
