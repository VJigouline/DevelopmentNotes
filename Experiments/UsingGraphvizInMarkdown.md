
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