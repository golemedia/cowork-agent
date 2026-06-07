# cowork-agent

A template for building a persistent, self-maintaining AI agent using Claude's Cowork mode and an Obsidian-compatible markdown vault as external memory.

The agent remembers everything across sessions — not because the AI has memory, but because every important decision, plan, and piece of context is written to the vault in real time and read back at the start of every session.

---

## The Problem This Solves

AI sessions compact. As a conversation grows, the model's context window fills and older content gets compressed or lost. In a long working session — the kind where real work actually happens — you can lose important context mid-conversation. Start a new session the next day and you're rebuilding from scratch.

The standard workarounds don't scale: pasting notes into every new conversation is tedious, uploading documents works until the document itself becomes stale, and trying to keep the AI "caught up" at the start of every session wastes the session.

**The vault approach flips this.** Instead of fighting context limits, you make the vault the source of truth. The agent writes to it constantly. At the start of every session, the agent reads it. The conversation is ephemeral; the vault is permanent.

---

## How It Works

```
┌─────────────────────────────────────────┐
│              Cowork Session             │
│                                         │
│  Agent reads vault → works with user    │
│       → writes decisions to vault       │
│       → reads vault next session        │
└─────────────────────────────────────────┘
         ↕
┌─────────────────────────────────────────┐
│           Obsidian Vault (files)        │
│                                         │
│  CLAUDE.md         ← identity + rules   │
│  Brainstorms.md    ← async user input   │
│  progress-log.md   ← session history    │
│  open-questions.md ← unresolved items   │
└─────────────────────────────────────────┘
```

Three things make it work:

**1. CLAUDE.md as identity and operating system.** The agent's name, purpose, rules, and protocols all live here. Every session starts with a full read. This file is what makes the agent consistent — not the AI's training, but the instructions it reads every time.

**2. The Brainstorms file as async input.** Between sessions, you drop ideas, questions, and instructions into `Brainstorms.md`. The agent processes this file at the start of every session: acts on what it can, surfaces what needs discussion, clears what's done. You never lose a thought because you weren't in a session when you had it.

**3. Non-negotiable documentation mandates.** The agent is instructed to write immediately when anything important happens — not to summarize at the end of a session, but to document in real time. The test is simple: if this session ended right now, does the vault have everything needed to continue? If not, write it now.

---

## Quickstart

### 1. Fork or clone this repo

```bash
git clone https://github.com/golemedia/cowork-agent my-agent
cd my-agent
```

### 2. Point Cowork at the folder

In Claude's Cowork mode, create a new project and select the folder you just cloned as the workspace.

### 3. Set up the Instructions field

Open `meta/cowork-instructions-field.md` and paste its contents into the Cowork project's **Instructions** field. Replace `[AGENT_NAME]` with your agent's name.

### 4. Start the session — the agent does the rest

On first run, the agent detects that the vault is uninitialized and walks you through a guided conversation to define:
- The agent's name and purpose
- Your working style and priorities
- Any tools or integrations it should know about
- Any domain-specific rules

It then populates `CLAUDE.md` and the supporting vault files with your answers. After that, every subsequent session starts with the Startup Ritual — the agent reads the vault and tells you where things stand before you've typed a word.

---

## Vault Structure

```
cowork-agent/
├── CLAUDE.md                          ← Agent identity + all operating rules
├── Brainstorms.md                     ← Async input — processed every session
├── progress/
│   └── progress-log.md                ← Session-by-session narrative
├── roadmap/
│   └── open-questions.md              ← Unresolved questions
├── meta/
│   └── cowork-instructions-field.md   ← Ready-to-paste Cowork setup
└── templates/
    ├── progress-entry.md
    └── open-question.md
```

---

## The Non-Negotiable Mandates

These are baked into `CLAUDE.md` and should not be removed during customization. They are what makes the vault useful:

**Startup Ritual** — The agent always reads the vault before responding. No exceptions.

**Real-Time Documentation** — Important decisions, plans, and context are written immediately, not summarized at session end.

**Brainstorms Protocol** — The async input file is always processed at session start.

**Session Close Protocol** — "Update and close" triggers a full vault write before the session ends.

**Vault Accuracy** — Stale or incorrect information is corrected immediately when discovered.

---

## Customization

Everything in `CLAUDE.md` between the mandates is yours to customize. During the initialization conversation, the agent will populate:

- Agent name and purpose
- Domain and working context
- Standing priorities
- Known integrations and tools
- Domain-specific hard constraints

You can also edit `CLAUDE.md` directly at any time. The agent re-reads it at the start of every session.

---

## Multi-Agent Version

If you need a coordinator managing multiple specialized agents across different projects, see [cowork-overseer](https://github.com/golemedia/cowork-overseer). That template extends this pattern with:

- An Overseer coordinator that maintains awareness across all projects
- Sub-agent vaults for each project, each with their own identity
- An identity firewall preventing sub-agents from absorbing the coordinator's instructions
- A one-way dispatch channel from Overseer to sub-agent Brainstorms files
- Cross-project pattern recognition

---

## Obsidian Integration

The vault is plain markdown and works as an Obsidian vault out of the box. Open the folder in Obsidian to get the graph view, backlinks, and search across all your agent's memory. The agent follows Obsidian's wiki-link conventions, so links resolve correctly in both the app and the agent's context.

---

## Inspiration

This system grew out of frustration with losing context in long AI working sessions. The core insight — using a markdown vault as external persistent memory — was inspired by Obsidian's approach to knowledge management. The agent-vault pairing makes something that behaves like a persistent collaborator rather than a stateless tool.
