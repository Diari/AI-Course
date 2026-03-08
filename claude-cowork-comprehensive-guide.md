# The Complete Guide to Claude Cowork

A comprehensive, de-duplicated reference for getting maximum value from Claude Cowork — covering setup, context architecture, skills, plugins, connectors, scheduled tasks, safety, and advanced workflows.

---

## What Is Cowork?

Cowork is an agentic desktop tool built into the Claude Desktop app. It gives Claude direct read/write access to folders on your computer, the ability to execute multi-step tasks autonomously, and the capacity to coordinate parallel sub-agents — all without a terminal or command line.

It launched January 12, 2026. Availability expanded to Pro subscribers on January 16, Team and Enterprise on January 23, and Windows (x64) on February 10. On February 24, Anthropic shipped a major enterprise update with new plugins, connectors, and admin controls.

Cowork is built on the same agentic architecture as Claude Code (Anthropic's terminal-based coding tool), but rebuilt for non-technical knowledge workers.

**Cowork is still a research preview.** Anthropic is explicit: agent safety is under active development. Treat it accordingly.

### How Cowork Differs from Chat and Claude Code

| Mode | What It Is | Who It's For |
|------|-----------|-------------|
| **Chat** | Prompt-response conversation. You ask, Claude answers. You're always in the loop. | Everyone — brainstorming, writing, answering questions |
| **Claude Code** | Terminal-based agentic developer tool. Writes code, executes commands, manages git repos. | Developers |
| **Cowork** | Autonomous task execution through a visual interface. Claude reads files, makes a plan, breaks work into subtasks, executes in a sandboxed VM, and delivers finished files. | Non-technical knowledge workers — PMs, marketers, operators, founders |

The key distinction: Chat is prompt-response. Cowork is task delegation. You describe an outcome, step away, and come back to completed work.

### How It Works Under the Hood

Cowork runs in a lightweight Linux virtual machine (VM) on your computer using Apple's Virtualization Framework on macOS. This provides controlled file and network access while keeping code execution isolated from your main operating system. Claude can make real changes to the files you share, but the VM provides a security boundary.

### Requirements

| Requirement | Details |
|-------------|---------|
| **Platform** | macOS (universal) or Windows (x64 only). No arm64 Windows, no mobile, no web |
| **Subscription** | Any paid plan: Pro ($20/mo), Max 5x ($100/mo), Max 20x ($200/mo), Team ($25+/seat/mo), Enterprise (custom) |
| **Desktop App** | Latest version from [claude.com/download](https://claude.com/download) |
| **Internet** | Active connection required throughout the session |
| **App must stay open** | Closing the desktop app kills the session. Sleep is fine; quitting is not |

### Key Capabilities

- **Professional outputs**: Polished Excel spreadsheets with working formulas, PowerPoint presentations, formatted Word documents, and more
- **Long-running tasks**: Complex, multi-step work without conversation timeouts or context limits
- **Scheduled tasks**: Recurring automation that runs on a cadence you choose
- **Sub-agent coordination**: Parallel workstreams for complex tasks
- **File system access**: Direct read/write to folders on your computer
- **Deletion protection**: Claude requires explicit permission before permanently deleting files

---

## Initial Setup (30 Minutes)

### Step 1: Install and Access Cowork

1. Download the Claude Desktop app from [claude.com/download](https://claude.com/download).
2. Sign in with your paid account.
3. Open the app. Find the mode selector at the top and click the **Cowork** tab.

### Step 2: Create Your Working Folder

Create a dedicated folder for Cowork rather than pointing it at your entire Documents folder. This limits blast radius if something goes wrong.

```
~/Claude-Workspace/
├── context/          # Your standing context files
├── projects/         # Active project folders
│   ├── client-a/
│   └── client-b/
└── outputs/          # Where Claude delivers finished work
```

**Back up first.** Before your first real task, back up anything in the folders you plan to share.

**Cloud sync tip:** Cowork is desktop-only with no built-in sync. If you work across machines, put your workspace in a cloud-synced folder (iCloud Drive, Dropbox, OneDrive) so your files stay consistent across devices.

### Step 3: Create Your Context Files

This is the highest-leverage setup step. Create a folder called `Claude Context` (or `00_Context` so it sorts first). Add three Markdown files:

**about-me.md** — Your professional identity. Not your resume. What you actually do day-to-day, who you serve, what your current priorities are, and one or two examples of your best work.

```markdown
# About Me

## Role & Responsibilities
- [Name, title, company]
- [What you do day-to-day]
- [Key stakeholders you work with]
- [What success looks like in your role]

## Domain Context
- [Industry/sector specifics]
- [Key terminology or frameworks you use]
- [Tools and platforms in your workflow]

## Example Work
[Paste 1-2 examples of output you're proud of — this gives Claude a concrete
reference for quality and style.]
```

**brand-voice.md** — How you communicate. Tone, vocabulary, phrases you use, phrases you hate, and two to three paragraphs of your actual writing as reference.

```markdown
# Communication Style

## Tone
- [e.g., Direct and concise. No filler. Technical when warranted.]
- [Phrases you use naturally]
- [Phrases that sound wrong to you]

## Writing Samples
[Paste 2-3 short examples of your actual writing.]

## Anti-patterns
- [e.g., Never use "leverage" as a verb]
- [e.g., Don't open with "I hope this email finds you well"]
```

**working-preferences.md** — How you want Claude to behave. Collaboration rules, output format defaults, quality standards, and things to avoid.

```markdown
# How I Want Claude to Work

## Process
- Always ask clarifying questions before starting non-trivial tasks
- Show me your plan before executing
- Save outputs as [.docx / .xlsx / .md — your preference]

## Output Style
- [Short vs. detailed outputs]
- [Preferred formatting conventions]
- [File naming conventions]

## Guardrails
- Never delete files without explicit confirmation
- Never modify files outside the designated output folder
- Flag assumptions explicitly before acting on them
```

**The compounding effect:** These files get better over time. After every session where Claude's output missed the mark, update the relevant context file. Each refinement improves every future session.

### Step 4: Set Global Instructions

Go to **Settings → Cowork → Edit** next to Global Instructions. These load before everything else — before your files, before your prompt, before Claude even looks at your folder.

Example:

```
I'm [Name], [Role] at [Company]. I work on [domain].

Communication: Direct, concise, no filler. Default to .md for drafts, .docx for deliverables.

Process: Always ask clarifying questions before starting complex tasks. Show your plan. Explain assumptions.

Quality bar: Every deliverable should be client-ready without editing. If confidence is low, say so.

Safety: Never delete files without my explicit approval. Flag any destructive actions before executing.
```

Keep this concise. Global instructions load every session, consuming context window.

### Step 5: Set Folder Instructions

Folder Instructions are project-specific. When you select a folder in Cowork, Claude reads and can update these automatically.

Example for a client project folder:

```
Client A is a Series B fintech company. Brand voice: professional but approachable.
Key contacts: [names]. Current priorities: [list].
Use their terminology: "members" not "users", "platform" not "app".
```

**The three-layer model:** Global Instructions set universal behavior. Folder Instructions add project context. Your prompt specifies the task. Each layer is more specific than the last.

---

## Context Architecture (Advanced)

### Build a _MANIFEST.md for Working Folders

For folders with more than ~10 files, create a `_MANIFEST.md` that tells Claude which documents are the source of truth, which subfolders map to which domains, and what to skip entirely.

Structure it in three tiers:

- **Tier 1 (Canonical):** Source-of-truth documents Claude must read first — brand guidelines, project brief, current strategy
- **Tier 2 (Domain):** Subfolders mapped to specific topics. Claude only loads these when the task touches that domain
- **Tier 3 (Archival):** Old drafts, superseded versions, reference material. Claude ignores these unless explicitly asked

The underscore prefix keeps it sorted to the top of your folder.

### Scope Context Deliberately

Claude's context window is enormous (over a million tokens on Opus 4.6), but bigger context doesn't mean better output. The more irrelevant files Claude reads, the more noise enters its reasoning.

Add to your Global Instructions:

```
When starting any task, look for _MANIFEST.md first. Load Tier 1 files. Only load
Tier 2 files when the task explicitly touches that domain. Never load Tier 3 files
unless I specifically ask.
```

For subagents, scope even tighter: give each subagent only the minimum context it needs for its specific subtask.

---

## Five Core Features

### 1. File System Access

Claude reads and writes files in the folder you select. No uploads or downloads — it works directly in your environment.

This means Claude can read your old reports to match formatting, pull data from last month's spreadsheet, reference brand guidelines mid-task, and save deliverables directly to the right folder.

### 2. AskUserQuestion

When Claude needs more information, it stops and generates a structured form — multiple-choice questions, specific options — rather than guessing and producing polished garbage.

Use this as the starting point for every non-trivial task:

```
I want to [YOUR TASK] so that [WHAT GOOD LOOKS LIKE].
First, read all uploaded files completely before responding.
DO NOT start executing yet. Ask me clarifying questions to refine the approach.
Only begin work once we've aligned.
```

### 3. Plugins

Pre-built specialist packs that bundle skills, slash commands, and sub-agent configurations for specific job functions.

### 4. Instructions (Global & Folder)

Permanent memory that loads automatically at the start of every session, eliminating the need to re-explain yourself.

### 5. Connectors

Live integrations with Slack, Drive, Notion, Gmail, and 50+ other tools via MCP (Model Context Protocol). Connect once, then Claude can reference live data from these tools in every session.

---

## Skills

### What Is a Skill?

A skill is a folder containing a `SKILL.md` file — a markdown document with instructions that Claude can access whenever it needs them. No code. No API. No server. If you can write documentation, you can build a skill.

Skills follow the [Agent Skills](https://agentskills.io) open standard, meaning the same skill works in Claude Code, Claude.ai, the Claude API, and increasingly in third-party tools like Cursor, Windsurf, Codex CLI, and Gemini CLI.

### How Claude Decides When to Use a Skill

Skills use a **progressive disclosure system** with three levels:

| Level | What Loads | When |
|-------|-----------|------|
| **1. Frontmatter** | Name and description (~50–100 tokens per skill) | Always loaded into Claude's system prompt |
| **2. Markdown body** | Full instructions | Only after Claude decides the skill is relevant |
| **3. Supporting files** | Templates, examples, reference docs, scripts | Only when the specific task requires them |

This means you can have dozens of skills installed without overwhelming Claude's context window.

**Important:** Claude only consults skills for tasks it can't easily handle on its own. Simple, one-step requests may not trigger a skill even if one exists.

### Three Ways to Build a Skill

**Method 1: Write Skill Instructions (5 minutes)**

Best for simple skills. Go to **Settings → Capabilities → Skills → Add**. Select "Write skill instructions." Fill in name, description (the trigger), and instructions.

**Method 2: Build Through Conversation (30 minutes)**

Best for complex workflows. Start a conversation with Claude. Describe your workflow. Walk through a real example. Correct output. Refine. When output matches your standards, say: "Turn this into a skill."

Claude reads its built-in skill-creator skill and structures everything into a properly formatted `SKILL.md`. When finished, you can download the file and import it from Settings, or use the "Copy to skills" button.

**Method 3: The Skill-Creator Pipeline (1–2 hours)**

Best for production-grade skills. Enable the skill-creator skill in **Settings → Capabilities**. Then say: "Help me create a skill for [your workflow]."

This walks you through a complete development pipeline: understanding intent, writing instructions, generating test cases, running evaluations with a grader subagent, blind A/B comparisons between versions, description optimization for trigger accuracy, and final packaging.

### SKILL.md File Structure

#### Part 1: YAML Frontmatter

```yaml
---
name: expense-report-generator
description: Create formatted expense reports from receipt images. Use when asked to process receipts, categorize expenses, or generate expense summaries.
---
```

Key fields:

| Field | Required | Description |
|-------|----------|-------------|
| `name` | No (uses directory name if omitted) | Display name and /slash-command. Lowercase, hyphens, max 64 chars |
| `description` | Recommended | Most critical 200 characters. Claude uses this to decide whether to load the skill |
| `disable-model-invocation` | No | Set `true` to prevent auto-loading. Manual invoke only |
| `user-invocable` | No | Set `false` to hide from / menu. Background knowledge only |
| `allowed-tools` | No | Tools Claude can use without asking permission when skill is active |
| `context` | No | Set to `fork` to run in a subagent |
| `agent` | No | Which subagent type when `context: fork` is set |

#### Part 2: Instruction Body

Written in plain markdown. Best structure:

- **Overview:** 1–2 sentences on what the skill does
- **Process:** Step-by-step instructions in imperative form
- **Output format:** Exact structure of the deliverable, with a template if possible
- **Examples:** At least one example input and expected output (the single most effective way to improve quality)
- **Constraints:** Rules, guardrails, edge case handling

#### Part 3: Supporting Files

```
my-skill/
├── SKILL.md              # Core instructions (required)
├── reference.md          # Detailed documentation
├── templates/
│   └── report-template.md
├── examples/
│   └── sample-output.md
└── scripts/
    └── process.py
```

Keep `SKILL.md` under 500 lines. Move detailed reference material to separate files and link to them.

### Where Skills Live

| Location | Path | Applies To |
|----------|------|-----------|
| Enterprise | Managed settings | All users in your organization |
| Personal | `~/.claude/skills/<skill-name>/SKILL.md` | All your projects |
| Project | `.claude/skills/<skill-name>/SKILL.md` | This project only |
| Plugin | `<plugin>/skills/<skill-name>/SKILL.md` | Where plugin is enabled |

Higher-priority locations win when names conflict: enterprise > personal > project.

### Writing Descriptions That Trigger Correctly

The description is everything. If it's vague, Claude misses the trigger. If it's specific, Claude loads it at the right moment.

**The WHEN / WHEN NOT Pattern:**

- Bad: "Helps with documents."
- Good: "Apply Acme Corp brand guidelines to presentations and documents, including official colors, fonts, and logo usage."
- Best: "Stakeholder context for Project Alpha when discussing product features, UX research, or stakeholder interviews. Auto-invoke when user mentions Project Alpha, product lead, or UX research. Do NOT load for general stakeholder discussions unrelated to Project Alpha."

**Test with three prompt types:**

1. **Direct match:** Should obviously trigger. Does it?
2. **Edge case:** Related but not perfect match. Triggers appropriately?
3. **False positive:** Should NOT apply. Correctly stays inactive?

### Skill Composability

Skills can't explicitly reference other skills, but Claude uses multiple skills together automatically. A brand-voice skill + a presentation-structure skill + a data-visualization skill = Claude applying your brand colors to a well-structured presentation with properly formatted charts.

**Principle:** Many small, focused skills compose better than one monolithic skill. Keep each skill to one capability. Let Claude handle the orchestration.

### Seven High-Impact Skills to Build First

1. **Brand voice** — Writing style, tone, vocabulary, anti-patterns, and 2–3 writing samples
2. **Output standards** — Default file format, quality bar, uncertainty protocol, formatting rules
3. **Meeting notes** — Raw transcripts → structured summaries with decisions, action items, next steps
4. **Weekly report** — Pull data, compile accomplishments, flag blockers, generate formatted update
5. **Expense processing** — Read receipt images, extract data, build spreadsheet, mark unclear items as VERIFY
6. **Research synthesis** — Multiple sources → structured report with executive summary, findings, evidence quality
7. **Client communication** — Emails, updates, proposals in your voice with appropriate tone per client

### 8 Common Skill Mistakes

1. **Vague descriptions** — "Helps with writing" is useless
2. **Giant monolithic skills** — Split into focused skills that compose
3. **No examples** — Every production skill needs at least one example input/output
4. **Wrong YAML structure** — Metadata fields outside `---` markers, indentation errors
5. **No edge case handling** — Add: "If data is incomplete, flag the gap and ask instead of guessing"
6. **Hardcoded secrets** — Never put API keys or passwords in a SKILL.md
7. **Building without testing** — Use the skill-creator's evaluation pipeline
8. **Stuffing everything into SKILL.md** — Move docs to reference files, output structures to templates

---

## Plugins

### What Plugins Are

Plugins bundle skills, slash commands, MCP connectors, sub-agents, and hooks into role-specific packages. They're the distribution layer for skills.

### Plugin Structure

```
my-plugin/
├── .claude-plugin/
│   └── plugin.json        # Manifest (name, description, version)
├── skills/                 # Agent Skills with SKILL.md files
│   └── code-review/
│       └── SKILL.md
├── commands/               # Slash commands as Markdown files
├── agents/                 # Custom agent definitions
├── hooks/                  # Event handlers
├── .mcp.json               # MCP server configurations
└── settings.json           # Default settings when plugin is enabled
```

**Important:** Don't put `commands/`, `agents/`, `skills/`, or `hooks/` inside `.claude-plugin/`. Only `plugin.json` goes there.

### How to Install Plugins

1. In Cowork, click the **"+"** button in the chat bar, then **Plugins** to browse the library
2. Or visit [claude.com/plugins](https://claude.com/plugins)
3. Click Install on your chosen plugin
4. Type `/` in any Cowork chat to see available slash commands

### Available Plugins (As of February 2026)

**Core plugins (January 30):** Productivity, Marketing, Sales, Finance, Data Analysis, Legal, Product Management, Customer Support, Enterprise Search, Biology Research, Plugin Management.

**February 24 additions:** HR, Engineering, Design, Operations, Financial Analysis, Investment Banking, Equity Research, Private Equity, Wealth Management. Plus a partner-built Brand Voice plugin by Tribe AI.

### Recommended Starting Stack

- **Productivity** (always on — useful regardless of role)
- **One role-specific plugin** matching your job function
- Add more once you've got the basics dialed in

### Plugin Composability

Plugins are composable — install multiple and use capabilities from all of them in a single task. Example: Install Data Analysis + Sales plugins, then ask Claude to analyze pipeline data and draft personalized follow-up emails.

### Building Custom Plugins

Use the **Plugin Management** plugin to build plugins conversationally. Say: "Help me create a plugin for [your workflow]." Claude walks you through defining skills, slash commands, and configuration. No code required.

For teams: the February 2026 update enables private plugin marketplaces for distributing custom plugins across an organization.

---

## Connectors

### What Connectors Are

Connectors link Claude to external services via MCP (Model Context Protocol). Once connected, Claude can pull live data from these tools during Cowork sessions.

### How to Connect

1. Go to **Settings → Connectors** in Claude Desktop (or visit [claude.ai/settings/connectors](https://claude.ai/settings/connectors))
2. Browse available integrations
3. Click a connector and authenticate

You only do this once. After that, Claude can access live data from that tool in every session.

### Available Connectors (As of February 2026)

Google Drive, Gmail, Google Calendar, Slack, Notion, Asana, Linear, Jira, Monday, ClickUp, Figma, Amplitude, Pendo, Intercom, HubSpot, Close, Clay, ZoomInfo, Fireflies, Microsoft 365, Snowflake, Databricks, BigQuery, Hex, Box, Egnyte, DocuSign, FactSet, MSCI, Harvey, Apollo, Outreach, SimilarWeb, LegalZoom, WordPress, Canva, Ahrefs, Klaviyo, Guru, Benchling, and others.

### Where to Start

Connect whichever tool you use most. Slack, Google Drive, or Notion are the highest-leverage starting points for most knowledge workers.

---

## Scheduled Tasks

### What Scheduled Tasks Are

Tasks that run automatically on a recurring basis or on-demand. You describe the task once, and Claude executes it according to the specified schedule. Scheduled tasks access the same features as regular Cowork tasks, including connected tools, skills, and plugins.

### How to Create Scheduled Tasks

**Method 1 — Via /schedule command:** Type `/schedule` in any task. Claude walks you through setup.

**Method 2 — From Scheduled Tasks page:** Click **"Scheduled"** in the left sidebar → **"+ New task"** → fill in: task name, description, prompt, frequency (hourly, daily, weekly, weekdays, or manual), optional model selection, and optional working folder.

### Managing Scheduled Tasks

Access all tasks via the **"Scheduled"** sidebar option to view, pause, resume, delete, or manually run tasks on-demand. Each scheduled task runs as its own Cowork session with reviewable results.

### Critical Limitation

**Scheduled tasks only run while your computer is awake and the Claude Desktop app is open.** If your computer sleeps or the app closes during a scheduled run, Cowork skips that execution but automatically runs it when your system wakes up or you reopen the app. Notifications alert you to skipped runs.

### High-Value Scheduled Task Examples

- **Monday morning briefing:** Summarize Slack channels, calendar for the week, flag anything needing prep
- **Friday status report:** Pull completed tasks from project management tools, draft a status update
- **Daily competitor tracking:** Research competitors for news, product updates, pricing changes — only save if something's new
- **Weekly research brief:** Summarize industry news relevant to your business from the past 7 days
- **Morning email triage:** Check Gmail for invoices, extract amounts and dates, update expense spreadsheet

### Best Practices for Scheduled Tasks

- Start with low-risk automation (summaries, data compilation)
- Avoid scheduling tasks involving sensitive data, communications, purchases, or irreversible actions
- Review outputs regularly until you trust the workflow

---

## Task Design Best Practices

### Define the End State, Not the Process

Don't tell Claude how to do the work step by step. Tell it what "done" looks like.

- **Bad:** "Help me with my files."
- **Good:** "Organize all files in this folder into subfolders by client name. Use the format YYYY-MM-DD-descriptive-name for all filenames. Create a summary log documenting every change. Don't delete anything. If a file could belong to multiple clients, put it in /needs-review."

Every task prompt should answer three questions: What does "done" look like? What are the constraints? What should Claude do when it's uncertain?

### Always Request a Plan Before Execution

Add to your Global Instructions:

```
Show a brief plan before taking action on any task. Wait for my approval before executing.
```

Cost: an extra 30 seconds per task. Benefit: you never undo a 20-minute autonomous mistake.

### Handle Uncertainty Explicitly

Build uncertainty handling into every task:

```
If a date isn't clear, mark it as VERIFY. If a file could go in multiple folders,
put it in /needs-review. If you're less than 80% confident in a classification,
flag it instead of guessing.
```

### Batch Related Work

Don't run five separate sessions for five related tasks. Run one session with all five — Claude shares context across them, and each task informs the next. Fewer sessions with more tasks per session is almost always better.

### Use Subagents for Parallel Work

For tasks with independent parts, Claude can spin up multiple subagents working simultaneously.

Trigger with: "Spin up subagents to..." or "Work on these in parallel using subagents."

Example: "I'm evaluating four vendors. Spin up subagents to research each one's pricing, support reputation, and integration options. Give me a comparison table."

Subagents work best on Opus 4.6 and consume more tokens. Use them for complex tasks where time savings justify the cost.

---

## Cross-App Workflows

Cowork can pass context between Excel and PowerPoint add-ins. Claude can analyze data in Excel and move a chart directly into a presentation without you switching apps.

**Requirements:** Mac users on Max, Team, or Enterprise plans. Both Claude in Excel and Claude in PowerPoint add-ins must be installed. Windows support isn't available yet.

**Caution:** Data from one application may flow into another during a Cowork session. Avoid working with sensitive information in these add-ins while Cowork is active.

---

## Safety

### Anthropic's Three Protection Layers

1. **Model training:** Claude undergoes reinforcement learning to identify and refuse malicious directives, even when they appear authoritative or urgent
2. **Content classifiers:** Untrusted content entering Claude's context gets scanned to flag potential prompt injection attempts
3. **Deletion protection:** Users must explicitly approve file deletion through a permission prompt before Claude can proceed

### Seven Safety Best Practices

**1. Restrict file access.** Keep sensitive files (financial documents, credentials, personal information) in folders Cowork never touches. Create dedicated working folders rather than granting broad permissions.

**2. Monitor task execution.** Watch for unexpected patterns — unauthorized file/website access or scope creep. If something feels off, stop the task immediately. Monitor the first few runs of any new workflow before stepping away.

**3. Use caution with scheduled tasks.** Start with low-risk automation. Avoid scheduling tasks involving sensitive data, communications, purchases, or irreversible actions.

**4. Limit browser access.** If using Claude in Chrome, restrict access to trusted sites. Web content is the primary prompt injection vector.

**5. Vet plugins and connectors.** Install only verified extensions from official directories. Each extension expands the attack surface.

**6. Mind cross-app data flow.** Be aware that data flows between Excel, PowerPoint, and Claude during sessions. Avoid sensitive information in add-ins while Cowork is active.

**7. Report suspicious behavior.** Contact usersafety@anthropic.com if Claude exhibits unexpected resource access, unrelated discussion topics, or unprompted sensitive information requests.

### Critical Limitations to Know

- **No audit logging:** Cowork activity is not captured in audit logs, Compliance API, or data exports. Do not use Cowork for regulated workloads.
- **No cross-session memory:** Every new session starts completely fresh. Build continuity into files, not memory.
- **Session persistence:** The app must stay open. Closing it kills the task mid-execution with no recovery. Sleep is fine; quitting is not.
- **Usage consumption:** Complex Cowork tasks consume significantly more of your allocation than standard chat. Monitor Settings → Usage.
- **Desktop only:** No mobile, no web version, no cross-device sync.
- **No image generation:** Use dedicated image tools (Gemini Imagen, Midjourney, etc.) for visual art.
- **User responsibility:** You remain fully responsible for all Claude-executed actions, including published content, financial transactions, data modifications, and third-party terms compliance.

### Safety Defaults to Set

Add these to your Global Instructions:

```
- Never delete any files without my explicit confirmation
- Never modify files outside the designated output folder
- Show me your plan before executing any multi-step task
- If you're unsure about any instruction, ask rather than assume
```

---

## Example Use Cases

### Organize a Messy Folder

```
Organize all files in this folder into subfolders by type (receipts, contracts,
notes, images). Use the format YYYY-MM-DD-descriptive-name for all filenames.
Create a summary log documenting every change. Don't delete anything. If a file
could belong to multiple categories, put it in /needs-review.
```

### Client Deliverable from Raw Notes

```
Using the files in /client-a/raw-materials, create a client-ready report. Include:
executive summary, key findings, recommendations, and next steps. Match the format
of the template in /templates/client-report-template.docx. Save to
/client-a/deliverables.
```

### Weekly Research Brief (Scheduled)

```
Every Monday at 7am, research [competitor names] for news, product updates, or
pricing changes. Check [industry publication] for relevant articles. Save a summary
to /weekly-briefings/YYYY-MM-DD-brief.md. Only include items from the past 7 days.
```

### Verification Prompt (First-Time Setup)

```
Read all the files in the context folder. Then tell me:
1. What you know about me
2. How I prefer to work
3. What standing preferences you're aware of

Do not start any other work yet.
```

---

## The Mindset Shift

ChatGPT rewarded prompt engineering — writing the cleverest, most detailed prompts. Cowork rewards **system engineering** — investing in setup so your prompts can be short.

The people thriving with Cowork spent an afternoon building their context architecture (context files, global instructions, folder structure, skills) and now write 10-word prompts that produce client-ready deliverables. The prompt is the least important part of a Cowork session. The context, the structure, the skills, and the constraints you've built around it — that's where output quality comes from.

### Implementation Timeline

**Today (30 minutes):** Create your three context files and set Global Instructions. Run the verification prompt. This alone puts you ahead of most Cowork users.

**This week:** Add a `_MANIFEST.md` to your most-used project folder. Install 2–3 plugins matching your role. Connect your most-used tool (Slack, Drive, or Notion). Set up one scheduled task.

**This month:** Build your first custom skill for your most repeated workflow. Experiment with subagents on a complex research task. Refine your context files based on output quality. Start composing skills into multi-step workflows.

**Ongoing:** Every time Claude produces something you don't like, ask whether it's a prompt problem or a context problem. Nine times out of ten, it's context. Add one line to one file. Permanent fix. Your system gets better every week.

---

## Quick Reference

### Key Locations in Claude Desktop

| Setting | Where to Find It |
|---------|-----------------|
| Switch to Cowork | Mode selector at top of app |
| Global Instructions | Settings → Cowork → Edit |
| Folder Instructions | Select a folder → auto-loaded or manually set |
| Install plugins | "+" button in chat bar → Plugins, or [claude.com/plugins](https://claude.com/plugins) |
| Connect tools | Settings → Connectors |
| Scheduled tasks | "Scheduled" in left sidebar, or type `/schedule` |
| Usage monitoring | Settings → Usage |

### Essential Slash Commands

| Command | What It Does |
|---------|-------------|
| `/schedule` | Create a new scheduled task |
| `/productivity:start` | Review your day and set up task list (Productivity plugin) |
| `/data:explore` | Summarize a dataset and suggest analyses (Data Analysis plugin) |
| `/marketing:draft-content` | Draft marketing content (Marketing plugin) |
| `/sales:call-prep` | Prepare for a sales call (Sales plugin) |

### Prompt Templates

**Standard task opener:**
```
I want to [TASK] so that [OUTCOME].
First, read all relevant files.
DO NOT start executing yet. Ask me clarifying questions to refine the approach.
Only begin work once we've aligned.
```

**Delegating with full autonomy:**
```
[Describe what "done" looks like, constraints, and uncertainty handling.]
Show me your plan. Once I approve, execute fully and save to [location].
```

---

*Sources: [Get started with Cowork — Claude Help Center](https://support.claude.com/en/articles/13345190-get-started-with-cowork), [Use Cowork safely — Claude Help Center](https://support.claude.com/en/articles/13364135-use-cowork-safely), [Schedule recurring tasks in Cowork — Claude Help Center](https://support.claude.com/en/articles/13854387-schedule-recurring-tasks-in-cowork), [Extend Claude with skills — Claude Code Docs](https://code.claude.com/docs/en/skills), [Create plugins — Claude Code Docs](https://code.claude.com/docs/en/plugins), [Agent Skills open standard](https://agentskills.io)*
