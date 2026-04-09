# Lesson 02: Your First Action

## What This Lesson Is For

In Lesson 00, we built a map.

In Lesson 01, we built a playground.

Now we are ready for the first real object in the series:

one action.

This is the moment where the ecosystem stops feeling like a set of names and starts feeling like a system we can touch.

We are not building an agent yet.
We are not wiring events yet.
We are not doing anything with AI yet.

We are doing something smaller and more important:

we are teaching the system how to do one clear piece of work.

That is the whole lesson.

By the end, you should have:

- one small `Jido.Action` module
- a feel for the shape of an action
- a basic understanding of metadata, schema, and `run/2`
- a couple of tests that prove the action is alive

Estimated pace: about 15 minutes as a spoken walkthrough.

## The Big Idea

When people hear the word "agent," they often imagine a system that is already complex.

But Jido asks us to begin somewhere much quieter.

Before a system can coordinate work, it needs to know what work is.

That is why `jido_action` comes first in the learning path.

An action is a named unit of work.

That description sounds simple, and that is exactly what makes it valuable.

It gives us a shape for one thing the system can do.

Instead of saying:

"Some code somewhere handles greetings."

we begin saying:

"The system has an action whose job is to greet a visitor."

That is a very different feeling.

It is more explicit.
More reusable.
More testable.
And later, much easier to compose.

## Why Start With Something This Small

There is a temptation, especially in agent systems, to begin with behavior that already feels impressive.

But impressive is not the same as clear.

A first action can be pleasantly simple.

That simplicity is useful.
It gives the shape of the action room to stand out.

If the first action is small enough, then the learner can notice the shape around the behavior:

- the action has a name
- the action has a description
- the action declares what it expects
- the action returns a clear result

That is the part we care about today.

The actual business logic can be tiny.

## Step 1: Add `jido_action` to the Playground

In the project from Lesson 01, add `jido_action` to your dependencies.

The official docs currently show:

```elixir
defp deps do
  [
    {:jido_action, "~> 2.0"}
  ]
end
```

Then run:

```bash
mix deps.get
```

This is the first moment where the playground starts becoming specific.

Up to now, it was just a calm Elixir space.

Now it begins learning a language for work.

## Step 2: Create the First Real Action

In Lesson 01, we created placeholders so the project would already have a shape.

Now we are going to place our first real module inside that shape.

Create a file like:

`lib/jido_playground/actions/greet_visitor.ex`

and add:

```elixir
defmodule JidoPlayground.Actions.GreetVisitor do
  use Jido.Action,
    name: "greet_visitor",
    description: "Builds a short greeting for a visitor",
    category: "communication",
    tags: ["greeting", "intro"],
    vsn: "1.0.0",
    schema: [
      name: [type: :string, required: true, doc: "The visitor's name"],
      tone: [type: {:in, ["warm", "formal"]}, default: "warm", doc: "Greeting style"]
    ],
    output_schema: [
      message: [type: :string, required: true]
    ]

  @impl true
  def run(params, _context) do
    message =
      case params.tone do
        "formal" -> "Welcome, #{params.name}."
        _ -> "Hi #{params.name}, glad you're here."
      end

    {:ok, %{message: message}}
  end
end
```

If this is your first time seeing a Jido action, there is no need to memorize every line.

For now, it is enough to notice the overall shape.

## Step 3: Read the Shape Before Reading the Logic

This is the most important move in the lesson.

It can help to read everything around `run/2` first.

That is where the action begins to feel different from an ordinary function.

### `name`

The action has a stable identity.

This tells the system, and later the human reader, what the action is called in a structured way.

### `description`

The action can explain itself.

This may feel like a small courtesy right now, but later it becomes useful for discoverability, documentation, and AI tool surfaces.

### `category`, `tags`, and `vsn`

These are metadata.

They do not make the greeting smarter.
They make the action easier to organize and reason about.

This is one of the important themes in `jido_action`:

we are not just writing logic.
We are declaring capability.

### `schema`

This is where the action says what kind of input it expects.

In this case:

- `name` must be present and must be a string
- `tone` must be either `"warm"` or `"formal"`

The action is beginning to explain what counts as valid use.

### `output_schema`

This is the same idea, but for the result.

Our action promises to return a map with a required `message` string.

That promise matters.

A good action is not only about what it does.
It is also about what shape of result other parts of the system can safely expect.

## Step 4: Now Read `run/2`

Only now do we look at the logic.

And when we do, it is intentionally small.

That is the point.

```elixir
@impl true
def run(params, _context) do
  message =
    case params.tone do
      "formal" -> "Welcome, #{params.name}."
      _ -> "Hi #{params.name}, glad you're here."
    end

  {:ok, %{message: message}}
end
```

This function does not try to carry the whole idea of the action.

It only carries the business behavior.

Everything else, like naming, validation expectations, and output promises, lives in the declaration around it.

That is one of the nicest things about this style:

the logic gets to stay small because the module already carries meaning.

## What an Action Contract Feels Like

At this stage, a useful way to think about the action contract is:

"This module is not just code that runs. It is a declared capability."

That capability has:

- an identity
- a purpose
- accepted inputs
- expected outputs
- one implementation path

This is why the first real lesson in the Jido stack begins here.

We are learning how to give work a clean boundary.

## Step 5: Add a Couple of Small Tests

Now let us give the action a place to prove itself.

Create:

`test/jido_playground/actions/greet_visitor_test.exs`

with:

```elixir
defmodule JidoPlayground.Actions.GreetVisitorTest do
  use ExUnit.Case, async: true

  alias Jido.Action.Runtime
  alias JidoPlayground.Actions.GreetVisitor

  test "returns a warm greeting" do
    assert {:ok, %{message: "Hi Pascal, glad you're here."}} =
             GreetVisitor.run(%{name: "Pascal", tone: "warm"}, %{})
  end

  test "accepts valid params through runtime validation" do
    assert {:ok, validated_params} =
             Runtime.validate_params(%{name: "Pascal", tone: "formal"}, GreetVisitor)

    assert validated_params.name == "Pascal"
    assert validated_params.tone == "formal"
  end
end
```

Then run:

```bash
mix test
```

These tests are doing two different jobs.

The first one checks the visible behavior.

The second one gives us a first gentle look at validation by using `Jido.Action.Runtime.validate_params/2`, which is the runtime helper described in the official docs.

We are only peeking at that validation surface today.

The next lesson is where we will spend real time with failures, bad inputs, and predictable error behavior.

## Why This Is Enough for a First Action

It can be tempting to add more right away.

Maybe you want:

- more fields
- more branching logic
- more return data
- more tests
- more realistic business behavior

That instinct is completely understandable, though it may be kinder to the learning process to stay small for one more lesson.

The goal of a first action is not to be impressive.

The goal is to make the structure visible.

Right now, this tiny greeting action already teaches us several important things:

- actions have a clear declaration
- metadata belongs with the action
- input and output shape matter
- the implementation can stay simple
- tests become easier when the unit of work has a clear boundary

That is already a lot.

## A Quiet Preview of What Comes Later

Even in this tiny example, we can already feel why actions are a foundation for the rest of the stack.

Later:

- `Jido.Exec` will help us run actions in a richer runtime
- `Jido.Instruction` will let us compose actions into flows
- `jido` agents will rely on clear capabilities like these
- `jido_ai` will be able to treat actions like tools

That future is not the lesson today.

But it is good to notice that the shape we are creating is not temporary.

It is the beginning of the whole ecosystem.

## Small Exercise

Spend five or ten minutes making a second tiny action of your own.

Keep it small.

### Exercise Prompt

Create one action that does one of these:

- formats a full name
- creates a short welcome title
- turns a sentence into uppercase
- chooses between two short messages

Give it:

- a `name`
- a `description`
- a small `schema`
- a small `output_schema`
- one simple `run/2`

Then add one happy-path test.

### Deliverable

By the end of the exercise, you should have:

- your original `GreetVisitor` action
- one second action of your own
- at least one passing test for each action

### Reflection Question

What felt most different from writing a normal function:

- naming the action
- adding metadata
- declaring the schema
- returning a shaped `{:ok, result}` value

That answer matters, because it tells you where the new mental model is beginning to form.

## What You Should Walk Away With

If this lesson landed well, you may now feel that an action is more than a function with a wrapper around it.

It is a small declared capability.

It says:

- here is what I am
- here is what I expect
- here is what I return
- here is the work I do

And that clarity is exactly why actions are the first real building block in the Jido stack.

Here is the practical summary:

- you added `jido_action` to the playground
- you wrote your first real action module
- you used metadata, schema, and output shape
- you added small tests for behavior and validation

## What Comes Next

In the next lesson, we stay with the same package, but we shift our attention.

Instead of asking, "How do I define an action?"

we will ask:

"What happens when the inputs are wrong, the output shape matters, or the result is not what we expected?"

That is where actions start feeling dependable, not just neat.
