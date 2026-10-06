---
note_type: inbox
created: "2026-10-04 18:24"
---
You are a master teacher and technical author in the style of Feynman's
lectures, "Head First" books, and Bill Bryson's curiosity. You write for
working engineers who hate fluff.

TASK: Design a hands-on learning book: "AI Integration for Full Stack Web
Engineers": how to add LLM-powered features to real web applications.

LEARNER: A senior full-stack web developer (TypeScript/React on the front end;
Node, Python or PHP/Laravel on the back end; AWS for hosting). Strong on
HTTP, databases, auth, and deployment. New to LLMs, embeddings, and agents.
Learns in evening sessions of 1-2 hours. Wants to ship, not theorize.

OUTCOME: After finishing, the reader can:
1. Call an LLM API with structured, validated output and handle failures.
2. Build a streaming chat UI with cancellation and good loading states.
3. Build a retrieval (RAG) pipeline with embeddings, a vector store, and citations.
4. Give a model tools safely, and build a bounded agent loop.
5. Write evals, defend against prompt injection, and control cost and latency.

First, ask me up to 5 clarifying questions if anything critical is missing.
Then produce:

1. BOOK BIBLE
   - Voice: warm, dry-witted, direct, never condescending. Treat the reader
     as a peer who is new to this one topic.
   - Teaching method: concrete-before-abstract, one core idea per section,
     spaced callbacks to earlier chapters, every concept shown in running code.
   - Recurring elements: opening hook (a real-world failure or surprising
     behavior), "Why this matters", worked example with a deliberate bug and
     its fix, "Common trap", "Try it" exercise, 3-question retrieval quiz,
     5-bullet recap, and a "Production notes" box (cost, latency, security).
   - Code rules: one running project across the book; TypeScript primary,
     Python alternates where useful; every snippet complete and runnable;
     pin SDK versions; mark anything version-sensitive with [VERIFY].
   - Running metaphor: propose 3 options. My suggestion is a restaurant
     kitchen: the model is a brilliant, fast, occasionally overconfident
     line cook, and the engineer is the head chef and expediter.
   - Glossary rules: define on first use, stay consistent afterward.
   - Banned phrases: "delve", "in today's world", "it's important to note",
     "game-changer", "unlock the power of".

2. CURRICULUM: 12 chapters. For each: an intriguing title, the big question
   it answers, prerequisites, key concepts, the code the reader will write,
   and the "aha moment".

3. KNOWN MISCONCEPTIONS: the 10 most common errors web developers make
   when adding AI to their apps.

4. RUNNING PROJECT: propose a project that grows chapter by chapter.