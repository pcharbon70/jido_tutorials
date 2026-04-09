# Lesson 03: Action Inputs, Outputs, And Errors

## What This Lesson Is For

In the last lesson, we wrote our first real action.

That was an important step, but it only showed the sunny side of the story.

The action had a clear name.
It had a schema.
It had a small `run/2`.
It returned a nice result.

That is a good beginning.

But a useful system cannot stop at "works when everything goes well."

This lesson is about the next step:

making an action dependable.

That means learning to care about three things:

- whether the input is valid
- whether the output keeps its promise
- what kind of error comes back when something goes wrong

By the end, you should have:

- one more robust action
- a clearer feel for input and output boundaries
- a simple way to think about validation versus business failure
- happy-path and error-path tests

Estimated pace: about 15 minutes as a spoken walkthrough.

## The Big Idea

Last time, we learned that an action is a declared capability.

This time, we learn that a good capability needs edges.

Those edges are what make a system trustworthy.

Without them, a module may still "work," but other parts of the system cannot rely on it very safely.

With them, the action becomes easier to compose, easier to test, and much easier to debug.

A dependable action answers three quiet questions:

- "What am I willing to accept?"
- "What am I promising to return?"
- "How will I fail when I cannot complete the work?"

That is the heart of this lesson.

## Why This Matters So Early

It can feel a little soon to talk about failure.

After all, we only just got our first action working.

But this is exactly when the conversation becomes valuable.

If we learn actions only as happy little wrappers, then later lessons become confusing. We start composing actions before we trust them. We start wiring runtime behavior before the boundaries are clear.

So this is not a detour.

It is the lesson that turns the first action from "neat" into "reliable."

And reliability is what makes the rest of the Jido stack possible.

## A Simple Way to Think About Boundaries

For this lesson, let us use very plain language.

An action has:

- a front door
- a back door
- a way of saying "I cannot do this"

The front door is the input.
The back door is the output.
The refusal is the error.

That is the whole focus for today.

And once you start seeing actions this way, the code becomes much easier to read.

## Step 1: Build a Slightly More Serious Action

We will stay with the welcoming theme from the previous lesson, but make the action a little more realistic.

Create:

`lib/jido_playground/actions/prepare_welcome_card.ex`

and add:

```elixir
defmodule JidoPlayground.Actions.PrepareWelcomeCard do
  use Jido.Action,
    name: "prepare_welcome_card",
    description: "Creates a small welcome card for someone entering the system",
    category: "communication",
    tags: ["welcome", "card"],
    vsn: "1.0.0",
    schema: [
      name: [type: :string, required: true, doc: "The person's name"],
      audience: [type: {:in, ["visitor", "member"]}, default: "visitor", doc: "Who the card is for"],
      status: [type: {:in, ["active", "blocked"]}, default: "active", doc: "Whether the person can receive a welcome card"]
    ],
    output_schema: [
      title: [type: :string, required: true],
      message: [type: :string, required: true]
    ]

  alias Jido.Action.Error

  @impl true
  def run(params, _context) do
    if params.status == "blocked" do
      {:error,
       Error.execution_error(
         "Cannot prepare a welcome card for a blocked person",
         %{name: params.name, status: params.status}
       )}
    else
      title =
        case params.audience do
          "member" -> "Welcome back"
          _ -> "Welcome"
        end

      {:ok,
       %{
         title: title,
         message: "#{title}, #{params.name}."
       }}
    end
  end
end
```

This action is still small.

That is good.

The lesson is not about bigger logic.
It is about clearer edges.

## Step 2: Notice the Two Different Kinds of Rules

This action contains two kinds of rules, and it is worth separating them.

### Rule type 1: shape rules

These live in the schema and output schema.

They answer questions like:

- is `name` present?
- is `audience` one of the allowed values?
- does the result include `title` and `message`?

These are boundary rules.

They are about whether the action is being used correctly.

### Rule type 2: business rules

These live in `run/2`.

They answer questions like:

- should a blocked person receive a welcome card at all?

That is a different kind of problem than a shape problem.
It is a domain problem.

The inputs can be perfectly valid and the action can still decide, honestly and clearly, that it cannot complete the work.

This is one of the most useful distinctions in the whole lesson.

Not every failure is a bad input.

Some failures happen because the request is valid, but the business situation still says no.

## Step 3: Input Validation Is the Front Door

The official docs for `Jido.Action.Runtime` expose `validate_params/2` as the runtime helper for input validation.

That gives us a very clear way to think about the action's front door.

When the action receives the wrong shape, the front door can say so clearly.

For example:

- missing `name`
- unsupported `audience`
- unsupported `status`

Those do not have to feel mysterious.
They are simply invalid requests.

This is powerful because it turns a vague bug into a clean boundary.

Instead of thinking:

"Something broke somewhere."

we can think:

"The action rejected an invalid input before the real work began."

That is a much calmer story.

## Step 4: Output Validation Is the Promise on the Back Door

Now let us talk about the back door.

The `output_schema` is a promise.

It says:

"If this action succeeds, the rest of the system should be able to expect this shape."

That promise matters more than it may seem.

If actions are going to be composed later, or exposed as tools later, or used by agents later, then the output needs to be dependable.

The runtime helper `validate_output/2` exists for exactly that reason.

And this is an important emotional shift:

output validation is not bureaucracy.

It is kindness to the next part of the system.

It means the action is not just producing data.
It is keeping a promise.

## Step 5: Errors Should Be Legible

The official docs for `Jido.Action.Error` make an important point:

errors are meant to be structured.

That does not mean you need to memorize every error type today.

It only means we want failures to come back in a form that can be recognized and understood.

For this lesson, we only need a simple model:

- validation errors describe bad input
- execution errors describe work that could not complete even though the request made sense

That distinction is enough for now.

It keeps the action honest.

And honesty is what makes systems easier to trust.

## Step 6: Add Tests That Prove the Boundaries

Now let us write tests that do more than prove the happy path.

Create:

`test/jido_playground/actions/prepare_welcome_card_test.exs`

with:

```elixir
defmodule JidoPlayground.Actions.PrepareWelcomeCardTest do
  use ExUnit.Case, async: true

  alias Jido.Action.Error
  alias Jido.Action.Runtime
  alias JidoPlayground.Actions.PrepareWelcomeCard

  test "builds a valid welcome card" do
    assert {:ok, output} =
             PrepareWelcomeCard.run(
               %{name: "Pascal", audience: "member", status: "active"},
               %{}
             )

    assert {:ok, validated_output} =
             Runtime.validate_output(output, PrepareWelcomeCard)

    assert validated_output.title == "Welcome back"
    assert validated_output.message == "Welcome back, Pascal."
  end

  test "rejects invalid input through runtime validation" do
    assert {:error, error} =
             Runtime.validate_params(%{audience: "visitor"}, PrepareWelcomeCard)

    assert %{type: :validation_error} = Error.to_map(error)
  end

  test "returns a structured execution error for blocked people" do
    assert {:error, error} =
             PrepareWelcomeCard.run(
               %{name: "Pascal", audience: "visitor", status: "blocked"},
               %{}
             )

    assert %{type: :execution_error} = Error.to_map(error)
  end
end
```

Then run:

```bash
mix test
```

These three tests do a lot of teaching in a very small amount of code.

### Test 1

The happy path still matters.

We want to know the action can succeed and produce the promised shape.

### Test 2

This one protects the front door.

It shows that invalid input can be handled more clearly than an unexpected crash.
The action boundary can reject it clearly.

### Test 3

This one protects the action's honesty.

The input is valid.
The request makes sense.
But the business situation still says no.

That becomes a structured execution error rather than a silent failure or a confusing return value.

## An Important Subtlety

There is a gentle but important thing to notice here.

We are using `Runtime.validate_params/2` and `Runtime.validate_output/2` explicitly in tests.

Why?

Because this helps us see the boundary behavior directly.

We are not hiding the idea behind a larger runtime yet.

Later lessons will introduce richer execution flows, and those boundaries will start feeling more automatic.

But for now, it is good to meet them with the lights on.

That makes the mental model much stronger.

## What Predictable Contracts Feel Like

At this point, the action is becoming more than a named capability.

It is becoming a reliable contract.

A predictable contract says:

- if the input arrives in the wrong shape, I will reject it clearly
- if I succeed, I will return the shape I promised
- if the work cannot be completed, I will fail in a structured way

That kind of behavior is not flashy.

It is better than flashy.

It is composable.

And composable pieces are what make the later Jido lessons possible.

## A Tiny Preview of Composition

Even though we are not composing actions yet, you can probably feel why this lesson matters for that future.

Imagine chaining actions together.

If one action returns a vague error, the next layer has to guess what happened.
If one action returns a strange output shape, the next action may fail for reasons that are hard to trace.

But if actions have dependable edges, composition gets calmer.

That is why this lesson belongs here, before `Jido.Exec`, before instructions, before agents.

We are still preparing the language of reliable work.

## Small Exercise

Spend five or ten minutes strengthening one action of your own.

### Exercise Prompt

Take either `GreetVisitor` from Lesson 02 or a small action you invented yourself, and improve it in three ways:

1. add or refine its `output_schema`
2. add one test that checks invalid input
3. add one test or one branch that returns a structured error for a valid-but-unsuccessful case

If you want a concrete direction, you can create an action that:

- formats a short profile card
- drafts a title and subtitle
- prepares a welcome email subject and body

### Deliverable

By the end of the exercise, you should have:

- one action with a clear input boundary
- one action with a clear output promise
- at least one failure path that is intentionally structured
- passing happy-path and error-path tests

### Reflection Question

Which felt more important as you worked:

- input validation
- output validation
- clear execution errors

There is no wrong answer.

The goal is to notice which part made the action feel trustworthy to you.

## What You Should Walk Away With

If this lesson landed well, you may now feel that reliability is part of the action itself, not an extra decoration around it.

An action becomes dependable when:

- the input has a clear boundary
- the output keeps a promise
- errors tell a clear story

That is the practical summary:

- you built a slightly more robust action
- you separated shape problems from business problems
- you used `Jido.Action.Runtime` to validate inputs and outputs
- you used `Jido.Action.Error` to normalize failure into something legible

## What Comes Next

In the next lesson, we stay with the same package again, but the perspective widens.

So far, we have defined actions and tightened their boundaries.

Next, we will look at how actions are actually run in a richer way:

synchronously, asynchronously, and with runtime concerns like retries, timeouts, and cancellation.

That is where an action stops being just a good module and starts becoming part of a real execution story.
