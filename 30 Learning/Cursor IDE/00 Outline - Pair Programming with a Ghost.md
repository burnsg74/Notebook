---
note_type: Learning
title: Pair Programming with a Ghost — Outline
topic: Cursor IDE
cursor_version: ">= 3.23"
audience: Senior Full Stack Engineer
status: draft
tags:
  - cursor
  - ai-coding
  - learning-book
sources:
  - https://cursor.com/docs
  - https://cursor.com/learn
created: 2026-10-04
---
# Pair Programming with a Ghost
### A senior engineer's field guide to Cursor


## How this book is built

You've been shipping software for a long time. You don't need me to explain what a pull request is or why you should write tests. What you need is someone who has already made the embarrassing mistakes with an AI coding agent, figured out *why* they happened, and can hand you the mental model that makes them stop happening. That's the voice of this book: a colleague at the next desk, not a documentation portal.

Every chapter follows the same rhythm. We open with a real situation, the kind you'd actually face on a Tuesday. We work through it together in a running example codebase, **Ledgerline**, a fictional invoicing SaaS with a React/TypeScript front end, a Node/Postgres API, a crusty legacy PHP billing service nobody wants to touch, and a Terraform-managed AWS deployment. It's messy on purpose. Clean demo repos teach you nothing.

Sprinkled through each chapter you'll find four kinds of interactive beats:

| Beat | What it is |
|---|---|
| **Try This** | A hands-on exercise in your own Cursor install, five to fifteen minutes. |
| **Pause and Predict** | Guess what the agent will do *before* you hit Enter. The gap between prediction and reality is where the learning lives. |
| **Mentor's Margin** | Where I step out of the lesson and tell you what I actually think, including where I disagree with conventional wisdom. |
| **Checkpoint** | A short self-test closing each chapter, plus one question deliberately harder than the chapter. |

The structure loosely mirrors the two arcs in Cursor's own Learn course — AI Foundations, then Coding Agents — but we go considerably deeper and never pretend you're a beginner.

---

## Part I: Recalibrating

### Chapter 1 — You Already Know How to Delegate. You've Just Never Delegated to This.
We start with the uncomfortable truth: senior engineers are often *worse* at AI tooling than juniors, because our instincts about what's hard and what's easy are calibrated for humans. This chapter reframes Cursor as a coding agent rather than a smarter autocomplete, walks through the five foundational ideas from Cursor Learn (how models work, tokens and pricing, context, tool calling, and what makes something an "agent"), and translates each into terms you already own. Tokens become a budget line. Context becomes working memory with a hard ceiling. Tool calling becomes the agent's hands.

### Chapter 2 — Context Is the Whole Game
If you only read one chapter, read this one. We open Ledgerline for the first time and discover that the agent has no idea the PHP billing service exists unless we make it exist in context. You'll learn exactly what the agent can and can't see, how codebase indexing works, what @-mentions actually inject, and the single most important operational habit in this book: each mode keeps its own context, so you start a fresh chat when you change tasks. A Pause and Predict exercise asks the same question with three different context setups and watches answer quality swing wildly.

---

## Part II: The Daily Loop

### Chapter 3 — Tab Is Not Autocomplete
A short chapter with outsized payoff. Tab predicts multi-line edits and your next cursor position, not just the next token, and that changes how you type. The rhythm of accepting partials, when Tab is actively harmful (when you're thinking, not typing), and how to tune it so it stops fighting you.

### Chapter 4 — Meet the Agent
We open the Agent panel and give it a real task: add a "mark invoice as disputed" endpoint to the Node API. You'll watch the agent search, read, edit, and run commands, and we'll dissect every decision point: when it asks to run a shell command, what to approve, how to read the diff it produces, and how to revert when it goes sideways. The Agent is three pieces — a system prompt plus your rules, a tool set (file search, editing, shell, web search, browser, clarifying questions), and whichever model you've chosen. We make all three visible.

### Chapter 5 — Four Modes, One Conversation
Agent builds. Plan designs before building. Ask answers without touching anything. Debug gathers runtime evidence before fixing. You'll learn to flip between them with Shift+Tab and, more importantly, to feel in your gut which one a situation calls for. Try This: five realistic tickets, assign a mode to each before reading my answers.

### Chapter 6 — Plan Mode as the Design Review You Never Had Time For
A full chapter on one mode, because it separates people who get real leverage from people who get frustrated. We take a genuinely gnarly Ledgerline feature — multi-currency invoicing touching the React front end, the Node API, the PHP service, and a database migration — and run it through Plan Mode's actual sequence: clarifying questions, codebase research, a written implementation plan you can edit directly as a file, then Build. Mentor's Margin: why editing the plan file beats arguing in chat.

### Chapter 7 — Debug Mode: Evidence Before Opinion
Ledgerline has an intermittent bug where invoice totals drift by a cent under concurrent updates. Instead of guessing from source, Debug Mode forms hypotheses, instruments the code with logging, hands you reproduction steps, and waits for you to run them before proposing a fix. We work the bug end to end, with honesty about where the mode shines and where you're still the detective.

### Chapter 8 — Choosing Models Like You Choose Instance Types
You already think about compute in terms of cost, latency, and capacity. Models are the same decision. Auto routing, when Max Mode is worth the spend, pairing an expensive planning model with a cheaper implementation model, and reading your own usage to find waste. Cursor's harness work — trimming system prompts, loading tools dynamically, compressing file reads — is the case study in what "efficient" means at the agent level.

---

## Part III: Teaching Cursor Your Codebase

### Chapter 9 — Rules, AGENTS.md, and Never Repeating Yourself
Every time you've typed "use our existing error wrapper" into chat, you've written a rule by hand. This chapter makes those permanent: project rules in `.cursor/rules`, user rules for personal taste, team rules, and the `AGENTS.md` convention Cursor reads alongside everything else. Rules apply across Agent, Ask, Plan, and Debug. We write Ledgerline's rule set together, then deliberately write a bad rule to watch it poison the agent's behavior.

### Chapter 10 — Skills and Custom Modes
Rules say *how*. Skills say *what to do, step by step*. We build a Ledgerline skill for "add a new API endpoint with tests and OpenAPI docs," then pin it as a Custom Mode so the agent stays in that groove for a whole session. The `/review` skill gets special attention as a bridge to Part IV.

### Chapter 11 — MCP: Giving the Agent Hands Beyond the Repo
Your code is not your whole system. Databases, issue trackers, observability, docs wikis. MCP servers let the agent reach them. We wire a Postgres MCP server into Ledgerline so the agent inspects real schema instead of guessing, cover stdio versus HTTP transports and OAuth, and spend serious time on the approval model — an agent with database hands needs a leash.

### Chapter 12 — Hooks: Guardrails That Don't Depend on the Model's Mood
Rules are suggestions the model follows. Hooks are scripts that run no matter what. We build a `hooks.json` for Ledgerline that auto-formats after every edit, blocks shell commands touching production credentials, and logs every tool call for audit. Then conversation-level hooks — `beforeSubmitPrompt`, `afterAgentResponse`, `subagentStart`, `preCompact` — and a small self-correcting loop around agent output. The chapter where senior engineers sit up straight.

### Chapter 13 — Memory, Conversation Search, and the Agent That Remembers Tuesday
The agent can query your past conversations as a tool when it needs context you discussed before, and Automations can keep persistent memory files across runs. What should be remembered, what absolutely should not, and how to audit what's been stored.

---

## Part IV: Leaving the Editor

### Chapter 14 — The CLI: Same Agent, Different Room
The `agent` command runs the identical agent in your terminal, reads the same rules, `AGENTS.md`, and `mcp.json`, and supports the same modes via `--mode` flags or slash commands. Interactive first, then print mode from a script, then a CI job that triages failing tests. Prepending `&` to any message pushes the conversation to a cloud agent — our bridge to the next chapter.

### Chapter 15 — Cloud Agents and the Art of Letting Go
Cloud agents run in isolated VMs with full dev environments: cloned repos, dependencies, secrets, network access. They build features, fix bugs, write tests, open PRs, and record a video of the result. We hand Ledgerline's "migrate the PHP billing service's date handling" to a cloud agent, walk away, and come back to review. Then Automations on schedules or reacting to Slack and PRs, subagents on their own machines, and the Projects beta for long-running work. Mentor's Margin is blunt about what you should and shouldn't trust to run unattended.

### Chapter 16 — Bugbot and Closing the Review Loop
Bugbot reviews PR diffs for bugs, security issues, and quality problems, and `/review-bugbot` runs it from your agent before you push. We put a deliberately flawed Ledgerline PR through it, read the comments critically, and discuss where an automated reviewer earns its keep versus where it generates noise. Security Reviewer and Rollouts get a tour as the "bots that watch production" layer.

### Chapter 17 — Subagents and the Economics of Delegation
Subagents start with a fresh context window and report results back, so the parent never carries their full working context. That's a token-cost story and an architecture story at once. When delegation helps, when it's wasteful (Cursor itself walked back aggressive subagent use for codebase exploration), and pairing models across the parent/child boundary.

---

## Part V: Being Senior in an Agentic Shop

### Chapter 18 — Verification Is Your Job Now
The agent writes more code than you can read line by line. Building a verification practice that scales: what to read closely, what to test, how to design your review surface to catch the dangerous diffs. Five real failure modes and the habit that would have caught each one.

### Chapter 19 — Rolling It Out to a Team
Shared rules, team-published skills, team MCP servers, enterprise-managed hooks, privacy settings, and the cultural work of getting skeptical colleagues to trust the thing. Includes a one-page team "Cursor contract" template.

### Chapter 20 — Capstone: Ship a Feature End to End
No new concepts. A real Ledgerline epic from a vague Linear ticket through Ask Mode exploration, Plan Mode design, local Agent implementation, a cloud agent for the boring parts, hooks enforcing standards, Bugbot review, and merge. You do it; I narrate. The Checkpoint is a retrospective on your own session, not a quiz.

### Epilogue — The Changelog Is a Chapter That Rewrites Itself
Cursor ships fast. How to read releases critically, what to re-evaluate each quarter, and the small set of principles from this book that won't change no matter what the next release adds.

---

## Chapter files

Chapters will live alongside this outline as individual notes:

- [[01 You Already Know How to Delegate]]
- [[02 Context Is the Whole Game]]
- [[03 Tab Is Not Autocomplete]]
- [[04 Meet the Agent]]
- [[05 Four Modes, One Conversation]]
- [[06 Plan Mode as the Design Review]]
- [[07 Debug Mode - Evidence Before Opinion]]
- [[08 Choosing Models Like Instance Types]]
- [[09 Rules, AGENTS.md, and Never Repeating Yourself]]
- [[10 Skills and Custom Modes]]
- [[11 MCP - Hands Beyond the Repo]]
- [[12 Hooks - Guardrails]]
- [[13 Memory and Conversation Search]]
- [[14 The CLI - Same Agent, Different Room]]
- [[15 Cloud Agents and Letting Go]]
- [[16 Bugbot and the Review Loop]]
- [[17 Subagents and Delegation Economics]]
- [[18 Verification Is Your Job Now]]
- [[19 Rolling It Out to a Team]]
- [[20 Capstone - Ship a Feature End to End]]
- [[21 Epilogue - The Changelog Rewrites Itself]]
