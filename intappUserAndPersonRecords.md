flowchart TD

    %% Entities
    A[Person Record]:::person
    B[User Record]:::user

    %% Person Record details
    A -->|Represents| A1[Former or Current Employee]
    A -->|Stores| A2[Name, Title, Office, Department, Practice Area]
    A -->|Referenced in| A3[Clients (Lead), Engagements (Project Delivery Lead)]

    %% User Record details
    B -->|Represents| B1[Employee who can log into Intapp]
    B -->|Stores| B2[UserID, Name, Email Address]
    B -->|References| A

    %% Linking
    B -. personId .-> A
    note over A,B: Every User must have an associated Person<br/>If User created w/o Person → Intapp auto-creates Person

    %% Use case: New Worker
    C[Workday: New Hire]:::event --> D[Integration Creates Person Record<br/>Key = EmployeeID (PersonId)]
    D --> E[Integration Creates User Record<br/>Key = PrimaryWorkEmail (UserId)]
    E --> B
    D --> A

    %% Use case: Email Update
    F[Workday: Email Update]:::event --> G[Person Record: Email Updated]
    F --> H[User Record: Email + UserID Updated]
    H -. API getBy parameter required .- B

    %% Error case
    H -.-> I[If getBy not specified:<br/>API tries to create NEW user<br/>with updated email → ERROR:<br/>Duplicate PersonId]

    %% Styles
    classDef person fill:#fef3c7,stroke:#92400e,stroke-width:2px,color:#111;
    classDef user fill:#dbeafe,stroke:#1e3a8a,stroke-width:2px,color:#111;
    classDef event fill:#ecfdf5,stroke:#065f46,stroke-width:2px,color:#111;
