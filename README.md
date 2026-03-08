# CALM Test Area

This repository contains a collection of [FINOS CALM (Common Architecture Language Model)](https://calm.finos.org/) examples demonstrating architecture patterns, concrete implementations, and compliance controls.

## Overview

CALM is an open standard from FINOS for describing software system architectures in a machine-readable format using JSON Schema. This test area serves as a sandbox for creating, testing, and refining CALM architecture definitions.

## Repository Structure

### Architecture Files (`.architecture.json`)
Concrete architecture implementations representing specific system designs:
- **Web Applications**: `web-app.architecture.json`, `simple.architecture.json`
- **Database Variants**: `my-app-mysql.architecture.json`, `my-app-mongodb.architecture.json`
- **Conference Systems**: `conference2.architecture.json`, `mysql-conference.architecture.json`, `postgres-conference.architecture.json`
- **Messaging Systems**: `kafka.architecture.json`, `rabbitmq.architecture.json`, `rabbitmq-broker.architecture.json`, `sqs.architecture.json`

### Pattern Files (`.pattern.json`)
Reusable architecture patterns with JSON Schema validation:
- **`conference.pattern.json`**: Conference signup system with Kubernetes
- **`conference2.pattern.json`**: Alternative conference architecture
- **`app-component.pattern.json`**: Conference system with message broker integration
- **`message-broker.pattern.json`**: Service with message broker (RabbitMQ/Kafka)
- **`messaging.pattern.json`**: Messaging architecture pattern
- **`my-app.pattern.json`**: Generic application pattern
- **`web-app.pattern.json`**: Web application pattern

### Control Files (`.control.json`)
Security and compliance control definitions:
- **`encryption-in-transit.control.json`**: Data encryption requirements (NIST-800-53 SC-8)

### Documentation (`docs/`)
Generated Docusaurus documentation sites that visualize the architectures as static websites. These include node descriptions, relationship diagrams, and architecture decision records for various system configurations.

### Chat Logs (`chat_logs/`)
GitHub Copilot chat logs documenting pattern creation, refinement, and summarization sessions showing the iterative design process.

### Architecture Pattern Standard (`architecture-pattern-sandard/`)
Experimental and test files:
- `company.standard.json`: Company-specific standards
- `my-app.architecture.json`: Test architecture
- `my-pattern.pattern.json`: Test pattern

## Key Concepts

### Nodes
Components in the architecture (services, databases, web clients, clusters)

### Relationships
Connections between nodes with protocols and relationship types:
- **connects**: Direct connections between components
- **deployed-in**: Deployment relationships
- **options**: Alternative architecture choices

### Interfaces
Communication endpoints with hosts, ports, and URLs

### Patterns
Reusable architecture templates with validation rules using JSON Schema constraints

## Example Architectures

### Three-Tier Web Application
- Web Frontend (webclient)
- API Service (service)
- Database (MySQL/PostgreSQL/MongoDB options)

### Message-Driven Architecture
- Frontend
- Backend Service
- Message Broker (RabbitMQ/Kafka/SQS)
- Database
- Kubernetes Cluster

### Simple Service-Database
- Application Service
- Database (with choice of MySQL or other options)

## Usage

These CALM files can be:
1. **Validated** against the CALM JSON Schema
2. **Visualized** in architecture diagrams
3. **Generated** into documentation (Docusaurus)
4. **Used as Templates** for new architecture designs
5. **Analyzed** for compliance and patterns

## CALM Specification

This repository follows the CALM 1.2 specification:
- Schema: `https://calm.finos.org/release/1.2/meta/calm.json`
- Core definitions: `https://calm.finos.org/release/1.2/meta/core.json`
- Interface definitions: `https://calm.finos.org/release/1.2/meta/interface.json`
- Control definitions: `https://calm.finos.org/release/1.2/meta/control.json`

## File Naming Conventions

- `.architecture.json`: Concrete architecture instances
- `.pattern.json`: Reusable architecture patterns
- `.control.json`: Security/compliance controls
- `.standard.json`: Organizational standards

## Related Resources

- [FINOS CALM Specification](https://calm.finos.org/)
- [CALM on GitHub](https://github.com/finos/calm)
- [Docusaurus Documentation](https://docusaurus.io/)
