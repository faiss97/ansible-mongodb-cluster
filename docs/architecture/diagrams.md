# Architecture Diagrams

Visual representations of MongoDB cluster architectures supported by this role.

## Replica Set Architecture

A standard 3-node replica set with automatic failover:

```mermaid
graph TB
    subgraph "MongoDB Replica Set"
        direction TB
        P[("🟢 Primary<br/>mongo1:27017")]
        S1[("🔵 Secondary<br/>mongo2:27017")]
        S2[("🔵 Secondary<br/>mongo3:27017")]
        
        P <-->|"Replication<br/>(oplog)"| S1
        P <-->|"Replication<br/>(oplog)"| S2
        S1 <-.->|Heartbeat| S2
    end
    
    subgraph "Applications"
        APP1[App Server 1]
        APP2[App Server 2]
        APP3[App Server 3]
    end
    
    APP1 -->|"Read/Write"| P
    APP2 -->|"Read/Write"| P
    APP3 -->|"Read/Write"| P
    
    APP1 -.->|"Read (secondary)"| S1
    APP2 -.->|"Read (secondary)"| S2
```

## Sharded Cluster Architecture

Horizontally scalable sharded cluster:

```mermaid
graph TB
    subgraph "Applications"
        APP[Application Servers]
    end
    
    subgraph "Query Routers"
        MS1[mongos 1<br/>:27017]
        MS2[mongos 2<br/>:27017]
    end
    
    subgraph "Config Servers (Replica Set)"
        C1[(Config 1<br/>:27019)]
        C2[(Config 2<br/>:27019)]
        C3[(Config 3<br/>:27019)]
    end
    
    subgraph "Shard 1 (Replica Set)"
        S1P[(Primary)]
        S1S1[(Secondary)]
        S1S2[(Secondary)]
    end
    
    subgraph "Shard 2 (Replica Set)"
        S2P[(Primary)]
        S2S1[(Secondary)]
        S2S2[(Secondary)]
    end
    
    APP --> MS1
    APP --> MS2
    
    MS1 --> C1
    MS1 --> C2
    MS1 --> C3
    MS2 --> C1
    MS2 --> C2
    MS2 --> C3
    
    MS1 --> S1P
    MS1 --> S2P
    MS2 --> S1P
    MS2 --> S2P
    
    S1P <--> S1S1
    S1P <--> S1S2
    S2P <--> S2S1
    S2P <--> S2S2
```

## High Availability with DR

Cross-datacenter deployment with delayed secondary:

```mermaid
graph TB
    subgraph "Primary Datacenter (DC1)"
        direction TB
        P1[("🟢 Primary<br/>priority: 2")]
        S1[("🔵 Secondary<br/>priority: 1")]
    end
    
    subgraph "Secondary Datacenter (DC2)"
        direction TB
        S2[("🔵 Secondary<br/>priority: 1")]
        S3[("⏱️ Delayed Secondary<br/>delay: 1 hour<br/>priority: 0")]
    end
    
    P1 <-->|"Sync Replication"| S1
    P1 <-->|"Async Replication"| S2
    P1 <-->|"Delayed Replication"| S3
    
    S1 <-.->|Heartbeat| S2
    S2 <-.->|Heartbeat| S3
```

## Monitoring Architecture

Complete monitoring stack:

```mermaid
graph LR
    subgraph "MongoDB Cluster"
        M1[MongoDB 1]
        M2[MongoDB 2]
        M3[MongoDB 3]
    end
    
    subgraph "Exporters"
        E1[Exporter :9216]
        E2[Exporter :9216]
        E3[Exporter :9216]
    end
    
    subgraph "Monitoring Stack"
        PROM[(Prometheus)]
        AM[Alertmanager]
        GRAF[Grafana]
    end
    
    subgraph "Notifications"
        SLACK[Slack]
        PD[PagerDuty]
        EMAIL[Email]
    end
    
    M1 --- E1
    M2 --- E2
    M3 --- E3
    
    E1 --> PROM
    E2 --> PROM
    E3 --> PROM
    
    PROM --> AM
    PROM --> GRAF
    
    AM --> SLACK
    AM --> PD
    AM --> EMAIL
```

## Backup Architecture

Automated backup flow:

```mermaid
sequenceDiagram
    participant C as Cron
    participant B as Backup Script
    participant M as MongoDB
    participant L as Local Storage
    participant S as S3 Bucket
    
    C->>B: Trigger backup (daily 2am)
    B->>M: mongodump --oplog
    M-->>B: Backup data
    B->>L: Store backup.tar.gz
    B->>L: Create checksum
    B->>S: Upload to S3
    S-->>B: Confirm upload
    B->>L: Cleanup old backups
    Note over B,L: Retention: 7 days local
    Note over B,S: Retention: 30 days S3
```

## PITR (Point-in-Time Recovery) Flow

```mermaid
graph TB
    subgraph "Continuous Backup"
        OT[Oplog Tailer Service]
        M[(MongoDB)]
        OL[/Oplog Files/]
        SN[/Daily Snapshots/]
    end
    
    subgraph "Recovery Process"
        R[Recovery Script]
        TS[Target Timestamp]
        RS[(Restored DB)]
    end
    
    M -->|"Tail oplog"| OT
    OT -->|"Save every 60s"| OL
    M -->|"Daily snapshot"| SN
    
    SN --> R
    OL --> R
    TS --> R
    R -->|"Restore + Replay"| RS
```

## Network Security

Firewall and network configuration:

```mermaid
graph TB
    subgraph "External Network"
        INET[Internet]
    end
    
    subgraph "DMZ"
        LB[Load Balancer]
        APP[App Servers]
    end
    
    subgraph "Database Network (Private)"
        FW{Firewall}
        M1[MongoDB 1<br/>:27017]
        M2[MongoDB 2<br/>:27017]
        M3[MongoDB 3<br/>:27017]
    end
    
    INET -.->|"❌ Blocked"| M1
    INET --> LB
    LB --> APP
    APP --> FW
    FW -->|"✅ Allow 27017<br/>from App subnet"| M1
    FW -->|"✅ Allow 27017<br/>from App subnet"| M2
    FW -->|"✅ Allow 27017<br/>from App subnet"| M3
    
    M1 <-->|"Internal<br/>Replication"| M2
    M2 <-->|"Internal<br/>Replication"| M3
```

## Deployment Pipeline

CI/CD workflow for role deployment:

```mermaid
graph LR
    subgraph "Development"
        DEV[Developer]
        GIT[GitHub]
    end
    
    subgraph "CI/CD Pipeline"
        LINT[Lint]
        SEC[Security Scan]
        MOL[Molecule Tests]
        INT[Integration Tests]
    end
    
    subgraph "Release"
        REL[GitHub Release]
        GAL[Ansible Galaxy]
        DOC[GitHub Pages]
    end
    
    subgraph "Deployment"
        STG[Staging]
        PRD[Production]
    end
    
    DEV -->|Push| GIT
    GIT -->|Trigger| LINT
    LINT --> SEC
    SEC --> MOL
    MOL --> INT
    INT -->|Tag| REL
    REL --> GAL
    REL --> DOC
    GAL --> STG
    STG -->|Approve| PRD
```
