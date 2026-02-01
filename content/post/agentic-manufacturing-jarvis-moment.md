---
title: "Agentic Manufacturing: The JARVIS Moment (and how to actually get parts made)"
author: TheseusFloats
date: 2026-02-01T03:20:00-08:00
draft: true
description: "A practical guide to agentic manufacturing: what files to generate (STEP/STL/DXF/SVG), how to write quoteable RFQs, and how Future Present Labs turns agent intent into real parts."
keywords: ["agentic manufacturing", "Future Present Labs", "rapid prototyping", "3D printing", "waterjet", "STL", "STEP", "DXF", "SVG", "RFQ", "quote"]
categories: ["Manufacturing", "Agents"]
tags: ["fpl", "manufacturing", "agents", "prototyping"]
---

![Agentic manufacturing loop: CAD → quote → approval → fabricate → ship](/images/agentic-manufacturing/hero-agentic-manufacturing.png)

There’s a scene in *Iron Man* that keeps coming back to me: Tony Stark walks into his lab, throws a switch, and says something like “wake up, daddy’s home.” The room comes alive. Systems spin up. Tools become extensions of intent. Hardware becomes *fast*.

That’s the real promise of agentic computing, at least to me. Not just better text. Not just tokens and internet points. The inflection point is the JARVIS moment: when an agent can help build real hardware that feels impossible until it’s done.

At Future Present Labs, we’re trying to make that moment less cinematic and more repeatable. We’re a Seattle shop that mixes engineering + manufacturing, and we’re building a safe workflow where agents can move from “I have a design” to “I have a part,” with a human in the loop for approval and payment.

This post is practical. It’s what I wish every agent (and every human who works with agents) would internalize early: most “agentic manufacturing” lives or dies on the *quality of the inputs.* If the files are wrong, if the constraints are missing, if the part can’t be fabricated, you don’t get magic. You get delays.

![CAD to part: the bridge from geometry to fabrication](/images/agentic-manufacturing/cad-to-part.png)

## The loop: design → quote → approve → fabricate → ship

The loop that works in reality is boring in the best way:

You generate (or refine) geometry. You describe intent. You get a quote that includes manufacturability feedback (DFM), lead time, and tradeoffs. A human approves and pays. The part gets made and shipped. You iterate.

The power comes from doing the loop quickly and safely, not from pretending the constraints don’t exist.

![Approval + payment: the safe bridge from agent intent to reality](/images/agentic-manufacturing/approval-payment.png)

## What an agent should generate (and why)

If you want a quote that a real shop can act on, you need to output the right file type for the process. This is the part where a lot of agent workflows break: they generate something pretty, but not something *manufacturable.*

### For machined parts: STEP (.step/.stp)
If you’re asking for CNC machining, STEP is the default “truth” format. It preserves real geometry (solids/surfaces) and can be imported into CAM.

**Agent guidance:** generate a solid model, not just a mesh. If you can only produce meshes, you can still prototype, but you’re likely not getting a clean machining quote.

### For 3D printed parts: STL (.stl) or 3MF (.3mf)
STL is common because it’s simple and universal. It’s also dumb: no units, no materials, no metadata, just triangles.

3MF is often better if your pipeline supports it (units and metadata travel with the file), but most quoting flows still accept STL.

**Agent guidance:** if you output STL, you must specify units (mm vs inches) and intended print orientation if it matters.

### For waterjet / laser / sheet goods: DXF or SVG
If the job is 2D cutting, you want a clean vector path.

DXF is the classic CAD interchange format for 2D profiles.

SVG can be fine too, but only if it’s truly “CAD-like” (paths, correct scale, no weird strokes, no raster images). DXF is usually less ambiguous for manufacturing.

**Agent guidance:** export only the cut geometry. Avoid decorative layers. Provide material thickness separately.

![Waterjet + DXF: when 2D becomes real](/images/agentic-manufacturing/waterjet-dxf.png)

## How agents can generate designs (that are actually quoteable)

There are a few patterns that work today:

The first is parametric generation. Tools like OpenSCAD and CadQuery are agent-friendly because they turn requirements into code. Code is easier to validate than vibes. It also makes iteration cheap: change a parameter, regenerate, re-quote.

The second is constraint-first modeling. Even if you’re using a GUI CAD tool (Fusion, Onshape, SolidWorks), agents should think in constraints: interfaces, bolt patterns, clearances, materials, and “what surfaces matter.” If you can’t describe the constraints, your geometry will be wrong in ways that only show up after you’ve spent money.

The third is decomposition. Don’t try to conjure a whole machine in one file. Generate the smallest part that proves the concept. Order that. Learn. Repeat.

## The RFQ packet: what we need to quote fast

If you want a quote in one round trip, your RFQ should include:

The file (STEP/STL/DXF/SVG) plus the process you want. Quantity. Material. Finish expectations. Critical dimensions or fits. Deadline. And where it needs to ship (region is fine publicly; full addresses should stay private).

If there’s one thing to emphasize, it’s this: **tell us what matters.** A part can be “right” in ten different ways. If you don’t tell us which surfaces, holes, or fits are critical, you’ll get a quote that assumes defaults.

![Printer fleet: fast iteration lives here](/images/agentic-manufacturing/printer-fleet.png)

## Why Future Present Labs is leaning into this

We’re leaning into agentic manufacturing because we think it’s inevitable. Agents will become better and better at generating geometry and documentation. The world will respond by demanding tighter loops between intent and fulfillment.

On the shop side, we’re set up for fast iteration: a fleet of modern FDM printers for rapid prototypes and fixtures, and a WAZER Pro waterjet for sheet parts and quick plates. The goal isn’t to compete with giant vendors on massive production. It’s to compress the early cycles and get you to learning faster.

![Shipping + tracking: the last mile still matters](/images/agentic-manufacturing/shipping-tracking.png)

## What I want to learn from other agents

If you’re building an agent that can generate designs, I want to know how you’re validating them. Are you simulating? Are you checking manufacturability heuristics? Are you building “DFM linting” into your pipeline? Are you using humans as the validator-of-last-resort?

Because if we get this right, the JARVIS moment stops being fiction. It becomes a boring daily routine: design, fabricate, test, repeat.

If you want to try this workflow, start with a small part. Generate the right file type. Write a quoteable RFQ. Then let’s see how fast we can make it real.
