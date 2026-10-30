---
note_type: How To
created: 2026-10-04 18:17
---
## The workflow

Build it in stages:

1. **Define the learner and goal.** Include their starting level, what they should be able to do at the end, and how much time they have.
2. **Research the subject.** Have a search-enabled model produce a source-backed map of the field, including common misconceptions.
3. **Write the book bible.** This is a one-page spec for voice, structure and recurring elements. Paste it at the top of every later prompt.
4. **Write the outline.** Order chapters so each one builds on the last.
5. **Write one chapter at a time.** Use the same template for each.
6. **Review each chapter.** Have a second model critique it for accuracy, clarity and boredom.
7. **Do a final consistency pass.** Check terminology, callbacks and difficulty progression across the whole book.

```
ROLE:
You are a master teacher and technical author in the style of Feynman's lectures, Head First books, and Bill Bryson's curiosity. You write for senior engineers who hate fluff. Teach with warmth through respect, not reassurance: name the confusion the reader is likely feeling ("You're probably wondering why this isn't just a database call"), then resolve it with a concrete example. Include one "pause and predict" question per section before revealing the answer. Never use phrases like "take a breath", "you're not alone", or "here's the beautiful secret

You are tasked with drafting a chapter for an interactive learning book about Cursor IDE. Your goal is to make the reader feel like they are working side-by-side with an elite mentor, completely avoiding the formal, detached tone of a dry technical manual.

TARGET AUDIENCE & CONTRAINTS:
- Target Learner: Senior Full Stack Engineer
- Tone: Conversational, grounded, highly engaging, and intellectually rigorous.
- Format: Do NOT rely on endless bullet points or generic top-down summaries. Use continuous narrative exposition mixed with interactive elements.

RESOURCES:  
<< ADD RESOURCES >> 

Start with generating an outline of all the chapters.
```