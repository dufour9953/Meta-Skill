# Taproot: Session Intelligence for Claude Code

Every Claude Code session starts from zero. Your AI forgets what it built, what
it decided, and what you told it. You re-explain the same things every time.

Taproot fixes that. One file. No dependencies. No setup.

## Setup (30 seconds)

1. Grab `skill/CLAUDE.md` from this repo.
2. Copy it into the root of any project.
3. Start Claude Code.
4. That's it. Taproot initializes automatically.

On first boot, Taproot asks one question: "What is this project's most
important goal?" Your answer becomes the **North Star** that anchors every
session. Then it enters the Planning Phase.

## What's in this Repo

```
Meta-Skill/
  skill/
    CLAUDE.md           <- THE file. Copy this into your project. That's the skill.
    addons/
      ORCHESTRATION.md  <- Optional. Multi-agent orchestration guide.
  README.md             <- You're reading it.
  LICENSE               <- MIT. Use it however you want.
```

One file is the entire skill. The add-ons are optional power-ups.

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
No manual triggers required. It saves automatically when the task is done,
the conversation ends, or you say "done."

**On session end (visualization):** Taproot also prints a skill tree in your
terminal and generates a standalone HTML visualization you can open in any
browser. See "Skill Graph Visualization" below.

## Memory Architecture

Taproot creates and maintains this structure inside your project:

```
memory/
  NORTH_STAR.md         <- Your project's guiding goal (immutable)
  CHANGELOG.md          <- One-line log per session (append-only)
  skill-graph.json      <- Skill ecosystem data (feeds visualization)
  skill-graph.html      <- Interactive visualization (open in browser)
  sessions/             <- Full session records (decisions, work, handoff)
  directives/           <- Permanent rules ("always do X", "never do Y")
  skills/               <- Auto-generated reusable templates and checklists
  plans/                <- Build plans (versioned, approved by user)
  research/             <- Research swarm briefs and findings
```

## Skill Graph Visualization

Every session-end, Taproot maps your growing skill ecosystem:

**In the terminal:** An ASCII tree showing all skills and connections.

```
🌳 SKILL ECOSYSTEM
═══════════════════
Taproot (meta-skill) ──┬── Session Logger
                       ├── Gap Detector ──── Pattern Amplifier
                       ├── Directive Extractor
                       └── North Star Keeper

Skills: 6 | New this session: 1 | Gaps detected: 0
📊 Interactive view: open ./memory/skill-graph.html in your browser
```

**In the browser:** Open `memory/skill-graph.html` (just double-click it).
A self-contained D3.js visualization with force-directed layout, dark
background, and warm/cool color coding for meta-skills vs. specific skills.
No server required.

As Taproot generates more skills over time, the graph grows organically,
giving you a bird's-eye view of your entire skill ecosystem.

## The Dual Engine: Skill Generation

Taproot generates skills from two sources:

### Magnetic: Gap Detection (catches problems)

If you correct the same mistake twice across sessions, Taproot creates a skill
that prevents it from happening again. Includes the rule, context, and a
concrete fix template.

**Trigger:** 2+ corrections of the same type across sessions.

### Electric: Pattern Amplification (accelerates what works)

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
