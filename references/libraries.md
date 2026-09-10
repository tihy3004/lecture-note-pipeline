# The two libraries

Both libraries exist so the pipeline gets **more accurate over time**. They are
small, append-only text files that live beside the prompt configuration. Update
them at the end of every run — this step is not optional.

---

## Library 1 — ASR term-correction library

**File:** `ASR术语纠错库.md` (or `asr-corrections.md`)

Records speech-recognition mistakes observed in *real* transcripts, so future
runs recognize the same patterns instead of rediscovering them per lecture.

### Schema

```markdown
# ASR Term Correction Library

> Purpose: accumulate ASR errors observed in real transcripts.
> Maintenance: append new errors after every run. Add only — never edit or delete.

## General error patterns

| Type | Examples |
|------|----------|
| Homophones / near-homophones | 焓 → 含, 熵 → 商, critical → 林界 |
| Technical terms split apart | partial differential equation → 偏微分方城 |
| English words transliterated | conspicuous → conscu, Joneses → Jos |
| Digit / letter confusion | PV=nRT → PV+RT, r^-6 → R to the 6th, 3.167 → 3.157 |

> ⚠️ **Numbers are the most dangerous.** ASR corrupts numerals constantly
> (104.365 → 164.31). Any number used in a calculation must be verified against
> the transcript. Never trust a number from ASR at face value.

## <Subject>

| ASR mishearing | Correct term | Source |
|----------------|--------------|--------|
| 焦霍夫定律 | 基尔霍夫定律 | Lecture 1 |
| 礼盒 | 镓 (Gallium) | Lecture 1 |

## Uncertain

> Fragments that look like errors but cannot be resolved. Re-check these first
> when the same context appears again.

| Fragment | Where | Suspicion |
|----------|-------|-----------|
| AR数据库 | Chemistry L101 | probably "AI database" |

## Update log

| Date | Change | Source |
|------|--------|--------|
| YYYY/MM/DD | created; seeded from N transcripts | N transcripts |
```

### Rules

- **Append only.** Existing rows are never edited or removed — they are the
  memory that makes the library useful.
- Record `ASR mishearing | correct term | source lecture`.
- Put anything you are not sure about in the **Uncertain** section rather than
  asserting a correction you cannot justify.
- Add one row to the update log per run.

---

## Library 2 — Controlled concept vocabulary

**File:** the tail of the knowledge-association / linking prompt
(`知识关联网络提示词.md`, or `concept-vocabulary.md`)

Prevents the same concept from being written under two names. In Obsidian,
`[[临界参数]]` and `[[气体液化与临界参数]]` are **two disconnected nodes** — the
knowledge graph silently fragments.

### Why it matters

A vault can accumulate dozens of these near-duplicate concepts without anyone
noticing, until the graph view is full of orphan nodes. Detect them by collecting
every `[[...]]` target and looking for names that contain one another.

### Schema

```markdown
# Controlled Vocabulary

> Each concept has exactly one canonical name, used everywhere in the vault.

## Naming rules

1. One concept, one name — identical link text across all notes.
2. Granularity: do not merge concepts into one name
   (`[[气体液化与临界参数]]` ✗), and do not split too finely.
3. No course prefixes — write `[[Ohm's law]]`, not `[[Electrics/Ohm's law]]`.
4. Canonical form only; avoid abbreviations (`[[Maxwell–Boltzmann distribution]]`,
   not `[[MB distribution]]`).
5. On discovering a new spelling: register it below **and fix existing notes**.

## Aliases (do not write → canonical)

| Do not write | Canonical |
|--------------|-----------|
| 气体液化与临界参数 | 临界参数 |
| MB分布 | 麦克斯韦-玻尔兹曼分布 |

## Canonical concepts by subject

### <Subject>
`concept-a`, `concept-b`, `concept-c`

### Cross-course
`Calculus`, `Physics`, `Inorganic Chemistry`

### Do not link these
> Too generic or single-instance terms; leave them as plain text.
> `current`, `oxidation`, `period`, `element`
```

### Rules

- **Never rename an already-registered concept.** Renaming severs every existing
  link. Add, and if a conflict exists, migrate the old spelling deliberately.
- When you register a new alias, **also fix historical notes** that used the old
  spelling — otherwise the duplicate node still exists.
- Keep a "do not link these" list so the same debate is not repeated.

---

## Run-completion procedure

At the end of every pipeline run:

1. For each new ASR error found, append a row to library 1's subject table.
   Unresolved fragments go to **Uncertain**.
2. Add a row to library 1's **Update log**.
3. For each new concept name used, append it to library 2's subject list.
4. If a concept had multiple spellings, register the alias **and** fix the
   existing notes that used the wrong one.
5. Verify no concept link in the vault is absent from library 2.

### Verification snippet

Collect every wikilink target and diff it against the registered names:

```powershell
$links = @{}
Get-ChildItem -Recurse -File -Filter *.md | ForEach-Object {
  $t = [System.IO.File]::ReadAllText($_.FullName, [System.Text.Encoding]::UTF8)
  foreach ($m in [regex]::Matches($t, '\[\[([^\]\|#]+)')) {
    $k = $m.Groups[1].Value.Trim()
    if ($k -and $k -notmatch '\.(md|txt|pdf|base)$') { $links[$k] = 1 }
  }
}
# then compare against the vocabulary's registered names,
# excluding note/MOC/lecture-title links
```

Strip fenced code blocks before extracting backtick-quoted concept names, or the
``` fences will swallow whole sections and every concept will look unregistered.
