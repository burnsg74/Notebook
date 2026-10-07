---
note_type: Learning
title: "Chapter 1 — You Already Know How to Delegate. You've Just Never Delegated to This."
topic: Cursor IDE
book: Pair Programming with a Ghost
chapter: 1
part: "I: Recalibrating"
cursor_version: ">= 3.23"
audience: Senior Full Stack Engineer
status: draft
tags:
  - cursor
  - ai-coding
  - learning-book
up: "[[00 Outline - Pair Programming with a Ghost]]"
next: "[[02 Context Is the Whole Game]]"
created: 2026-10-04
---

# Chapter 1 — You Already Know How to Delegate. You've Just Never Delegated to This.

> [!quote] Where we're going
> By the end of this chapter you'll have a working mental model of what's actually sitting on the other side of the Agent panel: what it is, what it costs, what it can see, what it can touch, and why the thing you've been calling "AI autocomplete" is a category error. No code gets written yet. We're recalibrating the instrument before we trust the readings.

## The confession

Let me start with something I don't say in interviews.

The first month I used an AI coding agent seriously, I was worse with it than the junior on my team. Not a little worse. Embarrassingly worse. She would hand it a vague ticket, let it run, glance at the result, nudge it twice, and ship. I would write a beautifully precise prompt, watch it do something I hadn't asked for, get irritated, take over, and finish the task by hand while muttering that the tool wasn't ready.

It took me longer than I'd like to admit to see what was happening. Twenty-plus years of experience had given me an extremely well-tuned sense of what's hard and what's easy, what needs explaining and what can be assumed, when to hover over someone and when to let them go. Every one of those instincts was calibrated for humans. And the thing in the panel was not a human.

That's the whole premise of this chapter. You don't have a tooling problem. You have a calibration problem. Your delegation instincts are excellent, and they're pointed at the wrong target. We're going to re-aim them.

## What's actually in the box

Cursor's own Learn course breaks the foundations into five ideas: how models work, tokens and pricing, context, tool calling, and what makes something an agent. That's a good decomposition and I'm going to keep it. But I'm going to translate each one into something you already own, because you don't need these concepts explained to you from scratch. You need them mapped onto the furniture already in your head.

Here's the one-sentence version you can hold onto while we go deeper: **a coding agent is a stateless function that takes text in and emits text out, wrapped in a loop that lets it ask for things.** Everything else is detail. Important detail, but detail.

### Idea one: the model is a very good guesser with no memory

Strip away the chat interface and a language model does one thing. Given a sequence of tokens, it produces a probability distribution over what the next token should be. Pick one, append it, do it again. That's inference. That's all of it.

I know you know this. Stay with me, because the consequences are where senior engineers trip.

The model doesn't "know" your codebase. It doesn't remember yesterday's conversation. It doesn't have opinions that persist between calls. Every single time it produces a response, it's being handed the entire conversation so far as one big input and asked, in effect, "given all this, what comes next?" The appearance of memory, personality, and continuity is produced entirely by what gets fed back in on each turn.

Here's the translation. You've worked with a stateless HTTP service. You know that every request has to carry its own context, that there's no session unless something outside the handler is maintaining one, and that if the request is missing a field, the handler can't go find it. The model is exactly that. Cursor is the thing outside the handler maintaining the session. When the agent seems to remember what you said three messages ago, that's not the model remembering. That's Cursor re-sending it.

Why does this matter on a Tuesday? Because when the agent "forgets" something, your human instinct says it wasn't paying attention, and you get annoyed. The correct diagnosis is that the thing you wanted it to remember wasn't in the request. We'll spend all of [[02 Context Is the Whole Game|Chapter 2]] on how to make sure it is.

### Idea two: tokens are your billing unit, and they're not what you think

A token is roughly three-quarters of an English word. Roughly. For code it's often worse: identifiers like `getInvoiceLineItemsByCustomerId` fragment into many tokens, whitespace and punctuation each cost something, and a big JSON blob or a stack trace can eat tokens at a shocking rate.

Every token you send and every token the model produces has a cost. The cost differs by model, and output tokens are usually priced several times higher than input tokens, because generation is the expensive part. Cursor's plans give you a pool of usage measured against that underlying model cost; heavier models and larger contexts draw it down faster. The exact numbers change often enough that I won't print them here. Check your own usage page, which we'll do in a minute.

The translation you want is **cloud spend**. You already think about compute in terms of cost per unit, you already know that the expensive resource isn't always the one you'd guess, and you already know that the way to control spend is to look at the bill, find the outlier, and ask why. Tokens are the same discipline. The engineers who get the most out of Cursor aren't the ones who use it the most. They're the ones who've looked at their usage and know where it goes.

And here's a thing that surprises people: the biggest token sink in most sessions isn't the conversation. It's the tool results. When the agent reads a file, that file's contents enter the context as tokens. When it runs your test suite and the output is four hundred lines, that's tokens. When it greps and gets eighty hits, tokens. A single sloppy "read everything in `src/`" can cost more than an hour of careful back-and-forth.

> [!tip] Try This — Feel the tokenizer
> Take fifteen seconds and find a public tokenizer playground (OpenAI publishes one; so do others). Paste in three things: a paragraph of plain English, a function from your current project, and a chunk of a log file or stack trace. Compare the token counts to the character counts. You'll see English land near four characters per token and code land considerably worse. Now imagine the agent reading your 1,200-line legacy controller because you were vague about which function you meant. That's the intuition I want you to have before you ever open the Agent panel.

### Idea three: context is working memory with a hard ceiling

Everything the model sees on a given turn lives inside its context window. The system prompt. Your rules. The conversation so far. Every file it's read. Every tool result. Your latest message. All of it, together, in one buffer with a fixed maximum size.

When the buffer fills, something has to go. Cursor summarizes or compacts earlier parts of the conversation to make room. That's useful, and it's also exactly when the agent starts "forgetting" the architectural constraint you mentioned forty minutes ago.

The translation here is **working memory**, but I want to be more precise than that, because senior engineers have a specific experience that maps perfectly. You've debugged a problem that spans six files, held all six in your head, and felt the moment where you opened a seventh and the first one fell out. You know that feeling. That's the context window, except the model doesn't feel it. It just confidently proceeds without the thing that fell out.

This is why the single most important operational habit in this book is also the most boring: **start a new conversation when you change tasks.** Each mode in Cursor keeps its own context, and dragging a four-hour conversation about the auth refactor into a question about CSS is paying for a huge context that's actively working against you. The agent isn't a colleague you've been chatting with all day. It's a function call. Make the call clean.

A related consequence: the order and salience of what's in context matters. Something you said once, early, in passing, competes with the eight files the agent has read since. If a constraint matters, it belongs in a rule (Chapter 9), not in a sentence you hope it remembers.

### Idea four: tool calling is how the model gets hands

A model by itself can only emit text. It cannot read a file, run a command, or search the web. What makes an agent possible is a convention: the model can emit a specially formatted request that says, in effect, "I want to call `read_file` with path `src/billing/InvoiceService.php`." The harness around the model, which in our case is Cursor, intercepts that request, actually performs the operation, and feeds the result back into the context as the next piece of input. Then the model runs again.

That's it. That's tool calling. The model asks; the harness does; the result goes in the buffer.

You've built this pattern. It's a **command dispatcher with a plugin registry.** The model is the caller that knows the interface but not the implementation. Each tool is a plugin with a name, a schema for its arguments, and a description that tells the caller when to use it. The harness is the dispatcher that validates, executes, and returns. When you wire up an MCP server in Chapter 11, you're registering more plugins. When you write a hook in Chapter 12, you're adding middleware to the dispatcher.

The thing to internalize now is that **every tool call is a round trip through the model.** The agent reads a file: one model call to decide to read it, the file enters context, another model call to decide what to do next. A task that takes the agent twelve tool calls is twelve-plus inferences, each one carrying the full context so far. That's where latency comes from, that's where cost comes from, and that's why an agent with good context up front and a clear task finishes in three calls while the same agent with a vague task wanders through fifteen.

### Idea five: an agent is a model in a loop with a goal

Put the four ideas together and you have an agent: a model, a set of tools, and a loop that keeps running until the model decides the task is done or the harness stops it. The model reads the situation, picks a tool or writes a response, the harness executes, the result comes back, repeat.

The translation is a **worker process consuming a queue**, except the worker writes its own next job. It's autonomous within bounds. It will keep going. It does not get bored, it does not get tired, and critically, **it does not get embarrassed.** That last one is the inversion that breaks senior instincts the hardest, so it gets its own section.

## Where your instincts are backwards

Here's a short, incomplete list of things I believed about delegation that are true for humans and false for agents.

**"If I explain it well once, they'll remember."** False. The agent remembers nothing between conversations unless you've put it somewhere persistent (rules, `AGENTS.md`, a skill). Explaining well once is table stakes for a human and useless for an agent unless the explanation lands in a file.

**"A senior person doesn't need to be told the obvious."** False in a specific way. The agent is extraordinarily capable at the thing in front of it and has zero knowledge of the thing you didn't put in front of it. It will write a flawless Express handler and have no idea you have an error-wrapping convention three directories over. "Obvious" is a function of shared history, and you share none.

**"Hovering is disrespectful; let them work."** Half true. Letting the agent run is right. But letting it run on a task you haven't verified it understood is how you get a confident, well-tested, beautifully formatted implementation of the wrong thing. The move is to front-load the check (that's what Plan Mode is for, Chapter 6) and then let it run.

**"If they're struggling, they'll ask."** Sometimes. The agent can ask clarifying questions, and in Plan Mode it's designed to. In Agent Mode on a vague task, it will more often guess, and guess confidently. You have to build the asking in.

**"Push back means they disagree."** The agent doesn't disagree. If you say "no, do it the other way," it will do it the other way with the same cheerful confidence it had the first time. This is wonderful for iteration speed and dangerous for judgment, because you'll never get the signal a good human engineer gives you when they say "I'll do it, but I think you're wrong." You have to supply that skepticism yourself, or ask for it explicitly.

> [!note] Mentor's Margin — On juniors and ghosts
> People love the "it's like a junior engineer" analogy, and I think it's actively harmful past the first week. A junior has a tiny set of skills and a growing memory. The agent has a vast set of skills and *no* memory. A junior gets better over months as you invest in them. The agent gets better only as you invest in its *environment*: rules, skills, hooks, context. A junior's errors cluster around not knowing how; the agent's errors cluster around not knowing *what you meant*. If you manage it like a junior, you'll over-explain the how and under-specify the what, and you'll get exactly the failures I got in my first month. Manage it like a brilliant contractor who's never seen your codebase and will be gone tomorrow. Write things down.

## Meet Ledgerline

Through this book we'll work in a fictional codebase called **Ledgerline**, a small invoicing SaaS. I chose it because it has the kind of mess real companies have.

There's a React and TypeScript front end that's mostly modern, with a couple of class components nobody's gotten around to converting. There's a Node API over Postgres that's reasonably clean and has decent test coverage. There's a PHP billing service that predates everyone currently on the team, handles money, and is wrapped in a thick layer of "don't touch it." And there's a Terraform setup for AWS that works, mostly, if you know the three manual steps that aren't documented.

I'm not going to pretend you need to clone a specific repo to follow along. If you have a project of your own with at least two services and some history, use it. The exercises are written so they transfer. Where I reference Ledgerline specifics, read them as "the equivalent thing in your project." The only requirement is that it's real enough to have the kind of implicit knowledge that lives in people's heads and not in files. That's where the lessons are.

## Your first look at the Agent panel

Open Cursor on your project. Open the Agent panel (the sidebar chat; on current builds it's the main AI surface, and if you've got an older layout from before 3.x you should update before continuing). Don't type anything yet. Look.

There are three things in that panel that you should now be able to name from this chapter.

The **mode picker** is where you'll choose Agent, Plan, Ask, or Debug. Each one is the same model with different tools enabled and different instructions in the system prompt. Ask, for instance, has its editing tools turned off. That's the plugin registry idea: same dispatcher, different set of registered commands.

The **model picker** is where you choose which model answers, or leave it on Auto and let Cursor route. This is your instance-type decision. We'll go deep in Chapter 8; for now, know that Auto is a reasonable default and that the expensive options exist for a reason.

The **context area** is where @-mentions, attached files, and rules show up. This is the input to the stateless function. Whatever's here is what the model sees. Whatever isn't, it doesn't.

Now we're going to do one thing, and I want you to predict the outcome first.

> [!question] Pause and Predict — The two questions
> Switch to **Ask** mode. You're going to ask two questions, in two *separate* new conversations.
>
> **Question A:** "What does this project do?" with nothing attached.
>
> **Question B:** "In one paragraph, explain how an invoice gets created end to end, naming the specific files involved." Also with nothing attached.
>
> Before you run either: how many tool calls do you think each one triggers? Which one will the agent answer more accurately? Which one will cost more? Write your guesses down. Actually write them.
>
> Now run them. Watch the panel as the agent works; it shows you each file it reads and each search it runs. Count.

What most people find is that Question A triggers a handful of reads, usually the README and a package manifest or two, and produces an answer that's about right and a little generic. Question B triggers a lot more: searches for "invoice," reads of several files, maybe a wrong turn into the PHP service before finding the Node handler. It produces a better answer, and it costs noticeably more.

Neither of those is a problem. What I want you to notice is that **you could see all of it.** Every read, every search. The agent's reasoning isn't a black box; its tool calls are a trace. Learning to read that trace is a skill, and it's the one that separates people who trust the agent appropriately from people who either trust it too much or not at all.

One more thing to notice: did the agent read anything you'd consider irrelevant? Did it miss anything you'd consider essential? Hold that thought. It's the whole of Chapter 2.

## Looking at the bill

> [!tip] Try This — Your usage, honestly
> Open your Cursor account's usage view (Settings, or the dashboard on cursor.com). Look at the last week. You're not looking for a number. You're looking for *shape*. Which model took the most? Were there sessions that cost far more than the others? Can you remember what those sessions were?
>
> If you've been using Cursor for a while, you'll probably find one or two sessions that dwarf the rest, and if you're honest, they'll be the ones where you kept a conversation going too long or let the agent read half the repo looking for something you could have pointed at. If you're new, bookmark this page. We'll come back to it in Chapter 8 and compare.

This exercise is not about frugality. It's about the discipline you already have with cloud infrastructure, applied to a new resource. You wouldn't run a fleet without looking at Cost Explorer. Don't run an agent without looking at usage.

## The reframe, stated plainly

Let me put the whole chapter in a shape you can carry.

The agent is a stateless, extremely capable function. It sees exactly what's in its context window and nothing else. Every file it reads and every command it runs costs tokens and a round trip. It will work autonomously toward whatever it understood the goal to be, with total confidence and no embarrassment. It remembers nothing between conversations unless you've written it down somewhere it reads.

Your job, then, is not to be a better prompt writer. Your job is to be a better **environment designer**. Get the right things into context. Keep the wrong things out. Write down the things that need to persist. Front-load verification of intent, then let it run. Read the trace. Look at the bill.

Every chapter from here is a specific technique for one of those sentences.

## Checkpoint

Answer these without looking back. If you can't, reread the relevant section; it's short.

1. The agent "forgot" a constraint you mentioned earlier in a long conversation. In terms of this chapter's model, what actually happened, and what's the fix that doesn't involve repeating yourself?
2. You give the agent a task and it makes fourteen tool calls. Name two distinct reasons that number might be high, one that's your fault and one that isn't.
3. Ask mode and Agent mode use the same model. What's actually different between them, in terms of the dispatcher-and-plugins picture?
4. Why is "it's like a junior engineer" a misleading analogy? Give the specific inversion.

**The hard one.** The agent produces a correct, well-tested implementation of a feature that doesn't match what the ticket author actually wanted, because the ticket was ambiguous and the agent picked an interpretation. In a human team, we'd call that a communication failure and split the blame. In this chapter's model, where does the failure actually live, and what's the earliest point in the loop where it could have been caught? Think about which mode you'd have used and why, and keep your answer; we'll test it against Chapter 6.

---

*Next: [[02 Context Is the Whole Game]]. We open Ledgerline for real, and the agent fails to notice an entire service exists. On purpose.*
