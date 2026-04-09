# Jido Action Summary

Source:
- https://hex.pm/packages/jido_action
- https://hexdocs.pm/jido_action/readme.html
- https://hexdocs.pm/jido_action/

Version reviewed: `v2.2.1`

This note mirrors the current HexDocs README flow and sidebar order.

## README Flow

### Overview

`jido_action` is the action layer for the Jido ecosystem. The docs position it as a way to turn plain Elixir operations into structured, validated, reusable units that can also be exposed as AI tools.

### Why Do I Need Actions?

The main problem the package solves is that ad hoc function calls do not carry enough shape, metadata, validation, or runtime guarantees for larger systems. The docs frame actions as a better fit when you want:

- input and output validation
- consistent error handling
- metadata and discoverability
- workflow composition
- LLM tool/function-call compatibility

### Key Features

- Structured action definition: actions declare schemas, metadata, and lifecycle hooks instead of relying on loose function contracts.
- AI tool integration: the package can derive tool definitions from action modules so the same code works for both application logic and LLM tool calling.
- Robust execution engine: `Jido.Exec` handles sync and async runs, retries, timeout behavior, cancellation, and telemetry hooks.
- Workflow composition: `Jido.Instruction` and `Jido.Plan` let you normalize and sequence actions into larger flows.
- Built-in tools: the docs highlight a bundled tool set for common filesystem, arithmetic, HTTP, and workflow tasks.

### Installation

The docs recommend installing with Igniter first. Manual installation is straightforward, but Igniter also wires in default runtime configuration for timeouts, retries, and backoff.

### Quick Start

The quick start walks through this progression:

1. Define an action module with `use Jido.Action`.
2. Execute it with `Jido.Exec`.
3. Compose actions with `Jido.Instruction`.
4. Reuse the same action as an AI tool.

### Core Components

- `Jido.Action`: the macro/behavior for defining validated actions.
- `Jido.Exec`: the execution runtime.
- `Jido.Instruction`: the workflow instruction format.
- `Jido.Plan`: DAG-style planning for dependent or parallel work.

### Bundled Tools

The docs group the built-in tools into these buckets:

- `Jido.Tools.Basic`: utility actions like sleep, logging, placeholders, and simple state helpers.
- `Jido.Tools.Arithmetic`: small math-oriented actions.
- `Jido.Tools.Files`: file and directory operations.
- `Jido.Tools.ReqTool`: a macro/behavior for HTTP request actions built on `Req`.
- `Jido.Tools.Workflow`: higher-level multi-step workflow execution.
- specialized tools: branching, parallelism, compensation patterns, and advanced helpers.

### Advanced Features

The advanced sections emphasize three ideas:

- compensation-aware error handling for rollback-style flows
- lifecycle hooks for validation and execution customization
- telemetry hooks so action execution is observable in production

The configuration section also calls out instance-scoped supervisors for multi-tenant isolation.

## Guides In Sidebar Order

### Getting Started

- `Getting Started`: first action, first execution path, and base setup.
- `Your Second Action`: extends the beginner flow with another concrete example.

### Core Concepts

- `Actions`: how action modules are declared and what contract they expose.
- `Schemas & Validation`: how params, outputs, and config are validated.
- `Execution Engine`: how `Jido.Exec` handles runtime behavior.
- `Instructions & Plans`: how to assemble actions into workflows.
- `Error Handling`: how failures, retries, and compensation are modeled.

### How-To Guides

- `Built-in Tools`: when to use the bundled tool set versus custom actions.
- `AI Integration`: how to turn actions into LLM-callable tools.
- `Configuration`: runtime defaults, isolation, and execution settings.
- `Security`: how to limit unsafe or overly broad tool surfaces.
- `Testing`: how to test actions directly and within workflows.

### Help & Reference

- `FAQ`
- `PR #68 Triage`
- `Changelog`
- `Apache 2.0 License`

## API Surface In Sidebar Order

### Core

- `Jido.Action`: main behavior and macro.
- `Jido.Action.Error`: normalized error model.
- `Jido.Action.Tool`: action-to-tool conversion helpers.
- `Jido.Action.Util`: shared utilities.

### Schemas & Validation

- `Jido.Action.Runtime`
- `Jido.Action.Schema`

These modules back parameter checking, output validation, and JSON schema generation.

### Execution Engine

- `Jido.Exec`
- `Jido.Exec.Async`
- `Jido.Exec.Chain`
- `Jido.Exec.Closure`
- `Jido.Exec.Compensation`
- `Jido.Exec.Retry`
- `Jido.Exec.Supervisors`
- `Jido.Exec.Telemetry`
- `Jido.Exec.Validator`

This group covers how actions actually run, retry, report, and stay isolated.

### Planning & Workflows

- `Jido.Instruction`
- `Jido.Plan`
- `Jido.Plan.PlanInstruction`

These modules are the workflow/planning layer for ordered, parallel, or dependency-based action execution.

### Built-In Tool Modules

- `Jido.Tools.Arithmetic`
- `Jido.Tools.Basic`
- `Jido.Tools.Files`
- `Jido.Tools.Workflow`
- `Jido.Tools.ReqTool`
- `Jido.Tools.ActionPlan`
- `Jido.Tools.LuaEval`

### Examples And Error Types

The sidebar also includes an example module plus a set of typed error modules for config, execution, validation, internal failures, and timeouts.

## Short Takeaway

If `jido` is the agent runtime, `jido_action` is the package that makes individual capabilities safe, composable, and AI-friendly. The docs are strongest on action definition, execution guarantees, and using the same action contract across normal Elixir workflows and LLM tool calls.
