# Visionary Cockpit Design Spec

**Date:** 2026-03-20  
**Owner:** BALLER  
**Author:** Codex CLI  
**Status:** Draft for autonomous execution  

## 1. Product Definition

**Visionary Cockpit** is the flagship Windows application for the Visionary Architects stack: a WinUI-native shell that unifies OpenClaw, VTS, projects, knowledge, agents, automations, and system operations into one operating environment.

This is **not** a literal OpenClaw port. It is a native Windows product that:

- borrows the proven information architecture of the OpenClaw Control dashboard
- promotes the app home page into a shared human + AI workspace
- expands the dashboard into a full founder cockpit for BALLER's real stack
- can supervise the local stack directly instead of acting as a passive dashboard

## 2. Core Decisions

### 2.1 V1 Scope

V1 uses the **Founder Cockpit** navigation set:

- Home
- Operations
- Projects
- Agents
- Knowledge
- System

### 2.2 Home Interaction Model

The home page is the defining experience:

- **Left pane:** persistent chat rail
- **Right pane:** adaptive shared canvas
- **Behavior:** the AI can actively manipulate the right-hand canvas by default

The result should feel structurally closer to a CrewAI Studio-style collaborative workspace than to a static monitoring dashboard.

### 2.3 Product Stance

This app is the **primary desktop shell** for the Visionary stack.

It must be able to:

- connect to stack services
- supervise stack lifecycle
- start, stop, monitor, and heal local services
- expose deep operational and project workflows
- act as the main daily driver surface for BALLER

### 2.4 Packaging Model

V1 should be **unpackaged during active build-out** for fast local build/run loops, easier process orchestration, and direct CLI integration. The architecture should remain package-ready for a later packaged release if needed.

### 2.5 V1 Product Contract

V1 is not allowed to stop at static shell scaffolding.

V1 must ship with these working end-to-end capabilities:

- a real WinUI shell with production-quality navigation, theming, and launch behavior
- a real home workspace with left chat rail and right adaptive canvas
- real local supervision for **VTS**, **OpenClaw**, **Ollama**, and **ngrok**
- real read visibility into OpenClaw and VTS operational state
- real project and knowledge surfaces backed by the knowledge hub repository
- real agent management visibility sourced from OpenClaw

V1 may use thinner surfaces for lower-priority modules, but the shell must already function as a credible daily-driver control environment.

## 3. Design Principles

- **Native first:** use WinUI shell patterns, controls, theming, and window behavior instead of recreating a web dashboard in XAML.
- **Chat is a control surface, not a novelty:** the assistant must drive real workflows.
- **Shared canvas over static pages:** the right pane is an actionable workspace, not just a document viewer.
- **Operational clarity over chrome:** status, actionability, and hierarchy matter more than decoration.
- **Beautiful but disciplined:** Fluent materials, subtle motion, and strong typography without gimmicks.
- **ADHD-aware:** low friction, obvious next actions, reduced context switching, fast launch paths, and strong information scent.

## 4. Information Architecture

## 4.1 Global Shell

The app uses a stable left `NavigationView` shell with a content host to the right.

Top-level destinations:

1. **Home**
2. **Operations**
3. **Projects**
4. **Agents**
5. **Knowledge**
6. **System**

Global shell affordances:

- command bar for global actions
- environment/status strip for stack health
- app-wide search / command palette
- assistant presence indicator
- profile / workspace context switcher

## 4.2 Module Mapping

### Home

Primary landing page and daily-driver workspace.

Responsibilities:

- persistent chat rail
- adaptive shared canvas
- current mission / focus context
- suggested next actions
- active alerts, agents, and running flows

### Operations

Direct successor to the OpenClaw control and VTS operations surfaces.

Responsibilities:

- stack health
- VTS health, endpoints, tool availability, mounted servers
- OpenClaw gateway status
- Ollama / model runtime status
- ngrok / remote connectivity
- logs, incidents, and quick recovery actions
- jobs, cron, and automation monitoring

OpenClaw-derived subpages:

- Overview
- Channels
- Instances
- Sessions
- Usage
- Cron

VTS-derived expansions:

- Tools catalog
- Mounted servers
- connector health
- local observability
- service actions

### Projects

Execution surface for BALLER's active products and plans.

Responsibilities:

- active products
- roadmap lanes
- current implementation plans
- specs and execution context
- project health, blockers, and next actions

Seeded products:

- VTS
- OpenClaw / BabyClaw
- Project Eden
- VKeys
- visionaryarchitects.dev

### Agents

Operational and configuration hub for agentic workflows.

OpenClaw-derived subpages:

- Agents
- Skills
- Nodes

Expanded responsibilities:

- agent identities
- model routing
- permissions and tool profiles
- orchestration state
- automation bindings
- runtime transcripts and recent outcomes

### Knowledge

Human-readable and machine-operable context system.

Responsibilities:

- truth pack
- decisions
- active plans
- session log
- memory access
- docs and operational references
- contextual retrieval tied to current project/module

### System

Deep configuration, debugging, and environment control.

OpenClaw-derived subpages:

- Config
- Debug
- Logs

Expanded responsibilities:

- connector configuration
- environment diagnostics
- local machine readiness
- stack bootstrap and repair tools
- secure settings panels

## 4.3 V1 Module Minimums

The following minimums define what must exist in V1 versus what can wait for later phases.

### Home minimum

- persistent chat rail
- adaptive canvas host
- at least three canvas modes: operations, project, knowledge
- AI-driven canvas focus/open/replace behavior
- session restore for the last active home workspace

### Operations minimum

- stack summary for VTS, OpenClaw, Ollama, ngrok
- service start / stop / restart actions
- startup-order aware boot action
- OpenClaw-derived pages for Overview, Sessions, Usage, and Cron
- VTS visibility for health, mounted servers, and tool count/catalog summary
- logs surface for recent service output and recovery prompts

Deferred from Operations:

- full OpenClaw page-for-page parity for every detail panel
- long-tail observability and historical analytics

### Projects minimum

- project home for the five seeded flagship products
- current status, next step, blockers, and linked spec/plan/session references
- quick-open actions into current project context

Deferred from Projects:

- full PM suite parity with external tools
- advanced planning analytics

### Agents minimum

- agent roster
- selected-agent detail
- skills visibility
- nodes visibility
- model/identity/tool-profile read views

Deferred from Agents:

- full in-app editing for every agent file/config surface
- advanced graph visualizations

### Knowledge minimum

- truth pack browser
- decisions browser
- active plans browser
- session log browser
- project-linked knowledge panel in canvas

Deferred from Knowledge:

- full semantic memory authoring studio
- deep diff/merge workflows

### System minimum

- configuration overview
- diagnostics / environment readiness view
- logs view
- connector and runtime troubleshooting surfaces

Deferred from System:

- full parity with every raw OpenClaw config subtree
- advanced plugin/package management

## 5. Home Page Design

## 5.1 Layout

The home page uses a two-region layout:

- **Left rail:** conversation, tasks, prompts, context, agent handoffs
- **Right adaptive canvas:** tabbed or composable workspace surface

The home page must remain useful at three states:

- **Overview mode:** broad situational awareness
- **Work mode:** focused execution on one task or module
- **Intervention mode:** immediate operational troubleshooting

## 5.2 Left Chat Rail

The chat rail is persistent and should support:

- session history
- pinned contexts
- mode indicators
- quick model / agent targeting
- inline action cards
- message-linked canvas jumps

The AI should be able to:

- open and rearrange canvas views
- populate forms
- focus relevant panels
- stage commands
- annotate what it is doing in human-readable terms

## 5.3 Right Adaptive Shared Canvas

The canvas changes by current context.

Examples:

- Operations context: dashboards, incidents, service controls, logs, tool panels
- Project context: roadmap, spec, tasks, implementation notes, file context
- Agent context: agent roster, skill panel, node output, permission controls
- Knowledge context: decisions, truth pack entries, session handoff blocks, memory retrieval

The canvas should support:

- multiple cards or panes
- resizable regions
- contextual inspector/detail panels
- AI-driven focus changes
- explicit user takeover without fighting the assistant

## 6. AI Operating Model

The AI is a **full co-pilot** inside the shell.

Default behavior:

- it can manipulate the UI and canvas directly
- it can navigate modules
- it can prepare commands and changes
- it can proactively surface next steps and likely fixes

Guardrails:

- destructive actions still require explicit approval
- external side effects should be obvious and reviewable
- high-risk actions should be staged in a review panel first
- all service actions should emit receipts and status events

## 6.1 AI Action Classes

To make "full co-pilot" implementable, actions are grouped into four classes.

### Class A: View actions

Examples:

- navigate modules
- open or rearrange canvas surfaces
- filter, sort, and focus UI state
- expand log/detail panels

Default policy:

- allowed automatically
- no approval required
- reversible through normal UI history/state

### Class B: Draft and staging actions

Examples:

- populate forms without applying
- prepare service commands
- draft config changes
- queue project/task updates for review

Default policy:

- allowed automatically
- must appear in a visible staged state before execution
- user can inspect, edit, or discard

### Class C: Local side-effect actions

Examples:

- start/stop/restart supervised local services
- apply non-destructive local settings
- refresh/reindex local cached state

Default policy:

- allowed automatically for explicitly reversible low-risk actions
- requires an approval interstitial for state-changing actions with operational impact
- must create an action receipt with timestamp, actor, target, outcome, and rollback guidance

### Class D: Destructive or external side-effect actions

Examples:

- deleting data
- modifying git state
- pushing remote changes
- changing remote service configuration
- triggering external sends, posts, or messages

Default policy:

- never run silently
- always require explicit approval
- must show scope, target, expected impact, and confirmation affordance

## 6.2 Conflict and Undo Rules

- user input always wins over AI focus changes when they conflict in real time
- AI-initiated layout changes should be reversible through a recent-actions stack
- staged actions should expire or clear when the underlying context changes materially
- service actions must expose the last successful stable state when rollback is possible
- the shell should visually distinguish AI suggestion, AI staging, AI executing, and AI completed states

This app should feel like BALLER and the AI are sharing the same instrument panel.

## 7. Service Supervision Model

The shell must supervise the local stack.

Managed services:

- VTS
- OpenClaw
- Ollama
- ngrok
- optional auxiliaries such as OpenMemory and observability services

Supervisor responsibilities:

- health polling
- process start/stop/restart
- startup sequencing
- recovery actions
- incident surfacing
- status history

The app should understand stack dependencies:

- VTS and OpenClaw are critical
- Ollama and ngrok are important but downstream
- AIKit is optional / training-only and must not be treated as a failure in green-runtime views

## 7.1 Supervisor Failure Semantics

- health state should distinguish **healthy**, **degraded**, **offline**, **starting**, **stale**, and **recovering**
- poll failures should not immediately flip a service to offline; use timeout + retry thresholds first
- retries should use bounded backoff, not infinite hammering
- boot actions must enforce the documented startup order where dependencies matter
- the UI must support manual override when automatic recovery is wrong or unwanted
- stale health should be explicit when the app has lost telemetry but not proven a service dead
- service actions should stream progress and final receipts into both Operations and the chat rail
- optional services such as AIKit must render as optional, not as red critical failures

## 8. Technical Architecture

## 8.1 App Shape

Recommended WinUI architecture:

- WinUI 3 desktop shell
- MVVM-oriented presentation model
- clear service layer boundaries
- module-oriented navigation
- shared state bus for shell, services, AI actions, and canvas composition

Primary layers:

1. **Shell layer**
   `NavigationView`, title bar, global status, command surfaces, routing

2. **Workspace layer**
   chat rail, adaptive canvas, shared interaction state, focus and layout orchestration

3. **Module layer**
   Operations, Projects, Agents, Knowledge, System

4. **Integration layer**
   OpenClaw gateway client, VTS API clients, local process supervisor, filesystem/context services

5. **Persistence layer**
   local app preferences, workspace layout state, cached diagnostics, recent sessions

## 8.2 Integration Boundaries

OpenClaw should be integrated as:

- source for dashboard IA
- live gateway client
- agent, skills, node, config, and logs provider

VTS should be integrated as:

- tool spine and service metadata provider
- source for mounted tool/server visibility
- source for system health, observability, and orchestration actions

Knowledge hub should be integrated as:

- canonical project memory and decision source
- plan/spec/session context provider

## 8.4 Source-of-Truth and Sync Rules

Ownership rules:

- **Knowledge Hub repo:** canonical source for plans, specs, decisions, and session handoff records
- **OpenClaw runtime:** canonical source for live gateway, agents, skills, node, and control runtime state
- **VTS runtime:** canonical source for tool/server/runtime health and orchestration metadata
- **Visionary Cockpit local cache:** convenience layer only, never canonical

Write rules:

- edits to plans/specs/decisions/session logs should write through to the knowledge hub working tree
- edits to live operational settings should write through the owning runtime/client surface, not to a shadow copy first
- cached UI state can persist locally but must not silently override live or canonical sources

Staleness rules:

- any cached operational data must show last refresh time
- stale cache should remain viewable but visibly marked
- if local cached state conflicts with live state, live state wins for operational surfaces
- if local staged content conflicts with knowledge hub file changes, the app should force an explicit merge/review state

Offline rules:

- knowledge surfaces may run in read-only cached mode when the repo is unavailable
- operational surfaces may show last known state, but all action surfaces must be disabled or explicitly marked risky when live connectivity is missing

## 8.3 Local Data and State

Persist:

- shell layout
- open canvas surfaces
- last active projects and sessions
- preferred startup view
- approved automation context where safe

Do not over-persist secrets in plain local UI state.

## 9. Visual Direction

The visual language should feel like a **premium operational studio**, not a web admin clone.

Recommended direction:

- Mica-backed main shell
- restrained acrylic on transient surfaces only
- strong, theme-aware hierarchy
- left nav and chat rail with compact density
- brighter focus and action emphasis inside the canvas
- subtle motion for pane changes, state changes, and AI interventions

Tone:

- serious
- high-signal
- modern Windows-native
- slightly cinematic, but not playful

Avoid:

- excessive custom borders
- dashboard card spam
- generic white admin panels
- trying to mimic a browser SPA visually

## 10. Responsiveness and Windowing

The shell should support:

- wide desktop workspace mode
- medium-width laptop mode
- narrow compact mode

Behavior by width:

- narrow widths collapse navigation
- chat rail can become overlay or compact rail
- canvas simplifies into one primary pane
- secondary surfaces move into inspector tabs or drawers

V1 should start with a single main window. Detached tool windows can be phase 2 if justified.

## 11. V1 Delivery Boundaries

## 11.1 In Scope

- WinUI shell
- Home page with chat rail + adaptive canvas
- Operations module
- Agents module
- Knowledge module
- Projects module
- System module
- local service supervision
- real OpenClaw/VTS operational integration for V1 minimums
- beautiful flagship-grade design system foundations

## 11.2 Explicitly Out of Scope for First Delivery

- every possible Visionary experiment repo
- multi-window studio complexity
- plugin marketplace
- complex collaborative multi-user features
- full parity with every historical web setting on day one

The goal is a strong flagship foundation, not a giant first-drop blob.

## 12. Delivery Strategy

Recommended implementation phases:

### Phase 1: Shell Foundation

- app scaffold
- shell, navigation, theme, title bar, design tokens
- real shell with launch verification and non-placeholder module shells

### Phase 2: Home Workspace

- persistent chat rail
- adaptive canvas host
- context switching model
- AI action orchestration surface

### Phase 3: Operations

- service supervisor
- health dashboards
- OpenClaw/VTS operational pages

### Phase 4: Projects + Knowledge

- project cockpit
- specs/plans/session views
- memory and truth pack integration

### Phase 5: Agents + System

- agent management
- deeper settings, debug, logs, environment surfaces

## 13. Error Handling and Safety

- degraded services should render as recoverable operational states, not blank panels
- every module should define loading, empty, degraded, and actionable failure states
- service actions need clear receipts
- AI actions need visible provenance
- sensitive controls need confirmation and auditability

## 14. Testing and Validation

The app must be validated across:

- build success
- launch success with a real top-level window
- light and dark theme
- high contrast
- text scaling / large text
- keyboard navigation
- narrow-width behavior
- persistence restore for shell/home workspace state
- supervisor timeouts, retries, and startup-order enforcement
- supervisor actions against local services
- module fallback behavior when services are offline
- responsiveness of the home workspace under active updates
- unpackaged development flow plus package-readiness regression checks for later distribution

## 15. Success Criteria

The design is successful when BALLER can:

- launch one Windows app and see the real state of the stack
- talk to the AI in a persistent left rail
- watch the AI operate a shared right-hand canvas
- manage VTS and OpenClaw from one native shell
- move between operations, projects, agents, knowledge, and system work without tool thrash

## 16. Implementation Default

Unless a later decision supersedes this spec:

- the product working name is **Visionary Cockpit**
- the implementation target repo should be **`D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS`**
- the development packaging mode should start **unpackaged**
- OpenClaw parity is a content source, not a visual ceiling
