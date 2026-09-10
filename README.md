# lecture-note-pipeline

An agent skill that turns lecture/class recording transcripts (speech-to-text
output) into structured Markdown course notes — and gets more accurate every
time you use it.

## The problem

Summarizing a lecture transcript with an LLM gets you a note that looks fine and
is quietly incomplete. In practice, notes built this way:

- lose **30–45% of the lecture content**, usually the second half;
- drop **almost all exam, grading, and assignment information** — the part a
  student actually needs most;
- carry over speech-recognition errors in technical terms and numbers
  (`104.365` silently becomes `164.31`);
- occasionally record a policy **backwards** — a note that says "do not use AI
  on the online exam" when the instructor said AI was encouraged.

Each transcript is processed once, and the same mistakes repeat next week.

## What this skill does differently

Three things:

1. **Audits instead of duplicating.** If a note for that lecture already exists,
   the skill compares it against the transcript and *fills it in* rather than
   generating a second copy.
2. **Treats exam/assignment information as mandatory**, with dedicated sections
   and an explicit instruction to hunt for grading policy, formats, marks,
   thresholds, and submission rules.
3. **Grows two libraries from every run** — an ASR correction table and a
   controlled concept vocabulary — so accuracy compounds instead of resetting.

The third point is the reason to use it more than once.

## Installation

The skill is a single folder. Copy it into your agent's skills directory:

```bash
# Claude Code / generic agents
cp -r lecture-note-pipeline ~/.claude/skills/

# Codex
cp -r lecture-note-pipeline ~/.codex/skills/

# OpenCode
cp -r lecture-note-pipeline ~/.opencode/skills/
```

Or, for an Obsidian vault using the Copilot plugin, place it under any of:

```
<vault>/.agents/skills/lecture-note-pipeline/
<vault>/.claude/skills/lecture-note-pipeline/
<vault>/.opencode/skills/lecture-note-pipeline/
```

`SKILL.md` carries the frontmatter the loader reads; `references/` holds the
templates it pulls in. Keep both.

## Usage

Drop transcript `.txt` files into your transcript inbox folder (loose in the
root — that is the "pending" signal), then tell the agent to process them:

> Generate the course summaries.

The skill reads the files directly — no copy-pasting transcripts. Multiple
transcripts are processed in parallel.

## What you need to adapt

The skill ships with Chinese course-note defaults because that is where it was
developed. Adapt these to your vault:

| Item | Where |
|------|-------|
| Inbox and archive folder names | `SKILL.md` → Directory layout |
| Frontmatter key language and fields | `references/note-template.md` |
| Section names | `references/note-template.md` |
| Library file paths | `references/libraries.md` |

The *structure* is language-independent: an inbox with a location-based
pending/archived split, an audit-before-create rule, mandatory exam and
assignment sections, and two append-only libraries.

## Repository contents

```
lecture-note-pipeline/
├── SKILL.md                        # the skill (frontmatter + instructions)
├── references/
│   ├── note-template.md            # note skeleton and section requirements
│   └── libraries.md                # both libraries: schemas and update rules
├── README.md
├── LICENSE
└── .gitignore
```

## Design notes

**Why location-based pending detection?** A file that is inside a dated folder
has been processed; one loose in the inbox root has not. This needs no marker
files, no database, and makes re-running the trigger idempotent.

**Why plain links instead of embeds?** Transcripts run 20k–30k characters.
Embedding one makes the note unreadable; a link keeps it navigable.

**Why "append only" libraries?** They are the pipeline's memory. Editing history
out of them destroys the signal that makes later runs better.

## License

MIT — see [LICENSE](LICENSE).
