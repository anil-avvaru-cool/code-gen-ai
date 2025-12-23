Understanding **entity relationships in HCL Domino** requires a different mental model than traditional relational databases. Below is a **practical, enterprise-oriented framework** you can use to systematically discover and document relationships in a Domino application.

---

## 1. Shift the Data Model Mindset (Critical)

Domino is **document-oriented**, not relational.

* **Entity** = NSF document type (Form-based)
* **Relationship** = reference via:

  * UNID
  * Document links
  * Key fields (lookup values)
  * Hierarchical (response / response-to-response)

There are **no enforced foreign keys**.

---

## 2. Identify Core Entities (Forms)

Start with **Forms**, not tables.

### How

* Open NSF in **Domino Designer**
* Review **Forms** and **Subforms**
* Each Form ≈ an entity type

### What to Capture

* Form name
* Purpose
* Key fields (IDs, reference fields)
* Whether it allows responses

Example:

* `Issue`
* `Comment`
* `UserProfile`
* `Approval`

---

## 3. Identify Relationships (Five Common Patterns)

### 1. Parent–Child (Hierarchical)

* Implemented using **Responses**
* Stored via `$REF` and `$Parents` internally

Example:

* Issue → Comments

Indicators:

* “Response” or “Response to response” form type
* Views with “Show response hierarchy”

---

### 2. UNID-Based Reference

* Document stores another document’s **UNID**
* Most common “foreign key” pattern

Example:

* `Issue.AssignedToUNID → UserProfile.UNID`

How to detect:

* Fields named `xxxUNID`
* Code using `GetDocumentByUNID`

---

### 3. Key-Based Reference (Lookup)

* Document stores a business key
* Often uses `@DbLookup` or `@DbColumn`

Example:

* `Issue.ProjectCode → Project.Code`

Risk:

* No referential integrity
* Susceptible to data drift

---

### 4. Document Links (DocLinks)

* Rich-text fields containing document links
* Semi-structured, user-driven relationships

Example:

* “Related Issues” field

Detection:

* Rich-text fields
* NotesRichTextItem with embedded links

---

### 5. View-Driven (Computed Relationships)

* Relationships inferred through **Views**
* Not persisted in documents

Example:

* View joining Issues and Approvals by shared key

Important:

* Logical relationship only
* Exists in UI, not storage

---

## 4. Analyze Views to Understand Relationships

Views are **the best documentation of relationships** in Domino.

### Look For

* Selection formulas (`SELECT Form = "Issue"`)
* Categorization (grouping keys)
* Column formulas referencing other docs
* `@DbLookup` inside column formulas

Views often represent:

* One-to-many relationships
* Aggregations
* Status-driven relationships

---

## 5. Inspect Code for Hidden Relationships

### LotusScript

* `GetDocumentByUNID`
* `GetDocumentByKey`
* `@DbLookup`, `@DbColumn`
* `NotesView.GetDocumentByKey`

### Java / XPages

* NSF data access via document keys
* REST exposure patterns

### Agents

* Batch processing logic often reveals dependencies

---

## 6. Build an ER Model (Domino-Style)

Traditional ER diagrams do not map 1:1. Use:

### Entity Representation

* Entity = Form
* Attributes = Fields
* PK = UNID (implicit)

### Relationship Representation

* Labeled arrows with:

  * Reference type (UNID, Key, Hierarchy)
  * Cardinality (1–N, N–N)
  * Enforced? (No)

Example:

```
Issue
 ├─(UNID)→ UserProfile
 ├─(Key) → Project
 └─(Response)→ Comment
```

---

## 7. Tool-Assisted Discovery (Recommended)

### Native

* Domino Designer
* Application Analyzer (limited)

### Programmatic

* Parse design XML (`nsexport`)
* Tree-sitter LotusScript parsing
* Build graph (Neo4j) from:

  * Forms
  * Fields
  * Lookups
  * Code references

(This aligns with your previous reverse-engineering and dependency-graph work.)

---

## 8. Validation Through Data Sampling

Always validate assumptions:

* Pick sample documents
* Inspect raw items
* Confirm keys, UNIDs, hierarchy

Domino apps often contain **historical inconsistencies**.

---

## 9. Common Pitfalls

* Assuming relational integrity exists
* Ignoring Views as relationship definitions
* Missing relationships embedded in code
* Overlooking response hierarchies
* Treating rich-text DocLinks as structured data

---

## Executive Summary

* Domino entities are **Forms**
* Relationships are **implicit and application-defined**
* Views and code define the real data model
* ER modeling is **interpretive, not enforced**
* Graph models work better than relational ERDs

---
