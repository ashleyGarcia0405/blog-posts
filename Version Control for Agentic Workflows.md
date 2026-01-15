Startups have raised over $6.7 billion to build autonomous coding agents and multi-agent orchestration frameworks. A piece is missing: how those agents will coordinate changes to shared codebases.

The $6.7 billion invested in 2024 has produced three categories: autonomous coding agents, multi-agent orchestration frameworks, and AI-powered development environments; they’re all layered on top of traditional git.

Traditional git, designed for human developers with social coordination, faces critical mismatches with agent workflows - semantic understanding gaps and merge conflict chaos when 10+ agents work simultaneously.

[https://github.com/anthropics/claude-code/issues/1315](https://github.com/anthropics/claude-code/issues/1315)

Notable papers:

- MetaGPT's "Code = SOP(Team)" framework presented at ICLR 2024, which simulates entire software companies with specialized agent roles
    - [https://www.ibm.com/think/topics/metagpt](https://www.ibm.com/think/topics/metagpt)
    - [http://github.com/FoundationAgents/MetaGPT](http://github.com/FoundationAgents/MetaGPT)
- SWE-bench benchmark from Princeton that became the standard for evaluating autonomous agents (though models initially solved only 1.96% of real GitHub issues)

Research on semantic code understanding and merge conflict resolution:

- MergeBERT achieved 63-68% accuracy on merge resolution synthesis
    - [https://dl.acm.org/doi/10.1145/3698038.3698525](https://dl.acm.org/doi/10.1145/3698038.3698525)
- Research on class-level code generation shows LLMs perform worse when handling interdependent methods (76.2% have class-level dependencies), suggesting agents need richer contextual understanding than git's text-based diffs provide.
    - [https://mingwei-liu.github.io/assets/pdf/ICSE2024ClassEval-V2.pdf](https://mingwei-liu.github.io/assets/pdf/ICSE2024ClassEval-V2.pdf)

Cognition Labs’ Devin is deployed to Goldman Sachs, Palantir, Cisco. Works really well autonomously integrating with GitHub - it’s operating within git’s constraints: creating branches, committing changes, opening PRs for review (idk if needs to be human reviews anymore). They recently acquired Windsurf, covering developing environment, but not version control limitations.

Replit’s Agent 3 seems to come pretty close, but it’s still using git under the hood.

Really interesting approaches to research more:

- Neon’s collab with Azure AI Agents - database versioning experiments rather than code versioning. They use separate database branches for each agent version, allowing for isolated testing and clean rollback.
    - this pattern prevents agents from contaminating shared state but scaled poorly beyond 10 agents and doesn’t address code coordination.
- Cloudflare's VibeSDK provides infrastructure for building custom coding platforms but remains agnostic to version control paradigms

Git’s architecture fundamentally conflicts with autonomous agent workflows:

- Git tracks character-level changes in text files while agents need to understand what changed in intent and behavior. Semantic understanding represents git’s most fundamental limitation. When an agent renames a method consistently across 50 files, git reports thousands of line changes—semantically a no-op, but indistinguishable from 50 different behavioral modifications. Without Abstract Syntax Tree (AST) parsing or Language Server Protocol (LSP) integration, agents lack the semantic code understanding that modern IDEs provide humans.
    - Martin Fowler's experiments with AI-generated Spring Boot applications found agents added "features we hadn't asked for," bundled into commits that obscured what actually changed.
    - repository-wide refactoring that took "under an hour" manually with IDE tools required "many hours" for the AI agent using text-based git operations. (claude code issue #1315)
- Merge conflicts occur at dramatically higher rates when multiple agents work simultaneously.
    - MergeBERT research found even state-of-the-art AI achieves only 63-68% accuracy for merge resolution, and agents lack awareness of parallel work. When three agents simultaneously modify authentication logic - one updating the database schema, another the API endpoints, a third the frontend components—git's optimistic concurrency model (detect conflicts on merge) creates cascading failures.
- Commit granularity patterns differ drastically between humans and agents. Agents generate either massive commits spanning multiple files and concerns (violating atomic commit principles) or dozens of micro-commits for minor fixes during iteration.
- State management complexity reveals git's human-centric assumptions. Git tracks file system state at commit points but has no awareness of agent intentions, goals, reasoning traces, or tool usage history. When agents restart after session limits, they must rebuild understanding from commit messages and code diffs alone—losing crucial context about why decisions were made.

Git handles persistence and orchestration frameworks handle agent coordination. Let’s re-imagine version control itself.

## Core Features

- Semantic Versioning Layer
    - AST-aware commits: parse code into abstract syntax trees using LSP servers so commits capture intent and not text deltas
    - Semantic diffs - replacing git diff with tree-level comparison
        - output like “Refactored login handler: extracted validate_token()”
    - Intent-based history: each commit is an event {intent, agent_id, context_hash}
- Multi-dimensional commits with code snapshot, agent configuration (model, prompt, memory state), toolchain version, execution results (tests, benchmarks, logs), reasoning trace hash → checking out a commit reconstructs not only the code, but also the cognitive and runtime context that produced it
- Coordination primitives. We want to replace branch/merge logic with workspace coordination:
    - Function-level locks: agents claim specific semantic units (methods, modules, etc)
    - Intent dependencies: “Agent A’s DB schema change must merge before Agent B’s API”
    - Event notifications: when Agent A commits, others subscribed to affected modules auto-refresh context.
    - Conflict semantics: merge engine distinguishes between compatible and competing intents.

## Identity, Trust, and Governance

- We could integrate with standards like MCP identity or Verified AI Agent (RFC 9421). Every commit → {agent_pubkey, signature, trust_score}
    - Humans and orgs can configure specific rules.
- Track merge success, rollback frequency, and test stability per agent. Display “trust scores” in UI (e.g., Agent42: 93% merge reliability).
- Configurable oversight modes:
    - Autonomous: Agents merge automatically if trust ≥ threshold.
    - Hybrid: PRs routed to reviewer agents first.
    - Manual: Human review required on sensitive files.

### Causal Intelligence

- Every commit links to causes/effects
    - “Commit X introduced latency regression Y.”
    - “Commit Z fixed behavior introduced by Agent Q.”

