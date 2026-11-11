# Spatial Stair Planner — Resource Register

This register records external resources that StairPlanner can reuse or align with. The project should reference established standards and practices rather than re-articulate them in product prose or duplicate their normative text.

**Reviewed:** 2026-11-03  
**Policy:** Link to the authoritative source, record how it informs StairPlanner, and keep implementation-specific interpretation in the project. Do not treat a reference implementation or a general web article as a code authority.

## 1. Normative rules and accessibility

### International Building Code

- **Resource:** [2024 IBC, Chapter 10 — Means of Egress](https://codes.iccsafe.org/content/IBC2024V2.0/chapter-10-means-of-egress)
- **Use:** Source material for egress stair requirements, stair geometry, landings, and related means-of-egress constraints.
- **StairPlanner treatment:** Implement as a versioned rule profile. Do not assume the 2024 IBC applies to every project or jurisdiction.
- **Caution:** The ICC site may require access or licensing for complete code content. We should store references and rule identifiers, not copy protected code text into this repository.

### ADA / U.S. Access Board

- **Resource:** [Access Board — Stairways guide](https://www.access-board.gov/ada/guides/chapter-5-stairways/)
- **Use:** Reference for accessible stair requirements, uniform risers and treads, nosings, handrails, extensions, and related accessibility guidance.
- **StairPlanner treatment:** Keep accessibility checks separate from general geometric validity and associate each warning with its rule/profile identifier.
- **Scope note:** The guide explains when ADA stair requirements apply; it is not a complete substitute for the governing building code or project-specific accessibility requirements.

## 2. BIM and information-model standards

### buildingSMART IFC

- **Resource:** [IFC4 IfcStair](https://standards.buildingsmart.org/IFC/RELEASE/IFC4/FINAL/HTML/schema/ifcsharedbldgelements/lexical/ifcstair.htm)
- **Resource:** [IFC4.3 IfcStairFlight](https://standards.buildingsmart.org/IFC/RELEASE/IFC4_3/HTML/lexical/IfcStairFlight.htm)
- **Use:** Vocabulary and decomposition for a stair assembly, flights, landings, slabs, railings, and relationships between elements.
- **StairPlanner treatment:** Use IFC concepts to shape the canonical model and future interchange mapping. Preserve StairPlanner’s richer constraint and solution-history data alongside any IFC export.
- **Useful properties:** Riser count, tread count, riser height, tread length, walking-line offset, headroom, and structural/waist thickness.

## 3. AutoCAD integration

### Autodesk AutoCAD .NET API

- **Resource:** [AutoCAD .NET API overview](https://help.autodesk.com/cloudhelp/2024/ENU/OARX-DevGuide-Managed/files/GUID-390A47DB-77AF-433A-994C-2AFBBE9996AE.htm)
- **Resource:** [In-process versus out-of-process .NET applications](https://help.autodesk.com/cloudhelp/2026/DEU/OARX-DevGuide-Managed/files/GUID-C8C65D7A-EC3A-42D8-BF02-4B13C2EA1A4B.htm)
- **Use:** Reference for AutoCAD drawing entities, dimensions, transactions, application/document access, and the boundary between in-process plug-in code and external applications.
- **StairPlanner treatment:** Follow the LayerHerder/AcadBridgeKit pattern: a thin AutoCAD connector owns drawing access and approved apply operations while the Rust planner remains host-independent.
- **Verification:** Documentation and builds do not count as AutoCAD verification. AutoCAD behavior requires a user-controlled verification pass.

## 4. Existing implementation references

### FreeCAD Parametric Custom Stairs

- **Resource:** [Beton1/Parametric-Custom-Stairs](https://github.com/Beton1/Parametric-Custom-Stairs)
- **License:** MIT, as identified by the repository.
- **Use:** Reference for path-driven stair generation, irregular footprints, orientation handling, and possible geometry edge cases.
- **StairPlanner treatment:** Use for comparative study and test-case inspiration. Do not copy its architecture or assume its geometry conventions match StairPlanner.

## 5. Constraint-solving references

### Google OR-Tools CP-SAT

- **Resource:** [Constraint Optimization — OR-Tools](https://developers.google.com/optimization/cp)
- **Use:** Reference if StairPlanner later needs a general constraint-programming engine to rank alternatives across many discrete variables.
- **StairPlanner treatment:** The first solver should remain deterministic and explainable without this dependency. Reconsider CP-SAT only after profiling real multi-run cases and documenting the limitations of the simpler search.

## 6. UI toolkit candidates

### Raven (oscar-soirey)

- **Resource:** [oscar-soirey/Raven](https://github.com/oscar-soirey/Raven) ([releases](https://github.com/oscar-soirey/Raven/releases), [site](https://oscar-soirey.github.io/Raven/))
- **License:** Apache-2.0, as identified by the repository.
- **Use:** Lightweight C GUI library for editors and tools (retained-mode widget tree, XML layouts, docking, text editor, node graph, timeline, Vulkan renderer) with a Rust binding. Not related to mjishnu/Raven, a Microsoft Store client.
- **StairPlanner treatment:** Experimental only, to compare against egui/Spatial UI Kit. Not a dependency and not a decision. Pre-1.0 (0.2.x at 2026-11-06) with an unstable public API; its Rust binding is a thin `unsafe` FFI wrapper over C. Reviewed 2026-11-06 from the README only, not built or run.

## 7. How resources enter the project

Each adopted external rule or mapping should have:

1. A source URL and access/review date.
2. A version or edition identifier where applicable.
3. A project-owned interpretation expressed as data, tests, or a rule-profile note.
4. A clear distinction between normative requirements, advisory guidance, and implementation examples.
5. A license or reuse check before copying code, images, tables, or substantial text.

The project should prefer links, identifiers, and concise paraphrases over copied standards text. Generated warnings should identify the active rule profile and source section without presenting StairPlanner as a code-compliance authority.

