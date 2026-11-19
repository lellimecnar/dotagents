# Diagram selection

Pick the diagram by the question the owner is asking. Draw one diagram per question, at one abstraction level.
The brief carries at most one focal diagram; further views go in the ADR or an appendix. C4 is notation- and
tool-independent; the views below map onto it (https://c4model.com/).

**Format.** Use the repository's or output's existing diagram convention first. Otherwise pick a text-native
format confirmed to render in the target (repository host, document tool, PDF, email), and check the rendered
result before sending. Mermaid is a reasonable fallback where the target renders it; it supports C4 syntax
(https://mermaid.js.org/syntax/c4.html). If nothing renders, ship the source plus the text equivalent.

| Owner's question | Diagram | Include when |
|---|---|---|
| What is this, who uses it, what does it talk to? | C4 System Context | For a new or changed system, only when the audience lacks shared context. Aim for 5–9 boxes |
| What are the deployable pieces and technology choices? | C4 Container | Usually the focal diagram |
| What happens, in what order, and what if a step fails? | Sequence (C4 Dynamic) | Latency, retries, failure paths |
| Where does it run, and what fails together? | Deployment | Hosting, HA, DR, cost-per-node questions |
| Where can it be attacked, and where does sensitive data go? | Data-flow diagram with trust boundaries | Security, privacy, compliance questions; then apply STRIDE (https://cheatsheetseries.owasp.org/cheatsheets/Threat_Modeling_Cheat_Sheet.html) |
| How does it fit the wider estate? | C4 System Landscape | Multi-system proposals only |
| What's inside one container? | C4 Component | Only if the decision hinges on it |
| How do options' costs behave as load grows? | Cost curve (line per option, crossover marked) | Cost-driven decisions (a design suggestion, not a researched standard) |
| How do options compare on two dimensions? | 2×2 quadrant | Positioning a few options; label the axes with real meaning |

**Charts to avoid:** radar or spider charts. Their area depends on axis order and they are hard to compare; use a
table or a bar or dot chart per criterion (https://www.perceptualedge.com/articles/dmreview/radar_graphs.pdf).

**Other diagrams-as-code categories**, by need: PlantUML-family C4 libraries for teams already on PlantUML
(https://plantuml-stdlib.github.io/C4-PlantUML); model-based DSLs that keep several C4 views consistent from one
model (https://docs.structurizr.com/dsl/language); declarative layout languages for polished standalone diagrams
(https://d2lang.com/). Check current syntax in each tool's docs; syntax evolves.

## Communication checklist (every diagram)

- One sentence: who the reader is and what question this diagram answers.
- A title and a purpose line.
- Every element at the same abstraction level.
- Names consistent across all views and the text.
- The primary path visually dominant.
- A legend that explains notation, not the story.
- Labelled relationships: what flows, and over what protocol.
- **Text equivalent next to the diagram:** prose stating the conclusion, the principal relationships and any
  material failure or trust boundaries. A reader who can't see or render the diagram loses nothing essential.
- Rendering checked in the target format.

Checklist structure adapted (attribution only) from magnus919's C4 communication review (MIT,
https://github.com/magnus919/agent-skills/blob/main/c4-diagramming/references/technical-diagram-communication.md);
the rules rest on C4 (https://c4model.com/) and the OWASP data-flow guidance above.

---

## Snippets (illustrations: a hypothetical invoicing app)

### System context
```mermaid
C4Context
  title System context: Invoicing app
  Person(owner, "Business owner", "Creates and sends invoices")
  System(app, "Invoicing app", "Web application")
  System_Ext(email, "Email provider", "Delivers invoice emails")
  System_Ext(pay, "Payment provider", "Takes card payments")
  Rel(owner, app, "Uses", "HTTPS")
  Rel(app, email, "Sends email via", "API")
  Rel(app, pay, "Creates payment links", "API")
```

### Container
```mermaid
C4Container
  title Containers: Invoicing app
  Person(owner, "Business owner")
  System_Boundary(sys, "Invoicing app") {
    Container(web, "Web app", "One codebase", "UI and API")
    Container(worker, "Job worker", "Same codebase", "Sends emails, renders PDFs")
    ContainerDb(db, "Database", "Postgres", "Invoices and jobs")
  }
  System_Ext(email, "Email provider")
  Rel(owner, web, "Uses", "HTTPS")
  Rel(web, db, "Reads and writes", "SQL")
  Rel(worker, db, "Claims jobs", "SQL")
  Rel(worker, email, "Sends", "API")
```

### Sequence (with a failure path)
```mermaid
sequenceDiagram
  autonumber
  actor U as User
  participant W as Web app
  participant D as Database
  participant J as Job worker
  participant E as Email provider
  U->>W: Send invoice
  W->>D: Insert invoice and job in one transaction
  W-->>U: Accepted
  J->>D: Claim next job (SKIP LOCKED)
  J->>E: Send email
  alt provider error
    J->>D: Schedule retry with backoff
  else success
    J->>D: Mark job done
  end
```

### Deployment
```mermaid
flowchart TB
  subgraph region["Region A"]
    subgraph vm["Server: app-1 (single box)"]
      web["Web app"]
      worker["Job worker"]
      db[("Postgres")]
    end
  end
  backup[("Off-site object storage: nightly backups")]
  db -- "backup, restore tested monthly" --> backup
```

### Data flow with trust boundaries
```mermaid
flowchart LR
  subgraph internet["Trust boundary: public internet"]
    user(["User: external entity"])
  end
  subgraph app["Trust boundary: application server"]
    web["Web app: process"]
    worker["Job worker: process"]
  end
  subgraph data["Trust boundary: data tier"]
    db[("Database: data store")]
  end
  ext(["Email provider: external entity"])
  user -- "credentials, invoice data over HTTPS" --> web
  web -- "SQL over TLS" --> db
  worker -- "SQL over TLS" --> db
  worker -- "customer email address, PDF" --> ext
```

### Cost curve (shape only; no real prices)
```mermaid
xychart-beta
  title "Relative monthly cost vs load (illustrative shape)"
  x-axis "Load (requests/s)" [1, 10, 100, 1000]
  y-axis "Relative cost" 0 --> 100
  line [5, 8, 30, 90]
  line [20, 22, 28, 45]
```
*Caption the lines in text (this chart type has no legend). E.g. "Line 1: pay-per-use; line 2: fixed server.
Crossover near 100 req/s under the stated assumptions."*

### 2×2 quadrant (switching cost vs unique utility, after Hohpe's lock-in matrix)
```mermaid
quadrantChart
  title Lock-in: switching cost vs unique utility
  x-axis Low switching cost --> High switching cost
  y-axis Low unique utility --> High unique utility
  quadrant-1 Accept knowingly
  quadrant-2 Best of both
  quadrant-3 Commodity
  quadrant-4 Avoid
  Managed database: [0.35, 0.6]
  Proprietary queue: [0.8, 0.3]
```
Lock-in framing: https://martinfowler.com/articles/oss-lockin.html (quadrant labels are ours).
