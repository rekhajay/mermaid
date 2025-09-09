# Intapp User and Person Records — Lifecycle and Upsert Logic

```mermaid
flowchart TD

    %% =========================
    %% Person Record Domain
    %% =========================
    subgraph PERSON [Person Record Domain]
      P[Person Record]
      P -->|Purpose| P_PUR[Represents staff former or current employee]
      P -->|PK| P_PK[PersonID equals EmployeeID from iBridge mapping]
      P -->|Store| P_FIELDS[Name Title Office Department Practice Area]
      P -->|Reference in| P_REF1[Clients lead]
      P -->|Reference in| P_REF2[Engagements project delivery lead]
      P -->|Store| P_EMAIL[Email keep in sync]
    end

    %% =========================
    %% User Record Domain
    %% =========================
    subgraph USER [User Record Domain]
      U[User Record]
      U -->|Purpose| U_PUR[Login identity in Intapp]
      U -->|PK| U_PK[UserID primary key set equal Email for SSO]
      U -->|Store| U_FIELDS[UserID Email Name]
      U -->|Link field| U_LF[personId points to Person]
    end

    %% Minimal cross-link for clarity
    U -- Link via personId --> P

    %% =========================
    %% Lifecycle and Upsert
    %% =========================
    subgraph LIFECYCLE [Lifecycle and Upsert]
      WD[Workday]
      INT[Integration Boomi]

      %% New hire
      WD -- New hire --> INT
      INT -- Create and Store --> P
      INT -- Create and Store --> U
      INT -- Set --> L1[Set personId on User]
      L1 --> U

      %% Email update and upsert behavior
      WD -- Email change --> INT
      INT -- Update and Store --> P_EMAIL2[Update Person email]
      INT -- PATCH getBy --> PATCH1[Intapp Users PATCH]
      PATCH1 -- Use external id --> GBY[getBy external.WorkdayID with EmployeeID]

      %% Outcomes
      GBY -- Match found --> U_UPD[Update User set Email and set UserID equals Email]
      GBY -- No match --> U_NEW[Create new User record]
    end

    %% Notes and guardrails
    NOTE1[Maintain Email on both Person and User records]
    NOTE2[If getBy missing or wrong API may try to create new User and fail due to duplicate personId]

    P --> NOTE1
    U --> NOTE1
    PATCH1 --> NOTE2
