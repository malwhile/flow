---
title: "Hybrid AI Workflows: Balancing Cost and Capability"
author: ["Halvo: (Human)", "Claude: (AI)"]
date: 2026-04-16
summary: "How I learned to split planning and implementation work between Lumo and Claude Code to save compute time, money, and sanity on larger projects."
tags: ["AI", "workflow", "productivity", "decision-making", "Claude", "Lumo"]
categories: ["Development", "Tools", "Strategy"]
draft: false
---

## Introduction

I hit a wall. After tackling increasingly complex projects with Claude, I started bumping against usage limits and watching my bill climb. The problem wasn't that Claude couldn't do the work, it was that I was asking it to do *everything*: architecture decisions, implementation, code review, infrastructure planning. For a massive codebase like [plutus](https://github.com/malwhile/plutus), that meant long contexts, multiple iterations, and a lot of compute sitting in the "thinking" phase.

Then I realized something obvious, Claude can use just planning docs and Git issues to perform it's tasks. Claude doesn't need to generate those docs. That's when the hybrid workflow clicked.

## The Problem: All-or-Nothing AI

Before this shift, my workflow was simple and expensive:
1. I'd write instructions for Claude to `/plan` with a detailed problem to be solved
2. Have claude then take that plan, break it into pieces and generate Git Issues
   - I would then go through the Git issues, make sure they make sense and fill in any gaps
3. Claude Code would then generate another `/plan` based off a given issue
4. Implement that plan
5. Do an initial review pass (a different agent/command)
6. After generating a PR I would then do my own pass of a review
7. Iterate back to 4 based on the two reviews

For larger projects, this meant multiple sessions burning through tokens, fast.

The plutus project was the catalyst. Complex domain logic, unclear architecture decisions, and I kept circling back to Claude for "do we refactor first or build features?" conversations. Each loop cost tokens and time.

## The Solution: Divide and Conquer

I started experimenting with a two-tier approach:
- **Tier 1 (Lumo):** High-level design, decision-making, planning, and bash scripting for issue generation
- **Tier 2 (Claude Code):** Implementation, refactoring, code reviews, and problem-solving

The split works because Lumo is cheaper (I already have it through Proton) and genuinely good at *talking through* architecture. I don't need Claude's full inference power to decide "should we use Go or Rust for this CLI tool?" I need a thinking partner who can reason about trade-offs.

## How It Works in Practice {#how-it-works}

Here's the workflow for something like [heimwatch](https://github.com/malwhile/heimwatch) or [sjles-pta-vote](https://github.com/malwhile/sjles-pta-vote):

1. **Lumo Phase** — I describe the project, desired outcomes, and constraints
   - We narrow down language choices
   - We sketch out the architecture
   - We decide on infrastructure approach
   - No Claude Code tokens spent

2. **Bash + gh Phase** — Still in Lumo, I ask it to help write a bash script that generates GitHub issues using `gh`
   ```bash
   # Something like:
   gh issue create --title "Feature: XYZ" --body "Description..."
   ```
   Lumo helps structure these without eating into Claude's budget

3. **Claude Code Phase** — Once the plan is solid and issues are created, Claude Code handles the actual engineering
   - Implementation against the spec
   - Real code review cycles
   - Integration with existing systems

## Why This Actually Works

**Cost:** Lumo (not Lumo+) is free with a Proton account (which I already have). So I can conduct long, detailed, conversations, come to conclusions, and have it write out scripts to automate some tasks. This reduces the reliance on the costly Claude Code.

**Focus:** Claude Code isn't context-switched by high-level debates. It gets a clear spec and moves fast. No "should we do this differently?" spirals.

**Speed:** Planning upfront means fewer "wait, I need to rethink the whole approach" moments during implementation. The implementation phase is cleaner.

**Sanity:** I actually enjoy the workflow now. I'm not trying to compress all my thinking into a single expensive conversation.

## What I Learned

### Human Involvement

Every step of the way, a skilled human is absolutely necissary.

Going back to [How It Works in Practice](#how-it-works)

1. **Lumo Phase**: Planning must be detailed
   - Clear and concise goal
      - Goals are more inportant than inputs and outputs, what the outputs are *needed for* informs what they *are*
      - Once and overall architecture is set, break the problem set down smaller and focus on those goals
      - Lumo was helpful in creating those sub-goals as well
   - Have a conversation
      - Ask Lumo to ask questions
      - Have it "slow down and think"
      - When it answers, as the human, "slow down and think" if the output makes sense and how to tweak
2. **Bash + `gh` Phase**: Think through all the parameters
   - Lumo would use code blocks inside the script, breaking the output, be specific on formatting
   - The scripts often would create issues with tags and milestones that were not created yet
   - Be sure to go through the created issues 
      - Link them up properly (sub issues or epics)
      - Add additional detail, this is what Claude will eventually use
3. **Claude Code Phase**: Watch it closely
   - Keep the code changes to a minimum, it's less likely for Claude to mess up and easier for Human review
   - Check changes as you go, to make sure Claude is still headed in the right direction
   - Always create PRs that you as a human can review and iterate on

### Don't be Afraid to Start Over

Sometimes AI agents will start to go in a bad direction. Lumo will suggest coding languages, database structures, or architectures that don't make a lot of sense, but then defend it's decision rather than change it. Start a new conversation, but add restrictions up front.

## Conclusion

Using a hybrid approach is helpful financially, as I can rely on the "free" Lumo to help setup the project and instructions, then use the "expensive" Claude for implementation. It's also helpful, since both use different models which provide different perspectives.
   
Not every project needs this split. Small features? Single-file fixes? Claude Code solo is fine. But anything that involves significant architectural decisions, infrastructure choices, or language selection? Hybrid wins.

The plutus project taught me I had a tool (Lumo) I wasn't using effectively. Now I use it. Sounds simple in hindsight.

## References

- [Prompt Used](/prompts/hybrid-ai-workflows-prompt.md)
