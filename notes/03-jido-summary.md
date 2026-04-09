# Jido Summary

Source:
- https://hex.pm/packages/jido
- https://hexdocs.pm/jido/readme.html
- https://hexdocs.pm/jido/

Version reviewed: `v2.2.0`

This note mirrors the current HexDocs README flow and sidebar order.

## README Flow

### Overview

`jido` is the core agent framework in the ecosystem. The docs position it as an OTP-native way to build autonomous or multi-agent systems using ordinary Elixir modules, supervised processes, and explicit contracts between state, actions, and side effects.

### The Jido Ecosystem

The README presents `jido` as the center of a broader package set. The core package focuses on agent runtime behavior, while optional packages such as `jido_action`, `jido_signal`, and `jido_ai` fill in actions, signaling, and AI-specific runtime features.

### Why Jido?

The docs are clear that raw `GenServer` is powerful enough, but repetitive patterns appear once systems need many cooperating agents. Jido formalizes those patterns so teams do not keep rebuilding:

- state contracts
- effect handling
- agent coordination
- runtime supervision
- plugin-based capabilities

### Key Features

- Immutable agent architecture: agents follow a state-in, state-out model centered on `cmd/2`.
- Directive-based effects: side effects are returned as directives rather than executed inline.
- OTP runtime integration: `AgentServer` runs agents under supervision with lifecycle management.
- Composable plugins: capability modules can extend agents without collapsing everything into a single module.
- Execution strategies: different runtime strategies are available, including FSM-oriented flows.
- Multi-agent orchestration: pods, plans, and coordination patterns support larger agent systems.

### Installation

The docs recommend Igniter for the smoothest setup. Manual installation is still simple, but the guidance expects you to define a Jido instance module and place it in your supervision tree.

### Quick Start

The quick start follows the core runtime model:

1. Define an agent.
2. Define an action.
3. Execute commands against the agent.
4. Run the agent under `AgentServer`.

### Core Concepts

- `cmd/2` contract: the central agent callback that returns updated state plus directives.
- actions vs directives vs state operations: the docs distinguish pure state transformation, described effects, and internal runtime state transitions.
- `Jido.Agent.StateOp`: internal state transitions handled by the strategy layer.
- directive types: the built-in effect vocabulary for emitting, spawning, scheduling, stopping, and related runtime actions.

### Documentation And Development

The README points readers from quick start into a much larger guide set covering fundamentals, coordination, operations, integrations, advanced runtime behavior, and migration from earlier versions. The development section covers prerequisites, tests, and quality checks.

## Guides In Sidebar Order

### Start Here

- `Quick Start`
- `Core Loop`
- `Choosing a Runtime Pattern`
- `Your First Plugin`
- `Your First Sensor`
- `Seeing What Happened`

This section introduces the mental model before diving into the full API.

### Fundamentals

- `Agents`
- `Actions`
- `Signals & Routing`
- `Directives`
- `State Operations`
- `Plugins`
- `Strategies`
- `Runtime`

This group defines the main building blocks of a Jido system.

### Coordination

- `Await & Coordination`
- `Multi-Agent Orchestration`
- `Pods`
- `Multi-Tenancy`

These guides focus on orchestration patterns once you move beyond a single agent.

### Operations

- `Debugging`
- `Observability`
- `Testing`
- `Configuration`
- `Persistence & Storage`
- `Worker Pools`
- `Scheduling`

This section is about production runtime behavior.

### Extending

- `Sensors`
- `Discovery`
- `Custom Strategies`

### Integrations

- `Phoenix Integration`
- `Ash Integration`

### Advanced

- `Orphans & Adoption`
- `FSM Strategy Deep Dive`
- `Error Handling`

### Migration And Project Docs

- `Migrating from 1.x`
- `Contributing`
- `Changelog`
- `Apache 2.0 License`

## API Surface In Sidebar Order

### Core

- `Jido`
- `Jido.Agent`
- `Jido.AgentServer`
- `Jido.Await`
- `Jido.Pod`
- pod topology modules

This group contains the primary runtime types and process model.

### Strategies

- `Jido.Agent.Strategy`
- `Jido.Agent.Strategy.Direct`
- `Jido.Agent.Strategy.FSM`
- related FSM state and snapshot modules

These modules define how the runtime interprets commands and transitions.

### Plugins

- `Jido.Plugin`
- config, manifest, requirement, route, schedule, and instance helpers
- `Jido.Pod.Plugin`

The docs present plugins as reusable capability units with their own contracts and lifecycle hooks.

### Identity

- `Jido.Identity`
- identity modules for agents, plugins, profiles, and evolve actions

### Directives

- `Jido.Agent.Directive`
- built-in directive modules such as `Emit`, `Spawn`, `SpawnAgent`, `Schedule`, `Cron`, `Stop`, and child-management directives

### State Operations

- `Jido.Agent.StateOp`
- concrete state operation modules like `SetState`, `ReplaceState`, `SetPath`, `DeletePath`, and related helpers

### Agent Internals

The sidebar includes a large internal runtime set around lifecycle, directive execution, child tracking, signal routing, server status, and state handling. These modules matter when extending the runtime, not just using it.

### Built-In Actions

Built-in action groups cover:

- control actions
- lifecycle actions
- scheduling actions
- status actions

### Sensors, Thread, Memory, And Storage

The docs also expose dedicated groups for:

- sensors and sensor runtime/spec modules
- thread and thread-store modules
- memory modules
- storage and persistence adapters, including ETS, file, and Redis

### Observability And Utilities

Observability modules include debugging, tracing, telemetry, and logging helpers. Utility modules cover worker pools, discovery, the scheduler, and shared runtime helpers.

### Exceptions

The exception group covers compensation, execution, internal, routing, timeout, and validation failures.

## Short Takeaway

`jido` is the runtime heart of the ecosystem. The docs focus on one consistent idea: agents stay predictable when state transitions are explicit, side effects are returned as directives, and orchestration lives in the runtime instead of being spread across ad hoc process code.
