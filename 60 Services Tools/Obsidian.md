---
note_type: Service
created: 2026-08-16 12:48
---
[Obsidian](https://obsidian.md) is a **markdown-based note-taking app** that helps you build a personal knowledge base by linking notes together. It stores files locally on your device, supports plugins and themes, and is popular for **PKM, writing, and research**.

---
# Account

**URL**   : https://obsidian.md/account/
**Name**  : Gregory Daniel Burns
**Email** : gregburns74@gmail.com

---

**Key Design Decisions**

**1. Separate "Learning" (raw) from "Knowledge Base" (distilled)**

This mirrors exactly what you described. ⁠03-Learning is your scratchpad/zettelkasten-ish space where you dump research, half-formed thoughts, links to articles. Periodically (weekly/monthly review), you "graduate" mature insights into ⁠04-Knowledge-Base as clean, atomic, evergreen notes. Link back to the original learning note as a source (⁠[[03-Learning/AI-Usage/some-note]]) so you keep provenance without cluttering the polished note.

**2. Work Logs as single growing files, not one-note-per-day**

For each project, keep one ⁠Work-Log.md with dated headers (⁠## 2026-07-19), appended to over time. This is far easier to scan than dozens of tiny daily notes, and you can still link out to specific decisions/notes from each entry.

**3. Use tags for cross-cutting concerns**

Folders answer "what kind of note is this?" Tags answer "what's it about / status?" Examples:

- ⁠#status/active ⁠#status/archived — for clients/projects

- ⁠#tool/cloudflare ⁠#tool/1password — link service notes to client notes that use that tool

- ⁠#topic/ai ⁠#topic/obsidian-workflow — for learning notes

This lets you build a Dataview query like "show me every project that uses Cloudflare" pulling from tags rather than manually cross-referencing.

**4. Templates folder + Templater plugin**

Set up Obsidian's core Templates plugin (or the community "Templater" plugin for dynamic fields like current date, client name prompts). Suggested templates:

- **Client-Note** — name, contact info, timezone, communication preference, links to active projects

- **Project-Brief** — scope, tech stack, deadlines, budget, repo links

- **Work-Log-Entry** — date, time spent, summary, blockers

- **Meeting-Notes** — attendees, agenda, decisions, action items

- **Email templates** — proposal follow-up, invoice reminder, project kickoff, project wrap-up

- **AI-Prompt templates** — reusable prompt skeletons for code review, spec writing, debugging, client communication drafting

**5. Service/Tool notes act as your personal "how I use X" wiki**

Each note (Obsidian.md, Raycast.md, etc.) should have consistent sections: ⁠Setup, ⁠Config/Snippets, ⁠Shortcuts I use, ⁠Gotchas, ⁠Links to official docs. Then link from client/project notes whenever a specific config decision was made (e.g., "Set up Cloudflare DNS — see [[Cloudflare]] for standard config").

**Recommended Plugins to Support This**

- **Dataview** — query/aggregate notes by tag, folder, frontmatter (e.g., list all active clients, sum hours logged this month)

- **Templater** — smarter templates with prompts and dynamic dates

- **Tasks** or built-in checkboxes + Dataview — track action items across client notes

- **Periodic Notes** — daily/weekly notes for your own review/planning (separate from client work logs)

- **QuickAdd** — one-command creation of new client/project structures from templates

**Frontmatter Convention**

Use YAML frontmatter consistently for status and metadata, since this is what makes Dataview powerful:

  

---

type: client

status: active

tools: [cloudflare, google-workspace]

started: 2026-07-19

---

  

This lets you build a dashboard note like "Active Projects" or "Clients using Cloudflare" automatically instead of maintaining index pages by hand.

**Suggested Workflow Loop**

1. Capture everything messy into ⁠00-Inbox or directly into the relevant ⁠03-Learning note.

- During weekly review, process inbox notes into proper client/project/service notes.

- When a learning topic feels "solved" or stable, write a clean evergreen note in ⁠04-Knowledge-Base linking back to sources.

- Use templates for anything repeatable (new client, new project, work log entries, emails, AI prompts) so structure stays consistent without extra thought.

Would you like me to draft the actual template file contents (e.g., Client-Note, Project-Brief, Work-Log-Entry) so you can drop them straight into Obsidian?