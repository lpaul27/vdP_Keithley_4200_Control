``` mermaid 
flowchart TB
A(Run ALD Process)
B[Deposit Precursor A]
C[Nitrogen Purge]
D[Van der Pauw measurement]
E[Deposit Precursor B]
F[Nitrogen Purge]
G[Van der Pauw Measurement]

A --> B
B --> C
C --> D
D--> E
E--> F
F --> G
G --> B



