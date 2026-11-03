# Spatial Stair Planner — Design Brief

**Project:** Spatial Stair Planner  
**Suite:** Spatial Suite  
**Status:** Concept and workflow definition  
**Audience:** Product, interaction, geometry, and integration design

## 1. Purpose

Spatial Stair Planner is a reusable component that turns stair requirements into a valid, inspectable stair solution. The user supplies levels, constraints, run and landing preferences, circulation direction, and structural depth. The planner solves and displays the stair as it is being defined, identifies conflicts, and exports coordinated 2D graphics and application-specific model elements.

The same planning model should be usable inside Spatial Drawing Board, Spatial Sketchpad, and an AutoCAD plug-in. The host application may change the final representation, but not the underlying stair logic.

## 2. Product promise

The user should be able to answer, in one continuous workflow:

1. What levels does the stair connect?
2. How many risers and runs are required?
3. Where do landings occur, and how are they oriented?
4. What run lengths and clearances are acceptable?
5. What is the actual stair structural depth, including the portion below the walking line?
6. Does the resulting stair satisfy the selected code and project constraints?
7. What plan, section, dimensions, and model output should be created?

The planner must show the consequences of a change immediately. A change to floor height, riser count, landing position, or structural depth should update the solution, dimensions, warnings, and previews together.

## 3. Initial scope

### Stair types

- Straight flight
- Return/U stair with intermediate landing
- L stair with quarter-turn landing
- Multiple runs and multiple landings between two or more floors
- Repeated floor-to-floor stair configurations

The data model should not assume that every run is parallel, identical, or on one floor-to-floor cycle.

### User-controlled parameters

- Lower and upper level elevations
- Intermediate floor elevations and floor-to-floor relationships
- Desired or allowed riser height
- Desired or allowed going/tread depth
- Number of risers, when fixed by the user
- Number of runs
- Run direction and turn direction
- Run lengths, either calculated or user-constrained
- Landing count, type, dimensions, and location
- Landing clear width and length
- Stair clear width
- Stair structural depth/thickness
- Headroom and other project clearances
- Nosing, tread, riser, and finish conventions
- Reference line / walking line convention
- Units and precision
- Code or rule profile

The UI must distinguish values that are fixed, preferred, derived, and currently infeasible.

## 4. Core workflow

### A. Start a stair

The user chooses a stair type or starts from a blank multi-run diagram. The planner establishes the connected levels and a first solution using defaults from the active project or host application.

### B. Define levels and vertical requirements

The user enters or selects floor levels. The planner calculates total rise, proposes riser counts, and shows the resulting riser height. A user may lock the count or allow the solver to search an acceptable range.

### C. Define runs and landings

The user adds, removes, reorders, and turns runs. Landings are first-class elements, not implicit gaps between runs. Each run has an editable direction, length mode, and relationship to adjacent landings.

### D. Define horizontal constraints

The user sets clear width, preferred or minimum going, run limits, landing dimensions, offsets, walls/obstructions, and available footprint. The planner reports which constraints are driving the solution.

### E. Define structural depth

The user specifies the actual stair structural depth/thickness. The preview shows the walking surface, stair body, slab/opening relationship, and headroom consequences. Structural depth is part of the solved geometry, not a late annotation.

### F. Review and resolve

The planner presents a live plan and section, dimension strings, a solution summary, and warnings. Conflicts should be actionable: selecting a warning identifies the affected run, landing, or constraint and offers the smallest useful adjustment.

### G. Commit and output

The user commits a solution version. The component can then produce 2D plan and section graphics with dimensions, or host-specific 3D/parametric elements. Recalculation after commitment must preserve the prior solution as a recoverable version.

## 5. Solver and geometry model

The solver should separate requirements, constraints, solution state, and presentation geometry.

### Inputs

- Levels and elevations
- Run/landing topology
- Locked values
- Preferred ranges
- Minimum/maximum constraints
- Available footprint and obstacles
- Structural depth and clearance rules
- Active code/rule profile

### Solve sequence

1. Validate topology and level connectivity.
2. Calculate total rise for each connected level pair.
3. Generate candidate riser counts and riser heights.
4. Generate candidate going/run-length combinations.
5. Place landings according to topology, locked locations, and dimensional constraints.
6. Solve run directions and coordinates within the available footprint.
7. Apply structural depth and clearance checks.
8. Rank candidates by locked constraints first, then preferred values, compactness, and minimal change from the current solution.
9. Return the best valid solution plus explainable failures when no valid solution exists.

The first implementation should favor deterministic, explainable search over opaque optimization. Every derived value should identify its source constraints.

### Geometry entities

- Stair system
- Level connection
- Run
- Landing
- Tread/riser pattern
- Walking line
- Structural body
- Opening / void
- Guard/handrail reference geometry (future)
- Annotation and dimension anchors

All geometry should use a shared coordinate and unit model compatible with SpatialGeometry and host precision rules. The solved model should be independent of any one renderer.

## 6. User interface direction

The component will use Spatial UI Kit.

The primary layout should combine:

- A compact parameter panel grouped by intent: Levels, Runs, Landings, Constraints, Structure, Output.
- A central interactive plan/section preview with synchronized selection.
- A solution status strip showing Valid, Needs adjustment, or No solution.
- A concise diagnostics list ordered by severity and constraint impact.
- An optional solution comparison/history view for before-and-after changes.

Direct manipulation should be available for run length, landing position, turn direction, and footprint placement, while preserving numeric editing and keyboard access. The interface must make it clear whether a drag changes a locked value, a preferred value, or a derived result.

The first UX study should test whether plan and section should be side-by-side, tabbed, or stacked, and how users understand a multi-floor stair without losing the current floor context.

## 7. Outputs

### Common outputs

- Solved parametric stair data
- Plan graphics
- Section graphics
- Dimension and annotation data
- Constraint and warning report
- Reproducible solution/version metadata

### Spatial Drawing Board

- Native 2D objects for runs, landings, treads, structural bodies, openings, dimensions, and annotations.
- Editable parametric source retained so the stair can be recalculated or exploded deliberately.

### Spatial Sketchpad

- Parametric 3D stair elements and presentation geometry, subject to Sketchpad's modeling conventions.
- Camera/section previews may be generated from the same solved model.

### AutoCAD

- Native or host-compatible 2D entities and dimensions initially.
- Later: configurable 3D solids/surfaces or block/parametric representations.
- Communication through the same owned-window, named-pipe JSON bridge and AutoCAD command-context pattern used by LayerHerder via AcadBridgeKit.

## 8. Integration boundaries

The planner core must not depend on egui, AutoCAD assemblies, or a specific host document model. Recommended boundaries:

- `stair-core`: units, constraints, topology, solver, diagnostics, and canonical geometry.
- `stair-format`: serialization, versioning, and interchange fixtures.
- `stair-render-2d`: plan, section, dimensions, and annotation primitives.
- `stair-ui`: Spatial UI Kit-facing workflow and interaction state.
- Host adapters: Drawing Board, Sketchpad, and AutoCAD/AcadBridgeKit.

The AutoCAD adapter should remain a thin connector. It should launch/own the Rust UI, identify the active drawing, send requests and snapshots over the established bridge, and apply approved output in AutoCAD's command context.

## 9. Phased delivery

### Phase 0 — Workflow and UX definition

- Validate terminology and the end-to-end workflow.
- Produce wireframes for single-run and multi-landing/multi-floor cases.
- Define constraint states, warnings, and solution history behavior.

### Phase 1 — Solver proof

- Implement a host-independent model for straight and two-run stairs.
- Add deterministic candidate generation and explainable diagnostics.
- Create golden fixtures for representative layouts and edge cases.

### Phase 2 — Multi-run planning

- Add arbitrary run/landing topology, direction changes, multiple floors, footprint constraints, and structural depth.
- Add plan/section preview geometry and dimensions.

### Phase 3 — Spatial Suite integration

- Integrate Spatial UI Kit.
- Add Drawing Board and Sketchpad adapters.
- Preserve parametric source and versioned recalculation.

### Phase 4 — AutoCAD integration

- Add AcadBridgeKit connector using the LayerHerder communication pattern.
- Start with read/preview and explicit apply for 2D output.
- Add packaging, supported AutoCAD-era targets, and user-run AutoCAD verification.

## 10. Acceptance criteria for the first usable release

- A user can define a multi-run stair between two or more levels without manually calculating every riser and run.
- The planner clearly distinguishes valid, preferred, derived, and conflicting values.
- Changing a run, landing, level, or structural depth updates plan, section, dimensions, and diagnostics consistently.
- No-solution states explain the blocking constraints and identify useful adjustments.
- The same canonical solution can produce Drawing Board 2D output and an AutoCAD 2D preview/apply payload.
- Representative solutions are covered by deterministic tests and golden geometry fixtures.

## 11. Open design decisions

- Which code/rule profiles are required for the first release?
- Should the first solver optimize for a single best solution or expose ranked alternatives?
- How much of handrails, guards, nosings, and finishes belongs in the first model?
- Should host adapters apply native elements immediately, or always require an explicit review/commit step?
- What is the minimum interchange format needed for round-tripping between hosts?
- Which dimensions and annotations are generated automatically versus user-authored?

These decisions should be resolved through the workflow and UX studies before implementation commitments are made.

