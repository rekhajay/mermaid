# TAX Preparer Projects – Flow Diagram (2026 Export Box Updated)

```mermaid
graph TD
    %% Start Nodes
    A["2025 Projects"]:::start --> A1_role["Manual Workday Team"]:::role
    A1_role --> A1["Extract Forecast Data from Warehouse Whole Year"]

    B["2026 Projects<br/>Full Roll Strategy:<br/>&bull; Only the projects rolled by 11/25 TAX, 12/15 for A&amp;A and RaaS<br/><br/>Skinny Roll Strategy:<br/>&bull; Any 2026 projects created after the production cutover for DS — Demand happens in DS"]:::start --> B1["Strategy 1: Skinny Roll 2025 Projects Minus Forecast and RPL"]
    B --> B2["Strategy 2: Full Roll 2025 Projects With Forecast and RPL"]

    %% Branch Details
    B1 --> B1a_role["Manual Workday Team"]:::role
    B1a_role --> B1a["Extract Forecast Data from Warehouse Whole Year (Skinny Roll Path)"]

    B2 --> B2a_role["Manual Workday Team"]:::role
    B2a_role --> B2a["Extract Forecast Data from Warehouse Whole Year (Full Roll Path)"]

    %% Exports
    A1 --> A2["Export Forecasts into Dayshape 7/1/2025 and Forward (Template Provided)"]
    B1a --> B3["Export Forecasts into Dayshape Jan–Dec 2026 (Template Provided)<br/>Final Date Span decided based on what Tax wants"]
    B2a --> B3

    %% Demand creation
    A2 --> C["Steps to Create Demand in Dayshape from Exported Workday Project Forecasts"]
    B3 --> C

    %% DS Integration
    C -->|Integration DS Packaged| D1["Create 2025 Projects in Dayshape (Batch Processing)"]
    C -->|Integration DS Packaged| D2["Create 2026 Projects in Dayshape (Batch Processing)"]

    %% Manual DS Team
    D1 --> E1_role["Manual DS Team"]:::role
    E1_role --> E1["Import Forecasts Into Dayshape 7/1/2025 and Forward"]

    D2 --> E2_role["Manual DS Team"]:::role
    E2_role --> E2["Import Forecasts Into Dayshape Jan–Dec 2026"]

    %% Writeback to Workday
    E1 -->|Integration DS Packaged| F1["Upsert RPL Lines Back to Workday (2025 7/1 and Forward)"]
    F1 --> G1["Delete and Replace Forecast in Workday With DS Forecast (Cutover) – For 2025 Projects, ONLY 7/1/2025 and Forward Forecasts Will Exist"]

    E2 -->|Integration DS Packaged| F2["Upsert RPL Lines Back to Workday (2026 Jan–Dec)"]
    F2 --> G2["Delete and Replace Forecast in Workday With DS Forecast (Cutover) – For 2026 Projects, Full Year Forecasts Will Exist"]

    %% Styles
    classDef start stroke:#008000,stroke-width:4px;
    classDef role fill:#f2f2f2,stroke:#555,stroke-dasharray: 3 3;
