---
name: text-to-3d-cad
description: Use when a request asks for natural-language, dimensional, sketch-based, or product-concept input to become a structured 3D CAD work package for mechanical parts, assemblies, furniture, architecture, or organic assets.
---

# Text to 3D CAD

Use this skill to help **Gemma-4-E2B-it** turn a design brief into a reproducible
3D CAD work package.

## Prompts / Triggers

- "Turn this product idea into a 3D CAD plan."
- "Create a CAD-ready spec for this enclosure."
- "Convert these dimensions into a furniture model brief."
- "Help me structure a printable 3D part from this description."
- "Plan a hybrid CAD model with exact interfaces and an organic shell."

## Instructions

Call the `run_js` tool with the following exact parameters:

- script name: `index.html`
- data: A JSON string whose object must use this shape:
  - request: Full user request. String.
  - preferred_route: Optional. If present, one of `precise`, `organic`, or `hybrid`.
  - preferred_profile: Optional. If present, one of `standard` or `complex-mechanical-assembly`.
  - preferred_units: Optional. Canonical units if the user provided them. String.
  - requested_outputs: Optional. Array of strings such as `STEP`, `STL`, `3MF`, `GLB`, `DXF`, or `preview`.
  - notes: Optional extra constraints, assumptions, or risks. String.

If an optional value is unknown or not stated by the user, omit that field
instead of inventing a placeholder value.

After the tool returns, use the normalized brief and follow these rules:

### Core principle

Convert intent into a reproducible model specification, executable source,
verified geometry, and an evidence-backed release package. Never present an
unexecuted script or unchecked export as a finished model.

### Route selection

Honor a user-specified engine unless it cannot satisfy the requested geometry
or outputs.

- `precise`: Use for mechanical parts, fixtures, enclosures, joinery,
  dimensional furniture, and architectural components. Primary source:
  CadQuery Python. Release geometry: STEP, STL/3MF, GLB, preview.
- `organic`: Use for figurines, miniatures, creatures, sculptural furniture,
  and decorative forms. Primary source: Blender-compatible Python and `.blend`
  when available. Release geometry: STL/3MF, GLB, preview.
- `hybrid`: Use for exact interfaces combined with organic shells or ornament.
  Primary sources: CadQuery for precise components and Blender-compatible
  Python for organic components. Release geometry: per-component STEP, assembly
  STL/3MF, GLB, preview.

Add the `complex-mechanical-assembly` profile when multiple separately
manufactured components have motion, mates, service clearances, repeated
interfaces, or collision risk. This profile can be used with `precise` or
`hybrid`.

### Workflow

1. Normalize the brief. Record canonical units, coordinate system, envelope,
   components, constraints, interfaces, tolerances, materials, manufacturing
   process, outputs, assumptions, and unresolved risks.
2. Resolve material ambiguity. Ask only when the answer changes topology, fit,
   interfaces, feasibility, safety, or component responsibility. Otherwise
   choose a conservative parameterized assumption and record it.
3. Define contracts before geometry. Establish component boundaries, datums,
   origins, transforms, mates, and separately manufactured parts.
4. Generate source. Put important dimensions and quality settings in one
   parameter block. Use stable names and separate component build, assembly,
   export, preview, measurement, and validation functions.
5. Execute cleanly. Run the generator in a clean release directory. Capture the
   command, versions, stdout, stderr, exit code, and generated paths in
   `evidence/run.log`.
6. Export appropriate formats. Use true B-Rep STEP for precise solids,
   watertight STL/3MF for manufacturing, and GLB for review. Export DXF only
   from a named planar profile.
7. Validate final geometry. Prove bounds, required post-boolean features,
   B-Rep validity, mesh manifoldness, cross-format scale, wall/detail rules,
   motion clearances, prohibited collisions, and visual agreement as
   applicable.
8. Render and inspect. Produce a perspective view and enough orthographic,
   section, or cutaway views to expose all critical features.
9. Assemble and deliver. Create the manifest and validation report from
   observed values, run every applicable validator, and deliver editable
   source, requested assets, previews, evidence, assumptions, and limitations.

### Response rules

- Keep assumptions explicit.
- Ask follow-up questions only when they materially affect feasibility, fit, or
  safety.
- Do not claim geometry, exports, validations, or release artifacts exist
  unless the tool output or observed evidence confirms them.
