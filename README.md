# Work

Personal knowledge vault. Markdown files are the record. Each note has YAML frontmatter; the body is the note.

## Folders

| Folder | What goes here | Template |
| --- | --- | --- |
| `00 Inbox` | Unsorted capture (`note_type: inbox`) | `Templates/Inbox.md` |
| `10 Clients` | Client, project, and job-hunt work | See below |
| `20 Notes` | Evergreen notes of every kind; type is `note_type` in frontmatter | Pick the template that matches the type |
| `90 Archive` | Finished or dropped (`note_type: Archive`) | `Templates/Archive.md` |
| `Templates` | Note templates | — |

`note_type` values used in `20 Notes`:

| `note_type` | Meaning | Template |
| --- | --- | --- |
| `Note` | One evergreen note per tool or topic | `Templates/Note.md` |
| `Learning` | Learning material | `Templates/Learning.md` |
| `How To` | Procedures | `Templates/How To.md` |
| `Research` | Research still being sorted | `Templates/Research.md` |
| `Service` | Accounts and how a service is used | `Templates/Service.md` |
| `Reference` | Stable reference | `Templates/Reference.md` |

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
