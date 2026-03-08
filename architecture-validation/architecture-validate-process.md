# The Three Files and How They Connect

The relationship is:

```
Architecture  → validated against → Pattern
Pattern       → references        → Standard
Standard      → extends           → CALM schema
```


## 1. Architecture file (your system)

This is the file you write describing the system.

Example:

```json
{
  "$schema": "https://calm.finos.org/release/1.2/meta/calm.json",
  "name": "Order System",
  "nodes": [
    {
      "unique-id": "order-api",
      "name": "Order API",
      "node-type": "service",
      "metadata": {
        "costCenter": "CC-2001",
        "owner": "Order Services Team",
        "environment": "production"
      }
    }
  ]
}
```

Notice:

There is **no reference to a Standard here**.

The architecture just contains the properties that the Standard requires.

---

# 2. Standard definition

The **Standard defines rules** that nodes must follow.

Example:

```json
{
  "$id": "company-node-standard.json",
  "allOf": [
    {
      "$ref": "https://calm.finos.org/release/1.2/meta/core.json#/defs/node"
    },
    {
      "type": "object",
      "properties": {
        "metadata": {
          "type": "object",
          "properties": {
            "costCenter": {
              "type": "string",
              "pattern": "^CC-[0-9]{4}$"
            },
            "owner": {
              "type": "string"
            },
            "environment": {
              "enum": ["development", "staging", "production"]
            }
          },
          "required": ["costCenter", "owner", "environment"]
        }
      }
    }
  ]
}
```

This says:

A valid node must be

```
CALM node
AND
contain metadata fields required by the organization
```

---

# 3. Pattern (this is what connects them)

The **Pattern applies the Standard to the architecture**.

Example:

```json
{
  "$id": "company-pattern.json",
  "type": "object",
  "properties": {
    "nodes": {
      "type": "array",
      "items": {
        "$ref": "#/$defs/extended-node"
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
          "$ref": "company-node-standard.json"
        }
      ]
    }
  }
}
```

This says:

Every node must satisfy:

```
CALM node
AND
company-node-standard
```

---

# Validation Step (Where Everything Connects)

You run:

```bash
calm validate \
  --architecture architecture.json \
  --pattern company-pattern.json
```

During validation:

```
architecture.json
      ↓
pattern applies standard
      ↓
validator checks rules
```

If the architecture violates the Standard:

Example architecture:

```json
{
  "unique-id": "order-api",
  "name": "Order API",
  "node-type": "service"
}
```

Validation fails because:

```
metadata.owner missing
metadata.costCenter missing
metadata.environment missing
```

---

# Visual Model

```
                 +------------------+
                 |   Standard       |
                 |  org rules       |
                 +--------+---------+
                          |
                          |
                referenced by
                          |
                 +--------v---------+
                 |    Pattern       |
                 | validation rules |
                 +--------+---------+
                          |
                          |
                validates
                          |
                 +--------v---------+
                 |  Architecture    |
                 | system model     |
                 +------------------+
```

---

# Why CALM Designed It This Way

This separation allows:

**Standards**

* reusable governance rules

**Patterns**

* apply standards to architectures

**Architectures**

* remain clean system descriptions

This makes it possible to apply **different governance policies to the same architecture**.

Example:

```
dev-pattern.json
security-pattern.json
finops-pattern.json
```

Each referencing different Standards.

---

# Simple Mental Model

Think of it like this:

```
Architecture = the building
Standard     = building code
Pattern      = inspection checklist
```

You don't put the building code inside the building blueprint.

Instead, the **inspector checks the blueprint against the building code**.
