# Setup Guide

This guide walks through recreating the scenario from scratch in Make.com.

## Prerequisites

- A Make.com account (Free tier is sufficient for low volume; check operation limits for production use)
- A Gmail account/alias used as the shared support inbox
- A Google account with access to Google Sheets
- Access to Google Gemini AI (via Make.com's Google Gemini AI app/connection)

## Step 1 — Prepare the Google Sheets Database

Create a single Google Sheet (or separate sheets) with the following tabs:

### `Customers`
| Column | Description |
|---|---|
| Email | Customer's email address (lookup key) |
| Name | Customer's full name |
| Account ID | Internal customer/account identifier |
| Plan/Tier | Optional — subscription tier or account type |

### `Billing Issues`
| Column | Description |
|---|---|
| Ticket ID | Auto-generated unique ID |
| Customer Email | Sender's email |
| Customer Name | From the Customers lookup |
| Subject | Original email subject |
| Summary | AI-generated summary of the issue |
| Status | e.g., Open, In Progress, Resolved |
| Date Received | Timestamp |

### `Product Issues`
Same structure as `Billing Issues`.

### `Customer Complaints and Feedback`
| Column | Description |
|---|---|
| Customer Email | Sender's email |
| Customer Name | From the Customers lookup |
| Feedback Summary | AI-generated summary |
| Date Received | Timestamp |

## Step 2 — Connect Accounts in Make.com

1. Create a new scenario
2. Add a **Gmail** connection (OAuth) with read access to the shared inbox
3. Add a **Google Sheets** connection (OAuth) with access to the sheet created in Step 1
4. Add a **Google Gemini AI** connection using your API key/credentials

## Step 3 — Build the Trigger

1. Add module: **Gmail → Watch Emails**
2. Configure the folder/label to watch (e.g., Inbox, unread only)
3. Set the scenario schedule — every 15 minutes is recommended as a starting point

## Step 4 — Customer Lookup

1. Add module: **Google Sheets → Search Row**
2. Configure it to search the `Customers` sheet
3. Set the search column to `Email` and the search value to the sender's email from the Gmail trigger

## Step 5 — Intent Classification

1. Add module: **Google Gemini AI → Generate a Response**
2. Provide a system/user prompt instructing Gemini to classify the email into exactly one of: `Billing`, `Product`, `Feedback`, and to return the result in a consistent, parseable format (e.g., a single word or a small JSON object)
3. Pass the email subject and body as input variables

Example prompt structure:
```
You are an email classification assistant. Read the email subject and body below
and classify it into exactly one category: Billing, Product, or Feedback.
Respond with only the category name.

Subject: {{subject}}
Body: {{body}}
```

## Step 6 — Router 1 (Feedback vs. Non-Feedback)

1. Add a **Router** module immediately after the classification step
2. Create two routes:
   - **Route A (Feedback):** filter where classification equals `Feedback`
   - **Route B (Billing/Product):** filter where classification is NOT `Feedback`

## Step 7 — Feedback Branch (Route A)

1. Add **Google Gemini AI → Generate a Response** to summarize the feedback content
2. Add **Google Sheets → Add Row**, targeting `Customer Complaints and Feedback`
3. Add **Gmail → Send an Email** to send a simple acknowledgment (no ticket number)

## Step 8 — Ticket ID Generation (Route B)

1. Add **Tools → Set Variable**
2. Generate a unique ticket ID — a common approach is combining a prefix with the current timestamp, e.g. `TCK-{{formatDate(now; "YYYYMMDDHHmmss")}}`

## Step 9 — Router 2 (Billing vs. Product)

1. Add a second **Router** module after the ticket ID step
2. Create two routes:
   - **Route B1 (Billing):** filter where classification equals `Billing`
   - **Route B2 (Product):** filter where classification equals `Product`

## Step 10 — Logging and Acknowledgment (Route B1 and B2)

For each route:
1. Add **Google Sheets → Add Row**, targeting the corresponding sheet (`Billing Issues` or `Product Issues`)
2. Add **Gmail → Send an Email**, including the generated ticket ID and a professional acknowledgment message

## Step 11 — Test the Scenario

1. Send yourself test emails covering all three categories
2. Run the scenario manually ("Run once") and verify:
   - Correct customer lookup
   - Correct classification
   - Correct sheet logging
   - Correct acknowledgment email content, including the ticket ID where expected
3. Once verified, enable the scheduled run (e.g., every 15 minutes)

## Troubleshooting

| Issue | Likely Cause |
|---|---|
| Customer not found | Sender's email doesn't exactly match a row in `Customers` — check for typos/whitespace |
| Wrong classification | Prompt needs tightening — add more explicit category definitions or examples |
| Router sends everything to one branch | Filter conditions on the router routes are misconfigured or case-sensitive mismatch |
| Ticket ID missing in email | Variable mapping from the Set Variable module to the Gmail module is broken |
