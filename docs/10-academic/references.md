# WealthWise — References

**Document Version:** 1.0  
**Status:** Academic Definition  
**Project Type:** Major Project  
**Product:** WealthWise

---

# 1. Purpose

This document contains the technical, academic, and external references used during the design and development of WealthWise.

References are organized according to their role in the project.

---

# 2. Technology References

## 2.1 React

Used as the primary frontend library for building the WealthWise user interface.

- Official Documentation: https://react.dev/

---

## 2.2 Node.js

Used as the server-side JavaScript runtime for the backend.

- Official Documentation: https://nodejs.org/docs/

---

## 2.3 Express.js

Used for building the REST API and backend application layer.

- Official Documentation: https://expressjs.com/

---

## 2.4 MongoDB

Used as the primary database for persistent application data.

- Official Documentation: https://www.mongodb.com/docs/

---

## 2.5 Mongoose

Used for MongoDB data modelling, schema definition, and validation.

- Official Documentation: https://mongoosejs.com/docs/

---

## 2.6 Git

Used for source-code version control.

- Official Documentation: https://git-scm.com/doc

---

## 2.7 GitHub

Used for source-code hosting, version history, and project collaboration.

- Official Documentation: https://docs.github.com/

---

# 3. Artificial Intelligence References

## 3.1 Generative AI / Large Language Models

Generative AI concepts are used in WealthWise for:

- natural-language financial explanations,
- conversational interaction,
- personalized recommendations,
- scenario interpretation.

The AI layer is designed to operate on validated financial context rather than acting as the source of numerical financial truth.

---

## 3.2 AI-Assisted Decision Support

The WealthWise architecture follows the principle that deterministic application logic should establish financial facts before Generative AI is used for interpretation.

Conceptually:

```text
Financial Data
      ↓
Deterministic Calculations
      ↓
Validated Context
      ↓
Generative AI
      ↓
Explanation / Recommendation

4. Software Engineering References

The following software-engineering concepts inform the design of WealthWise:

modular architecture,
separation of concerns,
RESTful API design,
layered application architecture,
authentication and authorization,
software testing,
version control,
incremental development.

These concepts are reflected in the project's architecture, development methodology, and testing strategy.

5. Financial Concepts

The financial analytics implemented by WealthWise are based on common personal-finance concepts including:

income,
expenses,
savings,
savings rate,
budgeting,
recurring expenses,
financial goals,
discretionary spending,
cash-flow analysis.

The application uses these concepts for informational and decision-support purposes.

6. Project Documentation References

The following internal documents define the WealthWise system:

docs/
├── 01-product/
├── 02-requirements/
├── 03-features/
├── 04-design/
├── 05-architecture/
├── 06-database/
├── 07-api/
├── 08-ui-ux/
├── 09-testing/
└── 10-academic/

Important internal references include:

Product Bible
Requirements Specification
Feature Specification
System Design
System Architecture
Database Design
API Documentation
UI/UX Documentation
Testing Strategy
Test Plan
Project Overview
Objectives and Scope
Development Methodology
Technology Stack
Limitations and Future Scope
7. Reference Management Principle

References should be added to this document when they are actually used during the development of WealthWise.

The project should avoid including:

unrelated sources,
unused technologies,
fabricated research papers,
unverifiable citations,
sources that did not contribute to the project.
8. Future References

As development progresses, this section may be expanded to include:

AI model documentation,
API documentation,
security standards,
academic research papers,
datasets,
financial-analysis references,
libraries and frameworks,
deployment documentation.

Each reference should be added when it becomes relevant to an implemented or documented component.

9. Reference Format

Where appropriate, references should contain:

Author / Organization
Title
Platform / Publication
Year
URL
Access Date

For software documentation, the official documentation of the technology should be preferred.

10. Conclusion

This document serves as the central reference list for WealthWise.

It should evolve throughout the project so that the final academic report accurately reflects the technologies, concepts, research, and external resources actually used during development.