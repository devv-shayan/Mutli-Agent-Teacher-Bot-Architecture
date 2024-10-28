# Mutli-Agent-Teacher-Bot-Architecture

```mermaid
flowchart TD
    classDef userNode fill:#e6f3f,stroke:#4a90e2,stroke-width:2px
    classDef interfaceNode fill:#f5f55,stroke:#666,stroke-width:2px
    classDef aiNode fill:#fff0f,stroke:#ff69b4,stroke-width:2px
    classDef dbNode fill:#f0fff,stroke:#3cb371,stroke-width:2px
    classDef humanLoopNode fill:#ffe66,stroke:#ff4d4d,stroke-width:2px

    %% Users
    Teacher([👤 Teacher]):::userNode
    

    %% Interface Layer
    subgraph Frontend["Frontend Interface"]
        Interface[Next.js Web Interface]:::interfaceNode
    end

    %% AI Processing Layer
    subgraph AI_Layer["AI Processing Layer"]
        direction TB
        Chat[Bidirectional Q&A System]:::aiNode
        ContentEngine[Content Generation Engine]:::aiNode

        subgraph AI_Processing["AI Processing Components"]
            TextGen[Textbook Generator]:::aiNode
            InteractionGen[Interactive Content Generator]:::aiNode
            AnalyticsGen[Analytics Generator]:::aiNode
        end
    end
    subgraph Human_Loop["Human-in-the-Loop Oversight"]
            Review[Teacher Review & Approval]:::humanLoopNode
            Feedback[Teacher Feedback Loop]:::humanLoopNode
            Intervention[Manual Intervention]:::humanLoopNode
        end

    %% Database Layer
    subgraph Storage["Data Storage (Neo4j AuraDB)"]
        direction TB
        Contents[(Course Content DB)]:::dbNode
        Questions[(Interactive Content DB)]:::dbNode
        Progress[(Student Progress DB)]:::dbNode
    end

    %% Connections
    Teacher --> Interface
    Interface <--> Chat

    Chat <--> ContentEngine
    ContentEngine <--> TextGen
    ContentEngine <--> InteractionGen
    ContentEngine <--> AnalyticsGen

    %% Human-in-the-Loop Connections
    ContentEngine --> Review
    Review --> Feedback
    Feedback --> ContentEngine
    Review --> Intervention
    Intervention --> ContentEngine

    ContentEngine --> Contents
    ContentEngine --> Questions
    ContentEngine --> Progress

    Contents -.-> Chat
    Questions -.-> Chat
    Progress -.-> Chat

    %% Styles
    style Frontend fill:#f8f9f,stroke:#dee2e6,stroke-width:2px
    style AI_Layer fill:#fff0f,stroke:#ffb6c1,stroke-width:2px
    style Storage fill:#f0ff0,stroke:#b6ffb6,stroke-width:2px
    style Human_Loop fill:#ffe6e,stroke:#ffcccc,stroke-width:2px
    style AI_Processing fill:#ff7ff,stroke:#b6d4ff,stroke-width:2px

    %% Link Styles
    linkStyle 0,1 stroke:#4a90e2,stroke-width:2px %% Teacher-Interface connections
    linkStyle 2,3,4,5 stroke:#ff69b4,stroke-width:2px %% AI layer connections
    linkStyle 6,7,8,9,10 stroke:#ff4d4d,stroke-width:2px %% Human loop connections
    linkStyle 11,12,13 stroke:#3cb371,stroke-width:2px %% Database connections
    linkStyle 14,15,16 stroke:#3cb371,stroke-dasharray:5,stroke-width:2px %% Dotted database connections
```

# Humna In Loop Flow
```mermaid
sequenceDiagram
    participant CE as Content Engine
    participant R as Review
    participant F as Feedback
    participant I as Intervention
    
    Note over CE,I: Normal Flow
    CE->>R: Sends generated content
    R->>F: Teacher reviews content
    F->>CE: Provides feedback/corrections
    CE->>R: Sends updated content
    
    Note over CE,I: Intervention Flow
    R->>I: Identifies major issues
    I->>CE: Manual corrections/overrides
    CE->>R: Regenerates with intervention
    
    Note over CE,I: Approval Flow
    R-->>CE: Content approved
    CE->>Storage: Stores approved content
```
