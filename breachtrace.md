flowchart LR
  subgraph Client["Client / Integrations"]
    A1[Mobile/Web App]
    A2[Service Principal / Backend Job]
  end

  subgraph APIM["Azure API Management (Gateway)"]
    P1[Inbound Policy: generate<br/>x-correlation-id]
    P2[Auth: OAuth2/AAD / mTLS]
    P3[Masking/PII Filtering]
    P4[Outbound Policy: forward<br/>x-correlation-id]
  end

  subgraph Backends["Backends / Services"]
    B1[Azure App Service / Functions]
    B2[Workday REST/SOAP]
    B3[Other SaaS / DB]
  end

  subgraph Telemetry["Centralized Telemetry & Security"]
    L1[Diagnostic Settings → Log Analytics]
    L2[App Insights (traces, deps)]
    L3[Azure AD Sign-in & Audit Logs]
    L4[Workday Audit / Access Logs]
    S1[Microsoft Sentinel (Hunting, Alerts, UEBA)]
    D1[Microsoft Purview<br/>Data Catalog & DLP]
  end

  A1 -->|HTTPS + JWT| APIM
  A2 -->|HTTPS + Client Creds| APIM
  APIM -->|Policies P1–P4 + x-correlation-id| B1
  APIM -->|Proxy to Workday + x-correlation-id| B2
  APIM -->|Proxy/Transform + x-correlation-id| B3

  %% Logging edges
  APIM -. request/response, client_id, UPN, IP, x-correlation-id .-> L1
  B1 -. traces w/ x-correlation-id .-> L2
  B2 -. vendor logs w/ ext corr id .-> L4
  B3 -. service logs .-> L2
  L2 --> L1
  L3 --> S1
  L1 --> S1
  L4 --> S1
  D1 --- S1

  %% Outcomes
  S1 -->|Hunting Query: join by x-correlation-id| R1[End-to-End Reconstruction]
  S1 -->|Alerting on PII endpoints / anomaly| R2[SecOps Triage & IR]
