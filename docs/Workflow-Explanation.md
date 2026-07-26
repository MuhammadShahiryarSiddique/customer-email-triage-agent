# Workflow Explanation

This document describes the end-to-end flow of a single email through the system, from arrival to resolution logging.

## Step-by-Step Flow

1. **Email arrives** in the shared support inbox.
2. **Scenario triggers** on its schedule (every 15 minutes) and picks up the new email via the Gmail Watch Emails module.
3. **Customer lookup** runs against the `Customers` sheet using the sender's email address, retrieving the customer's name and account details.
4. **AI classification** runs via Google Gemini, which reads the subject and body and returns one of three categories: Billing, Product, or Feedback.
5. **Router 1 evaluates the category:**
   - If **Feedback**, the flow moves to the feedback branch.
   - If **Billing** or **Product**, the flow moves to the ticketing branch.
6. **Feedback branch:**
   - Gemini summarizes the feedback into a short note.
   - The note is logged as a new row in the `Customer Complaints and Feedback` sheet.
   - A simple, friendly acknowledgment email is sent — no ticket number is included, since feedback is not ticketed.
7. **Ticketing branch:**
   - A unique ticket ID is generated via the Tools/Set Variable module.
   - **Router 2 evaluates the category again:**
     - If **Billing**, the issue is logged to the `Billing Issues` sheet.
     - If **Product**, the issue is logged to the `Product Issues` sheet.
   - An acknowledgment email is sent to the customer, including the generated ticket ID.
8. **End state:** every email results in exactly one log entry in exactly one sheet, and exactly one outbound acknowledgment email — with no manual step anywhere in the process.

## Category Definitions

| Category | Definition | Ticket Created? |
|---|---|---|
| Billing | Payment issues, invoices, refunds, subscription charges | Yes |
| Product | Bugs, feature questions, usage issues, technical problems | Yes |
| Feedback | Suggestions, praise, general comments, non-actionable input | No |

## Why Two Routers Instead of One

A single three-way router could technically handle Billing/Product/Feedback in one step. This project deliberately splits the decision into two routers because:
- The **first decision that matters operationally** is "does this need a ticket at all" — feedback should never touch the ticketing system.
- The **second decision** — which department — is only relevant once we already know a ticket is required.
- This mirrors how a human support lead actually thinks about triage, making the automation easier to explain, audit, and extend later (e.g., adding a new ticketable category only requires extending Router 2).
