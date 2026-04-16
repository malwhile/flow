Topic: Hybrid AI Workflows: Balancing Cost and Capability
Slug: hybrid-ai-workflows
Date: 2026-04-16

Context:
For the past couple blog entries, I've used Lumo exclusively for writing and Claude for coding. Lumo has been having trouble hitting tone and formatting expectations, so I'm switching to Claude for this blog post.

For recent coding projects (heimwatch, sjles-pta-vote), I've used a hybrid approach:
- Lumo for high-level design, decision-making, and creating bash scripts to generate GitHub issues using `gh`
- Claude Code for actual implementation and code reviews

This hybrid workflow saves significant compute time and money by offloading planning work to Lumo (not Lumo+), which I already have through Proton. I've had "conversations" with Lumo to narrow down decisions without using any Claude Code compute time.

Before adopting this approach, I hit Claude usage limits on larger codebases and complex changes (e.g., plutus project). Those issues came directly from Claude and made me realize the value of splitting high-level thinking (Lumo) from implementation (Claude Code).

Key points to cover:
1. The original problem: hitting limits on Claude with large projects
2. The hybrid solution: Lumo for planning/design, Claude Code for implementation
3. How this saves money and compute time
4. Real project examples (heimwatch, sjles-pta-vote, plutus)
5. The workflow process and why it works well
6. Lessons learned and what to watch for

Tone: Casual, technically accurate, self-deprecating where appropriate
Length: Short to medium (~600-900 words)
