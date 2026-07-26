# Security Policy

## Data Handled by This Project

This automation processes customer emails, which may contain personally identifiable information (PII) such as names, email addresses, and details of billing or product issues. It also stores this data in Google Sheets and sends it to Google Gemini AI for classification and summarization.

## Reporting a Security Issue

If you discover a security concern related to this project's documented setup (for example, a misconfiguration pattern in the setup guide that could expose credentials or customer data), please open an issue or contact the maintainer directly rather than disclosing it publicly.

## Handling Credentials

- Never commit Make.com blueprint exports, API keys, OAuth tokens, or service account files to this repository. The included `.gitignore` is configured to help prevent this, but it is not a substitute for careful review before committing.
- Store all credentials (Gmail OAuth, Google Sheets OAuth, Gemini API key) only within Make.com's connection manager, not in any file in this repository.

## Data Retention Considerations

- Consider setting a retention/archival policy for the `Billing Issues`, `Product Issues`, and `Customer Complaints and Feedback` sheets, since they will accumulate customer PII indefinitely by default.
- If deploying this for a real company, review applicable data protection regulations (e.g., GDPR, CCPA) relevant to where your customers are located.

## Scope

This is a demonstration/portfolio automation project. It is provided as-is (see `LICENSE`) with no security guarantees for production deployment.
