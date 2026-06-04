# HappyFeet School ERP Domain Knowledge

## Overview

This repository contains a structured domain intelligence system for designing and implementing a School ERP tailored to a privately owned preschool environment (Happyfeet - Hyderabad, India).

The work focuses on understanding the domain deeply to system design decisions.

The output is organized as a layered set of artifacts representing business structure, operational workflows, organizational behavior, and domain constraints.

---

## Scope of the System

The ERP is designed for:

* Preschool (18 months – 6 years)
* Single-owner institution (current)
* One active branch + planned expansion
* Mixed stakeholder environment (technical + non-technical users)

Core operational areas include:

* Admissions and enrollment lifecycle
* Staff and role management
* Studnets lifecycle and records management 
* Fee collection and reconciliation
* Attendance and child tracking
* Parent communication systems
* Safety and welfare escalation handling
* Daily classroom activity logging
* Compliance and reporting

---

## Repository Structure

### Core Artifacts

#### ✔ DOMAIN_OVERVIEW

Defines foundational understanding of the preschool domain, including:

* Stakeholder model
* Age-specific constraints (safety, supervision, pickup authorization)
* Core operational characteristics
* System boundaries and assumptions

---

#### ✔ BUSINESS_CAPABILITY_MAP

Maps the system into structured business capabilities:

* Core capabilities (admissions, fees, attendance, etc.)
* Supporting capabilities (communication, reporting, safety)
* Administrative capabilities (staff, configuration)
* Compliance and regulatory touchpoints

---

#### ✔ ORGANIZATION_MODEL

Defines operational structure and role-based responsibilities:

* Admin / Owner
* Branch Admin
* Teachers
* Coordinators (front desk)
* Accountant
* Parents/Guardians

Includes:

* Role boundaries
* Decision rights
* Escalation paths
* Operational constraints between roles

---

#### ✔ BUSINESS_PROCESSES

Detailed process definitions across operational domains:

* Admissions workflow
* Daily classroom operations
* Fee lifecycle handling
* Attendance tracking flows
* Safety and welfare escalation handling
* Communication workflows
* Migration / go-live processes
* Reporting and audit flows

Focus: end-to-end operational behavior of the institution.

---

## Work In Progress

#### ⏳ BUSINESS_RULES (Pending)

Will define:

* Formal business constraints
* Validation logic for workflows
* Edge case handling rules
* Operational invariants (non-negotiable rules)
* Exception conditions and enforcement logic

---

#### ⏳ DOMAIN_MODEL (Pending)

Will define:

* Core entities (Child, Parent, Staff, Enrollment, Fee, Attendance, etc.)
* Relationships between entities
* Lifecycle states
* Data structure blueprint for implementation layer

---

## Design Philosophy

This repository follows a **domain-first engineering approach**:

1. Understanding and documenting the real-world system before software design
2. Separate:

   * business behavior
   * organizational structure
   * process flows
   * data models
3. Avoid premature technical implementation decisions
4. Treat safety, compliance, and role boundaries as first-class constraints

---

## Key Design Constraints Identified

* Child safety is a non-negotiable system constraint (age group 18 months–6 years)
* Role-based access is strictly enforced (especially financial and welfare data)
* Some workflows are intentionally irreversible (e.g., fee acknowledgments, migration phases)
* Communication and safety events may bypass user preferences in critical cases
* System is designed for controlled expansion (not open SaaS multi-tenancy)

---

## Current Status

| Artifact                | Status   |
| ----------------------- | -------- |
| DOMAIN_OVERVIEW         | Complete |
| BUSINESS_CAPABILITY_MAP | Complete |
| ORGANIZATION_MODEL      | Complete |
| BUSINESS_PROCESSES      | Complete |
| BUSINESS_RULES          | Pending  |
| DOMAIN_MODEL            | Pending  |

---

## Purpose of This Repository

This is not an technical implementation repository.

It is a **pre-implementation domain intelligence** that ensures:

* fewer incorrect assumptions during development
* clearer stakeholder alignment
* reduced rework during ERP build phase
* stronger mapping between real-world operations and system behavior

---

## Notes

All artifacts are iterative and may evolve as stakeholder feedback is integrated.
