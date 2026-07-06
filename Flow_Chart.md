``` mermaid 
flowchart TB
A[Run ALD] --> B(Precursor deposition)
B --> C(Nitrogen Purge)
C --> F[Run Resistivity test]
F --> G[Run vdP]
G --> H(Log values)
H --> I(Use Newton's method to solve vdP equation)
I --> J{log Resistivity}
J --> B