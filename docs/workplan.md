# Spatial Stair Planner — Workplan

This workplan turns the design brief into an ordered sequence of decisions, prototypes, and implementation increments. The project remains documentation-first until the workflow and solver contract are stable.

## Working principles

- Keep the stair model independent of any host application.
- Make every derived result explainable: show which inputs and constraints produced it.
- Treat runs, landings, levels, openings, and structural depth as explicit model entities.
- Preserve a user's locked choices while exploring alternatives around them.
- Verify geometry with deterministic fixtures before adding host integrations.
- Require explicit review before applying generated elements to a host drawing/model.

## Milestones

| Milestone | Outcome | Exit condition |
|---|---|---|
| M0 — Product framing | Agreed vocabulary, workflow, scope, and first-use scenarios | The brief and this plan identify no unresolved ambiguity that would change the first prototype materially |
| M1 — Workflow prototype | Testable interaction flow for defining and revising stairs | Chris can walk through representative cases and identify missing controls or confusing states |
| M2 — Canonical model | Versioned, host-independent stair data model | Fixtures can represent levels, runs, landings, constraints, structure, and diagnostics |
| M3 — Solver v0 | Deterministic solver for straight and two-run stairs | Valid solutions, no-solution explanations, and stable golden outputs pass |
| M4 — Multi-run solver | Arbitrary run/landing topology and multi-floor support | Representative multi-landing cases solve with structural depth and clearance checks |
| M5 — 2D preview/output | Synchronized plan, section, dimensions, and diagnostics | Preview and exported geometry agree with solver fixtures |
| M6 — Spatial host integration | Spatial UI Kit plus Drawing Board/Sketchpad adapters | One canonical solution can be previewed and committed in each host |
| M7 — AutoCAD adapter | LayerHerder-style AutoCAD connector and 2D apply path | Read/preview/apply works in a user-run AutoCAD verification pass |

## Phase 0 — Product framing

1. Confirm the first release's stair vocabulary: level, run, landing, tread, riser, walking line, opening, structural body, and constraint states.
2. Select three anchor scenarios:
   - single straight stair;
   - U stair between two levels;
   - multi-landing stair spanning multiple floors.
3. Define the first rule profile and explicitly separate code rules from general geometric validity.
4. Decide which values can be locked, preferred, derived, or overridden.
5. Record decisions in `docs/decisions/` as short ADR-style notes.

**Deliverable:** revised workflow map, scenario fixtures, terminology list, and decision log.

## Phase 1 — Workflow and UI prototype

1. Sketch the start flow: choose stair type, establish levels, and generate an initial solution.
2. Sketch editing flows for riser count, run length, landing location, direction, clear width, and structural depth.
3. Define synchronized plan/section behavior and selection rules.
4. Define status states: valid, changed, constrained, warning, and no solution.
5. Define diagnostics as actionable items tied to model entities and constraints.
6. Test whether plan/section should be side-by-side or tabbed for multi-floor work.

**Deliverable:** UI wireframes and a clickable or scripted walkthrough; no production solver required.

## Phase 2 — Canonical model and contracts

1. Define units, precision, coordinate conventions, and level reference behavior.
2. Define serialized entities for stair systems, levels, runs, landings, constraints, structural bodies, openings, dimensions, and diagnostics.
3. Define stable IDs, versioning, migration expectations, and solution history.
4. Define the solver request/result contract, including partial and no-solution results.
5. Define renderer-neutral geometry primitives for plan, section, and dimensions.
6. Create representative JSON fixtures from the three anchor scenarios.

**Deliverable:** model contract and fixtures, reviewed before implementation begins.

## Phase 3 — Solver v0

1. Validate level connectivity and topology.
2. Generate candidate riser counts and calculate riser heights.
3. Generate going/run-length candidates within locked and preferred constraints.
4. Solve straight stairs and two-run stairs with one landing.
5. Apply clear width, landing, headroom, and structural-depth checks.
6. Rank candidates deterministically and report rejected candidates by constraint.
7. Add unit tests and golden solution fixtures for normal, edge, and no-solution cases.

**Deliverable:** host-independent solver crate/module with explainable diagnostics.

## Phase 4 — Multi-run and multi-floor solver

1. Support arbitrary ordered run/landing topology.
2. Support run direction changes and non-parallel runs.
3. Support intermediate floors and repeated floor-to-floor configurations.
4. Add footprint boundaries, obstructions, offsets, and opening relationships.
5. Add user-locked landing locations and run lengths.
6. Add structural body geometry and clearance propagation.
7. Add solution comparison so edits show what changed and why.

**Deliverable:** multi-run solver with fixtures covering multiple landings and floors.

## Phase 5 — 2D geometry, dimensions, and review UI

1. Generate plan geometry for runs, treads, landings, openings, and structural bodies.
2. Generate section geometry for risers, treads, walking line, structural depth, levels, and headroom.
3. Generate dimension anchors and annotation data without hard-coding a host renderer.
4. Bind preview selection to model entities and diagnostics.
5. Add explicit review/commit state and recoverable solution versions.
6. Verify preview geometry against solver fixtures and visual snapshots where appropriate.

**Deliverable:** interactive preview and exportable renderer-neutral 2D output.

## Phase 6 — Spatial Suite integration

1. Adopt Spatial UI Kit controls, panels, status indicators, and selection patterns.
2. Build the Spatial Drawing Board adapter for native 2D objects and retained parametric source.
3. Build the Spatial Sketchpad adapter for parametric 3D/presentation elements.
4. Define host undo/redo, persistence, refresh, and explode behavior.
5. Verify that both hosts round-trip the same canonical solution without silent geometry changes.

**Deliverable:** host integrations using one shared planner core.

## Phase 7 — AutoCAD integration

1. Define the AutoCAD request/response payload using the established AcadBridgeKit named-pipe JSON pattern.
2. Keep the connector thin: active-drawing identity, launch/ownership, snapshot requests, and approved apply commands.
3. Start with preview and explicit 2D apply for lines, polylines, blocks, and dimensions as appropriate.
4. Add drawing-unit and coordinate-system translation tests.
5. Package supported AutoCAD-era targets using the LayerHerder approach.
6. Run user-controlled AutoCAD verification; do not treat build success as AutoCAD verification.

**Deliverable:** documented AutoCAD preview/apply path and manual verification record.

## Cross-cutting backlog

- Code/rule profile registry and rule explanations.
- Alternative-solution ranking and user comparison.
- Handrails, guards, nosings, finishes, and annotation standards.
- Accessibility and keyboard-only editing.
- Localization and unit-system presentation.
- Export/interchange format for external consumers.
- Performance limits for large multi-floor systems.
- Error recovery, autosave, and solution-history retention.

## First implementation slice

The first implementation should be deliberately small:

1. Canonical data model for one straight stair and one U stair.
2. Deterministic riser/going solver with locked and preferred values.
3. Explainable diagnostics for invalid rise, run, landing, and headroom constraints.
4. Renderer-neutral plan and section geometry.
5. JSON fixtures and tests before any host integration.

This slice proves the core workflow and solver contract while keeping later Spatial UI Kit and AutoCAD decisions reversible.

