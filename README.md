# Formal Verification Lab

A hands-on learning repository for formal verification in AI-assisted software
engineering. Start with small ideas, then build and check concrete examples.

## Start here

Begin with [Lab 01: Model before code](labs/01-model-before-code/README.md).
You only need a text editor and a few minutes; no tools need to be installed.
Work through the labs in order, predicting the outcome before reading each
walkthrough.

## Learning path

We use a tiny job queue as a running example so that each lab introduces a new
idea without introducing a whole new system.

| Lab | Question | Deliverable | Status |
| --- | --- | --- | --- |
| [01 - Model before code](labs/01-model-before-code/README.md) | What does "correct" mean? | States, actions, an invariant, and a handwritten counterexample | Available |
| 02 - Find the race with TLA+ | Can a tool discover the failure? | A deliberately buggy model and a TLC counterexample | Planned |
| 03 - Fix and check | Does an atomic claim prevent duplicate ownership? | A corrected model checked within explicit bounds | Planned |
| 04 - Challenge the assumptions | What changes when workers crash or retry? | An extended model and an introduction to safety versus liveness | Planned |
| 05 - First Lean proof | How is proving different from exploring? | A proof that a defined claim operation preserves exclusive ownership | Planned |
| 06 - Build the factory loop | How do these checks constrain AI-generated changes? | An implementation, tests, verification checks, and CI | Planned |

Each lab follows the same structure:

1. **Goal:** one question and one new concept.
2. **Predict:** decide what you expect before seeing the result.
3. **Experiment:** follow a small manual exercise or run an exact command.
4. **Observe:** interpret the result, not just whether a command succeeded.
5. **Change one thing:** test your understanding with a small variation.
6. **Limits:** identify what the result does not establish.
7. **Factory connection:** separate what AI proposes from what checks it.

We add labs as we reach them and install tools only when needed. Buggy and
corrected models will stay separate so both experiments remain reproducible.
Later automation will distinguish an expected counterexample from a broken
checker invocation.

## The starting idea

Tests check selected examples. Formal verification uses mathematical models and
precise claims to reason about behavior beyond those examples. Any guarantee is
limited by the specification, assumptions, and relationship to the actual code.

An AI assistant can propose a specification, implementation, or proof. A verifier
checks the submitted artifact against precise rules; it does not establish that
we asked for the right thing.

## Two tools we will explore

| Tool | What we describe | What gets checked |
| --- | --- | --- |
| TLA+ with the TLC model checker | States, allowed transitions, and properties of a system | Whether properties hold throughout the configured model's reachable behaviors |
| Lean | Mathematical definitions, claims, and proofs | Whether a proof establishes its claim under the stated assumptions, checked by Lean's kernel |

TLC explores a configured model, often using finite bounds. A successful bounded
check is not automatically a proof for every system size. TLA+ also has proof
tools, which are separate from TLC model checking.

Lean can support proofs about algorithms and programs, but a proof about a model
does not automatically verify a separate production implementation.

## Where this fits in a software factory loop

1. A human identifies a requirement and its assumptions.
2. The human and AI turn it into a precise property and a small model.
3. AI proposes a model change or proof.
4. A checker produces evidence: a successful check, a counterexample, or an error.
5. The human and AI investigate failures without weakening the intended requirement.
6. Implementation, tests, and review connect the checked artifact to real code.
7. CI reruns the relevant checks as the system changes.

The goal is not to trust AI more. It is to make important claims independently
checkable while keeping humans responsible for intent and assumptions.

## Current scope

Lab 01 is a manual exercise. No verification toolchain, executable models, or
CI checks have been added yet.
