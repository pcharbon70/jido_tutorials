# Jido AI Summary

Source:
- https://hex.pm/packages/jido_ai
- https://hexdocs.pm/jido_ai/readme.html
- https://hexdocs.pm/jido_ai/

Version reviewed: `v2.1.0`

This note mirrors the current HexDocs README flow and sidebar order.

## README Flow

### Where This Package Fits

`jido_ai` is the AI runtime layer for the Jido ecosystem. The docs describe it as the package that sits on top of:

- `jido` for agent runtime and signal lifecycle
- `jido_action` for typed tool contracts
- `req_llm` for model/provider access

In practice, it is the layer that turns Jido agents into tool-using, model-calling AI agents.

### Installation

The docs again prefer Igniter for setup. Manual installation requires adding the dependency, configuring model aliases, and supplying at least one provider credential.

### Quick Start

The shortest path through the docs is:

1. define one `Jido.Action` tool
2. define one `Jido.AI.Agent` using that tool
3. start the agent
4. call `ask_sync/3` or `ask/3` plus `await/2`

### Request-Scoped ReAct Controls

One of the more distinctive parts of the README is request-level control over tool usage:

- `allowed_tools` narrows the available tool list for one run
- `tools` can replace the tool registry for one run
- `request_transformer` can reshape each LLM turn dynamically

The docs make this feel like a production control surface, not just a demo feature.

### Choose Your Integration Surface

The package supports several entry points depending on how much of the runtime you want:

- full agent-based interaction
- direct action usage
- standalone ReAct runtime
- CLI-oriented workflows

### Strategy Quick Pick

The README highlights the built-in reasoning families:

- ReAct: default choice for tool use and API work
- CoD: cheaper, shorter reasoning
- CoT: explicit step-by-step reasoning
- AoT: algorithmic one-pass reasoning
- ToT, GoT, TRM, and Adaptive: more specialized exploration or control patterns

### Common First-Run Errors

The docs call out early setup failures directly, especially:

- unknown model aliases
- missing provider configuration
- registering or calling something that is not a valid tool

### Documentation Map

The README points readers first to:

- package overview
- getting started
- first agent

and then outward to deeper strategy, runtime, and reference material.

### Runnable Examples

Examples are intentionally kept outside the core compile path and live in the top-level `examples/` folder. The docs point to weather and ReAct demo agents as concrete starting points.

### Why Jido.AI

The README's value proposition is consistent:

- explicit request handles for safer concurrency
- multiple built-in reasoning strategy families
- one shared tool contract through `Jido.Action`
- direct compatibility with the rest of the Jido runtime

## Guides In Sidebar Order

### Build With Jido.AI

- `Package Overview (Production Map)`: high-level package boundaries and runtime placement.
- `Getting Started With Jido.AI`: first setup, aliases, and first agent.
- `First Agent`: the baseline build-run example.
- `Strategy Selection Playbook`: choosing the right reasoning style.
- `Strategy Recipes`: concrete recipes for each supported strategy family.
- `Request Lifecycle And Concurrency`: request handles, awaiting, and runtime lifecycle behavior.
- `Context And Message Projection`: how thread state becomes model messages.
- `Tool Calling With Actions`: action-backed tool registration and execution.
- `LLM Facade Quickstart`: direct model access without a full agent loop.
- `Model Routing And Policy`: routing defaults, policy enforcement, and dry-run policy behavior.
- `Retrieval And Quota`: memory retrieval plus budget controls.
- `Observability Basics`: signals, telemetry, and tracing expectations.
- `Standalone ReAct Runtime`: using the ReAct machinery outside the full agent surface.
- `Turn And Tool Results`: how model responses and tool outputs are normalized.
- `CLI Workflows`: command-line usage patterns and task ergonomics.

### Upgrading

- `Migration Guide: Plugins And Signals (v2 -> v3)`

This is the bridge for users moving from the older plugin/signal shape into the current runtime contracts.

### Extend Jido.AI

- `Architecture And Runtime Flow`
- `Strategy Internals`
- `Directives Runtime Contract`
- `Signals, Namespaces, Contracts`
- `Plugins And Actions Composition`
- `Skills System`
- `Security And Validation`
- `Error Model And Recovery`

This group is where the docs shift from using the package to extending its internals safely.

### Reference

- `Actions Catalog`
- `Configuration Reference`
- `Thread-Context Projection Model`

These pages act as the stable lookup layer once the user already understands the package.

## API Surface In Sidebar Order

### Core

- `Jido.AI`
- `Jido.AI.Agent`
- `Jido.AI.Observe`
- `Jido.AI.PluginStack`
- `Jido.AI.Request`
- `Jido.AI.Request.Handle`
- `Jido.AI.ToolAdapter`
- `Jido.AI.Turn`
- `Jido.AI.Validation`

This group defines the base runtime, request model, tool adapter, and validation layer.

### Errors

The docs expose a structured error model with API, auth, rate-limit, request, sanitize, unknown, and validation variants.

### Action Groups

The action modules are grouped by purpose:

- LLM actions
- planning actions
- reasoning actions
- retrieval actions
- tool-calling actions
- quota actions

This is useful because the package is not only an agent layer; it also exposes many capabilities as regular Jido actions.

### Reasoning Strategies

The strategy surface is one of the biggest parts of the API. The docs include strategy modules and support modules for:

- Adaptive
- Algorithm of Thoughts
- Chain of Draft
- Chain of Thought
- Graph of Thoughts
- ReAct
- TRM
- Tree of Thoughts

The sidebar also includes CLI adapters, machine/state modules, helper modules, and result types around those strategies.

### Convenience Agents

- `Jido.AI.AdaptiveAgent`
- `Jido.AI.AoTAgent`
- `Jido.AI.CoDAgent`
- `Jido.AI.CoTAgent`
- `Jido.AI.GoTAgent`
- `Jido.AI.TRMAgent`
- `Jido.AI.ToTAgent`

These modules package strategy choices into easier entry points.

### Plugins, Signals, And Directives

The docs expose dedicated groups for:

- plugins such as chat, model routing, planning, policy, quota, retrieval, and reasoning plugins
- signal modules for request lifecycle, usage, tool execution, and streaming deltas
- directives for LLM generation, embedding, streaming, tool execution, and error emission

Together these groups show that `jido_ai` is deeply integrated into the broader Jido runtime model.

### Skills, Quality, And CLI

The remaining groups cover:

- the skills system
- quality and quota storage/checkpoints
- CLI and Mix tasks, including `mix jido_ai`, `mix jido_ai.install`, `mix jido_ai.quality`, and `mix jido_ai.skill`

## Short Takeaway

`jido_ai` is the package that operationalizes AI behavior inside the Jido stack. The docs are strongest when explaining how tools, requests, reasoning strategies, plugins, and signals fit together into a production-oriented runtime instead of a thin wrapper around model APIs.
