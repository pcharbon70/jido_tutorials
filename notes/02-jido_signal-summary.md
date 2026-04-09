# Jido Signal Summary

Source:
- https://hex.pm/packages/jido_signal
- https://hexdocs.pm/jido_signal/readme.html
- https://hexdocs.pm/jido_signal/

Version reviewed: `v2.1.1`

This note mirrors the current HexDocs README flow and sidebar order.

## README Flow

### Overview

`jido_signal` is the signaling and eventing layer for the Jido ecosystem. The docs describe it as a CloudEvents-based toolkit for building event-driven and agent-based systems with routing, dispatch, history, and traceability built in.

### Why Do I Need Signals?

The package is presented as a structured alternative to raw message passing. Instead of sending opaque messages between processes, the docs encourage using signals when you need:

- a standard event envelope
- routing rules that can grow with the system
- pluggable delivery adapters
- replayable history and causality tracking
- clearer observability for distributed flows

### Key Features

- Standardized signal structure: CloudEvents-compatible envelopes with validation and metadata.
- High-performance signal bus: in-memory pub/sub with middleware and persistent subscription support.
- Advanced routing engine: trie-based routing with wildcard support and ordered execution.
- Pluggable dispatch system: multiple adapters for PID, PubSub, HTTP, logging, console, and related targets.
- Causality and conversation tracking: signal relationships are part of the model so debugging and audit trails are first-class.

### Installation

The docs support both Igniter-based setup and manual installation, with Igniter positioned as the easiest path for initial configuration.

### Quick Start

The quick start sequence is simple and practical:

1. Start a signal bus.
2. Define a subscriber.
3. Subscribe and publish signals.

That flow introduces the package through actual event movement rather than only type definitions.

### Core Concepts

- The signal: a structured event envelope carrying payload plus metadata.
- Custom signal types: typed signal modules with validation and extension policy.
- The router: matching and dispatch rules for signal paths.
- Dispatch system: the layer that delivers signals to real destinations.

### Advanced Features

The docs spend significant time on production behavior:

- persistent subscriptions
- middleware pipelines
- causality tracking
- full history and replay
- snapshots
- isolated infrastructure for multi-tenant or test usage

### Use Cases

The README explicitly frames the package around:

- microservices communication
- agent-based systems
- event sourcing
- distributed workflows

### Documentation And Development

The docs point readers toward guide pages for setup, routing, the bus, journaling, serialization, and advanced usage. The development section covers environment requirements, tests, quality checks, and docs generation.

## Guides In Sidebar Order

### Guides

- `Getting Started`: base setup and first signal flow.
- `Signals and Dispatch`: the envelope model plus adapter-based delivery.
- `Event Bus`: publish/subscribe, middleware, and runtime bus behavior.
- `Signal Router`: pattern matching and route definition.
- `Signal Extensions`: extending the base signal shape safely.
- `Signal Journal`: persistence, replay, and audit history.
- `Serialization`: JSON, MessagePack, Erlang term, and serializer concerns.
- `Advanced Usage`: operational and scaling scenarios beyond the basics.

## API Surface In Sidebar Order

### Core Signal

- `Jido.Signal`
- `Jido.Signal.Error`
- `Jido.Signal.ID`
- `Jido.Signal.Util`

This group defines the signal envelope, identifiers, and common helpers.

### Signal Routing

- `Jido.Signal.Router`
- `Jido.Signal.Router.Engine`
- `Jido.Signal.Router.HandlerInfo`
- `Jido.Signal.Router.NodeHandlers`
- `Jido.Signal.Router.PatternMatch`
- `Jido.Signal.Router.Route`
- `Jido.Signal.Router.Router`
- `Jido.Signal.Router.TrieNode`
- `Jido.Signal.Router.Validator`
- `Jido.Signal.Router.WildcardHandlers`

These modules make up the routing engine, including trie structures and pattern matching.

### Event Bus

- `Jido.Signal.Bus`
- `Jido.Signal.Bus.Middleware`
- `Jido.Signal.Bus.Middleware.Logger`
- `Jido.Signal.Bus.MiddlewarePipeline`
- `Jido.Signal.Bus.PersistentSubscription`
- `Jido.Signal.Bus.RecordedSignal`
- `Jido.Signal.Bus.Snapshot`
- `Jido.Signal.Bus.Snapshot.SnapshotData`
- `Jido.Signal.Bus.Snapshot.SnapshotRef`
- `Jido.Signal.Bus.State`
- `Jido.Signal.Bus.Stream`
- `Jido.Signal.Bus.Subscriber`

This is the operational pub/sub core, including persistence and snapshots.

### Signal Dispatch

- `Jido.Signal.Dispatch`
- `Jido.Signal.Dispatch.Adapter`
- `Jido.Signal.Dispatch.CircuitBreaker`
- `Jido.Signal.Dispatch.ConsoleAdapter`
- `Jido.Signal.Dispatch.Http`
- `Jido.Signal.Dispatch.LoggerAdapter`
- `Jido.Signal.Dispatch.Named`
- `Jido.Signal.Dispatch.NoopAdapter`
- `Jido.Signal.Dispatch.PidAdapter`
- `Jido.Signal.Dispatch.PubSub`
- `Jido.Signal.Dispatch.Webhook`

This group covers adapter-based delivery and resilience around outbound dispatch.

### Signal Journal

- `Jido.Signal.Journal`
- `Jido.Signal.Journal.Adapters.ETS`
- `Jido.Signal.Journal.Adapters.InMemory`
- `Jido.Signal.Journal.Adapters.Mnesia`
- `Jido.Signal.Journal.Persistence`

These modules back history, replay, and persistence.

### Serialization

- `Jido.Signal.Serialization.Config`
- `Jido.Signal.Serialization.ErlangTermSerializer`
- `Jido.Signal.Serialization.JsonDecoder`
- `Jido.Signal.Serialization.JsonSerializer`
- `Jido.Signal.Serialization.ModuleNameTypeProvider`
- `Jido.Signal.Serialization.MsgpackSerializer`
- `Jido.Signal.Serialization.Serializer`
- `Jido.Signal.Serialization.TypeProvider`

### System Infrastructure

- `Jido.Signal.Application`
- `Jido.Signal.Registry`
- `Jido.Signal.Registry.Subscription`

### Errors And Exceptions

The sidebar ends with a dedicated exception set for dispatch, execution, internal, invalid, routing, and timeout failures.

## Short Takeaway

`jido_signal` is the event backbone of the ecosystem. The docs emphasize that it is not just a message wrapper: it is a full signaling stack with routing, journaling, dispatch adapters, and enough structure to support debugging, replay, and multi-process coordination.
