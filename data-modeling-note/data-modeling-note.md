# Data Modeling Note

- [Data Modeling Note](#data-modeling-note)
  - [Conceptual Data Modeling](#conceptual-data-modeling)
  - [Logical Data Modeling](#logical-data-modeling)

---

Conceptual -> Logical -> Physical data modeling 

- Conceptual: "Big picture" view of business concepts 
- Logical: Technical detail without database specifics. 
- Physical: Implementation detail with database specifics.

## Conceptual Data Modeling

Conceptual data model is used to answer questions:  

- What are the key concepts in our business? 
- How to they relate to one another?

Conceptual Entity Relationship Diagram includes: 

- Entities (e.g., customer, policy, claim)
- Relationships with or without cardinality 

Cardinality defines how many of one thing can be associated with how many of another.

Crow's foot notation:

![crows-foot-notation.png](./img/crows-foot-notation.png)

Cardinality symbols: 

![cardinality-symbols.png](./img/cardinality-symbols.png)

---

## Logical Data Modeling

Logical data model is used to answer questions:  

- What data, which tables, how do tables link together? 

Logical Entity Relationship Diagram includes: 

- Entities (e.g., customer, policy, claim)
- Relationships with cardinality 
- Attributes
- Data types
- Primary keys
- Foreign keys 

Natural key: Real-world ID that already exists in the organization. 

Surrogate key: System-generated ID with no business meaning. 