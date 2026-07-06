# AI Hiring Pipeline Automation

An n8n workflow that automates the full candidate hiring lifecycle — from application intake to interview scheduling — built by [Sohag Gain](https://bd.linkedin.com/in/sohaggain), Founder & CEO of [AI Smart Galaxy](https://www.aismartgalaxy.com).

This project was originally built to manage hiring for AI Smart Galaxy itself, and demonstrates the same automation architecture used for client CRM, lead-routing, and business process automation work: multi-trigger orchestration, AI-assisted data extraction, stage-based branching logic, and multi-channel notifications.

## What It Does

The workflow is organized into five connected sub-flows, each handling a different stage of the hiring pipeline:

1. **Application Intake** — a Gmail trigger captures incoming applicant emails. GPT-4o-mini extracts the job title being applied for directly from the email subject line (structured JSON output), and the parsed data is logged to a **Job Applicants List** Google Sheet. An automated email is sent to selected candidates.

2. **Candidate Selection** — a form captures screening decisions, updates the **Selected Candidates** sheet, and notifies the AI Smart Galaxy team on Slack.

3. **Stage-Based Routing (Webhook + Filter + Switch)** — a webhook receives Google Sheets edit events. A Filter node checks whether a row's status flag is `TRUE`, then a Switch node branches on *which column* was edited (columns 4, 6, 8, or 10 — corresponding to different pipeline stages: rejected, screening, test approval, interview). Each branch triggers the appropriate action:
   - Rejection email to the candidate
   - Screening invitation email
   - Slack notification to the hiring team
   - Interview scheduling email

4. **Screening Data Collection** — a separate form collects screening test results, logs them to an **Entry Data Screening Candidates** sheet, and posts a Slack update.

5. **Interview Booking → Hire (Cal.com)** — a Cal.com trigger fires on `BOOKING_CREATED` when a candidate books an interview slot. The booking is logged to a **Hire Candidates** sheet and the CEO is notified on Slack.

## Tracking Sheet Structure

The Google Sheet backing this workflow tracks each candidate through the full pipeline with columns including:

`First Name | Last Name | Job | Rejected | Questions | Screening | Test Complete | Test Approved | Interview Schedule | Hire | Indeed Email | Email | Resume | Portfolio | Code`

Each checkbox column represents a pipeline gate — ticking it in Sheets fires the corresponding webhook branch above.

## Tech Stack

| Component | Tool |
|---|---|
| Orchestration | [n8n](https://n8n.io) |
| Email intake & outreach | Gmail |
| AI job-title extraction | OpenAI GPT-4o-mini |
| Candidate tracking | Google Sheets |
| Team notifications | Slack |
| Interview scheduling | Cal.com |
| Stage routing | Webhook + Filter + Switch nodes |

## Setup

1. Import the workflow JSON into your n8n instance.
2. Connect credentials for:
   - Gmail (OAuth2)
   - OpenAI API
   - Google Sheets OAuth2
   - Slack
   - Cal.com
3. Point each Google Sheets node at your own tracking spreadsheet, matching the column structure above.
4. Update the Webhook node's path and connect it to your Google Sheets' "on edit" trigger (e.g. via Apps Script or a Sheets-to-webhook connector).
5. Adjust the Switch node's column-index conditions if your sheet layout differs.
6. Update Slack channel targets and email templates to match your own hiring team and branding.
7. Activate the workflow.

## Notes

- This is a self-built internal tool, later adapted into a portfolio demonstration of multi-stage business process automation.
- The same branching pattern (webhook → filter → switch → multi-channel action) generalizes well beyond hiring — it's the same shape used for support ticket routing, lead qualification, and order-status pipelines.
- Sensitive data (candidate emails, resumes, credentials) should never be committed to a public repo — see the sanitization note below before publishing.

## Author

**Sohag Gain**
Founder & CEO, AI Smart Galaxy — AI Automation, AI Agents & Business Workflow Consulting
🌐 [aismartgalaxy.com](https://www.aismartgalaxy.com) | [LinkedIn](https://bd.linkedin.com/in/sohaggain)
