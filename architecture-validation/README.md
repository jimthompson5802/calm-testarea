# Architecture Validation

This folder demonstrates how to create and enforce organization-specific standards on top of the FINOS CALM specification.

Read [this](./architecture-validate-process.md) for more details on the validation process.

## Contents

### `the-company.standard.json`
A company-specific JSON Schema standard that extends the base CALM node definition with required organizational metadata:
- **costCenter**: Company cost center code (format: CC-####)
- **owner**: Owning team or person
- **environment**: Deployment environment (development, staging, production)

This standard can be referenced and enforced across all CALM architectures in the organization.

### `the-company.pattern.json`
A reusable pattern that enforces the company standard on all nodes within an architecture. This pattern uses JSON Schema's `$ref` mechanism to:
- Reference the CALM core node definition
- Reference the-company.standard.json to add organizational requirements
- Ensure all nodes in architectures using this pattern comply with company standards

### `my-app-standard-compliant.architecture.json`
An example "Simple Order System" architecture demonstrating compliance with the company standard. This three-tier architecture includes:
- **Web Frontend**: Browser-based UI (Digital Experience Team, CC-1001)
- **Order API**: Backend service for order processing (Order Services Team, CC-2001)
- **Order Database**: Persistent data storage (Data Platform Team, CC-3001)

Each node includes the required metadata fields (costCenter, owner, environment) as specified by the company standard.

### `my-app-non-standard-compliant.architecture.json`
An example architecture that intentionally violates company standards to demonstrate validation failures:
- **web-frontend**: Missing required `owner` field
- **order-api**: Invalid `costCenter` format ("COST-2001" instead of "CC-####")
- **order-db**: Invalid `environment` value ("prod" instead of enum values)

## Purpose

This directory demonstrates:
1. **Standard Definition**: How to create organizational standards extending CALM
2. **Pattern Enforcement**: How to embed standards into reusable patterns
3. **Compliant Implementation**: How to create architectures that satisfy organizational requirements

Organizations can use this approach to enforce governance, compliance, and operational requirements across all architecture definitions.

## Example Runs

### 1. Both architecture and pattern files are local
```bash
calm validate --architecture my-app-standard-compliant.architecture.json --pattern the-company.pattern.json -f pretty
```

```text
(node:23144) [DEP0040] DeprecationWarning: The `punycode` module is deprecated. Please use a userland alternative instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
info [calm-validate]:     Formatting output as pretty
Summary
- Errors: no (0)
- Warnings: no (0)
- Info/Hints: 0

No issues found.
```

### 2. Architecture file is local, pattern file is in GitHub
```bash
calm validate --architecture my-app-standard-compliant.architecture.json \
  --pattern https://raw.githubusercontent.com/jimthompson5802/calm-testarea/refs/heads/main/architecture-validation/the-company.pattern.json \
  -f pretty
```

```text
(node:23822) [DEP0040] DeprecationWarning: The `punycode` module is deprecated. Please use a userland alternative instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
info [file-system-document-loader]:     my-app-standard-compliant.architecture.json exists, loading as file...
info [calm-validate]:     Formatting output as pretty
Summary
- Errors: no (0)
- Warnings: no (0)
- Info/Hints: 0

No issues found.
```

### 3. Both architecture and pattern files are in GitHub
```bash
calm validate --architecture https://raw.githubusercontent.com/jimthompson5802/calm-testarea/refs/heads/main/architecture-validation/my-app-standard-compliant.architecture.json \
  --pattern https://raw.githubusercontent.com/jimthompson5802/calm-testarea/refs/heads/main/architecture-validation/the-company.pattern.json \
  -f pretty
```

```text
(node:24995) [DEP0040] DeprecationWarning: The `punycode` module is deprecated. Please use a userland alternative instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
info [calm-validate]:     Formatting output as pretty
Summary
- Errors: no (0)
- Warnings: no (0)
- Info/Hints: 0

No issues found.
```

### 4. Validate using url-mapping

Pattern file referencing a remote host that is not currently accessible
```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://example.com/patterns/company-base-pattern.json",
  "title": "Company Base Pattern",
  "description": "Pattern that enforces the Company Node Standard on all nodes.",
  "type": "object",
  "properties": {
    "nodes": {
      "type": "array",
      "items": {
        "$ref": "#/$defs/extended-node"
      }
    },
    "relationships": {
      "type": "array",
      "items": {
        "$ref": "https://calm.finos.org/release/1.2/meta/core.json#/defs/relationship"
      }
    }
  },
  "$defs": {
    "extended-node": {
      "allOf": [
        {
          "$ref": "https://calm.finos.org/release/1.2/meta/core.json#/defs/node"
        },
        {
          "$ref": "https://example.com/standards/the-company.standard.json"
        }
      ]
    }
  }
}
```


Url mapping file `url-mapping.json`

```json
{
    "https://example.com/standards/the-company.standard.json":"the-company.standard.json"
}
```


```bash
calm validate --architecture my-app-standard-compliant.architecture.json --pattern the-company-url-mapping.pattern.json -f pretty -u url-mapping.json
```

```text
(node:51336) [DEP0040] DeprecationWarning: The `punycode` module is deprecated. Please use a userland alternative instead.
(Use `node --trace-deprecation ...` to show where the warning was created)
info [calm-validate]:     Formatting output as pretty
Summary
- Errors: no (0)
- Warnings: no (0)
- Info/Hints: 0

No issues found.
```




### 5. Validate non-compliant architecture (demonstrates validation failures)
```bash
calm validate --architecture my-app-non-standard-compliant.architecture.json --pattern the-company.pattern.json -f pretty
```

```
info [calm-validate]:     Formatting output as pretty
Summary
- Errors: yes (6)
- Warnings: no (0)
- Info/Hints: 0

ERROR issues:
- In my-app-non-standard-compliant.architecture.json (/Users/jim/Desktop/calm-demos/calm-testarea/architecture-validation/my-app-non-standard-compliant.architecture.json):
  ERROR json-schema: must have required property 'owner'
    path: /nodes/web-frontend/metadata
    at line 19, col 19 (/Users/jim/Desktop/calm-demos/calm-testarea/architecture-validation/my-app-non-standard-compliant.architecture.json)
    schema: #/allOf/1/properties/metadata/required
    19 |       "metadata": {
       |                   ^
  ERROR json-schema: must match pattern "^CC-[0-9]{4}$"
    path: /nodes/order-api/metadata/costCenter
    at line 38, col 23 (/Users/jim/Desktop/calm-demos/calm-testarea/architecture-validation/my-app-non-standard-compliant.architecture.json)
    schema: #/allOf/1/properties/metadata/properties/costCenter/pattern
    38 |         "costCenter": "COST-2001",
       |                       ^^^^^^^^^^^
  ERROR json-schema: must have required property 'metadata'
    path: /nodes/sell-api
    at line 43, col 5 (/Users/jim/Desktop/calm-demos/calm-testarea/architecture-validation/my-app-non-standard-compliant.architecture.json)
    schema: #/allOf/1/required
    43 |     {
       |     ^
  ERROR json-schema: must be equal to one of the allowed values (expected one of ["development","staging","production"])
    path: /nodes/order-db/metadata/environment
    at line 65, col 24 (/Users/jim/Desktop/calm-demos/calm-testarea/architecture-validation/my-app-non-standard-compliant.architecture.json)
    schema: #/allOf/1/properties/metadata/properties/environment/enum
    65 |         "environment": "prod"
       |                        ^^^^^^
  ERROR json-schema: must have required property 'backupPolicy'
    path: /nodes/order-db/metadata
    at line 62, col 19 (/Users/jim/Desktop/calm-demos/calm-testarea/architecture-validation/my-app-non-standard-compliant.architecture.json)
    schema: #/allOf/2/then/properties/metadata/required
    62 |       "metadata": {
       |                   ^
  ERROR json-schema: must match "then" schema
    path: /nodes/order-db
    at line 57, col 5 (/Users/jim/Desktop/calm-demos/calm-testarea/architecture-validation/my-app-non-standard-compliant.architecture.json)
    schema: #/allOf/2/if
    57 |     {
       |     ^^
```
