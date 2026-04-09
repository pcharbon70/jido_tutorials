# Jido Evolutive Tutorial Skeleton Plan

Based on:

- [01-jido_action-summary.md](/Users/Pascal/code/agentjido/jido_tutorials/notes/01-jido_action-summary.md)
- [02-jido_signal-summary.md](/Users/Pascal/code/agentjido/jido_tutorials/notes/02-jido_signal-summary.md)
- [03-jido-summary.md](/Users/Pascal/code/agentjido/jido_tutorials/notes/03-jido-summary.md)
- [04-jido_ai-summary.md](/Users/Pascal/code/agentjido/jido_tutorials/notes/04-jido_ai-summary.md)

## Goal

Design a progressive tutorial series that helps a learner discover the Jido ecosystem in layers:

1. understand actions as structured units of work
2. understand signals as the event and communication fabric
3. understand Jido as the agent runtime that coordinates state and effects
4. understand Jido.AI as the AI-runtime layer built on top of those foundations

The series should feel evolutionary: each step introduces one new idea, reuses earlier concepts, and ends with a visible artifact.

## Audience

- Elixir developers who know basic OTP but are new to Jido
- developers curious about agent systems but wanting a concrete, code-first path
- advanced users who want a mental map of how the four packages fit together

## Teaching Strategy

- Start from small, local, deterministic examples.
- Delay full agents until actions and signals feel natural.
- Introduce runtime concepts only after the learner has something worth orchestrating.
- Introduce AI last, as an extension of already-familiar action, signal, and runtime patterns.
- End each part with a mini-project that becomes the starting point for the next part.

## Series Structure

### Part 0: Orientation And Setup

Tutorial 00. Ecosystem Map
- Objective: explain what `jido_action`, `jido_signal`, `jido`, and `jido_ai` each own.
- Outcome: the learner can place each package in the stack.
- Deliverable: a simple architecture diagram and dependency map.

Tutorial 01. Local Setup And Playground
- Objective: create the working Elixir project and verify dependencies.
- Outcome: the learner has a runnable sandbox for all later tutorials.
- Deliverable: starter project with placeholder modules and test setup.

### Part 1: Structured Work With Jido Action

Tutorial 02. Your First Action
- Objective: define a minimal `Jido.Action`.
- Concepts: action contract, schemas, validation, metadata.
- Deliverable: one small action module with tests.

Tutorial 03. Action Inputs, Outputs, And Errors
- Objective: explore validation, output shape, and failure cases.
- Concepts: runtime validation, action errors, predictable contracts.
- Deliverable: one robust action with happy-path and error-path tests.

Tutorial 04. Running Actions With `Jido.Exec`
- Objective: execute actions synchronously and asynchronously.
- Concepts: execution engine, retries, timeouts, cancellation.
- Deliverable: executable examples showing sync and async workflows.

Tutorial 05. Composing Work With `Jido.Instruction`
- Objective: combine multiple actions into a simple workflow.
- Concepts: instruction normalization, context passing, sequential execution.
- Deliverable: a small multi-step business flow.

Tutorial 06. Planning With `Jido.Plan`
- Objective: introduce dependency-aware and parallelizable plans.
- Concepts: DAG thinking, phases, dependencies, plan construction.
- Deliverable: one plan that runs a realistic multi-step operation.

Tutorial 07. Built-In Tools And Reusable Patterns
- Objective: explore bundled tools and when to build custom actions.
- Concepts: basic tools, file tools, arithmetic tools, HTTP tools.
- Deliverable: a toolbox demo project.

Mini-Project A. Action-Driven Workflow
- Objective: build a small but complete action-based workflow.
- Outcome: learner has a reusable execution pipeline without agents yet.

### Part 2: Events And Coordination With Jido Signal

Tutorial 08. Why Signals Instead Of Raw Messages
- Objective: introduce signals as structured event envelopes.
- Concepts: CloudEvents shape, metadata, traceability.
- Deliverable: a custom signal definition and event examples.

Tutorial 09. Publish/Subscribe With The Signal Bus
- Objective: stand up a local bus and move signals through it.
- Concepts: bus lifecycle, subscribers, publishing.
- Deliverable: a working pub/sub demo.

Tutorial 10. Routing Signals
- Objective: use routing rules to direct signals by pattern.
- Concepts: router, wildcard matching, routing strategy.
- Deliverable: one routed signal topology with multiple handlers.

Tutorial 11. Dispatch Adapters And Delivery Boundaries
- Objective: send signals to different destinations.
- Concepts: PID delivery, HTTP/webhook delivery, logging adapters.
- Deliverable: one signal fan-out example.

Tutorial 12. Journaling, Replay, And Snapshots
- Objective: understand signal history as an operational tool.
- Concepts: journaling, replay, persistence, snapshots.
- Deliverable: replayable signal history for a demo flow.

Tutorial 13. Middleware, Isolation, And Production Concerns
- Objective: show how signal systems stay maintainable in real deployments.
- Concepts: middleware pipeline, instance isolation, causality tracking.
- Deliverable: hardened signal infrastructure for the tutorial project.

Mini-Project B. Event-Driven Workflow Layer
- Objective: evolve the action workflow into an event-driven system.
- Outcome: learner sees actions and signals working together.

### Part 3: Agent Runtime With Jido

Tutorial 14. The `cmd/2` Mental Model
- Objective: explain how agents transform state and emit directives.
- Concepts: immutable agent state, directives, state operations.
- Deliverable: one minimal agent with predictable transitions.

Tutorial 15. Running An Agent With `AgentServer`
- Objective: move from pure modules to supervised runtime behavior.
- Concepts: OTP runtime integration, lifecycle, supervision.
- Deliverable: one running agent process.

Tutorial 16. Actions, Directives, And Signals Together
- Objective: connect the three core surfaces in one loop.
- Concepts: actions mutate intent, directives trigger effects, signals enter the runtime.
- Deliverable: one agent that reacts to incoming signals through actions.

Tutorial 17. Plugins As Capability Modules
- Objective: introduce plugins as a way to grow agents cleanly.
- Concepts: plugin contracts, capability composition, state isolation.
- Deliverable: one custom plugin added to the running agent.

Tutorial 18. Strategies And Runtime Patterns
- Objective: compare direct and FSM-style execution patterns.
- Concepts: strategies, state-driven workflows, runtime choice.
- Deliverable: two versions of the same agent behavior using different strategies.

Tutorial 19. Multi-Agent Coordination And Pods
- Objective: scale from one agent to several collaborating agents.
- Concepts: await/coordination, pods, orchestration, tenancy boundaries.
- Deliverable: a small coordinated agent group.

Tutorial 20. Observability, Scheduling, And Persistence
- Objective: make the runtime inspectable and durable.
- Concepts: telemetry, debugging, scheduling, persistence adapters.
- Deliverable: instrumented runtime with scheduled behaviors.

Mini-Project C. Stateful Agent System
- Objective: turn the event-driven workflow into a supervised agent-based application.
- Outcome: learner has a serious non-AI Jido system.

### Part 4: AI Systems With Jido.AI

Tutorial 21. Why `jido_ai` Comes Last
- Objective: connect AI features back to the earlier foundations.
- Concepts: `jido_ai` on top of `jido`, `jido_action`, and `req_llm`.
- Deliverable: architecture map for the AI layer.

Tutorial 22. First Tool-Using AI Agent
- Objective: wrap an existing action workflow with `Jido.AI.Agent`.
- Concepts: tools as actions, `ask_sync/3`, `ask/3`, `await/2`.
- Deliverable: one working tool-using AI agent.

Tutorial 23. ReAct And Request Lifecycle
- Objective: explain the default runtime loop for tool-using requests.
- Concepts: request handles, ReAct flow, per-request controls.
- Deliverable: agent with visible request lifecycle logging.

Tutorial 24. Tool Selection And Request-Scoped Controls
- Objective: control tool availability safely at runtime.
- Concepts: `allowed_tools`, request-local tool maps, request transformers.
- Deliverable: constrained agent runs with different tool policies.

Tutorial 25. Reasoning Strategy Tour
- Objective: compare ReAct, CoD, CoT, AoT, ToT, GoT, TRM, and Adaptive.
- Concepts: strategy choice by task shape, cost, latency, control.
- Deliverable: one benchmark-style comparison exercise.

Tutorial 26. Retrieval, Quota, And Policy
- Objective: introduce memory and resource governance.
- Concepts: retrieval actions, quota, routing and policy plugins.
- Deliverable: guarded agent with memory and budget controls.

Tutorial 27. Signals, Directives, And AI Runtime Contracts
- Objective: show how AI requests become signals and directives in the wider runtime.
- Concepts: runtime contracts, tool result envelopes, event flow.
- Deliverable: traced end-to-end AI request flow.

Tutorial 28. Extending Jido.AI
- Objective: explore plugins, skills, and custom strategy/runtime extensions.
- Concepts: plugin composition, skills system, extension boundaries.
- Deliverable: one custom extension added to the tutorial agent.

Mini-Project D. Production-Oriented AI Agent
- Objective: evolve the previous agent system into an AI-capable runtime with guardrails.
- Outcome: learner sees AI as an extension of a disciplined runtime, not a separate stack.

### Part 5: Final Capstone

Tutorial 29. End-To-End System Assembly
- Objective: combine actions, signals, Jido runtime, and Jido.AI into one coherent application.
- Deliverable: a full demo application architecture.

Tutorial 30. Operational Review
- Objective: review observability, safety, testing, and extension points.
- Deliverable: a production-readiness checklist for the capstone.

Capstone Project. Autonomous Event-Driven Assistant
- Scope: one multi-step, tool-using, signal-driven, supervised agent system.
- Layers reused: action contracts, signal fabric, runtime directives, AI reasoning.
- Expected outcome: the learner can explain why each package exists and where it belongs.

## Cross-Cutting Assets To Create Later

- one shared sample application that evolves over the whole series
- diagrams for action flow, signal flow, runtime flow, and AI request flow
- tests for every tutorial milestone
- a glossary of core concepts
- a migration map showing when the learner moves from one package to the next

## Suggested Tutorial Template

Each eventual tutorial should follow the same structure:

1. why this lesson exists
2. what we are building
3. concepts introduced
4. implementation steps
5. verification steps
6. common failure modes
7. how this connects to the next tutorial

## Authoring Order

To keep momentum and reuse earlier examples, author the tutorials in this order:

1. Part 0
2. Part 1
3. Mini-Project A
4. Part 2
5. Mini-Project B
6. Part 3
7. Mini-Project C
8. Part 4
9. Mini-Project D
10. Part 5 and capstone

## Success Criteria

- A learner can stop after any part and still have a coherent understanding of that layer.
- Each new package feels like a natural extension of the previous one.
- The full series explains both how to use the packages and why the architecture is layered this way.
- By the capstone, the learner can reason about the whole Jido stack from deterministic actions to AI-enabled agent systems.
