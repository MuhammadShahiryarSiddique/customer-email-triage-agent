# Customer Email Triage & Ticketing Agent

A no-code AI automation, built on Make.com, that reads incoming customer support emails, identifies the customer, classifies the query, creates tickets when needed, and sends a consistent acknowledgment — with zero manual handling.

---

## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Objectives](#objectives)
- [How It Works](#how-it-works)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Getting Started](#getting-started)
- [Examples](#examples)
- [Roadmap](#roadmap)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

Company ABC receives all customer communication — billing complaints, product issues, and general feedback — through a single shared inbox. Previously, a human agent had to read every email, manually look up the customer, decide on a category, raise a ticket, and send an acknowledgment.

This project replaces that manual pipeline with an automated agent built in Make.com. It uses Google Gemini for intent understanding and Google Sheets as a lightweight customer and ticket database, producing a system that classifies, logs, tickets, and responds to emails in real time — with no human intervention required for the standard case.

Full background is in [`docs/Problem-Statement.md`](docs/Problem_Statement.md).

## Problem Statement

The support team was manually:
- Reading every incoming email to understand its intent
- Searching the internal database for customer details
- Classifying each email as Billing, Product, or Feedback
- Creating tickets and routing billing/product emails to the right department
- Logging feedback separately, with no ticket required
- Sending acknowledgment emails, including ticket numbers where applicable

This caused slow response times, inconsistent handling, misclassification risk, and poor scalability as email volume grew.

## Objectives

- Automatically detect the intent of incoming emails
- Retrieve customer data using the sender's email address
- Classify emails into Billing, Product, or Feedback
- Create tickets only for Billing and Product issues
- Log feedback without generating a ticket
- Send professional, automated acknowledgment emails, including ticket numbers where relevant

## How It Works

At a high level, the scenario runs on a schedule, pulls new emails, enriches them with customer data, classifies them with AI, and branches through two sequential decision points before responding.

1. An incoming email triggers the scenario
2. The customer's record is looked up in Google Sheets using the sender's email address
3. Google Gemini analyzes the email and determines its intent
4. The first router separates **Feedback** from **Billing/Product** queries
5. Feedback is summarized by Gemini and logged in the Feedback sheet, then a simple acknowledgment is sent — no ticket is created
6. Billing/Product queries are assigned a unique ticket ID
7. A second router separates **Billing** from **Product**
8. The issue is logged in the corresponding sheet (Billing Issues or Product Issues)
9. An acknowledgment email is sent to the customer, including the ticket ID

A full module-by-module breakdown is in [`docs/Architecture.md`](docs/Architecture.md) and [`docs/Workflow-Explanation.md`](docs/Workflow-Explanation.md).

## Key Features

- Fully automated, schedule-driven email processing
- Customer lookup via Google Sheets using the sender's email address
- AI-based intent classification using Google Gemini
- Two-stage routing: feedback vs. ticketable queries, then billing vs. product
- Automatic, unique ticket ID generation for support cases
- Structured, categorized logging (separate sheets per category)
- Automated, consistent acknowledgment emails, with ticket numbers where applicable
- Scales to rising email volume without added manual effort

## Tech Stack

| Category | Tool |
|---|---|
| Automation platform | Make.com |
| AI / intent classification | Google Gemini |
| Database / ticket storage | Google Sheets |
| Email trigger & response | Gmail |
| Conditional logic | Make.com Router modules |
| Ticket ID generation | Make.com Tools / Set Variable |

## Repository Structure

See [`docs/Project-Structure.md`](docs/Project-Structure.md) for a full explanation of every folder and file in this repository.

## Getting Started

See [`docs/Setup-Guide.md`](docs/Setup-Guide.md) for the complete setup walkthrough, including sheet schemas, connection setup, and scenario configuration.

## Examples

Sample input/output pairs live in [`examples/`](examples/) — a raw customer email and the corresponding AI-generated acknowledgment, including ticket number.

## Roadmap

- SLA-based escalation for unresolved tickets
- Analytics dashboard for ticket volume and resolution time
- Multi-language email support
- Slack/Teams alerts for high-priority billing issues

See [`docs/CHANGELOG.md`](docs/CHANGELOG.md) for version history.

## Contributing

Contributions are welcome. See [`docs/CONTRIBUTING.md`](docs/CONTRIBUTING.md) for guidelines.

## License

This project is licensed under the MIT License — see [`LICENSE`](LICENSE) for details.
