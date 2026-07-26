# Problem Statement

## Background

Company ABC manages all customer communications through a single official email address. This inbox receives a wide range of queries, including billing issues, product-related concerns, feedback, and suggestions. Handling of these emails has been largely manual, creating operational inefficiencies and scalability challenges.

## Current (Manual) Workflow

- Every incoming email is manually read by the support team to understand the nature of the query
- Customer details are manually searched in the company's internal database using information available in the email
- Based on manual interpretation, the email is classified into one of the following categories:
  - Billing
  - Product
  - Feedback
- For billing and product-related emails:
  - A support ticket is manually created
  - The query is forwarded to the relevant department
  - An acknowledgment email containing the ticket number is sent to the customer
- For feedback-related emails:
  - No ticket is created
  - The feedback is manually noted
  - A simple acknowledgment email is sent to the customer

## Problems with the Manual Approach

- Manual reading and classification of emails leads to delays in response times
- Customer database lookups require additional effort and slow down query handling
- Human dependency increases the risk of misclassification and incorrect routing of queries
- Inconsistent handling of emails affects customer experience and internal tracking
- As email volume increases, the process becomes difficult to scale and manage efficiently
- Lack of automation makes it harder to maintain standardized acknowledgments, track resolution timelines, and measure operational performance

## Need for Automation

- An automated system is required to read incoming emails and understand their intent
- Customer identification should be automatically performed using existing database records
- Emails should be accurately classified into billing, product, or feedback categories without manual intervention
- Tickets should be created only for billing and product queries, while feedback should be logged without ticket generation
- Queries must be automatically routed to the appropriate department
- Customers should receive timely and consistent acknowledgment emails, including ticket numbers where applicable

## Objectives Derived From This Problem

- Automatically detect the intent of incoming emails
- Retrieve customer data using the sender's email address
- Classify emails into billing, product, or feedback categories
- Create tickets only for billing and product issues
- Log feedback without ticket generation
- Send professional acknowledgment emails automatically
