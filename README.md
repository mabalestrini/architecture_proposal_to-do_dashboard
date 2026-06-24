# Reference Architecture Template

A reusable framework for producing Reference Architecture documentation across any domain — network, ITSM, CMDB, automation, security, and others.

## How to use this template

1. Click **"Use this template"** on GitHub to create a new repository
2. Update `schemas/architecture-model.yaml` with your project name, areas, and actors
3. Fill in `docs/deliverables/` stubs with your project-specific content
4. Read `docs/network-principles/network-principles.md` — all designs must comply with these

---

## Folder structure

```
docs/
  network-principles/  Standing network constraints — inherited by every project (read-only)
  standards/           Naming, color, diagram governance, drawio and document standards
  deliverables/        Numbered deliverables: fill in for your project (D0–D3 stubs)
  templates/           Reusable document and slide templates
  input/               Source material for this project (pptx, csv, etc.)
  output/              Extracted or generated content

diagrams/
  drawio/              Base draw.io template
  templates/mermaid/   Mermaid diagram skeleton starters

schemas/               Machine-readable architecture model (YAML + CSV)
stencils/              Draw.io XML library + Visio construction guide
assets/svg/            Area and zone SVG icons
scripts/               Utility scripts (pptx text extraction)
```

---

## Deliverable numbering

| Prefix | Layer | Purpose |
|---|---|---|
| 00 | Principles | Project-specific architecture principles |
| 01 | D0 | Enterprise / program context overview |
| 02–07 | D1 | Domain-level overviews |
| 08–18 | D2 | Detailed designs per domain |
| 19 | D3 | Business / service flows |
| 20–21 | — | Reference appendices |

---

## Network principles

`docs/network-principles/network-principles.md` contains 19 principles covering design, security, and governance. Every deployment described in this RA must comply with them. If a design conflicts with a principle, document the conflict and record a risk acceptance.

---

## Key standards

| Topic | File |
|---|---|
| Naming conventions | `docs/standards/naming-standards.md` |
| Color coding | `docs/standards/color-standards.md` |
| Diagram governance | `docs/standards/diagram-governance.md` |
| Draw.io conventions | `docs/standards/drawio-standards.md` |
| Document template | `docs/standards/architecture-document-template.md` |
| Architecture model | `schemas/architecture-model.yaml` |

---

## Scripts

```bash
pip install -r scripts/requirements.txt
python scripts/extract_pptx_text.py path/to/file.pptx docs/output/file.md
```
