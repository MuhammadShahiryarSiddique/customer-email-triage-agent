# Frequently Asked Questions

**Q: Why Make.com instead of a custom-coded solution (e.g., Python + a cloud function)?**
Make.com allows the entire workflow to be built, visualized, and modified without writing or maintaining backend code, which suits a support-ops use case where the logic may need frequent, non-technical adjustments (e.g., changing acknowledgment wording, adding a category).

**Q: Why Google Sheets instead of a real database?**
For the scale this project targets, Sheets is fast to set up, requires no hosting, and is directly readable/editable by non-technical support staff. It can be swapped for Airtable, a proper SQL database, or a helpdesk tool (e.g., Zendesk, Freshdesk) without changing the overall architecture.

**Q: What happens if Gemini misclassifies an email?**
The scenario has no built-in human review step by default. For production use, consider adding a confidence threshold or a "review needed" branch that flags low-confidence classifications for manual review instead of auto-routing them.

**Q: Can this handle attachments?**
The base scenario processes subject and body text only. Attachment handling (e.g., OCR on scanned invoices) would need to be added as an additional module before the classification step.

**Q: How are duplicate/re-sent emails handled?**
The base scenario does not currently deduplicate. Adding a check against the ticket/feedback sheets (e.g., matching sender + subject within a time window) is a reasonable enhancement — see the Roadmap.

**Q: Is this GDPR/privacy compliant out of the box?**
No compliance guarantees are made by this project. Since customer emails and personal data flow through Google Sheets and a third-party AI provider (Gemini), you are responsible for reviewing your own data handling obligations before using this in production. See `docs/SECURITY.md`.
