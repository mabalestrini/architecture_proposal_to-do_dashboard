# CLAUDE.md — Reference Architecture Template

Project context for Claude Code. Read this before making any changes.

---

## What this is

A generic Reference Architecture template. It can be used to produce RA documentation for any domain: network, ITSM, CMDB, automation, security, cloud, and others.

The **network architecture principles** in `docs/network-principles/` are a standing inherited constraint — not deliverables of the current project. Any design proposed in this RA must comply with them.

---

## Folder map

```
docs/network-principles/   19 network principles — read-only inherited constraint
docs/standards/            RA framework standards (naming, color, diagram, document template, AADs)
docs/deliverables/         Project deliverables — fill these in (D0/D1/D2/D3 stubs)
docs/templates/            Reusable slide and document templates
docs/input/                Project source material — do not edit
docs/output/               Generated/extracted content

diagrams/drawio/           network_ra_template_V1.drawio is the base template
diagrams/templates/mermaid/ Mermaid skeleton starters

schemas/                   architecture-model.yaml (update for your project), CSVs
stencils/                  Draw.io XML library + Visio guide
assets/svg/                Area and zone SVG icons
scripts/                   extract_pptx_text.py utility
```

---

## Deliverable numbering

| Range | Layer |
|---|---|
| 00 | Project principles |
| 01 | D0 — Context overview |
| 02–07 | D1 — Domain overviews |
| 08–18 | D2 — Detailed designs |
| 19 | D3 — Business flows |
| 20–21 | Reference appendices |

---

## Working rules

- Every diagram needs an accompanying structured narrative (see `docs/standards/architecture-document-template.md`) — a diagram alone is not a complete deliverable
- `docs/network-principles/network-principles.md` is read-only — do not modify for project-specific reasons
- If a design conflicts with a network principle, document the conflict and record a risk acceptance in the relevant deliverable
- The canonical draw.io template is `diagrams/drawio/network_ra_template_V1.drawio`
- Do not edit files in `docs/input/` — they are source material
- Naming conventions are in `docs/standards/naming-standards.md` — follow them
- AAD standards in `docs/standards/` define mandatory rules for approved technologies — any design using those technologies must comply

---

## First steps for a new project

1. Update `schemas/architecture-model.yaml` — set project name, areas, actors
2. Update `README.md` — set project name and description
3. Fill in `docs/deliverables/01-D0-Context-Overview.md` first — scope and context before detail
4. Work deliverables in order: D0 → D1 → D2 → D3
