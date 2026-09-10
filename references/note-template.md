# Note template

The skeleton below is the contract for a generated note. Adapt the language and
section names to the vault, but keep the **section set and order** — the exam and
assignment sections are not optional.

## Frontmatter

Use the vault's existing key convention. Check a current note before writing; do
not introduce a second convention. A typical Chinese-key form:

```yaml
---
课程: "[[Course Name]]"
讲师: Instructor Name
日期: YYYY/M/D
标签:
  - 课程/Course Name
  - 知识点/Topic
  - 难度/基础
类型: 课堂笔记
---
```

Notes:

- The course field is a link, so all notes in a course cluster together.
- `类型` (type) is what index filters key off. Keep it consistent — a Base that
  excludes index pages by `类型 != "MOC"` depends on this field existing.
- Leave `讲师` as a placeholder rather than guessing.

## Body

```markdown
# 第N讲 Lecture Title

## 本节课核心
One sentence summarizing the lecture's content and goal.

## 核心知识点

### 1. Concept name
- **定义/定理**：
- **关键公式**：
- **推导逻辑**：
  1. step
- **适用条件**：
- **易错点**：
  > ⚠️ mistakes the instructor explicitly warned about

### 2. Concept name
...

## 典型例题

### 例题1：short description
- **题目**：
- **解题步骤**：
  1.
- **考察点**：
- **技巧/陷阱**：

## 考试重点
> 📌 exam scope, question types, point values, pass thresholds, grading policy

## 作业与要求
- 课后习题：
- 提交方式：
- 截止日期：
- 预习内容：
- 教材与参考书：

## 常见疑问
- Shared student confusions and the instructor's answers

## 附录
- 原始转写：[[<Title> 原始转写.txt]]
- 课程索引：[[<Course Name> MOC]]
```

## Section requirements

| Section | Rule |
|---|---|
| 本节课核心 | One sentence. No bullet lists. |
| 核心知识点 | 3–8 modules. Each needs definition, formula, derivation, applicability, pitfalls. |
| 典型例题 | One block per example: problem, numbered steps, what it tests, traps. |
| **考试重点** | **Mandatory.** Exam scope, formats, marks, thresholds, policy. |
| **作业与要求** | **Mandatory.** Submission method, deadlines, textbook, prep. |
| 常见疑问 | Only genuine shared confusions; drop one-off individual questions. |
| 附录 | Link the archived transcript and the course index. |

Delete any section you truly have no content for rather than leaving it empty.

## Formatting conventions

- Math in LaTeX: inline `$...$`, display `$$...$$`.
- Important conclusions in a `>` blockquote.
- Pitfalls prefixed `> ⚠️`; exam points prefixed `> 📌`.
- Concept links `[[Concept]]` — only for units that deserve their own note, never
  for ordinary vocabulary.
- Mark model-supplied content `[AI 补充]` inline, and list uncertain terms as
  `[待确认: term]` rather than guessing.

## Quality checklist before writing

1. No ASR residue in technical terms.
2. No dropped knowledge points, examples, or assignment requirements.
3. Every numeral traced back to the transcript.
4. No section left empty.
5. Links, LaTeX, and frontmatter all valid.
6. No filler words ("um", "you know", "so then") left in the prose.
