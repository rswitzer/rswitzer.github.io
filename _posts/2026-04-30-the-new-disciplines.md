---
layout: post
title: "The New Disciplines: Engineering in a World That Got Flipped Overnight"
date: 2026-04-30
categories: []
tags: []
---

If you've been writing software professionally for ten or twenty years, the last eighteen months have been disorienting. Your instincts didn't disappear. Pattern recognition built from a thousand bugs, a feel for when an abstraction is wrong, the muscle memory of typing out a function before your conscious mind has caught up. All of that is still there. What changed is the leverage. The job is the same job. The work is different work.

I spend a lot of time inside enterprises right now, and I see the same scene play out: a leadership mandate to "use AI more," a procurement decision between Claude Code, GitHub Copilot, and Cursor, a flurry of activity, and six months later... not much has changed. Throughput hasn't doubled. Quality hasn't improved. A few developers swear by their tool of choice; others quietly turn it off. The mandate gets renewed and the cycle starts again.

The problem isn't the tool. The problem is that nobody learned how the tool actually works.

This post is for engineers who feel that gap. You're not skeptical of AI. You've used it. You're skeptical of the hype, and you're frustrated that your team's investment isn't compounding. Good. That skepticism is the right starting point. Let's talk about what's actually changed, what to learn, and where to put your energy on Monday morning.


## Why "Vibe Coding" Is Failing Enterprises

Vibe coding is the practice of describing what you want, accepting whatever the model produces, and shipping it if the tests pass. It works for prototypes. It catastrophically fails in enterprise codebases, and the reasons are structural, not cultural.

Three failures, every time:

1. **Inconsistency.** Eight developers each prompt the agent slightly differently, get back eight subtly different solutions to the same shape of problem, and merge them all. Six months later, you have eight idioms for HTTP retry, four error-handling philosophies, and a codebase that pattern-matches against itself badly the next time an agent reads it.
2. **No validation.** The model said it works. The tests pass. Nobody actually read the diff. The bug ships, and the engineer who "wrote" it can't debug it because they never engaged with the logic.
3. **Drift.** Without explicit guardrails, agents make plausible local choices that violate global constraints. They reach for a library you've banned. They write to a database directly when your architecture says go through the service layer. Each instance is small. Together they erode the codebase.

None of these are model failures. They're system-design failures. The agent did what it was asked. Nobody told it what *not* to do.


## How Agentic AI Actually Works

To use these tools well, you have to understand two things: the agentic loop, and the context window. Most of the people I work with have a fuzzy intuition about both, but no operational model. Let's fix that.

### The Agentic Loop

A modern coding agent isn't a fancy autocomplete. It's a loop:

```
  [User intent]
       |
       v
  [Model reasons] <----------+
       |                     |
       v                     |
  [Need a tool?] -- yes --> [Call tool] --> [Result into context]
       |
       no
       v
  [Final response]
```

The model doesn't "know" your codebase. At each step, it decides what it needs (reading a file, running a test, searching for a symbol) and the result of that action gets appended to its context. Then it reasons again. The loop continues until the model decides the task is complete or it hits a limit.

This has implications most teams haven't internalized:

- **Every tool call is a chance to give the agent better information, or worse information.** A flaky test that returns gibberish poisons the loop. A linter that surfaces real issues sharpens it.
- **The agent's "memory" is whatever is currently in the context window.** It does not remember the last conversation. It does not remember yesterday. It remembers what's loaded right now.
- **Time-to-feedback matters.** A test suite that takes ten minutes to run is a ten-minute interruption in the agent's reasoning. Fast, deterministic feedback loops compound.

### The Context Window

The context window is the working memory the model has access to during a single task. Everything the agent "knows" in this moment lives there: your prompt, the files it has read, tool outputs, conversation history, and any standing instructions.

The window has a fixed size. When it fills up, things get evicted or compacted, and quality degrades. This is why two superficially similar tasks can produce wildly different results: one of them was given a clean, focused window with the right files; the other was 80% full of irrelevant junk before the work even started.

Different models have different window sizes and different attention profiles. Larger windows aren't automatically better. A model can get lost in a 200K-token window if half of it is noise. The skill isn't filling the window. The skill is **curating** it.

### Models Are Not Interchangeable

A fast, cheap model is great for narrow, well-scoped tasks: rename this variable, write this test, format this file. A larger reasoning model earns its cost on tasks that require holding multiple constraints simultaneously: refactor this module, design this API, debug this race condition.

Teams that use one model for everything are either overpaying or underperforming. Teams that match the model to the task, often inside the same workflow, get dramatically better economics.


## The New Disciplines

This is where vocabulary matters. The industry has settled, more or less, on three terms that describe three different jobs. They overlap, but they're distinct, and engineers who confuse them under-invest in two of the three.

### Prompt Engineering

The immediate instruction. "Refactor this function to use async/await." "Write a test for the edge case where the user is null."

Prompt engineering used to be most of the conversation. It's now the smallest part of the job. Models are good enough that a clear, specific prompt usually suffices. If you find yourself spending a lot of time crafting the perfect prompt, your effort is probably misallocated. The leverage is upstream.

### Context Engineering

Deciding what the model sees when it works. Which files are loaded. Which documentation is in scope. Which conventions are stated explicitly. Which past decisions are surfaced.

Context engineering is where most of the quality gap lives. Two teams using the same tool with the same prompts will produce wildly different output if one of them has invested in:

- A `CLAUDE.md` (or equivalent for your tool) at the root of the repo that states architectural conventions, banned libraries, testing expectations, and domain terminology.
- Curated reference docs the agent can pull in on demand.
- Clean, idiomatic code that the agent can pattern-match against.
- Explicit examples of "we do it this way, not that way."

This is the work most enterprises skip, and it's the work with the highest ROI.

### Harness Engineering

Designing the system the agent operates inside. The hooks, the validation gates, the subagents, the tool integrations, the CI checks, the rollback mechanisms.

If context engineering is "what does the agent know," harness engineering is "what is the agent allowed to do, and what happens when it tries." This is the discipline that turns a useful coding assistant into a reliable team member. It's also the most underdeveloped at every enterprise I've worked with.

A team without a harness is trusting vibes. A team with a harness is running a system.


## The Primitives: Hooks, Skills, and Agents

These are the building blocks you use to do the work above. Each maps onto one of the three disciplines.

### Hooks (Harness Engineering)

Hooks are deterministic checkpoints that fire at specific points in the agent's loop: before a tool runs, after a file is edited, when a session starts. They are not polite suggestions to the model. They are code that runs, and the agent has to live with the result.

A trivial example: every time the agent edits a TypeScript file, run the type checker and the linter. Reject the edit if they fail.

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "npx tsc --noEmit && npx eslint $CLAUDE_FILE_PATHS"
          }
        ]
      }
    ]
  }
}
```

This single hook eliminates an entire category of garbage commits. The agent cannot ship code that doesn't compile, because the hook makes it impossible. You haven't asked the model to be careful. You've made carelessness fail loudly.

Good hooks to consider:

- Format-on-write
- Lint-on-write
- Block edits to sensitive paths (`/infra`, `/secrets`, generated files)
- Run affected tests after a change
- Log every shell command for audit

Hooks are pure harness engineering: they are the rails the agent runs on.

### Skills (Context Engineering)

Skills are packaged expertise. A folder containing instructions, examples, and sometimes scripts, which the agent loads when relevant. Think of a skill as "the team's knowledge about how to do X, written down once, available everywhere."

A `SKILL.md` for your team's database migration workflow might describe the conventions, the commands, the gotchas, and the review checklist. When a developer asks the agent to write a migration, the skill loads, and the agent inherits your team's institutional knowledge instead of inventing its own.

```markdown
---
name: db-migrations
description: Use when creating, modifying, or reviewing database migrations.
  Triggers on phrases like 'migration', 'schema change', 'alter table',
  or file references under /migrations.
---

# Database Migrations

## Conventions
- All migrations are reversible. Every `up` has a tested `down`.
- Never drop a column in a single migration. Two-phase: deprecate, then drop.
- Index changes go in their own migration, never mixed with schema changes.

## Workflow
1. Generate migration: `npm run db:new <name>`
2. Write `up` and `down`. Both must be idempotent.
3. Test the round-trip locally before pushing.
4. Tag the PR with `migration` so the on-call DBA reviews it.
```

Skills are context engineering: they shape what the agent knows, on demand, scoped to the task.

### Agents and Subagents (Both)

A subagent is a specialized agent invoked from within another agent's loop. It has its own context window, its own focused instructions, and it returns only its conclusion to the parent. The parent's context stays clean.

Example: your main agent is implementing a feature and needs to understand how a legacy module works. Instead of dumping 4,000 lines of code into the main context (and degrading every subsequent decision), it dispatches a "code explorer" subagent that reads the legacy module and returns a 200-word summary. The main agent stays focused. The work gets done.

Subagents straddle both disciplines: defining what they do is context engineering; defining when and how they're invoked is harness engineering.


## Brownfield Cleanup Is the Highest-Leverage AI Investment

Here's the thing nobody wants to hear: the best thing you can do to make your AI tooling work better is **clean up your codebase**.

Agents pattern-match. They read your code, infer the conventions, and produce more of the same. If your codebase is consistent, well-organized, and idiomatic, the agent extends it correctly. If your codebase is a graveyard of half-finished refactors, three competing styles, and dead code, the agent confidently produces more of the mess.

This is the opposite of the order most teams attempt. They want to point the agent at the legacy mess and have it clean things up. That works at the margins. It does not work at scale, because the agent has no anchor for what "good" looks like in your context.

A more productive sequence:

1. **Pick one module or domain.** Don't boil the ocean.
2. **Establish the patterns by hand or in tight collaboration with the agent.** Two or three reference implementations done well.
3. **Document them.** In a `CLAUDE.md`, in skills, in architecture decision records.
4. **Now point the agent at the rest.** With pattern anchors and explicit guidance, it can extend the cleanup at scale.

Your codebase is the single largest piece of context any agent will ever load. Investing in its quality is investing directly in the quality of every future AI-assisted change.


## We Are Still Professional Developers

Here is where I want to push back on the discourse, gently. The narrative that "AI writes the code now" is half-true and badly framed. Yes, the typing has shifted to the model. But typing was never the job. The job was, and is:

- Understanding the problem precisely enough to specify it
- Choosing constraints that make the system robust
- Validating that the work actually solves the problem
- Owning the result

What changed is the medium. We used to express our judgment in code. We now express it in **guardrails and descriptiveness**: in the conventions we write down, the hooks we wire up, the skills we package, the contexts we curate, the validation we insist on. The agent does the keystrokes. We design the system that makes those keystrokes correct.

This is not less engineering. In some ways it is more. The discipline of being explicit is real work that most teams have avoided for decades. Writing down what was previously tribal knowledge. Codifying what was previously taste. That work is now the work.

The people I see succeeding with these tools are not the ones who type the cleverest prompts. They are the ones who treat the agent as a powerful, fast, senior teammate who needs context, needs guardrails, and needs to be checked. They test as they go. They validate every diff. They keep their codebase clean so the agent stays sharp. They are, in every sense that matters, still professional developers.

The floor moved. The craft didn't.


## Where to Start 

If you take one thing from this post: pick a single repo this week and add three things.

1. A `CLAUDE.md` (or your tool's equivalent) at the root that states your conventions, your stack, your banned patterns, and your testing expectations. Two pages, no more.
2. One hook that enforces a thing you actually care about: lint, type check, test coverage, whatever. Make it impossible to ship code that violates it.
3. A short audit of one area of the codebase where the patterns are inconsistent. Pick the canonical pattern, document it, and start the cleanup.

That is a week of work. Slow down to speed up. It will compound for the rest of the year. And it is an order of magnitude more valuable than another debate about which tool is best.
