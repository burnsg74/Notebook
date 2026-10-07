# Work

Personal knowledge vault. Markdown files are the record. Each note has YAML frontmatter; the body is the note.

## Folders

| Folder | What goes here | `note_type` | Template |
| --- | --- | --- | --- |
| `00 Inbox` | Unsorted capture | `inbox` | `Templates/Inbox.md` |
| `10 Clients` | Client, project, and job-hunt work | `Client`, `Project`, `Task`, `Job Board`, `Prompt`, `Contact`, `Note` | See below |
| `20 Notes` | One evergreen note per tool or topic | `Note` | `Templates/Note.md` |
| `30 Learning` | Learning material | `Learning` | `Templates/Learning.md` |
| `40 How To` | Procedures | `How To` | `Templates/How To.md` |
| `50 Research` | Research still being sorted | `Research` | `Templates/Research.md` |
| `60 Services Tools` | Accounts and how a service is used | `Service` | `Templates/Service.md` |
| `70 Reference` | Stable reference, when a note is ready to move here | `Reference` | `Templates/Reference.md` |
| `80 Contacts` | People | `Contact` | `Templates/Contact.md` |
| `90 Archive` | Finished or dropped | `Archive` | `Templates/Archive.md` |
| `Templates` | Note templates | — | — |

Inside `10 Clients`, use the template that matches the note:

| Kind | Template |
| --- | --- |
| Client | `Templates/Client.md` |
| Project | `Templates/Project.md` |
| Task | `Templates/Task.md` |
| Job board | `Templates/Job Board.md` |
| Prompt | `Templates/Prompt.md` |
| Contact | `Templates/Contact.md` |
| Other note | `Templates/Note.md` |

## Properties

`note_type` is the classifier. `created` is `YYYY-MM-DD HH:mm` when the time is known, or `YYYY-MM-DD` when it is not.

| Property | Used for |
| --- | --- |
| `note_type` | Every note |
| `created` | Every note |
| `title` | Learning pieces |
| `name` | Contacts, tasks, job boards, clients, projects, services |
| `topic` | Subject of a learning or research note |
| `url` | Job boards and services |
| `rank` | Job board order (number) |
| `status` | Tasks and drafts |
| `tags` | List |
| `book`, `chapter`, `part`, `cursor_version`, `audience` | Learning book |
| `sources` | List of source URLs |
| `up`, `prev`, `next` | Wikilinks |
| `disqualifying` | List of skills that reject a job |
