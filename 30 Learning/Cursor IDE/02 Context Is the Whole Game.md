---
note_type: Learning
title: Chapter 2 — Context Is the Whole Game
topic: Cursor IDE
book: Pair Programming with a Ghost
chapter: 2
part: "I: Recalibrating"
cursor_version: ">= 3.23"
audience: Senior Full Stack Engineer
status: draft
tags:
  - cursor
  - ai-coding
  - learning-book
  - context
up: "[[00 Outline - Pair Programming with a Ghost]]"
prev: "[[01 You Already Know How to Delegate]]"
next: "[[03 Tab Is Not Autocomplete]]"
created: 2026-10-04
---

# Chapter 2 — Context Is the Whole Game

> [!quote] Where we're going
> Last chapter we established that the agent is a stateless function that sees exactly what's in its context window and nothing else. This chapter is about controlling what's in that window. You'll learn the layers of context Cursor assembles on every turn, how codebase indexing decides what's findable, what each @-mention actually injects, why retrieval is probabilistic and pointing is not, and what happens when the window fills up. We're going to break something on purpose to make all of this visible.

## The service that wasn't there

Open Ledgerline. Start a fresh conversation in Ask mode. Attach nothing. Type:

> Where is sales tax calculated for an invoice? Walk me through the code path.

Watch the trace. The agent searches for "tax," "invoice," maybe "calculate." It finds `api/src/invoices/tax.ts` in the Node API, reads it, finds a `computeTax()` function that takes a line item array and a jurisdiction, and gives you a tidy, confident, well-structured walkthrough. It might even note that rates come from a lookup table and suggest where you'd add a new jurisdiction.

It's a good answer. It's also wrong.

In Ledgerline, `computeTax()` in the Node API is a *preview* calculation, used to show an estimate in the UI while the user is editing. The authoritative tax computation, the one that determines what actually gets billed, lives in `services/billing-legacy/src/Tax/Calculator.php`. The Node API calls the PHP service over HTTP at finalization time and overwrites its own estimate with whatever comes back. Everyone on the team knows this. It's the kind of thing that comes up in your second week when you ask why the numbers on the invoice don't quite match the preview.

The agent didn't mention the PHP service. Not once. Not as a caveat, not as a "you might also check." As far as the agent is concerned, that service does not exist.

Why?

Go look at the root of the repo. There's a `.cursorignore` file. Someone added it two years ago, and it contains `services/billing-legacy/` because that directory had a vendored `vendor/` folder with eleven thousand files that was choking the indexer. Reasonable fix at the time. Nobody's thought about it since. And it means the agent's primary discovery mechanism, semantic search over the codebase index, has never seen a single line of that service.

I set this up deliberately, but I didn't invent it. I've seen this exact pattern in three real codebases. The lesson isn't "check your ignore files," though you should. The lesson is that **the agent's confidence is completely uncorrelated with the completeness of its context.** It gave you a crisp answer because crisp answers are what it does. The only way you'd know something was missing is if you already knew what should have been there.

That's the whole chapter in one example. Now let's take it apart.

## The layers

On every single turn, Cursor assembles a context window from several layers and hands the whole thing to the model. You control some of these directly, some indirectly, and some not at all. Knowing which is which is most of the skill.

**The system prompt** is Cursor's own instructions to the model: how to behave as a coding agent, how to use tools, how to format edits. You don't see it and you don't edit it. It's the largest fixed cost in the window and it's the reason even an empty conversation isn't free.

**Rules** are your instructions, layered on top. Project rules from `.cursor/rules`, user rules from your settings, and anything in an `AGENTS.md` at the repo root. These get injected on every turn that they apply to, which is why they're the right place for anything that must persist. Chapter 9 is entirely about them. For now, know they're in the window.

**The conversation** is every message so far, yours and the agent's, plus every tool call and every tool result. This layer grows. It is the thing that fills the window.

**Explicit attachments** are what you @-mention or drag in. A file, a folder, a docs page, a git diff, a past chat. These are the most direct lever you have, and we'll go through them one by one shortly.

**Retrieved context** is what the agent fetches for itself using tools: semantic search over the index, grep, directory listings, file reads. This layer is the one that was broken in the tax example. It's also the layer people over-rely on, because it feels like the agent "just knows" the codebase. It doesn't. It's running searches, and searches can miss.

**Ambient context** is what Cursor surfaces from your editor state without you asking: the file you have open, your current selection, recent linter errors, sometimes recent terminal output. This layer is subtle and it bites people. If you have `tax.ts` open while asking about tax, the agent is more likely to anchor on it. If you have an unrelated file open, that's noise in the window.

Six layers. You own rules and attachments outright. You influence retrieval through indexing and how you phrase things. You influence ambient context by what you leave open. You don't touch the system prompt. The conversation layer you control by one blunt instrument: starting over.

## Indexing, or: what's findable

When you open a project, Cursor builds an index of it. In broad strokes, it walks the files that aren't ignored, splits them into chunks, computes an embedding for each chunk, and stores those vectors so that later a natural-language query like "where is tax calculated" can be matched against chunks that are semantically similar. When the agent calls its codebase search tool, this is what it's searching.

A few things follow from that description, and each one is a way the tax example could have gone wrong.

The index respects `.gitignore` and `.cursorignore`. Anything matched is invisible to semantic search. `.cursorignore` goes further than ignoring for the index; it's intended to keep those files away from the AI entirely, so even a direct read can be blocked. There's a softer sibling, `.cursorindexingignore`, which excludes files from the index but still lets the agent open them if it's pointed at them explicitly. That's the file the Ledgerline team should have used. The vendored dependencies would've stayed out of the index while the actual service code stayed reachable.

The index is per-workspace. If the PHP service were a separate repo you hadn't opened, it wouldn't be indexed no matter what your ignore files said. Multi-root workspaces are indexed together, which is how you'd handle that.

The index can be stale or incomplete. A fresh clone needs time to index. A big repo may hit size limits. You can see status under Cursor's indexing settings, and you can force a rebuild. If the agent seems oblivious to files you know exist, check here before anything else.

Semantic search is probabilistic. This one deserves its own section.

> [!tip] Try This — Audit your own blind spots
> In your project, open the indexing settings and look at what's actually indexed. Then open `.gitignore`, `.cursorignore`, and `.cursorindexingignore` if they exist. For each pattern, ask: is this excluded because it's *noise* (build output, dependencies, generated files) or because it was *inconvenient* at some point? Anything in the second category that contains real logic is a blind spot. Move it to `.cursorindexingignore` if you need it out of the index but reachable, or remove the pattern entirely and let it reindex. Then re-run the tax question, or your project's equivalent. Note what changes.

## Retrieval is a guess. Pointing is a fact.

Here is the thing about semantic search that senior engineers understand instantly once it's said out loud and almost never think about otherwise: it finds things that *look like* your question. It does not find things that *are the answer* to your question.

If you ask "where is tax calculated" and the authoritative code calls it `levy` because a previous CTO was British, semantic search might rank it low. If you ask about "the invoice finalization flow" and the code calls it `commit`, same problem. If the relevant logic is spread across six small files that individually don't look like much, the search might surface the one big file that *mentions* the concept in a comment instead. Embeddings are good. They're not magic, and they're not grep.

The agent knows this, in a sense; it'll often combine semantic search with grep and directory exploration. But every additional search is another tool call, another round trip, another chunk of tokens, and another opportunity to wander into the wrong directory.

So here's the operating principle: **when you know where something is, point at it. Retrieval is for when you don't.**

This feels inefficient to people at first. You're the senior engineer; isn't the whole point that the agent figures things out? No. The point is that the agent *does things*. Figuring out where things are is often faster for you, since you have two years of history in your head and it has an embedding index. A single `@services/billing-legacy/src/Tax/Calculator.php` in your message saves four tool calls and guarantees the right file is in the window. That's not hand-holding. That's writing a good function call.

> [!question] Pause and Predict — Three setups, one question
> You're going to ask the same question three times in three fresh Ask-mode conversations. Before each one, predict: how many tool calls, how good is the answer, does it mention the PHP service? Write the predictions down.
>
> **Setup 1 — Cold.** Nothing attached. "Walk me through how an invoice's final total is computed, from the user clicking Finalize to the number stored in the database."
>
> **Setup 2 — Pointed.** Same question, but attach `@api/src/invoices/finalize.ts` and `@services/billing-legacy/src/Tax/Calculator.php`. (If you've fixed the ignore file. If not, this setup fails interestingly; note how.)
>
> **Setup 3 — Mapped.** Same question, nothing attached, but first add a short `docs/ARCHITECTURE.md` to the repo containing three paragraphs: what each service owns, how they talk to each other, and the sentence "the PHP billing service is the source of truth for tax and totals; the Node API's calculations are previews only." Let it index. Then ask.
>
> Run all three. Compare tool-call counts and answer quality. Then ask yourself which setup you'd want to be the *default* for every engineer on the team, not just you.

Most people find that Setup 2 gives the best answer with the fewest calls, which is the pointing-beats-retrieval lesson. But Setup 3 is the interesting one. It's slower than pointing and it still beats cold, and it does so for *everyone*, on *every* question that touches the architecture, without anyone having to remember to attach anything. That's the first glimpse of what Chapter 9 is about: moving knowledge from your head into files the agent reads by default. We'll do it properly with rules. The architecture doc is the training-wheels version, and honestly, you should have one anyway.

## The @-menu, and what each thing costs

Type `@` in the input and you get a menu. Every item is a different kind of injection into the window, and they're not interchangeable. Let me walk them the way I think about them, which is by what they put in the context and what they cost.

**Files and folders** are the workhorse. A file attachment puts that file's contents in the window. A folder attachment puts the folder *structure* in and lets the agent decide what to read, which is cheaper than you'd fear but less deterministic than you'd want. If you know the three files that matter, attach the three files. If you know the subsystem but not the files, attach the folder and accept some exploration.

**Code** lets you attach a specific symbol or selection rather than a whole file. Use it when the file is large and the relevant part is small. Remember from last chapter that tokens are the bill; a 40-line function costs a fraction of its 1,200-line home.

**Docs** indexes external documentation from a URL so the agent can search it the way it searches your code. This is how you stop the agent from confidently using a library API that was deprecated two majors ago. If Ledgerline is on a specific version of a payment SDK, the SDK's docs for *that version* belong here. Chapter 11 picks this up when we talk about MCP, which is the heavier-duty version of the same idea.

**Git** attaches commits, diffs, or the current branch's changes. The most underused item in the menu. "Here's the diff from the last release; what could have caused the rounding regression?" is a context setup that would take you ten minutes to assemble by hand and takes one @-mention.

**Past chats** pulls in a previous conversation. Use it sparingly. It's a way to carry forward a decision or a summary, but it's also an easy way to drag a thousand tokens of stale discussion into a fresh window. We'll talk about a better handoff pattern in a minute.

**Rules** lets you explicitly invoke a rule that isn't set to apply automatically. Chapter 9.

**Linter errors**, **terminals**, and **recent changes** are the ambient layer made explicit. Attach the terminal when the agent needs to see the actual test output rather than your paraphrase of it. Attach linter errors when the task is "make this pass." Attach recent changes when the question is about what you just did.

**Web** lets the agent search the internet. Occasionally essential, often a distraction. If the answer is in your codebase or your docs, the web is noise. If you're integrating with a service whose docs you haven't indexed, it's the right tool.

The pattern across all of these: **each @-mention is you making a context decision that the agent would otherwise have to make by guessing.** Every one you make well saves tool calls and improves the answer. Every one you make badly, like attaching a whole `src/` folder "to be safe," pays for tokens that actively dilute the signal.

> [!note] Mentor's Margin — More is not better
> There's a reflex, especially among careful engineers, to attach generously. "I'll give it everything that might be relevant, it can sort it out." I did this for months. It's wrong, and it's wrong for a reason that doesn't exist with humans. When you hand a colleague a stack of files, they skim, triage, and focus. The model doesn't skim. Every token in the window is attended to with roughly equal weight, so the three files that matter are competing for attention with the twenty that don't. The answer gets vaguer, not sharper. The analogy I'd offer is a SQL query: adding more tables to the join doesn't make the result more correct, it makes it slower and more likely to be wrong. Attach what the task needs. If you're unsure, attach less and watch what the agent reaches for; it'll tell you what you missed.

## When the window fills

Everything we've put in the window so far is additive. The conversation grows, every tool result lands in it, every file read stays. Eventually you hit the ceiling.

Cursor shows a context indicator in the input area, a small gauge of how much of the window you've used. Watch it. When it gets high, Cursor compacts: it summarizes earlier portions of the conversation to free space. This is necessary and it's lossy. The constraint you mentioned forty minutes ago becomes a line in a summary, and then maybe not even that. The file the agent read early on is no longer in the window verbatim, so it may re-read it, which costs more tokens, which triggers more compaction.

I said last chapter that the most important operational habit is starting fresh when you change tasks. Let me be more specific about *why*, now that you know the layers. A long conversation has a bloated conversation layer, almost certainly a stale ambient layer, and a retrieval history that was relevant to the old task and is noise for the new one. Starting a new conversation resets all three while keeping rules and the index. You lose nothing you should have been relying on, and you drop everything that was quietly degrading the answer.

But sometimes you genuinely need continuity. You've made six decisions over an hour and you don't want to re-explain them. Here's the pattern.

> [!tip] Try This — The handoff note
> At the end of a long session, before you start a new conversation, ask the agent: "Write a concise handoff note for a fresh conversation. Include the goal, the decisions we made and why, what's done, what's next, and any constraints that must be preserved. Under 300 words. Don't include code." Read it. Correct anything it got wrong. Then start a new conversation and paste that note as your first message, or save it to a scratch file and @-mention it.
>
> You've just replaced a 60,000-token conversation with a 400-token summary that *you* verified. Compare the quality of the next few turns against what you were getting at the end of the old session. Most people find the fresh conversation is sharper, not vaguer.

This is a trick you'll use for the rest of the book, and it has a bigger lesson folded into it: the agent is a capable summarizer of its own state, and you are a capable auditor of that summary. Compaction does this automatically and you don't get to audit it. The handoff note does it deliberately and you do. Prefer deliberate.

## Context hygiene

A few habits that follow from everything above. I'm not going to bullet them, because they're not a checklist; they're a way of working.

Close files you're not working on before you start a conversation. The open-file effect is real: the agent anchors on what's in front of you. If you're asking about the PHP service with `tax.ts` open, you've put your thumb on the scale toward the wrong answer.

Paraphrase less, attach more. When something fails, your instinct is to type "the tests are failing with a type error in the invoice module." That's your interpretation, and it's lossy. Attach the terminal. The agent reads stack traces better than it reads your summary of one.

Watch what the agent reaches for. When it searches for something you could have pointed at, note it; next time, point. When it reads a file you'd never have thought of, note that too; it might be telling you something about your codebase's structure that you'd stopped seeing.

Treat large tool outputs as a cost. If the agent is about to run a command that'll dump thousands of lines, consider whether you can pipe it through `head`, `grep`, or `tail` first. You wouldn't `SELECT *` on a billion-row table to check one value. Same instinct.

And when the agent is confidently wrong, before you blame the model, ask: *what wasn't in the window?* Nine times out of ten, that's the answer. The tenth time, it's Chapter 8.

## Back to the service that wasn't there

Fix the ignore file. Move `services/billing-legacy/vendor/` to `.cursorindexingignore` and delete the broader pattern from `.cursorignore`. Let it reindex. Ask the tax question again, cold.

The agent will now find both calculators. It may or may not correctly identify which one is authoritative; that depends on whether the code makes it obvious, and in Ledgerline it doesn't, because the Node side just calls the PHP side without a comment explaining why. That's the gap your `ARCHITECTURE.md` fills, and it's the gap a rule fills better.

Notice what just happened across this chapter. We started with an agent that was confidently wrong. We didn't change the model. We didn't write a better prompt. We changed what was findable, we pointed where pointing was faster, we wrote down one sentence of tribal knowledge, and we got a correct answer. That's environment design. That's the job.

## Checkpoint

1. Name the six context layers and, for each, say whether you control it directly, indirectly, or not at all.
2. What's the difference between `.cursorignore` and `.cursorindexingignore`, and which one should the Ledgerline team have used for the vendored dependencies?
3. Give a concrete example from your own codebase where semantic search would plausibly miss the right file because of vocabulary mismatch.
4. You're forty turns into a conversation and the agent starts re-reading files it read earlier. What's happening, and what are your two options?

**The hard one.** The architecture doc in Setup 3 improved answers for everyone. But it's a file in the repo that the agent *finds via search*, which means it's subject to the same retrieval uncertainty as everything else: on some questions it won't be surfaced. Rules, which we'll meet in Chapter 9, are injected on every turn regardless. Given that, what kind of knowledge belongs in an architecture doc versus a rule? Think about size, frequency of relevance, and what happens when either one is wrong. Write down your answer; Chapter 9 will give you mine.

---

*Next: [[03 Tab Is Not Autocomplete]]. A short chapter about the feature you've been using wrong since the day you installed Cursor.*
