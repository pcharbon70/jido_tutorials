# Lesson 04: Running Actions With `Jido.Exec`

## What This Lesson Is For

So far, we have spent time on the action itself.

We learned:

- how to define one action
- how to think about its input boundary
- how to think about its output promise
- how to make its failures legible

That was the right place to begin.

But now we need to widen the frame.

An action can be beautifully defined and still leave one important question unanswered:

How does it actually get run in a real system?

That is the job of `Jido.Exec`.

This lesson is where we stop thinking only about the shape of the action and start thinking about the shape of execution.

By the end, you should have:

- a feel for why `Jido.Exec` exists
- one synchronous example
- one asynchronous example
- a simple understanding of timeout, cancellation, and retry concerns
- one or two small tests or runnable examples that prove the flow

Estimated pace: about 15 minutes as a spoken walkthrough.

## The Big Idea

An action tells us what the work is.

`Jido.Exec` tells us how that work gets carried through a runtime.

That difference matters.

If you call `run/2` directly, you are touching only the core logic of the action.

If you call `Jido.Exec.run/4`, you are stepping into the execution layer that can:

- validate inputs and outputs
- normalize context
- apply timeout rules
- support retries and backoff
- emit telemetry
- run asynchronously when needed

That is why this lesson belongs here.

We have now reached the point where the action is trustworthy enough to deserve a real execution story.

One small but important point to make explicit:

`Jido.Exec` does not replace the action's `run/2`.

It is the layer that prepares and manages execution around that callback, and then executes the action through its `run/2` function.

## Why Not Just Call `run/2` Forever

This is a helpful question to ask early.

After all, `run/2` already works.

Why add another layer?

Because direct calls are good for understanding an action, but they are not the whole story of running actions in a system.

A real system cares about questions like:

- what if the params are invalid?
- what if the work takes too long?
- what if I want to run it in the background?
- what if I need retry behavior?
- what if I want a consistent execution surface across many actions?

Those are execution questions, not business-logic questions.

And `Jido.Exec` exists so the action itself does not have to carry all that weight alone.

## A Simple Way to Think About `Jido.Exec`

For this lesson, a very plain description is enough:

`Jido.Exec` is the runtime doorway for actions.

It lets you move from:

"Here is an action module."

to:

"Here is an action being executed in a consistent way."

That doorway becomes more valuable as the system grows.

But it is useful even now, while the examples are still small.

## Step 1: Prepare the Playground for Async Work

The official docs note that `run_async/4` uses `Task.Supervisor`.

So before we talk about asynchronous execution, let us give the playground that piece of support.

In your application supervision tree, add:

```elixir
children = [
  {Task.Supervisor, name: Jido.Action.TaskSupervisor}
]
```

If your `application.ex` already has children, just include it in the list.

This is an important little moment in the series.

Our project is starting to feel more like a runtime and less like a folder full of modules.

That is exactly the transition we want.

## Step 2: Run an Existing Action Synchronously

Let us begin with the calmest possible use of `Jido.Exec`.

We already have `PrepareWelcomeCard` from the previous lesson.

Now we ask the runtime to execute it for us.

```elixir
alias Jido.Exec
alias JidoPlayground.Actions.PrepareWelcomeCard

Exec.run(
  PrepareWelcomeCard,
  %{name: "Pascal", audience: "member", status: "active"},
  %{}
)

# {:ok, %{title: "Welcome back", message: "Welcome back, Pascal."}}
```

This is still simple, but the point of view has changed.

We are no longer calling the action directly.
We are asking the execution layer to do it.

That is a small shift in syntax and a big shift in meaning.

The action remains the capability.
`Jido.Exec` becomes the runtime path.

In other words:

- the action still owns `run/2`
- `Jido.Exec` is what calls that `run/2` in a fuller runtime context

## Step 3: Notice What the Runtime Is Quietly Doing

An easy first impression at this point is to look at `Exec.run/4` and think it is only a wrapper.

But the docs describe much more than that.

This execution layer is where several runtime concerns gather:

- validation
- timeout handling
- retry and backoff behavior
- telemetry
- context handling

That means `Jido.Exec` is not trying to replace the action.

It is trying to carry the runtime responsibilities around the action.

And that is a healthy separation.

The action owns the business capability.
The execution layer owns the act of running it well.

## Step 4: Add One Small Slow Action for Async Examples

To make asynchronous execution feel real, it helps to use an action that clearly takes a little time.

Create:

`lib/jido_playground/actions/slow_wave.ex`

with:

```elixir
defmodule JidoPlayground.Actions.SlowWave do
  use Jido.Action,
    name: "slow_wave",
    description: "Waits briefly and then returns a greeting",
    category: "demo",
    tags: ["async", "timing"],
    vsn: "1.0.0",
    schema: [
      name: [type: :string, required: true],
      delay_ms: [type: :integer, default: 300]
    ],
    output_schema: [
      message: [type: :string, required: true]
    ]

  @impl true
  def run(params, _context) do
    Process.sleep(params.delay_ms)
    {:ok, %{message: "Hello #{params.name}."}}
  end
end
```

This action is not here because sleeping is interesting.

It is here because time is now part of the lesson.

We need a tiny piece of work that lasts long enough for async behavior to become visible.

## Step 5: Run an Action Asynchronously

Now we can ask `Jido.Exec` to start the work and give us a reference back immediately.

```elixir
alias Jido.Exec
alias JidoPlayground.Actions.SlowWave

async_ref =
  Exec.run_async(
    SlowWave,
    %{name: "Pascal", delay_ms: 300},
    %{}
  )

result = Exec.await(async_ref)

# {:ok, %{message: "Hello Pascal."}}
```

This flow is worth reading slowly.

First:

- `run_async/4` starts the action and gives you an async reference

Then:

- `await/1` or `await/2` waits for the result later

That means execution is no longer a single tight moment.

It becomes a conversation with the runtime:

- start the work
- keep the handle
- decide when to wait

That is a big step forward for such a small example.

## A Very Human Way to Understand Async

Synchronous execution says:

"Do this now, and I will wait here until you finish."

Asynchronous execution says:

"Start this now, and I will come back for the answer."

That simple contrast is enough for now.

It does not need to feel magical.

And in the Jido docs, this async path still stays disciplined:

- you get an explicit reference
- the reference belongs to the process that started the work
- `await` and `cancel` are tied to that ownership model

That is a nice design choice because it keeps async behavior from becoming vague.

## Step 6: Timeouts Are Part of the Runtime Story

Once time enters the picture, timeout enters the picture too.

The docs for `await/2` show that if you wait too little for a slow action, you will get a timeout-shaped error.

For example:

```elixir
async_ref =
  Exec.run_async(
    SlowWave,
    %{name: "Pascal", delay_ms: 500},
    %{}
  )

Exec.await(async_ref, 100)

# {:error, %Jido.Action.Error{type: :timeout, ...}}
```

This is a lovely example of why the runtime layer exists.

The action itself is not necessarily "wrong."
It is just slower than the current execution contract allows.

So the runtime tells that story clearly.

That usually feels much better than a vague hang or a mysterious process failure.

## Step 7: Cancellation Is a Choice, Not a Crash

The docs also show that an asynchronous action can be cancelled with `cancel/1`.

```elixir
async_ref =
  Exec.run_async(
    SlowWave,
    %{name: "Pascal", delay_ms: 5_000},
    %{}
  )

Exec.cancel(async_ref)

# :ok
```

This matters because not every long-running piece of work should finish just because it started.

Sometimes the system changes its mind.

Maybe the user navigated away.
Maybe the result is no longer needed.
Maybe another path made the work irrelevant.

Cancellation lets the runtime say:

"Stop this intentionally."

That is a much healthier story than letting abandoned work drift on without purpose.

## Step 8: Add a Small Telemetry Hook

The official `jido_action` docs also show that actions emit telemetry events.

That means the runtime can tell the rest of the system that an action finished, how long it took, and what metadata came with it.

For a beginner lesson, the easiest way to feel this is to attach one tiny handler:

```elixir
:telemetry.attach(
  "playground-action-stop",
  [:jido, :action, :stop],
  fn _event, _measurements, metadata, _config ->
    Logger.info("Action completed: #{metadata.action}")
  end,
  %{}
)
```

You could place that in a temporary IEx session while experimenting, or in a small setup area for the playground if you want to observe action execution during development.

The point of the example is not to build a full observability system yet.

It is simply to notice that action execution is visible.

That matters because once actions become workflows, background jobs, or agent tools, being able to observe them stops being optional.

And again, this belongs in the runtime story around the action, not inside every action's core logic.

## Step 9: Retries and Backoff Are Runtime Concerns Too

The `Jido.Exec` docs also describe execution options like:

- `timeout`
- `max_retries`
- `backoff`
- `log_level`

You do not need to master all of those today.

It is enough to notice where they belong.

They belong here, in the execution layer.

For example, a call might look like:

```elixir
Exec.run(
  PrepareWelcomeCard,
  %{name: "Pascal", audience: "member", status: "active"},
  %{},
  timeout: 1_000,
  max_retries: 2,
  backoff: 100
)
```

The lesson is not "always retry."

The lesson is:

execution policy sits around the action, not inside every action.

That is a very useful design boundary.

## Step 10: Add Small Tests or Runnable Examples

Now let us turn the ideas into something the learner can actually run.

Create:

`test/jido_playground/actions/exec_test.exs`

with:

```elixir
defmodule JidoPlayground.Actions.ExecTest do
  use ExUnit.Case, async: true

  alias Jido.Exec
  alias JidoPlayground.Actions.PrepareWelcomeCard
  alias JidoPlayground.Actions.SlowWave

  test "runs an action synchronously through Jido.Exec" do
    assert {:ok, %{title: "Welcome", message: "Welcome, Pascal."}} =
             Exec.run(
               PrepareWelcomeCard,
               %{name: "Pascal", audience: "visitor", status: "active"},
               %{}
             )
  end

  test "runs an action asynchronously and awaits the result" do
    async_ref =
      Exec.run_async(
        SlowWave,
        %{name: "Pascal", delay_ms: 50},
        %{}
      )

    assert {:ok, %{message: "Hello Pascal."}} = Exec.await(async_ref, 1_000)
  end
end
```

Then run:

```bash
mix test
```

If you want to explore timeout behavior, try a manual variation with a very short `await/2` timeout.

That can be a better teaching move than turning timeout into a flaky test.

## What This Lesson Is Really Teaching

On the surface, this lesson is about a few functions:

- `run/4`
- `run_async/4`
- `await/1`
- `await/2`
- `cancel/1`
- `:telemetry.attach/4`

But underneath that, it is teaching something more important:

runtime behavior deserves its own language.

That is a big theme in Jido overall.

Instead of burying execution concerns inside every action, the system gives those concerns a proper place to live.

That makes the whole stack easier to reason about.

The action stays focused on its capability.
The execution layer stays focused on carrying that capability through time, waiting, failure, and cancellation.

## One Helpful Mental Model

If Lesson 02 taught:

"An action is a declared capability."

Then Lesson 04 teaches:

"Execution is a policy around that capability."

That policy can decide:

- how long to wait
- whether to retry
- whether to run in the background
- whether to cancel

And once you see that clearly, the next lessons start to feel natural.

Because composition is easier when actions already have a dependable runtime path.

## Small Exercise

Spend five or ten minutes running one action in two different ways.

### Exercise Prompt

Using either `PrepareWelcomeCard`, `SlowWave`, or one of your own actions:

1. run it once with `Jido.Exec.run/4`
2. run it once with `Jido.Exec.run_async/4` and `Jido.Exec.await/2`
3. if you use a slow action, try one very short timeout

If your playground is ready for it, also try one cancellation with `cancel/1`.

### Deliverable

By the end of the exercise, you should have:

- one synchronous execution example
- one asynchronous execution example
- one observed runtime behavior such as timeout or cancellation

### Reflection Question

What felt most useful about `Jido.Exec`:

- not calling `run/2` directly
- having async handles
- having timeout control
- knowing retries belong to the runtime layer

The point is not to choose the "best" one.

The point is to notice which runtime concern suddenly made the action feel more real to you.

## What You Should Walk Away With

If this lesson landed well, you may now feel that actions do not live alone.

They live inside an execution story.

That story is what `Jido.Exec` provides.

Here is the practical summary:

- `Jido.Exec.run/4` gives a consistent runtime path for synchronous execution
- `Jido.Exec.run_async/4` starts work in the background and returns an async reference
- `await/1` and `await/2` let you collect the result later
- `cancel/1` lets the runtime stop work intentionally
- timeout, retry, and backoff belong to the execution layer, not to every action's core logic

## What Comes Next

In the next lesson, we stay inside the `jido_action` phase, but we take another step outward.

Now that we know what an action is and how it gets run, we can start asking a new question:

How do several actions become one flow?

That is where `Jido.Instruction` enters the picture, and where single capabilities begin turning into workflows.
