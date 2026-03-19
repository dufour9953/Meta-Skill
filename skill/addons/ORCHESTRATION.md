# Multi-Agent Orchestration (Optional Add-on)

This guide enables a second IDE agent (such as Google's Antigravity IDE) to
launch, communicate with, and monitor Claude Code sessions. This creates a
multi-agent orchestration pattern where the IDE agent acts as the orchestrator
and Claude Code (with Taproot) acts as the builder.

**This is entirely optional.** Taproot works perfectly on its own in a single
Claude Code terminal. This add-on is for users who want to:

- Monitor Taproot's behavior from an external agent
- Automate multi-terminal workflows (e.g., one terminal builds, another
  researches)
- Test and iterate on their CLAUDE.md skills with automated prompting

## How It Works

```
IDE Agent (Orchestrator)
    |
    |--- launches ---> Claude Code Terminal 1 (Taproot builds)
    |--- launches ---> Claude Code Terminal 2 (Research swarm)
    |
    |--- monitors ---> memory/ filesystem (shared between all)
```

The orchestrator uses terminal commands to send prompts to Claude Code and
reads the output. The filesystem (`memory/`) is the shared communication
bus between all agents.

## Setup

1. Open your IDE (Antigravity, VS Code, Cursor, etc.)
2. Have Taproot's `CLAUDE.md` in your project root
3. Use the IDE's terminal to launch Claude Code

## The Protocol

### Step 1: Launch Claude Code

```bash
cd /path/to/your/project && claude --dangerously-skip-permissions
```

### Step 2: Send the Initialization Prompt

```
Initialize. Read your CLAUDE.md and follow its instructions.
```

Taproot will:
- Create the `memory/` directory (first run) or load existing memory
- Ask for the North Star (first run) or announce loaded context
- Enter Planning Phase or resume the current plan

### Step 3: Monitor via Filesystem

Instead of parsing Claude Code's terminal output, check the filesystem
for artifacts:

```bash
# Check what memory files exist
find ./memory -type f

# Read the North Star
cat ./memory/NORTH_STAR.md

# Check the latest session
ls -t ./memory/sessions/ | head -1

# Check if research findings landed
ls ./memory/research/findings-*.md
```

### Step 4: Research Swarm (Multi-Terminal)

When Taproot offers a Research Swarm:

1. Taproot saves a research brief to `./memory/research/swarm-brief-*.md`
2. Open a SECOND terminal
3. Launch Claude Code: `claude --dangerously-skip-permissions`
4. Point it to the research brief file
5. The research agent works independently, saves findings to
   `./memory/research/findings-*.md`
6. Go back to Terminal 1 and tell Taproot: "research complete"
7. Taproot reads the findings and integrates them into the build plan

### Step 5: Session Handoff

When a session ends, Taproot auto-saves to `./memory/sessions/`. On the
next launch, it picks up exactly where it left off. The orchestrator can
verify this by checking:

```bash
# Verify session was saved
cat ./memory/sessions/$(ls -t ./memory/sessions/ | head -1)

# Check changelog
tail -5 ./memory/CHANGELOG.md
```

## Best Practices

- **Filesystem is ground truth.** Don't rely on parsing terminal output.
  Check `memory/` for what actually happened.
- **One Claude Code session per terminal.** Don't try to multiplex.
- **Let Taproot drive.** The orchestrator monitors and provides input.
  Don't try to override Taproot's protocols.
- **Keep assessments brief.** Before each prompt to Claude Code, note what
  you observe and what you're sending. Keep momentum.

## Constraints

- The orchestrator cannot read Claude Code's TUI output reliably in all
  environments. Filesystem monitoring is always reliable.
- Claude Code's trust prompt requires confirmation on first launch per
  directory. After the first run, it's automatic.
- If Claude Code becomes unresponsive after 3 attempts, restart the session.
