# Interview Coach Skill — CLAUDE.md

> **For AI assistants**: This file explains the codebase structure, conventions, and development workflows for the Interview Coach skill. Read this before making any changes.

---

## What This Repository Is

**Interview Coach** is a comprehensive, AI-powered interview coaching *skill* for Claude Code (and compatible with OpenAI Codex / Cursor). It is entirely **markdown-based** — there is no compiled code, no package manager, and no build step. The "program" is a structured prompt system that runs inside an AI coding assistant's context.

Users activate it by renaming `SKILL.md` → `CLAUDE.md` (Claude Code) or `AGENTS.md` (OpenAI Codex), then opening the folder in their AI assistant.

---

## Repository Structure

```
interview-coach-skill/
├── SKILL.md                      # PRIMARY: Core system prompt (1,200+ lines)
├── README.md                     # User-facing documentation
├── VERSIONS.md                   # Version roadmap (v1–v5)
├── LICENSE                       # MIT
├── .gitignore                    # Excludes coaching_state.md, CLAUDE.md, .DS_Store
├── .claude/
│   └── settings.json            # Allowed tool permissions: Read, Edit, Write, WebFetch, WebSearch
├── references/
│   ├── commands/                 # 24 individual command workflow files
│   │   ├── kickoff.md           # Profile initialization, onboarding
│   │   ├── research.md          # Company research (3 depth levels)
│   │   ├── prep.md              # Role-specific interview prep
│   │   ├── decode.md            # JD analysis and batch triage
│   │   ├── concerns.md          # Anticipate interviewer concerns
│   │   ├── questions.md         # Generate tailored interview questions
│   │   ├── practice.md          # 8-stage drill progression
│   │   ├── mock.md              # Full mock interviews (5 formats)
│   │   ├── stories.md           # Storybank management + retrieval drills
│   │   ├── analyze.md           # Transcript analysis (format-aware)
│   │   ├── debrief.md           # Post-interview rapid capture
│   │   ├── progress.md          # Trend analysis and calibration
│   │   ├── feedback.md          # Capture recruiter outcomes
│   │   ├── resume.md            # Resume optimization (8 dimensions)
│   │   ├── linkedin.md          # LinkedIn profile optimization
│   │   ├── pitch.md             # Core positioning statements
│   │   ├── outreach.md          # Networking outreach coaching
│   │   ├── present.md           # Presentation round coaching
│   │   ├── salary.md            # Early-process comp coaching
│   │   ├── hype.md              # Pre-interview confidence warmup
│   │   ├── thankyou.md          # Post-interview follow-up drafts
│   │   ├── negotiate.md         # Post-offer negotiation
│   │   ├── reflect.md           # Post-search retrospective
│   │   └── help.md              # Context-aware command menu
│   ├── cross-cutting.md         # Shared modules: differentiation, gap-handling, signal-reading
│   ├── rubrics-detailed.md      # 5-dimension scoring rubrics with root cause mapping
│   ├── role-drills.md           # Role-specific drills (PM, Eng, Design, Data, Research, Ops, Marketing)
│   ├── differentiation.md       # Earned secrets and spiky POVs framework
│   ├── transcript-processing.md # Step-by-step transcript analysis
│   ├── transcript-formats.md    # Auto-detection + normalization for 8 transcript formats
│   ├── storybank-guide.md       # Story management and retrieval protocol
│   ├── story-mapping-engine.md  # Portfolio-optimized story mapping with fit scoring
│   ├── calibration-engine.md    # Scoring drift detection and recalibration
│   ├── challenge-protocol.md    # Level 5 challenge framework (5 lenses)
│   └── examples.md              # 11 worked examples with full output samples
└── releases/
    ├── v2.md                     # v2 release notes (Coaching Depth)
    └── v3.md                     # v3 release notes (Full Lifecycle)
```

**Note**: `coaching_state.md` is *not* committed — it is created locally by `kickoff` and contains the user's personal coaching data.

---

## Core Architecture

### How the Skill Works

1. The user renames `SKILL.md` to `CLAUDE.md` (activating it as the AI assistant's context)
2. The AI assistant reads `SKILL.md` on session start and loads all `references/` files
3. User types a command (e.g., `kickoff`, `practice`, `analyze`) in natural language
4. The AI executes the corresponding workflow from `references/commands/<command>.md`
5. Session state is persisted in a local `coaching_state.md` file

### Session State: `coaching_state.md`

This file is the user's personal coaching database. It is:
- **Created by**: `kickoff` command
- **Updated by**: Most commands after completion
- **Schema**: Defined in `SKILL.md` (see "Session State Schema" section)
- **Not committed**: Listed in `.gitignore`

Key sections of `coaching_state.md`:
- `## Profile` — seniority, target roles, industries, feedback directness level
- `## Active Targets` — companies and roles being pursued
- `## Storybank` — STAR stories table + full story text
- `## Interview Intelligence` — question bank, effective/ineffective patterns
- `## Interview Loops` — per-company round tracking
- `## Outcome Log` — real interview results correlated with practice scores
- `## Drill Progression` — current stage and role-specific scores
- `## Session Log` — timestamped history of sessions

### Priority Hierarchy (from SKILL.md)

When handling ambiguous or conflicting instructions, follow this order:
1. `SKILL.md` explicit rules
2. Active `coaching_state.md` context
3. `references/` module guidance
4. General coaching best practices

---

## Command System

### Command Categories

| Category | Commands |
|---|---|
| Setup & Strategy | kickoff, help |
| Application Materials | resume, linkedin, pitch, decode, outreach |
| Interview Prep | research, prep, concerns, questions, present, salary |
| Practice & Simulation | practice, mock, stories, hype |
| Analysis & Tracking | analyze, debrief, progress, feedback, thankyou, negotiate, reflect |

### Command File Structure (consistent pattern)

Every file in `references/commands/` follows this structure:

```markdown
# [Command Name]

### Step 1: [Input Collection / Context Check]
- Check coaching_state for relevant existing data
- Ask clarifying questions (one at a time)

### Step 2: [Analysis / Branching Logic]
- Branch based on coaching_state data
- Apply relevant rubrics or modules

### Step 3: [Output Generation]
- Generate coaching output
- Update coaching_state schema

### Schema:
- Input requirements
- Output format definition
- coaching_state fields to update
```

### Multi-Step Intent Detection

The skill detects compound requests and chains commands automatically. For example:
- `"prepare me for my Google interview next week"` → research → prep → concerns → hype
- `"I just got rejected, help me process it"` → feedback → reflect

---

## Scoring Framework

### 5 Dimensions (applied to all answers)

| Dimension | What It Measures |
|---|---|
| **Substance** | Depth of insight, specificity, real impact |
| **Structure** | Answer organization, flow, narrative coherence |
| **Relevance** | Fit to question asked, role, company context |
| **Credibility** | Evidence quality, ownership of outcomes, authenticity |
| **Differentiation** | Unique perspective, earned secrets, spiky POVs |

### Scoring Rules

- Scale: **1–5** per dimension (1=poor, 5=strong)
- Scores are **seniority-calibrated**: the same answer scores differently for L3 vs L5 candidates
- Every score below 4 maps to one of **9 root causes** (see below)
- Aggregate score = weighted average; weights shift by role type

### 9 Root Causes (for weak scores)

1. **Inability to identify core** — can't isolate what the question is really asking
2. **Reflexive "we"** — obscures individual contribution
3. **Conflict avoidance** — sanitizes tension or difficulty
4. **Status anxiety** — over-focuses on title/level rather than impact
5. **Narrative hoarding** — uses too many stories instead of deepening one
6. **Fear of being wrong** — hedges instead of committing to a POV
7. **Performance anxiety** — delivery masks substance
8. **Cultural style** — legitimate style differences misread as weakness
9. **Linguistic formality** — vocabulary or register mismatch for context

### Feedback Directness Levels

User-set in `coaching_state.md` (1–5):
- **1–4**: Standard coaching voice (strengths first, evidence-based critique)
- **5**: Challenge Protocol activates — answers are red-teamed, avoidance patterns named directly

---

## Differentiation Module

Differentiation is a **first-class dimension** across all outputs — not a nice-to-have. It appears in:
- Answer scoring rubrics
- Resume bullet rewrites
- LinkedIn sections
- Pitch construction
- Outreach messages
- Mock interview feedback

### Key Concepts

- **Earned Secrets**: Insights this candidate has that no one else could have from their exact path — surfaces via 5 reflection questions
- **Spiky POVs**: Defensible, experience-backed stances on contested topics
- **Anti-patterns to flag**: generic frameworks, buzzword-heavy answers, "we did X" without individual specificity

---

## Transcript Processing

The `analyze` command handles **8 transcript formats** with auto-detection:

| Format | Source |
|---|---|
| Otter.ai | Speaker-labeled, timestamped |
| Grain | Video highlight format |
| Google Meet | Transcript export |
| Zoom | Chat + transcript |
| Microsoft Teams | Teams export |
| Granola | AI note format |
| Tactiq | Real-time transcription |
| Manual / Pasted | Freeform user input |

### Interview Type Parsing

Each format is parsed differently depending on interview type:
- Behavioral Q&A
- Panel exchange
- System design (phase-aware)
- Technical + behavioral segments
- Case study

---

## Storybank Schema

Stories are stored in `coaching_state.md` as a table plus full narrative text.

### Story Table Columns

| Column | Description |
|---|---|
| ID | Unique story ID (S001, S002, …) |
| Title | Short descriptor |
| Primary Skill | Main competency demonstrated |
| Secondary Skill | Supporting competency |
| Impact | Quantified outcome |
| Domain | Industry/function context |
| Risk/Stakes | Level of difficulty/pressure |
| Earned Secret | Unique insight embedded in story |
| Strength | 1–5 score |
| Use Count | Times used in practice/mock |
| Last Used | Date |
| Notes | Weaknesses, coaching flags |

### Story Mapping Engine

The `stories` command includes a portfolio optimizer that:
- Maps each story to competency coverage
- Detects gaps in coverage
- Assigns **4-level fit scores** (Strong / Moderate / Weak / Forced)
- Resolves conflicts when multiple stories compete for the same slot

---

## Role-Specific Drills

`references/role-drills.md` defines drill sets for 7 archetypes:

| Role | Core Competencies Drilled |
|---|---|
| Product Manager | Prioritization, roadmap tradeoffs, metrics, stakeholder alignment |
| Engineering | System design, technical tradeoffs, debugging stories, scope/complexity |
| Design | Process, critique fluency, impact measurement, cross-functional influence |
| Data Science | Experiment design, insight communication, modeling tradeoffs, business impact |
| Research | Methodology rigor, synthesis, influencing without authority, ambiguity handling |
| Operations | Process improvement, firefighting, stakeholder management, metrics |
| Marketing | Positioning, campaign analysis, audience insight, cross-channel thinking |

---

## Development Conventions

### Adding a New Command

1. Create `references/commands/<command-name>.md` following the 3-step structure above
2. Add the command to the command table in `SKILL.md` (Commands Reference section)
3. Add it to `references/commands/help.md` (context-aware menu)
4. If the command updates `coaching_state.md`, add the schema fields to the Session State Schema in `SKILL.md`
5. Add 1–2 worked examples to `references/examples.md`
6. Update `README.md` command table and fast workflow examples if relevant
7. Update `VERSIONS.md` if this is a version-worthy addition

### Editing an Existing Command

- Keep the 3-step structure intact
- Preserve backward compatibility for `coaching_state.md` fields (existing users have data stored)
- If removing or renaming a `coaching_state.md` field, add a migration note in `SKILL.md` under the schema migration section
- Update `references/examples.md` if the output format changes significantly

### Editing Cross-Cutting Modules

Cross-cutting modules (`references/cross-cutting.md`, `references/differentiation.md`, etc.) are referenced by multiple commands. Changes here have wide blast radius:
- Test against at least 3 command workflows before finalizing
- Check `references/examples.md` to ensure worked examples still hold

### Editing Scoring Rubrics

`references/rubrics-detailed.md` is the canonical scoring reference. When editing:
- Keep seniority calibration rows (L3/L4/L5) consistent across dimensions
- Root cause mappings must remain internally consistent (each weak pattern maps to exactly one root cause)
- Update `references/examples.md` scored examples if thresholds change

### File Naming Conventions

- All files: lowercase, hyphenated (`story-mapping-engine.md`, not `StoryMappingEngine.md`)
- Commands: simple verb or noun (`analyze.md`, `kickoff.md`, not `run-analysis.md`)
- No numbered prefixes (ordering is determined by logical flow, not filename)

### Markdown Conventions

- Use `###` (H3) for steps within command files
- Use `**bold**` for key terms on first use per section
- Use tables for structured data (schemas, rubric rows, column definitions)
- Use code blocks (` ``` `) for sample outputs and user-facing text templates
- Keep line length under ~120 characters for readability in diffs

---

## Testing and Validation

There are no automated tests. Validation is done via:

1. **Worked examples** (`references/examples.md`): 11 end-to-end scenarios covering key commands. When modifying a command, verify its example still produces valid output.

2. **Schema consistency check**: After any schema change to `coaching_state.md`, verify that:
   - `kickoff.md` initializes the new field
   - All commands that read the field handle the case where it's missing (backward compatibility)
   - Migration notes are added to `SKILL.md`

3. **Cross-command integration**: Key integration points to verify manually:
   - `research` → `prep` (company intelligence flows through)
   - `analyze` → `stories` (new stories extracted from transcripts appear in storybank)
   - `practice`/`mock` → `progress` (drill scores are tracked and surfaced)
   - `feedback` → `calibration-engine` (outcome data updates calibration state)

---

## Commit Message Conventions

Use imperative present tense, scoped to the area of change:

```
feat(commands): add <command-name> command for <purpose>
fix(rubrics): correct seniority calibration for Credibility dimension
refactor(storybank): simplify fit-scoring to 3-level scale
docs(examples): add worked example for negotiate command
chore(skill): update session state schema for v3.1 fields
```

Scopes: `commands`, `rubrics`, `storybank`, `transcripts`, `skill`, `examples`, `docs`, `cross-cutting`

---

## Key Files Quick Reference

| File | When to Edit |
|---|---|
| `SKILL.md` | Adding/removing commands, changing session state schema, updating priority rules |
| `references/commands/<cmd>.md` | Changing command behavior, steps, or output format |
| `references/rubrics-detailed.md` | Changing scoring dimensions, thresholds, or root cause mappings |
| `references/cross-cutting.md` | Changing shared modules (differentiation, gap-handling, signal-reading) |
| `references/examples.md` | Adding or updating worked examples |
| `references/transcript-formats.md` | Adding support for a new transcript source |
| `references/role-drills.md` | Adding a new role archetype or drill set |
| `README.md` | User-facing documentation changes |
| `VERSIONS.md` | Logging new version features |

---

## What NOT to Do

- **Do not create `coaching_state.md`** — it is user-generated and excluded from the repo
- **Do not add executable code** (JS, Python, shell scripts) — this is a markdown-only skill
- **Do not add a `package.json` or build system** — there is nothing to build
- **Do not rename `SKILL.md`** — it is the canonical source; users rename their copy
- **Do not add CI/CD** — there is nothing to deploy
- **Do not introduce numbered step prefixes** in filenames (`01-research.md`) — the ordering is narrative, not sequential
- **Do not break backward compatibility** in `coaching_state.md` schemas without a migration note

---

## Version History Summary

| Version | Theme | Key Additions |
|---|---|---|
| v1 | Foundation | 16 commands, 5D scoring, storybank, 8-stage drills, mock interviews |
| v2 | Coaching Depth | 8 transcript formats, story mapping engine, outcome calibration, challenge protocol |
| v3 | Full Lifecycle | Resume/LinkedIn/pitch/outreach/salary/present/negotiate commands, 28 cross-cutting enhancements |
| v4 (planned) | Intelligence | Voice mode, session replay, calendar awareness |
| v5 (planned) | Platform | UI, platform expansion |
