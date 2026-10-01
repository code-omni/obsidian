# Work Order v3: Obsidian PKM Vault
A ightweight Obsidian vault for **frictionless capture → synthesis → knowledge development → action**. It combines Steph Ango–style Quick Notes, self-indexing Daily notes, Zotero-backed References, Common (smart/commonplace) notes, and Projects with Bets. Core plugins only, except Zotero Integration.

> **Capture without classification; classify and synthesize when useful.**

```text
References  → What did I encounter?
Quick Notes → What did I think?
Daily       → What did I think today?
Common      → What idea is emerging?
Projects    → What am I trying to make happen?
Bet         → What am I risking based on what I believe?
```

## Structure

```text
/
├── Home.md          landing page + review loop
├── Daily/           Quick Notes (YYYY-MM-DD HHmm) + Daily notes (YYYY-MM-DD)
├── References/      @citekey notes, generated from Zotero
├── Common/          claim-titled synthesis notes
├── Projects/        lifecycle via properties, not folders
├── Templates/       Quick Note, Daily, Common, Project, Zotero Reference
├── Bases/           Daily.base, Common.base, Home.base
└── Attachments/     images and files
```

No other organizational folders. New layers are added only when a genuine need emerges from use (v1's test).

## 1. Quick Notes
- One thought per note, in `Daily/`. Filename `YYYY-MM-DD HHmm.md`, created by the core **Unique note creator** (`Cmd/Ctrl+N`).
- **The core idea goes in `aliases`.** Obsidian's quick switcher and link autocomplete match aliases, so you find and link notes by idea rather than by timestamp. The Daily index displays the alias as the note's title.
- `created` is stored as a property rather than read from the file date, because sync can rewrite file dates.
- Tags go inline at the end (`#mastery #fun`) or in frontmatter; both count.
- **To append,** open the note by its alias and add below a `---` line with the date. The Daily note for that day lists it under *Continued today*.
- **Filename collisions:** two captures in the same minute will collide. Keep `HHmm` for now; switch the Unique note creator format to `YYYY-MM-DD HHmmss` if it becomes a problem.

Template:
```yaml
type: quick
created: {{date:YYYY-MM-DDTHH:mm}}
aliases: [core idea]
source:          # only for quotes with no Zotero item
```

## 2. Daily
- One note per day, `Daily/YYYY-MM-DD.md`, using the core Daily notes plugin (`Cmd/Ctrl+Shift+D`).
- **It indexes itself.** The template embeds two views from `Bases/Daily.base`. Since `this` refers to the embedding note, the same base works for every day.
  - **Captured today:** Quick Notes whose filename starts with the date. Columns are Time, Note (shown by alias), and Tags.
  - **Continued today:** older Quick Notes last edited on that date.
- The views live in a single `.base` file, so changing it updates every Daily note at once.

**Dataview alternative.** Use this if you prefer Dataview or want an index that never changes after the day ends. Replace the embed in `Templates/Daily.md` with:

````markdown
```dataview
TABLE WITHOUT ID dateformat(created, "HH:mm") AS Time,
  link(file.link, default(aliases[0], file.name)) AS Note, file.etags AS Tags
FROM "Daily"
WHERE type = "quick" AND startswith(file.name, this.file.name)
SORT file.name ASC
```
````

Bases is the default because it's a core plugin and needs no install.

## 3. References: Zotero owns sources, Obsidian owns your thinking

| Material | Where it goes |
|---|---|
| Papers, books, articles, blogs | Zotero item. Highlight in the Zotero reader, then import. |
| Web pages and clippings | Zotero Connector saves a snapshot. Highlight the snapshot in Zotero 7, then import. |
| YouTube and podcasts | Zotero item through the Connector. Put quotes with timestamps in the reference note's *My notes* section. |
| Physical books | Zotero item, added by ISBN. Type quotes with page numbers into *My notes*. |
| Quotes with no citable source (something said, a post, a sign) | Quick Note tagged `#quote` with `source:` filled in. Move it to Zotero only if you'd later cite it. |

Rules:
- **Create a reference note only when you import annotations or want to write about a source.** Most of the Zotero library never needs a note in Obsidian.
- The filename is `@citekey`, with citekeys from Better BibTeX. Link to it as `[[@citekey]]`.
- The template uses `{% persist %}` blocks, so *Synopsis* and *My notes* survive re-imports. On each re-import, only annotations added since the last import are appended.
- The Obsidian Web Clipper is **not** used, because it would split the source library between two tools.

## 4. Common
A Common note has two parts: **your synthesis** at the top, and an **aggregation** that maintains itself at the bottom.

- **The title is a claim,** written as a sentence: *Excellence Becomes Play*, *Architecture Determines Adaptability*.
- `gathers: [tags]` pulls in every note carrying those tags, through `Common.base#Gathered by tag`.
- `Common.base#Linked here` lists every note that links to this one.
- The synthesis sections are *Current understanding*, *Tensions & open questions*, *Related ideas*, and *Sources*.
- `maturity` goes `seed` → `developing` → `stable`.

### Promotion rules
Promote an idea when **any one** of these is true:
1. **Rule of three.** A tag or theme appears in 3 or more Quick Notes across at least 2 different weeks.
2. **Link pull.** You go to type `[[an idea]]` and it doesn't exist. Create it on the spot as a seed.
3. **Convergence.** Your own note and 2 or more references point at the same idea.
4. **Use.** You need the idea for writing or a project.

During capture or the daily review, flag possible promotions with `#promote`. The weekly review handles them.

**To promote:** write the claim as the title, fill in `gathers`, and write 1–3 sentences of current understanding. Never move or rewrite the original Quick Notes.

## 5. Projects and Bets
- **Status:** `candidate` (considered, not started) → `active` → `archived`. This replaces v2's `prompt`.
- **On archiving,** set `outcome` to `achieved`, `partial`, `missed`, or `abandoned`, and write *What I learned*. Promote durable lessons to Common.
- **A Bet** is a set of properties on a project, enabled with `bet: true`:

| Property | Type | Meaning |
|---|---|---|
| `hypothesis` | text | What I believe |
| `desired_outcome` | text | What I expect if I'm right |
| `investment` + `investment_unit` | number + unit | What I'm putting in, in hours, weeks, or $ |
| `risk` | text | What I lose if I'm wrong |
| `start`, `timeframe` | date | When the bet runs, and when it resolves |
| `review` | date | Next checkpoint. Shows up on Home when due. |
| `kill_criteria` | text | The condition under which I stop |

## 6. Home and the review loop
`Home.md` is bookmarked as the landing page. It holds:
- the layer map
- capture shortcuts
- this week's notes
- the review loop checklists
- the promotion rules
- the tags vs. links rule
- the success criteria
- live views from `Home.base`

The live views are:
- *Needs a title or tag*
- *Flagged to promote*
- *Common in progress*
- *Bets due for review*
- *Active projects*
- *Candidates*
- *Recent references*
- *Project archive*

The review loop runs at three cadences:
- **Daily (2 min):** title and tag today's notes, and flag anything for `#promote`.
- **Weekly (20 min):** clear the queues, check tag frequency in the Tags pane, promote, import from Zotero, develop one Common note, and review due bets and candidates.
- **Quarterly (45 min):** read archived project outcomes, promote lessons, and mark Common notes `stable`.

## 7. Note semantics
**Tags classify** (`#competition #mastery`). Use them freely.

**Links assert a meaningful relationship** ("This reminded me of [[Physics of Grappling]]"). Don't link two notes just because they share a topic.

```text
Experience / Read / Think
   ↓              ↓
Quick Notes   Zotero → References
   ↓ (Daily indexes)   ↓
Tags + natural links ←─┘
   ↓
Repeated ideas (#promote, rule of three)
   ↓
Common  ──→  Projects / Bets  ──→  lessons back to Common
```

## 8. Basics

**Core plugins** (enabled in the template):
- Daily notes, Unique note creator, Templates
- Bases, Properties
- Quick switcher, Search, Tags pane
- Backlinks, Outgoing links, Bookmarks
- Note composer, Command palette, Outline, Page preview, File recovery

**Community plugins:**
- **Zotero Integration** (mgmeyers). Requires **Better BibTeX** in Zotero 7, with Zotero running during imports.
- Dataview, only if you choose the alternative above.
- Optional: *Homepage*, to open Home on launch.

**Hotkeys:**

| Action | Hotkey |
|---|---|
| New Quick Note | `Cmd/Ctrl+N` (replaces the default new-file shortcut) |
| Today's Daily note | `Cmd/Ctrl+Shift+D` |
| Quick switcher | `Cmd/Ctrl+O` |
| Insert template | `Cmd/Ctrl+T` |

Use Insert template for Common and Project notes. Create those notes inside their folders.

**Mobile capture.** "Capture in seconds" depends on this more than anything else.
1. Obsidian mobile: open **Settings → Mobile**. Add *Unique note creator: Create new unique note* to the toolbar and make it the pull-down quick action.
2. iPhone: make a Shortcut that opens `obsidian://open?vault=<VaultName>`, then bind it to the Action Button or the Lock Screen. The pull-down action makes the note.
3. Use the Zotero iOS app and the Safari Zotero Connector for sources. Highlight on mobile and import on desktop.

**Zotero Integration settings:**

| Setting | Value |
|---|---|
| Database | Zotero |
| Note import location | `References` |
| Output path | `References/@{{citekey}}.md` |
| Template file | `Templates/Zotero Reference.md` |
| Image output | `Attachments/zotero/{{citekey}}` |
| Better BibTeX citekey format (in Zotero) | `auth.lower + year + shorttitle(1,0).lower`, e.g. `@ango2023vault` |
