# Myutopia - Intelligent Post-Diagnosis Follow-up System

## Overview

This project implements an intelligent post-diagnosis follow-up system for Myutopia using:

* Jira Cloud as the central CRM and source of truth.
* n8n as the automation and orchestration engine.
* OpenAI as the message interpretation engine.
* Gmail as the primary communication channel.
* WhatsApp as a complementary follow-up channel.

The objective is to automate the commercial follow-up process after a diagnostic session while maintaining traceability, operational control and human supervision for critical decisions.

---

# Solution Architecture

The system is built around four main components:

## Jira

Acts as the operational CRM.

Stores:

* Lead information
* Commercial status
* Follow-up history
* Follow-up deadlines
* AI-generated summaries
* Sentiment analysis
* Operational notes

Jira is considered the single source of truth.

---

## n8n

Acts as the orchestration layer.

Responsible for:

* Receiving messages
* Executing business rules
* Scheduling follow-ups
* Updating Jira
* Sending notifications
* Handling errors

---

## OpenAI

Acts as an interpretation engine.

Responsible for:

* Intent classification
* Sentiment detection
* Conversation summarization
* Follow-up date extraction

OpenAI does not make business decisions.

All actions are validated through deterministic business rules implemented in n8n.

---

## Communication Channels

### Gmail

Primary inbound and outbound channel.

Used for:

* Lead responses
* Automated follow-ups
* Administrative notifications

### WhatsApp

Secondary channel.

Used mainly for:

* Third follow-up attempt (Touch 3)
* Future bidirectional communication

---

# Workflow Architecture

The solution is divided into independent workflows to improve maintainability, fault isolation and scalability.

---

# Workflow 01 - Gmail Inbound Processing

File:

```text
myutopia_01_gmail_inbound_ia_jira.json
```

## Purpose

Processes incoming lead responses received through Gmail.

## Responsibilities

### Lead Identification

The workflow:

1. Receives a Gmail message.
2. Extracts sender information.
3. Searches the corresponding lead in Jira.

If no lead is found:

* An alert is generated.
* The message is not processed further.

---

### Duplicate Detection

Prevents processing the same message multiple times.

Validation is performed using:

* Gmail Message ID
* Gmail Thread ID

---

### AI Interpretation

The message is analyzed by OpenAI.

The model extracts:

* Intent
* Confidence
* Sentiment
* Summary
* Follow-up dates

---

### Business Rules Engine

n8n evaluates AI results.

Possible outcomes:

| Intent                     | Action                      |
| -------------------------- | --------------------------- |
| accepts_proposal           | Move to Proposal Accepted   |
| signed                     | Move to Signed              |
| asks_question              | Escalate to administrator   |
| price_objection            | Escalate to commercial team |
| requests_technical_meeting | Schedule technical meeting  |
| postpones                  | Move to Waiting             |
| rejects                    | Move to Discarded           |
| ambiguous                  | Manual review               |

---

### Jira Update

The workflow updates:

* Last contact date
* Sentiment
* Summary
* Next action
* Touch counter
* Follow-up deadline

---

### Status Transition

If required, the workflow performs Jira status transitions automatically.

---

### Notifications

Administrative alerts are generated for:

* Questions
* Objections
* Ambiguous messages
* Low-confidence AI results
* Unknown contacts

---

# Workflow 02 - Automated Follow-up Scheduler

File:

```text
myutopia_02_scheduler_toques.json
```

## Purpose

Executes automatic follow-up actions based on configured deadlines.

## Responsibilities

The workflow runs daily.

---

### Lead Evaluation

Searches Jira for leads:

* Pending follow-up
* Deadline reached

---

### Touch Sequence

The workflow manages:

#### Touch 1

First follow-up message.

Executed 48 hours after diagnosis.

---

#### Touch 2

Second follow-up message.

Executed 5 days after Touch 1.

---

#### Touch 3

Final follow-up.

Executed 10 days after Touch 2.

This touch is always sent through WhatsApp.

---

### Automatic Closure

If no response is received after Touch 3:

* The lead is moved to Discarded.
* The process is closed automatically.
* An audit record is generated.

---

### Jira Update

The workflow updates:

* Touch number
* Follow-up deadline
* Next action
* Operational history

---

# Workflow 03 - WhatsApp Integration

File:

```text
myutopia_03_whatsapp_inbound_simulado.json
```

## Purpose

Processes WhatsApp interactions using the same business logic as Gmail.

## Responsibilities

### Message Reception

Receives WhatsApp messages through:

* Webhook
* WhatsApp provider API
* Simulated endpoint (for technical assessment)

---

### Lead Identification

The lead is searched using:

```text
Phone Number
```

stored in Jira.

---

### AI Analysis

The same AI interpretation workflow is reused.

This guarantees consistent behavior across channels.

---

### Jira Synchronization

Updates:

* Status
* Notes
* Sentiment
* Follow-up information

---

# Workflow 04 - Error Management and Audit

Future workflow.

## Purpose

Provides centralized monitoring and error management.

## Responsibilities

### Error Classification

The workflow identifies:

* Network errors
* API errors
* Jira errors
* OpenAI errors
* Validation errors

---

### Recovery Actions

Possible actions include:

* Retry
* Escalation
* Logging
* Manual review

---

### Audit Trail

All incidents are recorded for traceability purposes.

---

# Jira Custom Fields

The solution relies on custom Jira fields.

| Field                       | Jira ID           |
| --------------------------- | ----------------- |
| Canal de seguimiento        | customfield_10040 |
| Fecha diagnóstico           | customfield_10041 |
| Tier propuesto              | customfield_10042 |
| Agente IA                   | customfield_10043 |
| Importe setup               | customfield_10044 |
| Nº de toque                 | customfield_10045 |
| Próxima acción              | customfield_10046 |
| Fecha de vencimiento        | customfield_10047 |
| Notas                       | customfield_10048 |
| Sentimiento                 | customfield_10049 |
| Resumen conversación        | customfield_10050 |
| Fecha último contacto       | customfield_10051 |
| Email del lead              | customfield_10052 |
| Teléfono WhatsApp           | customfield_10053 |
| Empresa                     | customfield_10054 |
| Nombre del contacto         | customfield_10055 |
| Gmail Thread ID             | customfield_10056 |
| Último Message ID procesado | customfield_10057 |
| Error técnico               | customfield_10058 |

---

# Security Considerations

The system implements:

* Duplicate message detection.
* AI confidence validation.
* Human escalation for critical scenarios.
* Full Jira audit trail.
* Controlled automatic transitions.
* Error recovery procedures.

---

# Design Principles

The solution follows the following principles:

1. Jira is the source of truth.
2. OpenAI interprets, but does not decide.
3. n8n executes deterministic business rules.
4. Critical decisions always allow human intervention.
5. Every action must be traceable.
6. Every workflow must be independently maintainable.

---

# Repository Structure

```text
/
├── workflows/
│   ├── myutopia_01_gmail_inbound_ia_jira.json
│   ├── myutopia_02_scheduler_toques.json
│   ├── myutopia_03_whatsapp_inbound_simulado.json
│
├── docs/
│   ├── Solution_Analysis.pdf
│   ├── Architecture_Diagram.png
│
├── README.md
```

---

# Future Improvements

Potential future enhancements include:

* Real WhatsApp Business integration.
* Multi-language support.
* CRM synchronization.
* Analytics dashboard.
* Lead scoring.
* SLA monitoring.
* Advanced AI assistants.
