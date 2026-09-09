---
title: "We Verified the Navier–Stokes Resolution From Our Machine Shop. Here's How."
author: Future Present Labs
date: 2026-09-09T00:00:00-07:00
draft: true
description: "A Seattle machine shop ran independent Lean kernel verification of the 2026 Navier-Stokes and Euler resolution repositories. Full build logs, axiom audits, and why a proof checker doesn't care who you are."
keywords: ["Navier-Stokes", "Lean 4", "formal verification", "OpenAI", "Buckmaster", "Millennium Prize", "machine shop", "independent verification"]
categories: ["Verification", "Formal Methods"]
tags: ["fpl", "lean", "navier-stokes", "millennium-prize", "verification"]
series: "millennium-verification"
---

![Hero: split frame — CNC machining on the left, a green Lean kernel build on the right, Future Present Labs footer](/images/we-verified-navier-stokes/hero-ns-verify.png)

> *"A new proposed competition for AI companies: rather than being the first to
> announce solutions to unsolved math problems, be the first to announce a new
> mathematical insight."* — Terence Tao, Sept 5, 2026

This week someone proved fluid equations break — and the proofs were checked by
a program, not a committee. We decided a machine shop should be part of that
history, so we built them ourselves.

## What happened (the short version)

Two groups released machine-checked resolutions of century-old fluid equations
within 24 hours of each other:

- **OpenAI** published a [Lean formalization](https://github.com/openai/NavierStokesAndEuler)
  of Clay Millennium alternatives (C) and (D) — smooth Navier–Stokes solutions
  with forcing provably break down, at every viscosity — plus the first-ever
  *unforced* 3D Euler blowup from smooth compact data.
- **Tristan Buckmaster (NYU) and Levent Alpöge (Anthropic)** published
  [formalized blowup results](https://github.com/tristanbuckmaster/fluid_lean)
  for 3D Euler, Boussinesq, and porous media with smooth forcing.

The two camps are also telling very different stories about how the week went
for them. We have no opinion on that fight. Kernels don't adjudicate people.

## What we did

We are [Future Present Labs](https://fpl.dev) — a machine shop in Seattle. We
make CNC parts, waterjet panels, and electronics enclosures. We are not
mathematicians. That's the point.

Our repo — [FuturePresentLabs/ns-verify](https://github.com/FuturePresentLabs/ns-verify) —
does exactly one thing:

1. `git clone` the upstream repo at a **pinned commit**, unmodified
2. `lake build` — every compile job, on a clean ephemeral cloud machine
3. `#print axioms <headline theorems>` — must report exactly
   `[propext, Classical.choice, Quot.sound]`, the standard Lean axioms. Any
   `sorryAx` or custom axiom means "someone skipped a proof." There were none.

Then we kept the receipts: raw build logs, per-step exit codes, toolchain
manifests, SHA-256 checksums of everything, and the exact upstream commits —
all committed to the repo, with a human-reviewed audit. When part of our own
infrastructure failed mid-run, the failed logs went in too. The logs are the
deliverable, including the ugly ones.

## The results

Both repositories, both green:

| Repo | Commit | Build | Axiom audit |
|---|---|---|---|
| [openai/NavierStokesAndEuler](https://github.com/openai/NavierStokesAndEuler) | `8937a8f` | ✅ 11,251 jobs, 41m57s | ✅ 4 theorems clean |
| [tristanbuckmaster/fluid_lean](https://github.com/tristanbuckmaster/fluid_lean) | `d012468` | ✅ 3 projects from source (Euler 4h07m) | ✅ 6 theorems clean |

The audited theorems are the headline claims themselves: `navier_stokes_breakdown_R3`
and `navier_stokes_breakdown_periodic` (the Clay alternatives), `euler_breakdown_R3`
(OpenAI's unforced Euler), `euler_smooth_force_blowup`, `boussinesq_smooth_force_blowup`,
and `forced_boussinesq_affine_core_blowup` (Buckmaster's). Every one reports
exactly the standard axiom trio — the same three axioms every undergraduate
theorem in Lean's math library rests on. No shortcuts anywhere.

![Build log excerpt showing completed Lean jobs](/images/we-verified-navier-stokes/build-log.png)

## Why a build log is evidence

This is the part we had to learn, so here it is in shop terms.

Lean is like a CMM for mathematics. A coordinate measuring machine doesn't
care who loaded the part or what the drawing *says* the dimension should be —
it probes the geometry and reports what's true. Lean's kernel does that for
proofs: every claim in every file gets re-derived from first principles, every
time you compile. The "calibration certificates" are public — Lean's kernel and
[Mathlib](https://github.com/leanprover/mathlib4), a 1.5-million-line library
of already-proved math.

So when the build finishes green, it means: every theorem in that repository —
including the claim that the formalization matches [the Clay Institute's own
problem statement](https://www.claymath.org/millennium/navier-stokes-equation/)
(as encoded by [DeepMind's FormalConjectures](https://github.com/google-deepmind/formal-conjectures),
a third-party formalization OpenAI's repo imports for comparison) — is true in
the same sense that 2+2 is true. Not "we believe the authors." Proven, from
axioms, by a program small enough to be trusted by an entire field.

## What it does NOT mean

- It doesn't settle who deserves credit. Both groups are claiming the other's
  account of the week is wrong. Not our circus.
- It doesn't mean physics is broken. Real fluids don't spin infinitely fast —
  the theorem says the *equations* — the 200-year-old model — can develop
  infinities. That's important for mathematicians and flight simulator
  programmers, not for anyone's safety at the machine.
- It doesn't mean the Clay Prize is claimed. OpenAI explicitly said they won't
  claim it.

## The point isn't the prize — it's the receipts

Terence Tao spent the same week making this precise. On the math: nothing in
principle blocks these methods from reaching unforced Navier–Stokes, and he
"would not be surprised if one could batter out such an extension by pouring
an enormous amount of compute and AI assistance at such a task — but such an
exercise does not particularly hold my interest." What holds his interest is
*digesting the proof methods and extracting the key new insights*.

He also named the failure mode this week exposed: good open problems are now
"mined in a non-renewable fashion," where *"even the rumor of someone working
on a problem can trigger a massive amount of AI-powered effort to flatten it
before the original research project has time to reach its full potential."*

Independent verification is our small answer to that. Receipts that anyone can
re-run — for a few dollars of compute and an afternoon — mean the announcement
race buys less, and the mathematicians extracting the actual insights don't
have to take anyone's word for what was proven. That's the whole job of the
[Verification Bureau](https://github.com/FuturePresentLabs/ns-verify): free
the people doing the understanding from having to referee the people doing
the announcing.

## Why a machine shop, though

Because the verification is the demo of what we actually sell.

We build parts where someone signs their name to a safety margin. Today that
margin comes from an FEA simulation with an *unquantified* error bar — a mesh
convergence eyeball, a factor of safety, a hope. Our thesis: the same trick
this week's proofs used — exact arithmetic plus a checker that refuses to lie —
can put a *proven* interval around a stress margin. Not "about 245 MPa."
"Between 240 and 260, and here's the theorem that guarantees it."

We're building that ([certified margins](https://github.com/FuturePresentLabs/certified-margins))
for real parts, in public. A machine shop that machines the parts it verifies
is a different kind of simulation vendor.

## Verify it yourself

The whole point is that you don't have to trust us, OpenAI, or Terry Tao. The
follow-up post walks through running the verification on a cloud box in an
afternoon — and it will agree with ours, because it isn't checking us, it's
checking the math.

*Questions about the builds, the repo, or the certified-margins work: that's
what [the issues tab](https://github.com/FuturePresentLabs/ns-verify/issues)
is for.*
