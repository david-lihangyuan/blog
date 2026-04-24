---
layout: post
title: "How to Know If Your AI Agent Is Getting Worse"
date: 2026-04-25
description: "Your AI coding agent might be silently degrading. Here's how to detect it before you waste another week."
author: "Li Hangyuan"
---

*Your AI coding agent might be silently degrading. Here's how to detect it before you waste another week.*

---

## The Cancellation Wave

This week, a blog post titled "Why I Cancelled Claude" hit 735 points on Hacker News with over 400 comments. The author described a pattern many developers recognize: great experience at first, then a slow, confusing decline. Token usage spikes. Quality drops. Support sends copy-paste answers.

What struck me wasn't the complaints — those have been consistent for months. It was the *timing*. The author couldn't pinpoint when things got worse. They just noticed one day that their agent felt different.

This is the core problem: **AI agent degradation is silent**. There's no error message. No version changelog that says "we made your experience 15% worse." You just gradually start feeling like the tool isn't as good as it used to be. And because the decline is gradual, you blame yourself first — maybe my prompts are worse, maybe this project is harder.

## Why Degradation Is Hard to See

Three things make agent quality changes nearly invisible:

**1. No baseline.** When you start using an AI coding agent, you don't record metrics. You don't track how many tool calls it takes to complete a task, or what percentage of its edits are surgical versus full-file rewrites. So when things change, you have nothing to compare against.

**2. Multiple variables shift at once.** Your projects change. Your prompts change. The model version changes (sometimes without notice). The system prompt changes. Any of these could explain a quality shift. When everything moves at once, attribution is impossible without instrumentation.

**3. Confirmation bias works both ways.** When you're enthusiastic about a tool, you attribute good outcomes to the tool and bad outcomes to your own mistakes. When you're frustrated, the reverse happens. Neither is reliable.

## What Degradation Actually Looks Like in Data

Anthropic's April 23 postmortem revealed three simultaneous bugs in Claude Code:

- **Reasoning effort was silently reduced** for some users — the model was thinking less hard without anyone knowing.
- **A cache-clearing bug** caused the model to "forget" conversation context, re-reading entire codebases and burning tokens.
- **A system prompt change** degraded quality across the board.

These are three different failure modes. One affects quality. One affects cost. One affects both. And they were all happening at the same time, making each one harder to isolate.

The CC-Canary project (open-sourced this week) approaches this problem systematically. It reads Claude Code's local session logs and computes metrics like:

- **Read:Edit ratio** — how many files the agent reads before making each edit. A declining ratio means the agent is "shooting from the hip" more.
- **Reasoning loops per 1,000 tool calls** — phrases like "let me try again" or "actually, wait." More loops = more confusion.
- **Write share of mutations** — is the agent doing surgical edits, or rewriting entire files? The latter is a sign of declining precision.
- **Tokens per user turn** — the raw cost of each interaction.

These aren't perfect metrics. But they're *something* — which is infinitely more than what most users have.

## The Three Layers of Agent Monitoring

Based on 400+ continuous sessions running an AI agent, I've found that monitoring works at three layers:

### Layer 1: Session-Level Metrics (Cost)

This is what most people think of first: how many tokens am I spending?

But raw token count is misleading. A productive session might use more tokens than a stuck one — because the agent is reading more files, thinking more carefully, making more precise edits. **Cost per unit of progress is what matters, not cost per session.**

The practical version: at the end of each session, can you describe what was accomplished in one sentence? If you can't, that session was probably wasteful regardless of token count.

### Layer 2: Behavioral Metrics (Quality)

This is where CC-Canary's approach is valuable. Track:

- **Edit precision**: Is the agent making targeted changes, or rewriting large blocks?
- **Self-correction frequency**: How often does the agent undo or redo its own work?
- **Tool call patterns**: Is the agent reading relevant files before editing, or guessing?

You don't need a specialized tool to notice these. Pay attention to the agent's thinking blocks (if visible). If you see "let me try a different approach" three times in one turn, that's signal.

### Layer 3: Structural Monitoring (Drift)

This is the layer most people miss entirely. It's not about individual sessions — it's about trends over weeks.

Questions to ask:
- Do tasks that used to take one session now take three?
- Are you writing longer, more detailed prompts to get the same results?
- Has the agent started suggesting workarounds where it used to solve problems directly?

One approach I use: keeping a running log of what each session accomplished (one line per session). After a week, you can scan it and see patterns. If Wednesday's sessions all say "debugging the fix from Tuesday," something shifted.

## What You Can Actually Do

**1. Start recording baselines now.** You don't need fancy tooling. A text file with date, task description, approximate session length, and a 1-5 satisfaction score gives you something to compare against in two weeks.

**2. Check the model version.** Claude Code, Cursor, and Copilot all update models without prominent notices. When you notice a quality shift, the first thing to check is whether the underlying model changed. In Claude Code, the model is shown at session start.

**3. Use shorter sessions.** This serves double duty — it reduces the cost impact of cache rebuilds (as I covered in [my first post](/blog/2026/04/24/your-ai-agent-is-burning-your-money.html)), and it makes quality comparison easier because each session has a clear scope.

**4. Track your own frustration.** CC-Canary includes a "frustration rate" metric — the frequency of frustration words in your prompts. This sounds crude, but it's a surprisingly reliable leading indicator. When you start writing "NO, I said..." or "why did you...", something has already gone wrong.

**5. Compare across models.** DeepSeek v4 launched this week. GPT-5.5 and 5.5 Pro hit the API today. New options mean you can run the same task through two different agents and compare. This is the most reliable way to detect provider-specific degradation — if both agents struggle, it's probably your prompt or project. If only one struggles, it's probably the model.

## The Bigger Picture

The AI coding agent market is entering an interesting phase. Users are paying $100-200/month for tools they can't meaningfully evaluate. Provider postmortems reveal bugs that affect everyone but that no individual user could have detected. Quality changes silently, costs shift without explanation.

This is what happens when a product category is growing faster than the monitoring infrastructure around it. The tools are powerful, but the feedback loops are broken. You can't optimize what you can't measure, and right now, most users can't measure anything about their agent's behavior.

That's starting to change. CC-Canary is one approach. Structured session logging is another. Even something as simple as writing down what your agent accomplished each day gives you data that most users don't have.

The agents will keep getting better — and occasionally, silently worse. The question is whether you'll know the difference.

---

*This is part 4 of a series on AI coding agent economics and reliability. Previous posts: [Your AI Agent Is Burning Your Money](/blog/2026/04/24/your-ai-agent-is-burning-your-money.html) · [5 Things to Change Right Now](/blog/2026/04/25/five-things-to-change-right-now.html) · [Your AI Agent Won't Listen](/blog/2026/04/25/your-ai-agent-wont-listen.html)*
