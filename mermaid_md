# Mermaid Graphs

Mermaid graphs in the site are generated manually by the mermaid live editor: https://mermaid-js.github.io/mermaid-live-editor

# Architecture

## 1st Graph: Overall graph

```mermaid
graph LR
    A(GUI)-->B
    A
    B[fgAPIServer]-->C
    C[(Database)]-->E
    subgraph FutureGateway core
      B
      C
      S
      E
    end
    E[APIServerDaemon]-->F
    E-->G
    E-->H
    B-->S{{Shared Filesystem}}
    S-->E
    F(Grid and Cloud Engine)-->Z
    G(ToscaIDC)-->Z
    H(Custom EI)-->Z
    Z[Cyber Infrastructures]
    subgraph Executor Interfaces
      F
      G
      H
    end
```

## 2nd Graph: API processing flow

```mermaid
flowchart LR
  subgraph GUI [GUI]
    W(Web App)
    M(Mobile App)
    A(APIs)
  end
  subgraph API [Rest APIs]
    C(COMMAND)
    R(RESPONSE)
  end
  GUI-- REST API -->C
  subgraph FE [Front-end]
    F1([AuthN/Z check])-->F2
    F2([Process the command])-->F3
    F3([Enqueue the command])-->F4([Prepare response])
  end
  C-->F1
  F4-->R
  subgraph EXT [AuthN/Z]
    P1(PTV)
    P2(Native AuthN/Z)
  end
  subgraph Q [DB Queue]
   ASDBQ(QUEUE)
  end
  F1---P1
  F1---P2
  F3-->Q
  R-- json -->GUI
```

## Third graph: Queue command

```mermaid
flowchart LR
  Q(Queue)
  subgraph C [Command]
    A(Action)
    EI(EI Name)
    A & EI --> Action
  end
  Q-->C
  Action-->CI(Cyber infrastructure)
```

