# Choose Your Quote, sanitised n8n workflows

These six JSON files are **portfolio copies**, not ready-to-run deployments.
The original n8n configurations were intentionally modified for public sharing:

- Removed stored credential references, n8n workflow and webhook IDs, and instance metadata.
- Replaced the live Google Sheets document ID and sheet identifiers with placeholders.
- Replaced the live confirmation webhook and unsubscription form URLs with placeholders.
- Replaced Workflow 01's call to Workflow 02 with an import-time placeholder.
- Set workflows inactive by default.
- Preserved node labels, parameters, scripts, conditions, connections and the five quote categories.

## Before running

1. Import all six workflows and create or connect your own Google Sheets and Gmail credentials.
2. Create a Google Sheets workbook from the provided separate template. Reselect the document and corresponding worksheet in every Google Sheets node.
3. In Workflow 01's Execute Workflow nodes, select the imported Workflow 02.
4. Configure the confirmation webhook URL in Workflow 02 to match Workflow 03's production webhook.
5. Configure the unsubscribe form URL in Workflow 04 to match Workflow 06's production form.
6. Configure the Gemini model credentials or n8n AI gateway in Workflow 05.
7. Configure n8n workflow timezone, review the schedule and unpin data before publication.
8. Test registration, updating, delivery, replenishment, valid/invalid tokens and unsubscription before activating schedules.

These are demonstration artifacts, not a claim of production readiness or full UK GDPR/ICO compliance.
