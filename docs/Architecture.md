# System Architecture

## High-Level Description

This project is implemented as a single Make.com scenario. It runs on a fixed schedule (every 15 minutes), checks the shared support inbox for new emails, enriches each email with customer data, classifies it using AI, and routes it through two sequential decision points before sending a response and logging the result.

The architecture is intentionally linear-with-branches rather than event-driven per category: every email goes through the same pipeline, and routers decide which branch of that pipeline applies.

## Module-by-Module Breakdown

### 1. Trigger — Gmail (Watch Emails)
Polls the shared inbox on a schedule and picks up new, unread emails. Each email's sender, subject, and body are passed downstream.

### 2. Customer Lookup — Google Sheets (Search Row)
Uses the sender's email address to search the `Customers` sheet and retrieve the matching customer record (name, account ID, plan/tier, or any other stored attributes). This replaces the manual database lookup from the original process.

### 3. Intent Classification — Google Gemini AI (Generate a Response)
The email's subject and body are sent to Gemini with a classification prompt. Gemini returns the email's category — Billing, Product, or Feedback — along with (optionally) a short structured summary used later in the flow.

### 4. Router 1 — Feedback vs. Non-Feedback
The first decision point. Based on Gemini's classification:
- **Feedback** → routed to the feedback branch (no ticket)
- **Billing or Product** → routed to the ticketing branch

### 5a. Feedback Branch
- Google Gemini AI summarizes the feedback content into a concise note
- Google Sheets (Add Row) logs the feedback into the `Customer Complaints and Feedback` sheet
- Gmail sends a simple acknowledgment email — no ticket number, since no ticket is created

### 5b. Ticketing Branch — Tools (Set Variable)
Generates a unique ticket ID (e.g., a timestamp-based or incrementing identifier) for the query. This ticket ID is passed forward to both the logging step and the acknowledgment email.

### 6. Router 2 — Billing vs. Product
The second decision point, applied only to non-feedback emails:
- **Billing** → logged to the `Billing Issues` sheet
- **Product** → logged to the `Product Issues` sheet

### 7. Logging — Google Sheets (Add Row)
The query, customer details, classification, and generated ticket ID are written to the appropriate sheet (Billing Issues or Product Issues), creating a persistent, structured record for the relevant department.

### 8. Response — Gmail
A final acknowledgment email is sent to the customer, including the ticket ID, confirming that their query has been received and logged.

## Design Rationale

- **Two separate routers** (rather than one three-way router) keep the "does this need a ticket at all" decision cleanly separated from the "which department" decision — this mirrors how the original manual process actually worked (feedback was never ticketed in the first place).
- **Google Sheets as the datastore** was chosen for speed of implementation and transparency; it is easy for a non-technical support lead to open and audit directly.
- **Gemini is used twice** — once for classification, once for feedback summarization — since these are different tasks with different prompt requirements.

## Data Flow Summary

```
Email → Customer Lookup → AI Classification → Router 1
                                                  ├─ Feedback → Summarize → Log → Acknowledge (no ticket)
                                                  └─ Billing/Product → Generate Ticket ID → Router 2
                                                                                              ├─ Billing → Log → Acknowledge (with ticket)
                                                                                              └─ Product → Log → Acknowledge (with ticket)
```
