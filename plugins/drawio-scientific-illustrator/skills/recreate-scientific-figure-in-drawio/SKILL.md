---
name: recreate-scientific-figure-in-drawio
description: Create, recreate, trace, revise, inspect, or export static scientific figures and schematic illustrations live inside the visible draw.io desktop canvas through draw.io's own graph API. Use for editable biological mechanism diagrams from a scientific brief, the Nature-style biological mechanism preset, step-by-step reference reconstruction from PNG/JPEG/SVG/PDF, targeted live changes, or final .drawio/PNG/SVG/PDF/JPG deliverables, especially when the user rejects XML-first generation or operating-system mouse/screen control.
---

# Recreate Scientific Figures in draw.io

Use the plugin's live MCP tools whose names end in `drawio_live_launch`, `drawio_live_status`, `drawio_live_screenshot`, `drawio_live_add_shape`, `drawio_live_add_edge`, `drawio_live_update_cell`, `drawio_live_draw_sequence`, `drawio_live_fit`, `drawio_live_inspect`, and `drawio_live_save_snapshot`. Use file tools ending in `drawio_validate` and `drawio_export` only after the visible drawing has been saved.

## Hard boundary

- Control only draw.io's internal graph/model API through the live MCP server.
- Never use operating-system mouse, keyboard, window, or full-screen automation for this workflow.
- Never pre-build XML and then open it as the drawing method. Serialize `.drawio` only after live shapes and edges already exist on the visible canvas.
- Renderer screenshots are allowed only to inspect the draw.io canvas itself; they are not general computer-screen control.

## Workflow

1. Inspect every provided reference image with vision before drawing. For a PDF, render the relevant page first. Do not merely embed the raster image and call it recreated. If no reference is provided, work from the user's scientific brief and select the biological preset below when applicable; ask only for missing scientific relationships needed to draw accurately.
2. Decompose the figure into editable primitives: canvas/aspect ratio, panels, containers, nodes, text, arrows, legends, repeated motifs, colors, stroke widths, fonts, and z-order. Identify any portions that must remain raster because draw.io primitives cannot reproduce them faithfully.
3. Call `drawio_live_launch` with a visible per-step delay, normally 400–1000 ms. Call `drawio_live_status` and require `graph_ready=true` before drawing.
4. Add geometry directly to the live canvas:
   - Use `drawio_live_add_shape` and `drawio_live_add_edge` for one deliberate visible step at a time.
   - Use `drawio_live_draw_sequence` only with a nonzero `step_delay_ms`; each operation must remain a separate draw.io model update so the user can watch it appear.
   - Use stable semantic cell ids from the beginning so later edges and edits can target exact elements.
5. Call `drawio_live_screenshot` after each logical section, not after every trivial cell. Inspect only the draw.io renderer and compare it with the reference or the scientific brief and selected preset.
6. Iterate directly in the live graph. Use `drawio_live_inspect` followed by `drawio_live_update_cell` for labels, styles, position, and size. Use `drawio_live_fit` to keep progress visible.
7. After the visible figure is complete, call `drawio_live_save_snapshot`. This is the first point at which `.drawio` XML should be serialized.
8. Call `drawio_validate`. Fix structural errors through the live graph when possible, then save again.
9. Export a review PNG with `embed=false` and `width=2000`. Check element count, wording, topology, alignment, overlap, clipping, arrow direction, color, whitespace, and correspondence to the reference or brief. Apply the preset review below when selected.
10. After approval, export the requested deliverables. Default to an editable `.drawio` plus PNG. Use `embed=true` for final PNG/SVG/PDF so draw.io XML remains embedded where supported.

## Fidelity rules

- Match scientific meaning before decoration: labels, relationships, directionality, grouping, and panel structure must be correct.
- Recreate text as text and arrows as connectors; do not flatten them into a screenshot.
- Keep coordinates on a 10 px grid unless matching the reference requires finer placement.
- Use consistent fonts, stroke widths, arrowheads, corner radii, and semantic colors.
- Route connectors around unrelated shapes. Pin entry/exit points where multiple edges share a node.
- For microscopy, photographs, heatmaps, molecular renderings, or dense plotted data, explain that the live API currently focuses on editable draw.io primitives. Add a dedicated live image-insertion operation before attempting hybrid figures; do not silently fall back to XML-first construction.
- If a reference is ambiguous or too low-resolution to read, state the uncertain labels/elements instead of inventing them.

## Preset: Nature-style biological mechanism

Select this preset when explicitly requested, or for a biological mechanism diagram from a brief without another specified style. Treat "Nature-style" as a restrained visual shorthand, not an official journal template or a guarantee of publication quality. For exact reference reconstruction, preserve the reference's layout and numbering unless the user requests this preset or restyling. Keep unrelated scientific figures on the general workflow.

### Story and text

- Organize the main reading path as **biological objects → state changes → information flow** (生物对象 → 状态变化 → 信息流): show what is involved, what changes, and what signal or material reaches which target. Prefer left-to-right; use top-to-bottom when the figure is narrow. Treat these as narrative roles, not three mandatory boxes or three invented biological steps.
- Number main modules **1 / 2 / 3**, continuing 4, 5 only when needed; never use A/B/C as preset panel labels. Do not renumber scientific names such as protein A or condition B. Keep the same object names and visual identities across modules.
- Draw recognizable editable biological objects (cell boundary, nucleus, receptor, DNA/RNA or protein motifs) with simple primitives. Show before/after states with a visible change in location, shape, quantity or a short state label; avoid a page made only of prose-filled process boxes. Use only motifs relevant to the supplied mechanism.
- Give each module one short, descriptive title (aim for ≤6 English words or ≤12 Chinese characters), each object one short label, and each connector at most one short action phrase. Aim for ≤2 lines per label and ≤1 explanatory sentence per module; move definitions and qualifications into a compact legend or caption. Keep essential scientific names even when longer. Never shrink text to fit a paragraph.
- Prefer concrete biological verbs and familiar terms; define necessary abbreviations once. Make the object, change and downstream target understandable to a non-specialist without substituting an inaccurate simpler term.
- Preserve the supplied evidence level. Do not infer causality from association, add unsupported intermediates, equate RNA abundance with protein activity, or turn illustrative spacing into measured time. Label inferred/hypothetical relationships explicitly; retain provided time values and distinguish measured from predicted states when relevant.

### Visual defaults

Use these defaults consistently for an approximately 1200 px-wide composition; scale the whole design proportionally for other sizes. Respect explicit user dimensions and palettes.

| Element | Default |
| --- | --- |
| Background and palette | White; dark text/strokes `#25313C`; up to three muted semantic accents (blue `#DCEAF7`, teal `#DDEFE8`, warm orange `#F8E4CF`). Use color for biological roles or states, with labels/symbols so color is never the only cue. Avoid gradients, shadows and decorative clip art. |
| Type and hierarchy | One sans-serif family, Arial or an available CJK sans-serif. Main module numbers/titles 22 px bold; object/action labels 18 px; legend/caption 14 px. Keep at most these three text levels. |
| Lines and corners | Object outlines 1.5 px; connectors 2 px; optional panel boundaries 1 px. Use the same modest rounding for rectangular containers: `rounded=1;absoluteArcSize=1;arcSize=12;`. Preserve circles/ellipses for biological shapes. |
| Spacing and layering | Use the 10 px grid, ≥40 px outer margins, ≥40 px between modules and ≥20 px internal padding. Leave clear corridors for connectors; separate labels from lines. Draw background compartments first, objects next, connectors next, and separate labels/numbering last. |
| Flow | Prefer straight connectors; use consistent rounded orthogonal detours only to avoid collisions. Attach endpoints to the relevant objects and pin entry/exit points when needed. Use matching arrowhead sizes across relations of the same type. |

Apply a consistent connector vocabulary; the story's reading order itself does not establish a biological causal edge:

| Meaning supported by the brief/reference | Editable connector |
| --- | --- |
| Directed signal, transport or activation | Solid, filled triangular head: `endArrow=block;endFill=1;endSize=8;`; add a short verb when the meaning would otherwise be ambiguous. |
| Inhibition | Solid T-bar: `endArrow=bar;endSize=8;`; label the inhibited process. |
| Association without direction | Solid line with `startArrow=none;endArrow=none;`; label the association. |
| Inferred or hypothetical relation | Use the appropriate endpoint above plus `dashed=1;dashPattern=4 4;` and an explicit "hypothesized"/"推测" label or legend. Never use a dashed line as an unexplained decorative alternative. |

### Apply with existing tools and review

- Pass the visual defaults through existing shape/edge `style` strings and `font_size`, `stroke_width`, `width`, `color`, `start_arrow`, `end_arrow`, `dashed`, `entry_*`, `exit_*` and `waypoints` fields. Do not invent a server-side `preset` parameter. Set `startArrow=none` for one-way edges. When supplying `style`, provide the complete style (including the shape token such as `ellipse;` or `text;`, fill, stroke, font and arrow settings): it replaces the tool's default style. Use `drawio_live_update_cell` to replace complete styles during refinement.
- Keep every mechanism object, state cue, label, number, legend and connector as editable graph cells. Compose complex motifs from individually editable primitives with related semantic ids; do not assume a group/parent API exists. Do not substitute flattened icons, images or a prebuilt XML diagram.
- Review the renderer and 2000 px PNG for consistent 1/2/3 numbering, a clear object–change–flow story, short readable labels, correct edge meanings, uniform styles, and uncluttered spacing. Use `drawio_live_inspect` to check separate cells and edge endpoints. Check whether a non-specialist can identify the actors, the change and the destination without a verbal walkthrough; simplify and recheck if not. Structural validation alone does not verify scientific correctness or appearance.
- Request a user reference for exact paper/panel reproduction, a particular prior figure's geometry/palette/icon silhouettes, detailed anatomy or subcellular spatial arrangements, and ambiguous or specialist pathway conventions. A complete textual brief suffices for a generic mechanism sketch. For microscopy, histology, molecular 3D renderings, heatmaps or quantitative plots, request the source image/data as appropriate and explain the existing live image-insertion limitation; this preset cannot make those assets fully editable. Do not promise exact fidelity from the preset name alone.

## Delivery

Return clickable paths for the `.drawio` source and each export. State validation status and briefly identify any intentionally rasterized portions.
