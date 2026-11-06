# Choose Your Quote

**An end-to-end email subscription management system built with n8n, Google Sheets, JavaScript and AI-assisted quotation classification.**

Choose Your Quote is a portfolio project that automates subscriber registration, email confirmation, subscription updates, personalised daily or weekly quote delivery, quote inventory replenishment and unsubscription.

It combines six connected n8n workflows into a single system, from the first registration form to the final unsubscribe confirmation.

> **Project status:** Functional portfolio prototype, tested through its main registration, update, confirmation and unsubscription paths. The repository contains sanitised demonstration exports rather than ready-to-run production configurations. Further work is required before use as a public subscription service.

## How it works

1. **Register:** A subscriber selects a quote category and delivery frequency using a form.
2. **Verify:** A time-limited confirmation token is generated, hashed for storage and sent by email.
3. **Activate or update:** A confirmation webhook validates the token, creates or updates the subscriber record and clears the pending request.
4. **Deliver:** A daily schedule selects subscribers due for delivery, matches an available quotation to their chosen category and sends an email with an unsubscribe link.
5. **Replenish:** A separate schedule checks category inventory thresholds, retrieves a quotation batch and uses Gemini to classify quotations into the supported categories before adding them to the repository.
6. **Unsubscribe:** A token-based form records the unsubscribe request, removes the subscriber and displays an appropriate result.

### Supported preferences

- **Categories:** Inspiration, Happiness, Success, Courage and Random.
- **Frequency:** Daily or Weekly. Weekly delivery uses the weekday of the original subscription submission.

## Workflows

| # | Workflow | Responsibility | Diagram |
|---|---|---|---|
| 01 | Subscriber Registration | Capture preferences, generate and hash confirmation token, distinguish new and existing subscribers | [View](screenshots/01-subscriber-registration.png) |
| 02 | Send Confirmation Email | Send a context-aware email with a confirmation link | [View](screenshots/02-send-confirmation-email.png) |
| 03 | Subscription Confirmation | Validate token and expiry, create or update subscriber, clear pending request | [View](screenshots/03-subscription-confirmation.png) |
| 04 | Scheduled Quote Delivery | Filter recipients, match quotations, send emails and mark quotations as used | [View](screenshots/04-scheduled-quote-delivery.png) |
| 05 | Quote Repository Replenishment | Monitor stock levels, fetch ZenQuotes batch, classify using Gemini, deduplicate and store | [View](screenshots/05-quote-repository-replenishment.png) |
| 06 | Subscriber Unsubscription | Validate unsubscribe token, log the request, delete subscriber and return a result | [View](screenshots/06-subscriber-unsubscription.png) |

## Technology stack

| Technology | Use |
|---|---|
| **n8n** | Workflow orchestration, forms, webhooks, schedules and integrations |
| **JavaScript** | Subscriber filtering, quote matching and AI request preparation |
| **Google Sheets** | Subscriber, pending request, quote library, inventory and unsubscription records |
| **Gmail** | Confirmation, quote delivery, expiry and completion notifications |
| **ZenQuotes API** | Quotation source |
| **Google Gemini 2.5 Flash-Lite** | Semantic classification of quotations |
| **Structured Output Parser** | Validate the expected AI classification output structure |

## Design decisions

### 1. Separate the subscription lifecycle into six workflows

Registration, confirmation, delivery, repository management and unsubscription are separated for clearer responsibilities, easier debugging and simpler future changes. Workflow 01 calls Workflow 02 to send confirmation emails.

### 2. Store a hash of the temporary confirmation token

Confirmation requests use time-limited tokens. The pending request stores a token hash rather than the raw token; the confirmation workflow hashes the received token for lookup. The persistent unsubscribe token has a separate purpose.

### 3. Fetch the quote repository only once per delivery run

In Workflow 04, **Get Available Quotes** uses n8n's *Execute Once* setting. This prevents redundant repository reads when multiple subscribers are due. JavaScript then assigns one randomly selected available quote per category for that run, which can be shared by recipients in the same category.

### 4. Classify a batch in one AI request

Workflow 05 groups the retrieved ZenQuotes items into one structured input for Gemini. This reduces the number of AI calls compared with classifying each quotation individually.

### 5. Apply inventory thresholds and deduplication

Quote inventory is monitored by category. Replenishment is limited to one batch per run when at least one category is at or below its reorder point. Deduplication is used in the workflow, although it is **not** a guaranteed comparison against every historical record in Google Sheets.

## Data model

A clean spreadsheet template is included in [`data-template/`](data-template/). It contains five worksheets:

| Worksheet | Purpose |
|---|---|
| **Subscribers** | Confirmed subscriber preferences and unsubscribe token |
| **Pending Requests** | Confirmation and update requests awaiting validation |
| **Quote Library** | Categorised quotations and usage status |
| **Quote Inventory** | Category-level available counts and reorder thresholds |
| **Unsubscription Records** | Unsubscription time, email and optional feedback |

No live subscriber records are included in the public template.

## Testing and current limitations

The main paths were tested during development, including new subscriptions, preference updates, valid and expired confirmation handling, scheduled-delivery logic and successful/invalid-token unsubscription. These tests do **not** constitute a production security, scalability or regulatory audit.

Known limitations and future improvements include:

- Add explicit consent evidence and a clear privacy notice before collecting real subscriber data.
- Define retention periods and suppression-list handling across the subscription lifecycle.
- Improve operational monitoring, alerts, failure recovery and rate-limit handling.
- Implement quote deduplication against the full existing library, not only prior processed items.
- Test concurrency, failed deliveries, token abuse cases and higher-volume operation.
- Review terms of use and attribution requirements for external quotation and AI services.

**Privacy note:** The project was designed with data minimisation and UK GDPR/ICO guidance in mind, but it is **not presented as fully UK GDPR or ICO compliant**.

## Repository contents

```text
choose-your-quote/
├── README.md
├── docs/
│   └── IMPORT_SETUP.md
├── workflows/
│   ├── 01 - Subscriber Registration.json
│   ├── 02 - Send Confirmation Email.json
│   ├── 03 - Subscription Confirmation.json
│   ├── 04 - Scheduled Quote Delivery.json
│   ├── 05 - Quote Repository Replenishment.json
│   └── 06 - Subscriber Unsubscription.json
├── screenshots/
│   ├── 01-subscriber-registration.png
│   ├── 02-send-confirmation-email.png
│   ├── 03-subscription-confirmation.png
│   ├── 04-scheduled-quote-delivery.png
│   ├── 05-quote-repository-replenishment.png
│   └── 06-subscriber-unsubscription.png
└── data-template/
    └── Email Subscription Management System.xlsx
```

## Importing the workflows

The JSON files in [`workflows/`](workflows/) are **sanitised demonstration copies**. They exclude original credentials and instance identifiers, and contain placeholders for external documents, cross-workflow references and public URLs.

For configuration steps, see **[Import and setup guide](docs/IMPORT_SETUP.md)**. Import each workflow into its own empty n8n workflow. Reconnect credentials, select your own spreadsheet and worksheets, and replace placeholders before testing. Do not publish imported schedules or webhook endpoints until you have configured and verified them.

---

*Portfolio project demonstrating workflow architecture, API integration, JavaScript data processing, AI classification and operational automation.*
