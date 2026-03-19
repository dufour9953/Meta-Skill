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
    |--- launches with -p ----> Claude Code (single-pass build)
    |
    |--- monitors -----------> memory/ filesystem (ground truth)
    |--- monitors -----------> project files (for build artifacts)
```

**Critical: Known Antigravity Terminal Bug.**
`command_status` may permanently return "RUNNING" or "No output" for completed
background commands. This is a confirmed platform bug (Google AI Developers
Forum, March 2026). DO NOT rely on terminal output piping.

**Validated Patterns (tested 2026-03-18):**

1. **`-p` flag with pre-answered prompts (RELIABLE).** Pass the full prompt
   including all clarifications in one shot. Claude Code runs silently and
   produces file artifacts. Monitor the filesystem for results.

2. **Filesystem monitoring is ground truth.** Use `find` or `ls` to check
   for new files in `memory/` and the project directory. Never wait for
   `command_status` output from TUI programs like Claude Code.

3. **For multi-turn (Taproot planning phase), pre-answer all questions**
   in the `-p` prompt so the agent can proceed without interaction.

## Setup

1. Open your IDE (Antigravity, VS Code, Cursor, etc.)
2. Have Taproot's `CLAUDE.md` in your project root
3. Use the IDE's terminal to launch Claude Code

## The Protocol

### Step 1: Launch Claude Code

**Preferred: Direct prompt mode (`-p` flag).** More reliable, avoids TUI
rendering issues with the orchestrator:

```bash
cd /path/to/your/project && claude --dangerously-skip-permissions -p "Initialize. Read your CLAUDE.md and follow its instructions. [Your North Star prompt here]"
```

**Fallback: Interactive REPL mode.** Use when you need multi-turn
conversation:

```bash
cd /path/to/your/project && claude --dangerously-skip-permissions
```

Then send the initialization prompt:
```
Initialize. Read your CLAUDE.md and follow its instructions.
```

**Tips:**
- Use short wait times (5-10s) when checking `command_status`.
- With `-p` mode, monitor the **filesystem** (`memory/`) instead of
  terminal output. The `-p` flag runs silently until completion.


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

### File Ownership Declaration

Before sending any task, declare the file scope to prevent overlap conflicts:

```
FILE OWNERSHIP:
  WILL TOUCH:  [files this agent will modify]
  READ ONLY:   [files it reads but must not modify]
  OFF LIMITS:  [files the orchestrator or another agent owns]
```

No two agents edit the same file simultaneously. The orchestrator always
has priority.

### Structured Prompt Format

When sending tasks to Claude Code, use this template:

```
[TASK]: One-line summary
[SCOPE]: Files to create or modify
[CONSTRAINTS]: What NOT to do
[EXPECTED OUTPUT]: What the finished state looks like
```

### Handoff Verification Checklist

Before terminating any session, verify:

```
HANDOFF CHECK:
  [ ] memory/sessions/ has a new or updated session file
  [ ] memory/CHANGELOG.md has been updated
  [ ] memory/NORTH_STAR.md exists (if first session)
  [ ] All expected output files exist on the filesystem
```

If any check fails, tell the agent: "Run your session-end protocol before
exiting."

## Constraints

- The orchestrator cannot read Claude Code's TUI output reliably in all
  environments. Filesystem monitoring is always reliable.
- Claude Code's trust prompt requires confirmation on first launch per
  directory. After the first run, it's automatic.
- If Claude Code becomes unresponsive after 3 attempts, restart the session.
- Never send a task without declaring file ownership first.
- Never terminate without running the handoff verification checklist.
- Always use the structured prompt format for non-trivial tasks.

