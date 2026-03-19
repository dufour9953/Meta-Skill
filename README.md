# Taproot: Session Intelligence for Claude Code

> "Your agent forgets everything. Mine levels up."

Every Claude Code session starts from zero. Your AI forgets what it built, what
it decided, and what you told it. You re-explain the same things every time.

Taproot fixes that. One file. No dependencies. No setup. Your agent remembers
everything, generates its own skills, and levels up with each session.

## Setup (30 seconds)

1. Grab `skill/CLAUDE.md` from this repo.
2. Copy it into the root of any project.
3. Start Claude Code.
4. That's it. Taproot initializes automatically.

On first boot, Taproot runs a quick onboarding: your project's **North Star**
(the most important goal), your **agent's name** (or skip and let it suggest
one), and your agent's **disposition** (Sentinel, Architect, Scholar, or let
it evolve naturally). Then it generates a procedural avatar, creates your
Agent Skill Tree, and enters the Planning Phase.

## What's in this Repo

```
Meta-Skill/
  skill/
    CLAUDE.md               <- THE file. Copy this into your project.
    addons/
      ORCHESTRATION.md      <- Optional. Multi-agent orchestration guide.
  agent-status-page/
    index.html              <- Agent Skill Tree visualization (open in browser)
    avatar.png              <- Generated agent avatar
  README.md
  LICENSE                   <- MIT. Use it however you want.
```

One file is the entire skill. The visualization and add-ons are optional.

## How It Works

Taproot operates in two modes: **Planning** and **Building**.

### Planning Phase (first session)

Before writing any code, Taproot:

1. **Asks clarifying questions** about your project (who it's for, constraints,
   what "done" looks like)
2. **Runs a gap analysis** on its own readiness: what it knows for certain,
   what it's assuming, and what could break downstream
3. **Offers a Research Swarm**: if there are medium-to-low confidence
   assumptions, Taproot generates a structured research brief and prompts you
   to run a second Claude Code terminal for deep research
4. **Proposes a build plan** with specific sessions, deliverables, and tech
   choices
5. **Waits for your approval** before building anything

### Build Phase (every session after plan approval)

Once the plan is approved, each session follows this cycle automatically:

```
Boot -> Load Memory -> Resume Plan -> Build -> Save -> Extract Rules -> Detect Patterns -> Log
```

**On session start:** Taproot reads your North Star, directives, skills, the
last session, and the current plan. It announces what it knows and asks what
to focus on.

**During the session:** You work normally. Taproot tracks decisions,
corrections, and emerging patterns.

**On session end:** Taproot writes a session file capturing decisions, work
completed, plan progress, corrections received, and a cold-start handoff note.
It also prints a skill tree, updates XP, and announces your agent's level.

## Gamification: XP, Rarity, and Agent Classes

Taproot isn't just memory. It's an evolving agent that grows with you.

### XP System

Every skill earns XP based on how it was created and how battle-tested it is:

| Origin | Base XP | Description |
|---|---|---|
| Gap Detection | 50 XP | Born from a repeated mistake |
| Pattern Amplification | 75 XP | Formalized from recurring success |
| Research Swarm | 100 XP | Backed by structured research |

Skills earn bonus XP: +25 for being cross-linked, +50 for being research-backed.
Higher-XP skills get **priority in agent decision-making**. Your most
battle-tested skills carry more weight.

### Skill Rarity

| Rarity | Color | Requirements |
|---|---|---|
| Common | Gray | Base skills, single origin |
| Uncommon | Green | 100+ XP |
| Rare | Purple | 100+ XP and cross-linked to 2+ skills |
| Legendary | Gold | Research-backed, 200+ XP, 3+ cross-links |

### Agent Classes

Your agent's class evolves based on what skills it generates:

| Class | Triggered When | Style |
|---|---|---|
| Sentinel | Majority gap-detected skills | Defensive, catches problems early |
| Architect | Majority pattern-amplified skills | Structural, builds frameworks |
| Scholar | Majority research-backed skills | Knowledge-driven, deep research |

### Agent Levels

| Level | Title | XP Required |
|---|---|---|
| 1 | Seed | 0 |
| 2 | Spark | 100 |
| 3 | Sprout | 250 |
| 4 | Heartwood | 500 |
| 5 | Canopy | 1,000 |
| 6 | Taproot | 2,000 |
| 7 | Old Growth | 3,500 |
| 8 | Grove | 5,000 |
| 9 | Sovereign | 7,500 |
| 10 | Mycelium | 10,000 |

## Agent Skill Tree (Visualization)

Open `agent-status-page/index.html` in any browser (just double-click it).
No server needed.

**Features:**
- Agent profile card with name, class, level, and XP progress bar
- Skill arsenal sorted by rarity (legendary first) with expandable descriptions
- Interactive D3 force-directed graph showing skill relationships
- Activity timeline with timestamps
- Shareable trading card (screenshot-friendly)
- Editable agent name (persisted in localStorage)
- Bioluminescent dark theme with micro-animations

At session-end, Taproot also prints an ASCII skill tree in your terminal:

```
SKILL ECOSYSTEM
==============================
Taproot (meta-skill) --+-- Research Sentinel [LEGENDARY] 200 XP
                       +-- Context Weaver [UNCOMMON] 150 XP
                       +-- Cascade Planner [RARE] 120 XP
                       +-- Memory Curator [UNCOMMON] 100 XP
                       +-- Plan Anchor [COMMON] 75 XP
                       +-- Style Guardian [COMMON] 50 XP

LVL 4 Specialist | 815 / 1000 XP | Class: Architect
```

## Memory Architecture

Taproot creates and maintains this structure inside your project:

```
memory/
  NORTH_STAR.md           <- Your project's guiding goal (immutable)
  agent-identity.json     <- Agent name, class, level, XP, avatar config
  avatar.svg              <- Procedural avatar (or link to custom image)
  CHANGELOG.md            <- One-line log per session (append-only)
  skill-graph.json        <- Skill ecosystem data (feeds visualization)
  skill-graph.html        <- Interactive D3 graph (open in browser)
  agent-skill-tree.html   <- Full Agent Skill Tree dashboard (open in browser)
  sessions/               <- Full session records (decisions, work, handoff)
  directives/             <- Permanent rules ("always do X", "never do Y")
  skills/                 <- Auto-generated reusable templates and checklists
  plans/                  <- Build plans (versioned, approved by user)
  research/               <- Research swarm briefs and findings
```

## The Dual Engine: Skill Generation

Taproot generates skills from two sources:

### Gap Detection (catches problems)

If you correct the same mistake twice across sessions, Taproot creates a skill
that prevents it from happening again. Includes the rule, context, and a
concrete fix template.

**Trigger:** 2+ corrections of the same type across sessions.

### Pattern Amplification (accelerates what works)

At session end, Taproot reflects on work across sessions. If the same pattern,
file structure, or approach keeps appearing, Taproot formalizes it into a
reusable template that loads on every future session.

**Trigger:** Same pattern in 2+ sessions, not yet formalized.

## Optional: Multi-Agent Orchestration

Want to supercharge Taproot? Use an IDE agent (like Google's Antigravity IDE)
to orchestrate multiple Claude Code terminals:

- **Terminal 1:** Taproot builds your project
- **Terminal 2:** Research swarm fills knowledge gaps
- **The IDE:** Monitors both, coordinates handoffs

See `skill/addons/ORCHESTRATION.md` for the full protocol. This is entirely
optional. Taproot works perfectly on its own in a single terminal.

## Constraints

- Memory is append-only. Session files are never deleted.
- North Star is immutable unless you explicitly change it.
- Only the most recent session file loads at boot (keeps context small).
- Gap-fix skills require 2 occurrences OR an explicit request.
- Pattern-amplifier skills require evidence from 2+ sessions.
- If a one-line directive solves it, Taproot writes a directive, not a skill.
- All writes to memory are announced. No silent mutations.
- Never starts building without an approved plan.
- Research Swarm is offered, never forced. You decide.

## License

MIT
