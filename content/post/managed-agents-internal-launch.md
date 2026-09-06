---
title: "FPL Managed Agents: Available Internally, Launch Planned for September"
author: "Future Present Labs"
date: 2026-09-05T12:00:00-07:00
draft: false
hero: "images/posts/fpl-managed-agents.png"
hero_alt: "FPL-branded aluminum enclosure with three individually separated compute modules on an engineering workbench."
hero_caption: "AI-generated concept illustration of isolated agent environments, not production hardware."
description: "FPL Managed Agents is live for internal use, starting with Hermes. A wider launch is planned for later September, with Shroud microVM isolation and identity-bound access to FPL tools."
categories: ["Agents", "Shroud"]
tags: ["agents", "hermes", "microvms", "fpl"]
---

**FPL Managed Agents is now available to our internal team. We're planning a wider launch later this month, in September 2026, starting with managed Hermes agents.**

The aim is straightforward: make it easier to run an agent that keeps its working context, connects to the tools you already use, and doesn't require you to maintain a server of your own. We're using the service ourselves before opening it more broadly.

## What's Running Today

The internal release brings agent setup and lifecycle management into Heimdall, our FPL account console. It includes:

- **Managed Hermes instances**, with persistent storage and start/stop controls.
- **Telegram setup**, so you can work with your agent through a connected bot.
- **An FPL model connection**, configured during onboarding, with model selection available afterward.
- **Identity-bound MCP access and FPL skills**, connected as part of setup.
- **An import path for existing Hermes installations**, rather than requiring everyone to start from scratch.

We've already moved an existing Hermes installation onto the managed service. Internal use is now helping us check onboarding, recovery, tool access, and day-to-day responsiveness against real work.

## Built on Shroud MicroVMs

![Shroud, FPL's microVM orchestration platform](/images/shroud-logo.png)

We've previously written about [Shroud and its use of Firecracker microVMs](/post/shroud_case_study/). Managed Agents applies that same foundation to long-running agent workloads.

Each managed agent runs in its own microVM, orchestrated by Shroud. This provides a virtual-machine boundary between the agent's execution environment and the host, rather than running the agent directly on a shared host. Persistent storage keeps its working state across restarts, while network restrictions limit access outside its intended environment.

Isolation is one part of the security model, not a promise that an agent cannot make mistakes. Tool permissions still matter, and actions against connected services can have real effects. A microVM also does not mean all inference happens locally: model requests may go to the configured model provider.

## Your FPL Identity, Your Authorized Tools

Managed agents use the existing FPL identity and permissions model. Onboarding connects the agent to our Model Context Protocol (MCP) gateway under its owner's identity, so supported, authorized integrations are available without separately configuring each connector inside the agent.

This is not a new blanket grant of access. The agent can use the integrations and operations that its identity is authorized to use. Creating an agent does not grant access to another person's connected accounts, make it a company administrator, or turn an individual's integration into a shared company resource. FPL skills are also made available according to the account's access.

The goal is less setup duplication while keeping the existing permission boundaries in place.

## The Rollout From Here

**Now: internal use.** We're gathering feedback from teammates and checking the full path from onboarding to ongoing operation.

**Later this month: planned wider launch.** Our target is September 2026. Before expanding access, we're focusing on reliable setup, migration, recovery, and a clear account of supported capabilities. We'll confirm availability and service details as that work is ready, rather than promise a specific day today.

**After that: broader capabilities.** Additional agent runtimes and controlled agent-to-agent delegation are follow-on work, not features we're presenting as available in this internal release.

For internal teammates who have received access, the starting point is [Managed Agents in Heimdall](https://heimdall.fpl.dev/agents). If you're outside the internal rollout and interested in the upcoming launch, [contact FPL](/contact/).

We'll share another update as wider access opens.
