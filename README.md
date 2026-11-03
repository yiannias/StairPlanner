# Spatial Stair Planner

Spatial Stair Planner is a parametric stair-planning component for the Spatial Suite. It will generate valid stair solutions from user-defined constraints, explain the resulting geometry, and produce coordinated plan, section, and—where supported—3D or parametric output.

The component is intended for integration with:

- Spatial Drawing Board
- Spatial Sketchpad
- AutoCAD, through the LayerHerder/AcadBridgeKit integration pattern

The project is currently in workflow and design-definition. See [`docs/design-brief.md`](docs/design-brief.md).

## Design goals

- Make stair layout an interactive constraint-solving workflow rather than a collection of disconnected calculations.
- Support single-run stairs as well as multi-run, multi-landing, multi-floor stair systems.
- Keep geometry, dimensions, warnings, and downstream representations synchronized.
- Share visual language and controls with Spatial UI Kit.

## Status

Design brief only. No production implementation has been started.

