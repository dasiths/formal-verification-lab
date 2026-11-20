# Formal Verification Lab

A hands-on learning repository for formal verification in AI-assisted software
engineering. Start with small ideas, then build and check concrete examples.

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

## First exercise

Start with a tiny job queue and one safety property: a job must not be assigned
to two workers at the same time. Describe states and transitions in plain
language before writing TLA+ or installing tools.

No verification toolchain or runnable examples have been added yet.
