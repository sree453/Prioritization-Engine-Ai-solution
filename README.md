# Prioritization-Engine-Ai-solution
System architecture and decision logic for an AI-driven notification engine—handling high-volume events with Now/Later/Never classification.
[cite_start]This repository contains the system architecture and design for a Notification Prioritization Engine, developed for the **Cyepro Solutions** Round 1 Solution Crafting Test[cite: 1, 3]. [cite_start]The engine classifies incoming notification events into **Now, Later, or Never** to reduce alert fatigue and improve user experience[cite: 7, 11].

## 1. High-Level Architecture
[cite_start]The system is designed for high event volume and low latency[cite: 29, 30].

```mermaid
graph TD
    %% Global Styles
    classDef client fill:#b3e5fc,stroke:#01579b,stroke-width:2px;
    classDef service fill:#ffffff,stroke:#333333,stroke-width:2px;
    classDef storage fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef engine fill:#fff9c4,stroke:#fbc02d,stroke-width:2px;
    classDef outcome fill:#dcedc8,stroke:#33691e,stroke-width:2px;

    %% Architecture Flow
    Start((Client Services)):::client --> Gateway[API Gateway]:::service
    Gateway --> Ingest[Ingestion Service]:::service
    Ingest --> Kafka[(Message Queue: Kafka)]:::storage
    
    %% Added padding to subgraph to prevent title overwriting
    subgraph Processing_Layer ["&nbsp;&nbsp;&nbsp; Core Processing Logic &nbsp;&nbsp;&nbsp;"]
        direction TB
        Kafka --> Dedupe[Deduplication Layer: Redis]:::storage
        Dedupe --> AI[Prioritization Engine: Rules + AI]:::engine
        AI --> Log[Explanation Logger]:::service
    end

    Log --> Router{Decision Router}:::engine

    %% Decision Branches
    Router -->|High Priority| Now[NOW: Dispatcher]:::outcome
    Router -->|Medium/Low| Later[LATER: Scheduler]:::outcome
    Router -->|Redundant| Never[NEVER: Suppressed]:::outcome

    %% Final Delivery
    Now --> User((User Notification)):::client
    Later --> DB[(Storage for Digest)]:::storage

    %% Force larger box sizes to prevent text overlap
    style Gateway min-width:150px
    style Ingest min-width:150px
    style Dedupe min-width:200px
    style AI min-width:200px
    style Log min-width:150px
