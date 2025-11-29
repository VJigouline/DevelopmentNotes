# Using Graphviz in Markdown on GitHub

GitHub supports rendering **Graphviz** diagrams in Markdown using the `mermaid` syntax block (which also supports Graphviz's DOT language). This guide explains how to create flowcharts, graphs, state diagrams, and other visualizations directly in your README files, issues, pull requests, and discussions.

---

## Prerequisites

- GitHub.com (or GitHub Enterprise with Mermaid rendering enabled, which includes Graphviz support)
- Use markdown code fences with language identifier `mermaid`, `dot`, or `graphviz`
- Your repository settings allow rendering (default on most public repos)

---

## 1) Basic syntax

Graphviz diagrams are written in **DOT language** and rendered via Mermaid on GitHub.

```markdown
```mermaid
digraph G {
  A -> B;
  B -> C;
  C -> A;
}
```
```

**Rendered output**: A directed graph with nodes A, B, C connected in a cycle.

---

## 2) Simple flowchart

Create a flowchart showing process steps.

```markdown
```mermaid
digraph Flowchart {
  Start [shape=ellipse, label="Start"];
  Process [shape=box, label="Process Data"];
  Decision [shape=diamond, label="Valid?"];
  End [shape=ellipse, label="End"];

  Start -> Process;
  Process -> Decision;
  Decision -> End [label="Yes"];
  Decision -> Process [label="No"];
}
```
```

---

## 3) Node shapes

Common node shapes in Graphviz:

| Shape | Syntax | Use case |
|-------|--------|----------|
| Box | `shape=box` | Default action/process |
| Circle | `shape=circle` | Simple node |
| Ellipse | `shape=ellipse` | Start/end points |
| Diamond | `shape=diamond` | Decision/branching |
| Hexagon | `shape=hexagon` | Specialized process |
| Trapezium | `shape=trapezium` | Input/output |
| Polygon | `shape=polygon` | Custom polygons |
| Record | `shape=record` | Structured data |

```markdown
```mermaid
digraph Shapes {
  A [shape=box, label="Box"];
  B [shape=circle, label="Circle"];
  C [shape=ellipse, label="Ellipse"];
  D [shape=diamond, label="Diamond"];
  E [shape=hexagon, label="Hexagon"];
  F [shape=trapezium, label="Trapezium"];
}
```
```

---

## 4) Node styling

Control node appearance with attributes:

```markdown
```mermaid
digraph Styling {
  A [label="Node A", fillcolor=lightblue, style=filled];
  B [label="Node B", fillcolor=lightgreen, style=filled, shape=box];
  C [label="Node C", fillcolor=lightcoral, style=filled, shape=circle];
  
  A -> B;
  B -> C;
}
```
```

Common styling attributes:
- `fillcolor=color` — fill color (requires `style=filled`)
- `style=filled` — enable fill color
- `style=dotted` — dotted outline
- `style=bold` — bold outline
- `fontcolor=color` — text color
- `penwidth=value` — border thickness

---

## 5) Edge (arrow) styling

Control connections between nodes:

```markdown
```mermaid
digraph Edges {
  A -> B [label="solid"];
  A -> C [label="dotted", style=dotted];
  A -> D [label="dashed", style=dashed];
  B -> E [label="bold", penwidth=2];
  B -> F [label="arrow", arrowhead=normal];
}
```
```

Arrow styles:
- `style=solid` — solid line (default)
- `style=dotted` — dotted line
- `style=dashed` — dashed line
- `arrowhead=normal` — standard arrow (default)
- `arrowhead=none` — no arrow
- `dir=forward` — forward direction (default)
- `dir=back` — backward direction
- `dir=both` — bidirectional
- `dir=none` — no direction

---

## 6) Directed acyclic graph (DAG)

A common pattern for workflows, dependencies, or hierarchies:

```markdown
```mermaid
digraph DAG {
  rankdir=TB;
  
  Source [shape=ellipse];
  Build [shape=box];
  Test [shape=box];
  Deploy [shape=box];
  Monitor [shape=box];
  
  Source -> Build;
  Build -> Test;
  Test -> Deploy [label="if passed"];
  Deploy -> Monitor;
}
```
```

- `rankdir=TB` — top to bottom (default)
- `rankdir=LR` — left to right
- `rankdir=BT` — bottom to top
- `rankdir=RL` — right to left

---

## 7) Undirected graphs

Use `graph` instead of `digraph` for undirected connections:

```markdown
```mermaid
graph G {
  A -- B;
  B -- C;
  C -- D;
  D -- A;
  A -- C;
}
```
```

---

## 8) State machine diagram

Model states and transitions:

```markdown
```mermaid
digraph StateMachine {
  rankdir=LR;
  
  Idle [shape=circle];
  Running [shape=box];
  Paused [shape=box];
  Stopped [shape=circle];
  
  Idle -> Running [label="start"];
  Running -> Paused [label="pause"];
  Paused -> Running [label="resume"];
  Running -> Stopped [label="stop"];
  Paused -> Stopped [label="stop"];
}
```
```

---

## 9) Class hierarchy / inheritance diagram

Model object-oriented relationships:

```markdown
```mermaid
digraph ClassHierarchy {
  rankdir=BT;
  
  Animal [shape=box];
  Mammal [shape=box];
  Bird [shape=box];
  Dog [shape=box];
  Cat [shape=box];
  
  Mammal -> Animal [label="inherits"];
  Bird -> Animal [label="inherits"];
  Dog -> Mammal [label="inherits"];
  Cat -> Mammal [label="inherits"];
}
```
```

---

## 10) Network topology

Represent system architecture or network layout:

```markdown
```mermaid
digraph Network {
  rankdir=LR;
  
  Client [shape=box, fillcolor=lightblue, style=filled];
  LB [shape=hexagon, label="Load Balancer", fillcolor=lightyellow, style=filled];
  Server1 [shape=box, label="Server 1", fillcolor=lightgreen, style=filled];
  Server2 [shape=box, label="Server 2", fillcolor=lightgreen, style=filled];
  DB [shape=cylinder, label="Database", fillcolor=lightcoral, style=filled];
  
  Client -> LB;
  LB -> Server1;
  LB -> Server2;
  Server1 -> DB;
  Server2 -> DB;
}
```
```

---

## 11) Subgraphs (clusters)

Group related nodes together:

```markdown
```mermaid
digraph Clusters {
  rankdir=LR;
  
  subgraph Frontend {
    HTML [label="HTML"];
    CSS [label="CSS"];
    JS [label="JavaScript"];
  }
  
  subgraph Backend {
    API [label="API Server"];
    Auth [label="Authentication"];
  }
  
  subgraph Database {
    DB [label="PostgreSQL"];
    Cache [label="Redis"];
  }
  
  JS -> API;
  API -> Auth;
  Auth -> DB;
  API -> Cache;
}
```
```

---

## 12) Entity-relationship diagram (ER model)

Model database schema:

```markdown
```mermaid
digraph ERDiagram {
  rankdir=LR;
  
  Users [shape=record, label="{Users|id|name|email|created_at}"];
  Posts [shape=record, label="{Posts|id|user_id|title|content}"];
  Comments [shape=record, label="{Comments|id|post_id|user_id|text}"];
  
  Users -> Posts [label="1:N"];
  Posts -> Comments [label="1:N"];
  Users -> Comments [label="1:N"];
}
```
```

---

## 13) Dependency graph (for build systems or packages)

Show project dependencies:

```markdown
```mermaid
digraph Dependencies {
  rankdir=TB;
  
  MyApp [shape=box, fillcolor=lightblue, style=filled];
  
  Express [shape=box, fillcolor=lightyellow, style=filled];
  Postgres [shape=box, fillcolor=lightyellow, style=filled];
  JWT [shape=box, fillcolor=lightyellow, style=filled];
  
  MyApp -> Express;
  MyApp -> Postgres;
  MyApp -> JWT;
  
  Express -> NodeJS [shape=box, fillcolor=lightgray, style=filled];
  Postgres -> C [shape=box, fillcolor=lightgray, style=filled, label="C Library"];
}
```
```

---

## 14) Decision tree

Binary or multi-branch decision flow:

```markdown
```mermaid
digraph DecisionTree {
  rankdir=TB;
  
  Start [shape=ellipse, label="Start"];
  Q1 [shape=diamond, label="Score >= 80?"];
  Q2 [shape=diamond, label="Score >= 60?"];
  Pass [shape=ellipse, label="Pass", fillcolor=lightgreen, style=filled];
  Fail [shape=ellipse, label="Fail", fillcolor=lightcoral, style=filled];
  
  Start -> Q1;
  Q1 -> Pass [label="Yes"];
  Q1 -> Q2 [label="No"];
  Q2 -> Pass [label="Yes"];
  Q2 -> Fail [label="No"];
}
```
```

---

## 15) Mind map (using subgraphs)

Visualize hierarchical ideas:

```markdown
```mermaid
digraph MindMap {
  rankdir=LR;
  
  Center [shape=box, label="Project", fillcolor=gold, style=filled];
  
  Design [shape=box, label="Design"];
  Dev [shape=box, label="Development"];
  Test [shape=box, label="Testing"];
  Deploy [shape=box, label="Deployment"];
  
  Center -> Design;
  Center -> Dev;
  Center -> Test;
  Center -> Deploy;
  
  Design -> UI [label="UI"];
  Design -> UX [label="UX"];
  
  Dev -> Backend [label="Backend"];
  Dev -> Frontend [label="Frontend"];
  
  Test -> Unit [label="Unit Tests"];
  Test -> Integration [label="Integration Tests"];
  
  Deploy -> Staging [label="Staging"];
  Deploy -> Production [label="Production"];
}
```
```

---

## 16) Colors and appearance

GitHub supports standard CSS color names and hex codes:

```markdown
```mermaid
digraph Colors {
  A [label="Red", fillcolor=red, style=filled, fontcolor=white];
  B [label="Green", fillcolor=green, style=filled, fontcolor=white];
  C [label="Blue", fillcolor=blue, style=filled, fontcolor=white];
  D [label="Yellow", fillcolor=yellow, style=filled];
  E [label="Custom", fillcolor="#FF69B4", style=filled, fontcolor=white];
  
  A -> B -> C -> D -> E;
}
```
```

Common colors: `red`, `green`, `blue`, `yellow`, `orange`, `purple`, `cyan`, `magenta`, `lightblue`, `lightgreen`, `lightcoral`, `lightyellow`, `gold`, `silver`, `gray`, `white`, `black`.

---

## 17) Record nodes (structured data)

Display structured information in nodes:

```markdown
```mermaid
digraph Records {
  Person [shape=record, label="{Person|Name: John|Age: 30|Email: john@example.com}"];
  Address [shape=record, label="{Address|Street: 123 Main|City: NYC|ZIP: 10001}"];
  
  Person -> Address [label="lives at"];
}
```
```

---

## 18) Multigraph (multiple edges between nodes)

Allow multiple connections:

```markdown
```mermaid
digraph MultiGraph {
  A -> B [label="edge 1"];
  A -> B [label="edge 2", style=dotted];
  A -> B [label="edge 3", style=dashed];
  
  B -> C [label="fast", penwidth=2];
  B -> C [label="slow", penwidth=1];
}
```
```

---

## 19) Real-world examples

**CI/CD Pipeline:**
```markdown
```mermaid
digraph CICD {
  rankdir=TB;
  
  Code [shape=ellipse, label="Git Push"];
  Lint [shape=box, label="Lint"];
  Build [shape=box, label="Build"];
  UnitTest [shape=box, label="Unit Tests"];
  IntTest [shape=box, label="Integration Tests"];
  StagingDeploy [shape=box, label="Deploy to Staging"];
  ApprovalGate [shape=diamond, label="Approved?"];
  ProdDeploy [shape=box, label="Deploy to Production"];
  Monitor [shape=ellipse, label="Monitor"];
  
  Code -> Lint;
  Lint -> Build;
  Build -> UnitTest;
  UnitTest -> IntTest;
  IntTest -> StagingDeploy;
  StagingDeploy -> ApprovalGate;
  ApprovalGate -> ProdDeploy [label="Yes"];
  ApprovalGate -> Code [label="No"];
  ProdDeploy -> Monitor;
}
```
```

**Microservices Architecture:**
```markdown
```mermaid
digraph Microservices {
  rankdir=LR;
  
  Client [shape=box, fillcolor=lightblue, style=filled];
  Gateway [shape=hexagon, label="API Gateway", fillcolor=lightyellow, style=filled];
  
  UserService [shape=box, label="User Service", fillcolor=lightgreen, style=filled];
  OrderService [shape=box, label="Order Service", fillcolor=lightgreen, style=filled];
  PaymentService [shape=box, label="Payment Service", fillcolor=lightgreen, style=filled];
  
  UserDB [shape=cylinder, label="User DB", fillcolor=lightcoral, style=filled];
  OrderDB [shape=cylinder, label="Order DB", fillcolor=lightcoral, style=filled];
  
  MessageQueue [shape=hexagon, label="Message Queue", fillcolor=lightyellow, style=filled];
  
  Client -> Gateway;
  Gateway -> UserService;
  Gateway -> OrderService;
  Gateway -> PaymentService;
  
  UserService -> UserDB;
  OrderService -> OrderDB;
  
  OrderService -> MessageQueue;
  PaymentService -> MessageQueue;
}
```
```

---

## 20) Tips and best practices

1. **Keep it simple**: Use clear, descriptive labels for nodes and edges.
2. **Consistent styling**: Use similar colors/shapes for related node types.
3. **Readable layout**: Use `rankdir` to orient diagrams appropriately.
4. **Avoid clutter**: Group related nodes with subgraphs when possible.
5. **Test locally**: Use online Graphviz editors to preview before committing.
6. **Document intent**: Add comments above complex diagrams explaining their purpose.
7. **Use semantic shapes**: Let shape choices reflect the node's role (ellipse=start/end, diamond=decision).

---

## 21) Troubleshooting

| Issue | Solution |
|-------|----------|
| Diagram not rendering | Ensure code fence uses `mermaid` or `dot` language identifier |
| Syntax error | Check DOT syntax; verify semicolons at end of statements |
| Text overflow | Use shorter labels or increase node size |
| Bad layout | Try different `rankdir` values or adjust `ranksep`/`nodesep` |
| Colors not working | Verify color name is valid CSS color or valid hex code |
| Too many edges | Use `concentrate=true` to merge parallel edges |

---

## 22) Limitations

- GitHub uses a subset of Graphviz features; not all DOT language features are supported.
- Custom node/edge styles may render differently than local Graphviz.
- Rendering requires JavaScript; disable JavaScript will prevent diagram rendering.
- Large, complex diagrams may render slowly.

---

## 23) Resources

- Graphviz official documentation: https://graphviz.org/documentation/
- DOT language reference: https://graphviz.org/doc/info/lang.html
- Graphviz attributes: https://graphviz.org/doc/info/attrs.html
- Online Graphviz editor: https://dreampuf.github.io/GraphvizOnline/
- GitHub Markdown diagrams: https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-diagrams
- Mermaid documentation: https://mermaid.js.org/syntax/flowchart.html

---

**Pro tip**: Use online Graphviz editors to design and test your diagrams before adding them to GitHub. This helps catch syntax errors early and lets you preview the final rendering.