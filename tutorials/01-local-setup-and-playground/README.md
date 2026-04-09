# Lesson 01: Local Setup And Playground

## What This Lesson Is For

In the first lesson, we built a mental map.

We learned that the Jido ecosystem becomes easier to understand when we meet it in layers:

- `jido_action` for work
- `jido_signal` for communication
- `jido` for runtime
- `jido_ai` for intelligence

That was the map.

This lesson is about the room where the map will become real.

We are going to create a local playground for the rest of the series.

Not a polished app.
Not a production system.
Not a clever demo.

Just a safe little place where each next lesson can land without chaos.

That may sound small, but it matters a lot.

When people skip this step, every tutorial after it feels more fragile than it should. You are never sure if a problem comes from the idea you are learning or from the setup underneath it.

So today, our job is humble on purpose:

- create a working Elixir project
- verify that the environment is healthy
- add a few placeholder modules for the ideas we will meet later
- make sure tests already have a place in the project

Estimated pace: about 15 minutes as a spoken walkthrough.

## The Big Idea

This lesson is not about understanding all of Jido.

It is about building a stable table before we start putting tools on it.

That image is worth holding onto.

If Lesson 00 was a map, Lesson 01 is a workbench.

We are preparing a space where future lessons can accumulate naturally.

That means we are not trying to do everything today.

We are not installing every dependency.
We are not writing our first agent yet.
We are not touching AI yet.

Instead, we are doing something more useful:

we are removing friction from the future.

## Why a Playground Matters

There is a difference between a project and a playground.

A project usually wants to prove something.
A playground wants to teach something.

In a project, you often optimize for speed and outcomes.
In a playground, you optimize for clarity.

That difference is important for this series.

We are going to meet several ideas that build on one another. If the learning space is messy, every new concept feels heavier. If the learning space is calm, each new concept has somewhere obvious to go.

So our playground needs only three qualities:

- it should run
- it should be easy to change
- it should leave room for future layers

That is enough.

## What We Are Building Today

By the end of this lesson, you should have a small Elixir application with:

- a supervision tree
- a working test command
- a few placeholder modules that mirror the layers from Lesson 00
- a simple sense of where future code will belong

This is less about writing code and more about arranging the room.

## Before We Start

We only need one thing to be true before we begin:

your Elixir environment should be able to create and run a normal Mix project.

If you want a quick confidence check, run:

```bash
elixir --version
mix --version
```

You are not looking for a perfect setup.
You are only checking that the machine recognizes Elixir and Mix.

If those commands work, we can move forward.

If they do not, that is not a Jido problem yet.
That is just the environment asking for a little care first.

## Step 1: Create a Home

We are going to make a plain supervised Elixir project.

That is an intentional choice.

We want the project to feel ordinary at the beginning, because ordinary is easier to trust.

Run:

```bash
mix new jido_playground --sup
cd jido_playground
```

The `--sup` flag matters because this series is eventually heading toward runtime behavior, long-lived processes, and agent systems. We are not using all of that today, but it is good to start with a house that already has a front door.

At this point, you have not built a Jido system.

You have only built the place where a Jido system can grow.

That distinction keeps the lesson light.

## Step 2: Make Sure the Floor Is Solid

Before we add anything else, ask the fresh project to prove that it works.

Run:

```bash
mix test
```

This is one of the most useful moments in the whole lesson.

Why?

Because now you know the baseline.

If the empty project works, then future failures are easier to understand.
You are no longer debugging the universe.
You are only debugging the new thing you added.

That is a quiet superpower.

In a learning series, the baseline matters almost as much as the examples.

## Step 3: Leave Room for the Four Layers

Now we are going to do something very simple that pays off later:

we are going to create placeholders for the four ecosystem ideas we met in Lesson 00.

Not real implementations.
Not final abstractions.
Just signposts.

Create these files under `lib/jido_playground/`:

- `actions.ex`
- `signals.ex`
- `agents.ex`
- `ai.ex`

And give them minimal modules like this:

```elixir
defmodule JidoPlayground.Actions do
end
```

```elixir
defmodule JidoPlayground.Signals do
end
```

```elixir
defmodule JidoPlayground.Agents do
end
```

```elixir
defmodule JidoPlayground.AI do
end
```

These modules do almost nothing.

That is fine.

Their job today is not to be useful.
Their job is to reserve mental space.

When we reach later lessons, we will already know where a new action example belongs, where a signal example belongs, where an agent example belongs, and where AI-specific pieces belong.

This may feel overly simple right now.
But it is exactly the kind of small choice that makes a tutorial series feel coherent instead of scattered.

## A Calm Way to Read These Placeholders

Here is the spirit of those four files:

- `JidoPlayground.Actions` means, "This is where the system learns to do work."
- `JidoPlayground.Signals` means, "This is where the system learns to speak."
- `JidoPlayground.Agents` means, "This is where the system learns to hold state and respond."
- `JidoPlayground.AI` means, "This is where the system learns to reason with models later."

Nothing is "real" yet.

But the project is no longer empty.
It has direction.

And direction is often more valuable than content at the beginning.

## Step 4: Give Tests a Permanent Seat at the Table

The next thing we want is a tiny test that belongs to us, not just the one Mix generated.

Create a file like:

`test/playground_smoke_test.exs`

with something simple like:

```elixir
defmodule PlaygroundSmokeTest do
  use ExUnit.Case, async: true

  test "the playground modules exist" do
    assert Code.ensure_loaded?(JidoPlayground.Actions)
    assert Code.ensure_loaded?(JidoPlayground.Signals)
    assert Code.ensure_loaded?(JidoPlayground.Agents)
    assert Code.ensure_loaded?(JidoPlayground.AI)
  end
end
```

Then run:

```bash
mix test
```

This test is intentionally modest.

It does not prove that the system is smart.
It does not prove that the architecture is complete.
It only proves that your playground is alive, shaped, and ready for the rest of the journey.

That is enough for today.

## Step 5: See the Shape of the Playground

Once those pieces are in place, your project will roughly feel like this:

```text
jido_playground/
  lib/
    jido_playground.ex
    jido_playground/application.ex
    jido_playground/actions.ex
    jido_playground/signals.ex
    jido_playground/agents.ex
    jido_playground/ai.ex
  test/
    jido_playground_test.exs
    playground_smoke_test.exs
```

This is not impressive in the flashy sense.

That is exactly why it is good.

It tells us:

- the app boots
- the code layout already reflects the learning path
- the tests run
- future lessons have obvious landing places

That is what a good playground should do.

## What We Are Not Doing Yet

It is just as important to notice what we are deliberately not doing in this lesson.

We are not:

- building a real Jido action yet
- defining real signals yet
- running an agent yet
- adding AI features yet
- optimizing the folder structure too early

Why not?

Because this series is built on evolutive discovery.

We want each new lesson to feel earned.

If we pull too many later concepts into the room too early, the setup becomes noisy. The learner starts carrying names they do not understand yet. The project looks advanced before it becomes understandable.

So restraint is part of the design.

This lesson is successful if the project feels calm and ready.
Not if it feels complete.

## A Helpful Mindset for the Whole Series

Here is a sentence that will help in almost every upcoming lesson:

"Today I only need to prepare the next layer, not the whole system."

That is the spirit of the playground.

When we reach `jido_action`, we will fill the `Actions` side with something real.
When we reach `jido_signal`, we will finally give movement to the system.
When we reach `jido`, the runtime will start to matter.
When we reach `jido_ai`, the AI layer will already have foundations beneath it.

So this lesson is not a delay.
It is the first act of good system design.

## Small Exercise

Set a timer for five or ten minutes and complete this tiny build-out.

### Exercise Prompt

Create your own playground project and make it pass `mix test` after adding the four placeholder modules.

As you do it, give each placeholder one short comment in your own words.

For example:

- `Actions` might mean "small jobs"
- `Signals` might mean "messages with meaning"
- `Agents` might mean "stateful behavior"
- `AI` might mean "model-powered decisions"

### Deliverable

By the end of the exercise, you should have:

- one supervised Mix project
- four placeholder modules
- one smoke test
- one successful `mix test` run

### Reflection Question

Which part felt most reassuring:

- creating the project
- seeing tests pass
- naming the four placeholder layers

There is no right answer.

The point is to notice what made the project start to feel real.

## What You Should Walk Away With

If this lesson worked, you should now have a small local sandbox that feels ready rather than empty.

More importantly, you should feel the logic of the structure:

- we did not start by building features
- we started by creating a place for features to land

That is a subtle difference, but it changes everything about how the rest of the series will feel.

Here is the practical summary:

- the playground is a normal supervised Elixir app
- the project can already run tests
- the folder shape now mirrors the ecosystem map
- later lessons can build one layer at a time without rearranging everything

## What Comes Next

In the next lesson, we finally stop preparing the room and start putting a real object on the table.

That object will be our first action.

So the next step is small, concrete, and exactly where it should be:

we will teach the system how to do one clear piece of work.
