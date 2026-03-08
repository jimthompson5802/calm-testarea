# Architecture Validation

This folder demonstrates how to create and enforce organization-specific standards on top of the FINOS CALM specification.

## Contents

### `company.standard.json`
A company-specific JSON Schema standard that extends the base CALM node definition with required organizational metadata:
- **costCenter**: Company cost center code (format: CC-####)
- **owner**: Owning team or person
- **environment**: Deployment environment (development, staging, production)

This standard can be referenced and enforced across all CALM architectures in the organization.

### `my-pattern.pattern.json`
A reusable pattern that enforces the company standard on all nodes within an architecture. This pattern uses JSON Schema's `$ref` mechanism to:
- Reference the CALM core node definition
- Reference the company.standard.json to add organizational requirements
- Ensure all nodes in architectures using this pattern comply with company standards

### `my-app.architecture.json`
An example "Simple Order System" architecture demonstrating compliance with the company standard. This three-tier architecture includes:
- **Web Frontend**: Browser-based UI (Digital Experience Team, CC-1001)
- **Order API**: Backend service for order processing (Order Services Team, CC-2001)
- **Order Database**: Persistent data storage (Data Platform Team, CC-3001)

Each node includes the required metadata fields (costCenter, owner, environment) as specified by the company standard.

## Purpose

This directory demonstrates:
1. **Standard Definition**: How to create organizational standards extending CALM
2. **Pattern Enforcement**: How to embed standards into reusable patterns
3. **Compliant Implementation**: How to create architectures that satisfy organizational requirements

Organizations can use this approach to enforce governance, compliance, and operational requirements across all architecture definitions.
