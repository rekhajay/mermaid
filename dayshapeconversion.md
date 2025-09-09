# TAX Preparer Projects – Flow Diagram (Final with A/B/C Numbering + Skinny Roll Note)

```mermaid
graph TD
    %% 2025 . Project Start Date, Renewal Year = 2024 Branch
    A["A1. 2025 Project Start Date , Renewal Year = 2024 "]:::start --> A1_role["Manual Workday Team"]:::role
    A1_role --> A2["A2. Extract Forecast Data from Warehouse Whole Year"]
    A2 --> A3["A3. Export Forecasts into Dayshape 7/1/2025 and Forward (Template Provided)"]

    %% 2026 (Project Start Date), Renewal Year = 2025 Branch
    B["B1. 2026 Project Start Date , Renewal Year = 2025 <br/>Full Roll Strategy:<br/>&bull; Only the projects rolled by 11/25 TAX, 12/15 for A&amp;A and RaaS<br/><br/>Skinny Roll Strategy:<br/>&bull; Any 2026 projects created after the production cutover for DS — Demand happens in DS"]:::start --> B2["B2. Strategy 1: Full Roll 2025 Projects With Forecast and RPL"]
    B --> B3["B3. Strategy 2: Skinny Roll 2025 Projects Minus Forecast and RPL"]

    %% 2026 Extracts
    B2 --> B2a_role["Manual Workday Team"]:::role
    B2a_role --> B4["B4. Extract Forecast Data from Warehouse (Date range and Actuals vs Plan) TBD based on what Tax wants (Full Roll Path)"]

    B3 --> B3a_role["Manual Workday Team"]:::role
    B3a_role --> B5["B5. Extract Forecast Data from Warehouse (Date range and Actuals vs Plan) TBD based on what Tax wants<br/><font color='red'>Not clear what the User finally sees for these Projects in Dayshape in terms of Demand after the Conversion</font><br/>
<br/><font color='red'> Expectation - Show forecast based on prior year actuals (at least till Jan - October)</font><br/>

(Skinny Roll Path)"]

    %% Export to DS (2026)
    B4 --> B6["B6. Export Forecasts into Dayshape Jan–Dec 2026 (Template Provided)<br/>Final Date Span decided based on what Tax wants"]
    B5 --> B6

    %% Common Flow
    A3 --> C1["C1. Steps to Create Demand in Dayshape from Exported Workday Project Forecasts"]
    B6 --> C1

    C1 -->|Integration DS Packaged| C2["C2. Create 2025 Projects in Dayshape (Batch Processing)"]
    C1 -->|Integration DS Packaged| C3["C3. Create 2026 Projects in Dayshape (Batch Processing)"]

    %% Manual DS Team
    C2 --> E1_role["Manual DS Team"]:::role
    E1_role --> C4["C4. Import Forecasts Into Dayshape 7/1/2025 and Forward"]

    C3 --> E2_role["Manual DS Team"]:::role
    E2_role --> C5["C5. Import Forecasts Into Dayshape Jan–Dec 2026"]

    %% Writeback
    C4 -->|Integration DS Packaged| C6["C6. Upsert RPL Lines Back to Workday (2025 7/1 and Forward)"]
    C6 --> C7["C7. Delete and Replace Forecast in Workday With DS Forecast (Cutover) – ONLY 7/1/2025 and Forward Forecasts Will Exist"]

    C5 -->|Integration DS Packaged| C8["C8. Upsert RPL Lines Back to Workday (2026 Jan–Dec)"]
    C8 --> C9["C9. Delete and Replace Forecast in Workday With DS Forecast (Cutover) – Full Year Forecasts Will Exist"]

    %% Styles
    classDef start stroke:#008000,stroke-width:4px;
    classDef role fill:#f2f2f2,stroke:#555,stroke-dasharray: 3 3;
