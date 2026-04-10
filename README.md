# Enterprise Architecture Vault – Aggregated Structure

**Purpose**  
This document defines the complete folder and file structure for the **enterprise-scale architecture artifact system**.  
It supports **multiple Git repositories** (one per domain/tribe) aggregated into a unified view via an `enterprise-atlas` repository using Git submodules.

**Core Design Principles**
- Domain-level repositories remain the **source of truth** (fast Git operations, clear ownership).
- `enterprise-atlas` provides a **single logical view** for discovery, cross-domain navigation, and high-level architecture overviews.
- All artifacts are plain Markdown files with YAML frontmatter and wiki-style links.
- The design stays fully tool-agnostic and works with any Markdown editor, Git workflow, or static site generator.

---

## 1. Overall Repository Layout

### Root Repository: `enterprise-atlas` (The Aggregation Layer)
```
enterprise-atlas/                          # Thin Git repo with submodules – unified enterprise view
├── README.md                              # Enterprise Home / Atlas Map of Content (MOC)
├── domains/                               # All domain vaults as Git submodules
│   ├── finance/                           # ← submodule pointing to domains-finance.git
│   ├── customer-experience/               # ← submodule
│   ├── supply-chain/                      # ← submodule
│   ├── identity-platform/                 # Shared / platform domain
│   ├── risk-management/
│   └── ...                                # One folder per business domain or engineering tribe
├── shared/                                # Cross-cutting artifacts
│   ├── resources/                         # Enterprise-wide resources (central Kafka, Auth service, etc.)
│   ├── capabilities/                      # Business capability map
│   ├── teams/                             # Optional: Team ownership entities
│   └── enterprise-context.md              # High-level C4 Context diagram
├── diagrams/                              # Enterprise-wide diagrams (Mermaid or other)
│   ├── enterprise-c4-context.md
│   ├── domain-landscape.md
│   └── technology-radar.md
├── governance/                            # Standards and processes
│   ├── contribution-guide.md
│   ├── templates/                         # Shared note templates
│   ├── adrs/                              # Architecture Decision Records index
│   └── validation-rules.md
├── scripts/                               # Automation helpers
│   ├── update-submodules.sh
│   ├── validate-links.py
│   ├── generate-enterprise-graph.py
│   └── sync-catalog.sh
├── .gitmodules                            # Defines all domain submodules
└── docs/                                  # Optional rendered documentation output
```

### Example Domain Repository (`domains-finance.git`)
```
domains-finance/                           # Independent Git repo owned by the Finance tribe
├── README.md                              # Finance Domain Atlas Map of Content (MOC)
├── index.md                               # List of all Systems in this domain
├── systems/
│   ├── index.md                           # Systems MOC for Finance
│   ├── payment-processing.md
│   ├── order-management.md
│   └── fraud-engine.md
├── components/
│   ├── index.md
│   ├── payment-gateway.md
│   ├── fraud-detection.md
│   └── settlement-service.md
├── resources/
│   ├── index.md
│   ├── stripe-api.md
│   ├── postgres-payments.md
│   └── kafka-finance-events.md
├── diagrams/
│   ├── finance-c4-context.md
│   └── payment-system-container.md
├── templates/                             # Optional: domain-specific templates
└── .github/                               # CI workflows, CODEOWNERS, etc.[27;2;13~[27;2;1
```

**Note**: Every domain repository follows the **same internal structure** (`systems/`, `components/`, `resources/`, `diagrams/`, `index.md` files).

---

## 2. Core Entity File Locations & Naming Conventions

- **System** files  → `domains/{domain}/systems/{kebab-case-slug}.md`
- **Component** files → `domains/{domain}/components/{kebab-case-slug}.md`
- **Resource** files → `domains/{domain}/resources/{kebab-case-slug}.md` (or `shared/resources/` for enterprise-wide)
- **Domain MOC**     → `domains/{domain}/index.md` and `domains/{domain}/README.md`
- **Enterprise MOC** → `enterprise-atlas/README.md`

**Naming convention**: Use `kebab-case` slugs. Slugs must be unique within their type.

---

## 3. YAML Frontmatter Standard (All Entity Files)

```yaml
---
type: System | Component | Resource
name: Human Readable Name
slug: kebab-case-slug
domain: Finance
tribe: Commerce
owner: team-payments-platform
status: active | deprecated | planned
version: "1.2"
tags: 
  - payments
  - critical
---
```


**Note**: Every domain repository follows the **same internal structure** (`systems/`, `components/`, `resources/`, `diagrams/`, `index.md` files).

---

## 2. Core Entity File Locations & Naming Conventions

- **System** files  → `domains/{domain}/systems/{kebab-case-slug}.md`
- **Component** files → `domains/{domain}/components/{kebab-case-slug}.md`
- **Resource** files → `domains/{domain}/resources/{kebab-case-slug}.md` (or `shared/resources/` for enterprise-wide)
- **Domain MOC**     → `domains/{domain}/index.md` and `domains/{domain}/README.md`
- **Enterprise MOC** → `enterprise-atlas/README.md`

**Naming convention**: Use `kebab-case` slugs. Slugs must be unique within their type.

---

## 3. YAML Frontmatter Standard (All Entity Files)

```yaml
---
type: System | Component | Resource
name: Human Readable Name
slug: kebab-case-slug
domain: Finance
tribe: Commerce
owner: team-payments-platform
status: active | deprecated | planned
version: "1.2"
tags: 
  - payments
  - critical
---
```

4. Aggregation Mechanics

Git Submodules are used in enterprise-atlas/domains/ to pull in each domain repository.
Relative links are written to work correctly when the full atlas structure is checked out.
Cross-domain references always use paths relative to the enterprise-atlas root.
Build-time unification (optional but recommended):
CI job in enterprise-atlas runs git submodule update --remote
Scripts generate master indexes, unified Mermaid diagrams, or feed data into a static site / catalog tool.



5. Navigation Layers

Enterprise Atlas (enterprise-atlas/README.md) – High-level entry point with links to all domains
Domain MOCs (domains/{domain}/index.md) – Business-oriented view of systems and capabilities
Type-specific MOCs (domains/{domain}/systems/index.md, etc.)
Embedded Mermaid diagrams in MOCs and entity files for C4-style visualizations


6. Governance & Maintenance Rules

Each domain repository has its own CODEOWNERS file.
Teams edit only inside their own domain repository.
enterprise-atlas is updated via submodules (manual or scheduled CI).
CI pipelines in every repo enforce:
Frontmatter validation
Internal link checking
Consistent slug and naming rules



How to Use This Structure

Clone the atlas with all submodules:Bashgit clone --recurse-submodules https://github.com/yourorg/enterprise-atlas.git
Work on a specific domain:
Navigate into domains/{domain}/ and edit files directly, or clone the domain repository separately for faster workflows.

After changes are merged in a domain repo, update the atlas:Bashcd enterprise-atlas
git submodule update --remote domains/finance
git commit -m "Update finance domain to latest"
git push
