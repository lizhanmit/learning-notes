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

---

Many-to-Many Relationship: 

Relational databases do not support many-to-many relationships without junction tables. 

Junction tables: 

- Represents the relationship itself as a table. 
- Contains foreign keys to both related tables. 
- Turns many-to-many into two one-to-many relationships. 

Example:

![junction-table-example.png](./img/junction-table-example.png)

---

Supertype and Subtype Relationships 

Option 1: Separate subtype tables

- Separates shared policy data from type-specific details.
- Shared data is stored once.
- Each policy type has a clear structure.
- Type-specific rules are easier to enforce.
- Introduces complexity with more tables.
- **When to choose:** Subtypes are often separated when they differ significantly, data quality and rules matter, and the system will evolve over time. 

Option 2: Single table

- Store all policies in a single table.
- Simple – queries only need to reference a single table, no joins.
- Low scalability - many columns are null for most rows.
- Business rules are harder to enforce.
- Table becomes cluttered.
- **When to choose:** Using a single table works best when types are very similar, type-specific attributes are minimal, and reporting simplicity is a priority. 

---

Data dictionary columns: 

- Table name
- Column name
- Data type
- Required (Yes/No)
- Description 
- Allowed values 
- Example values 