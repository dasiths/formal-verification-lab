# Lab 01: Model before code

[Course map](../../README.md)

**Prerequisites:** none. No installation or programming language is required.

## 1. Goal

How do we turn "the job queue should work correctly" into something checkable?

We will describe one job and two workers, Alice and Bob. Our requirement is:

> At most one worker may believe it has claimed the job at any time.

The new concept is an **invariant**: a condition that must hold in the initial
state and every state reachable through the allowed actions.

This is a safety property: it rules out something bad happening.

### State

A state is a snapshot of everything our model remembers.

| Variable | Meaning | Initial value |
| --- | --- | --- |
| `claimants` | Set of workers that believe they have claimed the job | Empty set |
| `ready` | Set of workers whose availability check succeeded | Empty set |

A set contains each member at most once. For example, `{Alice, Bob}` contains two
workers; `{Alice}` contains one.

We deliberately track workers' beliefs rather than only a single `owner` field.
A single field could hide the situation where both workers believe they own
the job.

Our invariant is:

```text
The number of workers in claimants is at most 1.
```

### Actions

An action describes one allowed state change. For either worker:

| Action | When it is allowed | What changes |
| --- | --- | --- |
| Check | `claimants` is empty and the worker is not in `ready` | Add the worker to `ready` |
| Claim | The worker is in `ready` | Add the worker to `claimants` and remove it from `ready` |

Anything not mentioned in an action's changes stays the same. In particular,
Claim does **not** check availability again or erase the other worker's readiness.

Each action is one indivisible model step, but Check and Claim are separate
steps. Either worker can take the next allowed action. This lets us describe
overlapping work by interleaving its steps.

There are no releases, crashes, retries, or additional jobs in this first model.

## 2. Predict

Before opening the walkthrough, write down your answers:

1. Does the initial state satisfy the invariant?
2. Can both workers complete Check before either completes Claim?
3. Does the invariant hold after every possible sequence of actions?

Try to justify your third answer with a sequence, not just an intuition.

## 3. Experiment

On paper or in a scratch editor, start with both sets empty. Apply the following
actions in order, checking that each action is allowed:

```text
Alice checks
Bob checks
Alice claims
Bob claims
```

After each action, record `ready`, `claimants`, and whether the invariant holds.
If you think an action is not allowed, identify which rule prevents it.

## 4. Observe

<details>
<summary>Open the walkthrough after making your prediction</summary>

| Step | Action | ready | claimants | Invariant holds? |
| --- | --- | --- | --- | --- |
| 0 | Initial state | `{}` | `{}` | Yes |
| 1 | Alice checks | `{Alice}` | `{}` | Yes |
| 2 | Bob checks | `{Alice, Bob}` | `{}` | Yes |
| 3 | Alice claims | `{Bob}` | `{Alice}` | Yes |
| 4 | Bob claims | `{}` | `{Alice, Bob}` | No |

Every action is allowed by our rules. At step 4, Bob only needs to be in `ready`;
his earlier observation is enough to let him claim.

This sequence is a **counterexample** to the invariant. A single reachable
failure is enough to disprove an "always" claim.

The problem is the gap between checking availability and acting on that
observation. Both workers followed the rules, but the rules permitted a race.

</details>

## 5. Change one thing

Change Claim so that it is allowed only when the worker is in `ready` **and**
`claimants` is empty. Keep the condition check and the update together in the
same indivisible action.

1. Replay the same sequence. Which action is now blocked?
2. Try a sequence where Bob claims first.
3. Explain why no allowed action can take `claimants` from one worker to two.

<details>
<summary>Check your reasoning</summary>

Bob's final Claim is blocked when Alice has already claimed. If Bob claims
first, Alice's Claim is blocked instead.

Initially there are no claimants. Check never changes `claimants`. The changed
Claim can only add one worker to an empty set. These facts explain why every
allowed step preserves the invariant, not just the two sequences we tried.

The condition and update must really be indivisible. Splitting them into
separate implementation steps would reintroduce the gap we just removed.

Bob may remain ready forever after Alice claims. That does not violate this
safety property: we have not required every worker to make progress.

</details>

## 6. Limits

- The counterexample establishes a bug in these model rules, not in an
  implementation we have inspected.
- The revised-rule argument depends on treating Claim as indivisible.
  Production code would need a mechanism that actually enforces that behavior.
- We have not modeled crashes, releases, retries, networking, or multiple jobs.
- Exclusive ownership does not establish eventual completion, fairness, or
  exactly-once effects.
- No verifier has run yet. The original model was refuted manually, and the
  revised model has a short informal preservation argument.

## 7. Factory connection

An AI assistant can propose states, actions, and candidate fixes. Your job is to
check that these describe the requirement you actually care about.

For example, deleting Claim would prevent duplicate claims, but it would also
prevent all useful work. Making a safety property pass is not the whole goal.

In the next lab, we will encode the original rules in TLA+ and ask TLC to find a
counterexample. TLC may find a different action order; the important evidence
will be an allowed execution that violates the same invariant.

**Before moving on:** explain the race, the proposed fix, and the fix's atomicity
assumption in your own words.

[Back to the course map](../../README.md)
