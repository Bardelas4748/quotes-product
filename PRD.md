# Product Requirements Document (PRD)
## QuotesProduct — AI-Powered Proposal Engine for SMBs

**Version:** 1.0
**Date:** February 26, 2026
**Status:** Draft — Pending Stakeholder Approval

---

## Table of Contents

1. [Product Overview & Vision](#1-product-overview--vision)
2. [Target User Personas](#2-target-user-personas)
3. [Core Architecture: Logic vs. Magic](#3-core-architecture-logic-vs-magic)
4. [Phase Definitions & Feature Map](#4-phase-definitions--feature-map)
5. [Functional Requirements — MVP](#5-functional-requirements--mvp)
6. [Functional Requirements — Phase 1.1](#6-functional-requirements--phase-11)
7. [Functional Requirements — Phase 2+](#7-functional-requirements--phase-2)
8. [Non-Functional Requirements](#8-non-functional-requirements)
9. [User Stories & Acceptance Criteria](#9-user-stories--acceptance-criteria)
10. [Out of Scope](#10-out-of-scope)
11. [Open Questions & Deferred Decisions](#11-open-questions--deferred-decisions)
12. [Success Metrics & KPIs](#12-success-metrics--kpis)
13. [Risk Register](#13-risk-register)

---

## 1. Product Overview & Vision

### 1.1 What It Is

A B2B SaaS platform that transforms raw client context — messy notes, voice memos, past proposals, meeting transcripts — into high-fidelity, professionally written commercial proposals and quotes in seconds.

### 1.2 The Problem

Business owners selling services at $1,000+ need to justify premium pricing by telling a story of quality, reliability, and solution-mapping. Today they:

1. Open a past Google Doc/Word template
2. Manually rewrite it for the new client (30-90 minutes)
3. Generate a PDF and email it
4. Have zero visibility into whether it was opened, read, or shared
5. Follow up by phone or email and "hope for the best"

This process is slow, inconsistent, generic, and untracked. It leads to lower close rates and lost revenue.

### 1.3 The Solution

A two-lobed engine:

- **The Logic Lobe (Rate Card Engine):** A structured, owner-defined pricing database. Deterministic. Computed, never generated. The AI never hallucinates a price.
- **The Magic Lobe (Narrative Engine):** An LLM-powered layer that translates raw context into high-conversion sales copy — framing features as benefits, addressing specific client pain points, and mirroring the owner's unique voice.

### 1.4 One-Sentence Positioning

> "It's like having a senior sales strategist listen to my client calls and turn them into a high-value, professional proposal before I've even finished my coffee."

### 1.5 Competitive Differentiation

| Competitor Category | Their Weakness | Our Advantage |
|---|---|---|
| Legacy CPQ (PandaDoc, Proposify) | High friction — manual data entry and template building | Zero friction — automatic extraction from context |
| Admin Tools (HoneyBook, Jobber) | Transactional — good for line items, bad for value storytelling | Narrative-first — bridges price and ROI |
| Generic AI (ChatGPT, Gemini) | No guardrails — math errors, generic output, no pricing integrity | Logic-locked — anchored to a verified Rate Card |

**Moat:** Integration at the ingestion layer (context processing) rather than just the formatting layer.

---

## 2. Target User Personas

### 2.1 Primary Persona: "Solo Sam"

- **Who:** Solo service provider or owner of a 1-3 person business
- **Verticals:** Landscaping, web design, marketing consulting, photography, home renovation, coaching — any service at $1,000+
- **Tech comfort:** Low to moderate. Comfortable with email and a browser. Does not live in Slack or Notion.
- **Proposal frequency:** 2-5 proposals per week
- **Pain:** Spends 30-90 minutes per proposal. Copy-pastes from old ones. Output looks generic and doesn't reflect the quality of the conversation they had with the client.
- **JTBD:** "I just had a great discovery call. I want to capture that energy and send a proposal that makes the client say 'these people get me' — before I lose momentum."
- **Pricing authority:** Full. Sets and approves all prices.

### 2.2 Secondary Persona: "Team Lead Tara"

- **Who:** Operations lead or sales manager at a 5-15 person service company
- **Tech comfort:** Moderate. Uses Google Workspace daily.
- **Proposal frequency:** 5-15 proposals per week across the team
- **Pain:** Inconsistent quality across team members. No visibility into proposal pipeline. No tracking.
- **JTBD:** "I need my team to send consistently great proposals that match our brand voice, and I need to see what's in the pipeline without chasing everyone."
- **Pricing authority:** Partial. May need owner/partner approval for quotes above a threshold.

### 2.3 Tertiary Persona (Phase 2+): "Enterprise Ed"

- **Who:** Sales director at a 20-50 person company
- **Needs:** Approval workflows, CRM integration, team analytics, custom branding per division
- **Deferred to:** Phase 2+

---

## 3. Core Architecture: Logic vs. Magic

### 3.1 The Logic Lobe — Rate Card Engine

The Rate Card is the owner's single source of truth for all pricing. The AI cannot override, estimate, or invent prices. Every number in a proposal is computed by the Logic Lobe, never generated by the LLM.

**Supported pricing models:**

| Model | Description | Phase |
|---|---|---|
| Fixed price | "Logo design: $2,000" | MVP |
| Hourly / unit-based | "Development: $150/hr × estimated hours" | MVP |
| Recurring | "Monthly hosting: $50/mo" | MVP |
| Tiered / volume | "First 100 hrs at $150, 101-500 at $120, 500+ at $100" | Phase 1.1 |
| Conditional | "If rush delivery, add 25%" | Phase 1.1 |
| Bundled packages | "Starter Package = A + B + C at combined price" | Phase 1.1 |

**Additional pricing features:**

| Feature | Description | Phase |
|---|---|---|
| Discounts | Percentage, flat-dollar, volume, bundle, or ad-hoc | MVP (ad-hoc only), Phase 1.1 (rule-based) |
| Optional line items | Client can toggle on/off, quantities adjust total | MVP |
| Payment terms | Configurable per proposal (Net 30, 50/50, milestones, custom) | MVP |
| Currency | One currency per proposal. USD and ILS supported. | MVP |
| Tax display | Owner-configurable label and percentage (e.g., "VAT 17%"). System calculates and displays the amount. No built-in tax rate database. | MVP |

**Pricing integrity rule:** Every AI-generated price in the review screen displays a "Verified" badge linking it back to the Rate Card entry. This eliminates the trust gap.

### 3.2 The Magic Lobe — Narrative Engine

The LLM generates all narrative content: executive summaries, scope descriptions, value propositions, benefit framing, and client-specific pain point addressing. It does NOT generate numbers.

**Accuracy targets:**
- **Logic (math/line items):** 95% accuracy. Math errors are trust-destroyers.
- **Magic (narrative):** 80% accuracy on first pass. Narrative is subjective and easily editable.

**The "Generic AI" prevention strategy:**
- Voice calibration from seed proposals (3-5 recommended)
- Website scraping for tone and positioning
- Industry-aware defaults
- Owner USP capture during onboarding
- Evolving voice profile that learns from owner edits over time (Phase 1.1)

### 3.3 The Separation of Concerns

```
[Context Input] → [Entity Extraction (LLM)] → [Extracted Requirements]
                                                        ↓
                                          [Rate Card Matching (Deterministic)]
                                                        ↓
                                          [Pricing Computation (Deterministic)]
                                                        ↓
[Owner Voice Profile] → [Narrative Generation (LLM)] → [Structured Draft]
                                                        ↓
                                          [Compositor: Merge Narrative + Pricing]
                                                        ↓
                                          [Live Structured Draft → Editor UI]
```

The LLM never sees final price calculations. The compositor merges narrative sections (from LLM) with computed pricing (from Logic Lobe) into the final structured document.

---

## 4. Phase Definitions & Feature Map

### Phase 0: Foundation (Weeks 1-4)
> Get a working prototype in front of one real user.

### MVP (Months 2-3)
> Minimum viable product for first paying customers.

### Phase 1.1 (Months 3-4)
> Refinements based on MVP feedback. Power user features.

### Phase 2 (Months 4-6)
> Growth features — expansion revenue and churn reduction.

### Phase 3 (Months 6-12)
> Platform play — embedded widget, deep integrations, analytics.

### Feature Map (MoSCoW by Phase)

#### Context Ingestion

| Feature | Priority | Phase |
|---|---|---|
| Copy-paste text input | Must | MVP |
| Structured form / guided questionnaire | Must | MVP |
| Document upload (PDF, DOCX — past proposals, briefs) | Must | MVP |
| Voice memo upload (.m4a, .mp3, .wav) | Should | Phase 1.1 |
| In-app voice recording | Should | Phase 1.1 |
| Meeting transcript upload (Zoom .vtt, Meet .sbv, Teams .docx) | Could | Phase 2 |
| Live call recording / phone call audio | Could | Phase 2+ |
| Multiple inputs per proposal (merge sources) | Must | MVP |

#### Extraction & Processing

| Feature | Priority | Phase |
|---|---|---|
| Entity extraction (client needs, pain points, services, timeline, budget signals) | Must | MVP |
| Extraction preview (show high-level points for user confirmation before generation) | Must | MVP |
| Noise filtering (strip small talk from transcripts) | Should | Phase 1.1 |
| Confidence scoring (Identity / Scope / Investment pillars) | Must | MVP |
| Gap analysis for low-confidence inputs | Must | MVP |
| "Clarification Email" generator for vague input | Should | Phase 1.1 |
| AI Interviewer mode (conversational gap-filling) | Could | Phase 1.1 |

#### Rate Card & Pricing

| Feature | Priority | Phase |
|---|---|---|
| Fixed price items | Must | MVP |
| Hourly / unit-based items | Must | MVP |
| Recurring items | Must | MVP |
| Ad-hoc discounts | Must | MVP |
| Optional line items (toggle on/off) | Must | MVP |
| Configurable payment terms per proposal | Must | MVP |
| USD + ILS currency support (one per proposal) | Must | MVP |
| Tax display (configurable label + percentage) | Must | MVP |
| "Verified" badge on all prices | Must | MVP |
| Flag & Draft for unrecognized services ([??] placeholders) | Must | MVP |
| "Add to Rate Card" prompt for new services | Must | MVP |
| Send-blocking until all placeholders resolved | Must | MVP |
| Tiered / volume pricing | Should | Phase 1.1 |
| Conditional pricing | Should | Phase 1.1 |
| Bundled packages | Should | Phase 1.1 |
| Rule-based discounts | Should | Phase 1.1 |
| Market baseline price suggestions | Won't | Phase 2+ |

#### Proposal Editor

| Feature | Priority | Phase |
|---|---|---|
| Live structured draft (never a static blob) | Must | MVP |
| Section-by-section text editing | Must | MVP |
| Reorder sections (drag and drop) | Must | MVP |
| Add / remove sections | Must | MVP |
| Confidence flags (yellow highlight on uncertain content) | Must | MVP |
| Swap button on line items (dropdown of Rate Card services) | Must | MVP |
| Quantity editing on line items | Must | MVP |
| Override AI content with manual text | Must | MVP |
| Regenerate individual sections | Should | Phase 1.1 |
| Inline AI prompting ("make more professional", "emphasize warranty") | Should | Phase 1.1 |
| Learning loop (save owner edits as future preferences) | Should | Phase 1.1 |

#### Proposal Output & Delivery

| Feature | Priority | Phase |
|---|---|---|
| PDF generation (A4 default) | Must | MVP |
| Web link (unique URL, branded, responsive) | Must | MVP |
| Live document with "Publish" gate (edits not visible until published) | Must | MVP |
| Version history (owner sees all versions, client sees current) | Must | MVP |
| Change notification to client on new version | Must | MVP |
| English proposal output | Must | MVP |
| Hebrew proposal output (RTL) | Must | MVP |
| Owner branding (logo, colors, fonts) | Must | MVP |
| 3-4 professionally designed base layouts | Must | MVP |
| Auto-suggested layout based on vertical | Should | Phase 1.1 |
| "Accept" button on web link | Must | MVP |
| E-signature on acceptance | Should | Phase 1.1 |
| Proposal duplication (clone + modify) | Must | MVP |

#### Proposal Sections (Default Template)

| Section | Required | Configurable |
|---|---|---|
| Cover page (branded) | Yes | Logo, colors, fonts |
| Executive summary / "Why us" | Yes | Editable, regenerable |
| Understanding of client needs | Yes | Editable, regenerable |
| Products & services | Yes | Editable, line items swappable |
| Scope of work (included AND excluded) | Yes | Editable |
| Deliverables & timeline | Yes | Editable |
| Investment / pricing table | Yes | Prices from Rate Card, editable quantities |
| Payment milestones / terms | Yes | Configurable per proposal |
| General terms & conditions | Yes | Editable, can be templated |
| About us / team | Optional | Editable |

Section order is configurable. Sections can be added or removed per proposal.

#### Tone & Voice

| Feature | Priority | Phase |
|---|---|---|
| Dynamic tone control (formal ↔ collaborative) | Must | MVP |
| Voice calibration from seed proposals | Must | MVP |
| Website scraping for tone/positioning | Should | Phase 1.1 |
| Industry-aware defaults | Must | MVP |
| Adaptive sophistication ("plumbing proposal" vs. "enterprise RFP") | Must | MVP |

#### Tracking & Notifications

| Feature | Priority | Phase |
|---|---|---|
| Proposal open tracking (yes/no + timestamp) | Must | MVP |
| View count (total opens) | Must | MVP |
| Last viewed timestamp | Must | MVP |
| Unique viewer count (IP-based) | Must | MVP |
| Scroll-depth checkpoint (viewed pricing section) | Must | MVP |
| Owner notification on open (in-app + email) | Must | MVP |
| Automated follow-up reminders (owner-configurable) | Must | MVP |
| SMS / Slack / push notifications | Won't | Phase 2 |
| Full engagement analytics (time per section, device) | Won't | Phase 2 |

#### Dashboard

| Feature | Priority | Phase |
|---|---|---|
| Total proposals by status (draft / sent / accepted / expired) | Must | MVP |
| Recent activity feed | Must | MVP |
| "New Proposal" CTA (prominent) | Must | MVP |
| Client directory (client → linked proposals) | Must | MVP |
| Revenue pipeline (total quoted value) | Should | Phase 1.1 |
| Win rate (accepted / sent) | Should | Phase 1.1 |
| Average time to acceptance | Should | Phase 1.1 |

#### Onboarding

| Feature | Priority | Phase |
|---|---|---|
| Fast path onboarding (2 min — company name, description, service list) | Must | MVP |
| Better path onboarding (10 min — seed proposals + guided questions) | Must | MVP |
| Quality indicator ("Good" → "Excellent" based on data provided) | Must | MVP |
| USP capture (guided questionnaire + extraction from uploads) | Must | MVP |
| "Upgrade your profile later" prompt for fast-path users | Must | MVP |
| Industry template auto-generation from onboarding context | Should | Phase 1.1 |

#### Vague Input Handling

| Feature | Priority | Phase |
|---|---|---|
| Confidence scoring (Identity / Scope / Investment) | Must | MVP |
| "Discovery Draft" generation for low-confidence input | Must | MVP |
| Gap analysis (bulleted "Missing Context" list) | Must | MVP |
| Status flag: "Discovery Draft Created (Incomplete Information)" | Must | MVP |
| "Preliminary Scope of Work" fallback (no pricing) | Must | MVP |
| "Clarification Email" template generator | Should | Phase 1.1 |
| AI Interviewer mode (chat-based gap filling) | Could | Phase 1.1 |

#### Incomplete Rate Card Handling

| Feature | Priority | Phase |
|---|---|---|
| Detection of unrecognized service entities | Must | MVP |
| Narrative generation for unpriced services | Must | MVP |
| [??] Price placeholder with "Action Required" highlight | Must | MVP |
| "Add to Rate Card" checkbox on price entry | Must | MVP |
| Send-blocking until all placeholders resolved | Must | MVP |
| Market baseline price suggestions | Won't | Phase 2+ |

#### Post-Acceptance

| Feature | Priority | Phase |
|---|---|---|
| "Accept" button on web link proposal | Must | MVP |
| Acceptance confirmation (status update + owner notification) | Must | MVP |
| E-signature capture | Should | Phase 1.1 |
| Payment integration (Stripe — "Accept and Pay Deposit") | Won't | Phase 2 |
| CRM handoff (push acceptance data) | Won't | Phase 2 |

#### Platform & Access

| Feature | Priority | Phase |
|---|---|---|
| Mobile-responsive web application | Must | MVP |
| Multi-tenant architecture (full data isolation) | Must | MVP |
| Authentication (email + password, Google OAuth) | Must | MVP |
| Single-user account | Must | MVP |
| "Share draft for review" (read-only internal link) | Must | MVP |
| Team seats with roles | Won't | Phase 2 |
| Approval workflows | Won't | Phase 2 |
| Native mobile app | Won't | Phase 3 |

#### Integrations

| Feature | Priority | Phase |
|---|---|---|
| Google Suite (Drive, Gmail) — export/share | Should | Phase 1.1 |
| Calendar integration (Google Calendar / Outlook) | Won't | Phase 2 |
| CRM integration (HubSpot, Salesforce) | Won't | Phase 2 |
| Embedded lead capture widget | Won't | Phase 3 |
| Public API | Won't | Phase 3 |

#### Subscription & Billing

| Feature | Priority | Phase |
|---|---|---|
| 3-tier subscription model | Must | MVP |
| Stripe billing integration | Must | MVP |
| Free tier (permanent, feature-gated) | Must | MVP |
| Usage metering (proposal count) | Must | MVP |
| Feature flags per tier | Must | MVP |
| 14-day trial OR X free proposals for conversion | Must | MVP |

---

## 5. Functional Requirements — MVP

### 5.1 Context Ingestion

**FR-CI-001: Copy-Paste Text Input**
- The system shall provide a text area where the user can paste unstructured text of any length.
- The system shall accept input in English and Hebrew.
- Maximum input size: 50,000 characters (approximately 60 minutes of transcript text).
- The system shall display a character count indicator.

**FR-CI-002: Structured Form Input**
- The system shall provide a guided questionnaire that captures: client name/company, project description, key requirements, timeline, budget range (optional), and any special considerations.
- Fields shall be optional (the user can skip any field and still generate).
- The form shall adapt based on the user's industry detected during onboarding.

**FR-CI-003: Document Upload**
- Supported formats: PDF, DOCX, DOC, TXT, RTF.
- Maximum file size: 25MB per file.
- Maximum files per proposal: 10.
- The system shall extract text content from uploaded documents for processing.
- The system shall display a confirmation that each file was successfully parsed.

**FR-CI-004: Multiple Inputs Per Proposal**
- The system shall allow combining any mix of input types (text + documents + form) into a single proposal context.
- The system shall merge extracted information from all sources into a unified entity extraction.
- When sources conflict, the system shall flag the conflict for user resolution in the extraction preview.

### 5.2 Extraction & Preview

**FR-EP-001: Entity Extraction**
- The system shall extract the following entities from raw input:
  - Client identity (name, company, role)
  - Client needs and requirements (bulleted list)
  - Client pain points (what's not working today)
  - Services required (matched to Rate Card where possible)
  - Timeline signals
  - Budget signals (if mentioned)
  - Special constraints or conditions
- Extraction shall work for both English and Hebrew input.

**FR-EP-002: Extraction Preview Screen**
- Before generating the proposal, the system shall display a summary of extracted points organized by category.
- Each extracted point shall be editable (modify, delete) by the user.
- The user can add missed points manually.
- A "Generate Proposal" button shall only appear after the user confirms the extraction.

**FR-EP-003: Confidence Scoring**
- The system shall compute a confidence score across three pillars: Identity (who is the client?), Scope (what are we doing?), Investment (what does it cost?).
- Each pillar shall be scored as: High / Medium / Low confidence.
- The confidence status shall be displayed in the extraction preview.

**FR-EP-004: Low-Confidence Handling**
- If any pillar scores "Low":
  - The system shall display a "Missing Context" list identifying the gaps.
  - The system shall offer to generate a "Discovery Draft" — a scope-focused document without pricing.
  - The system shall NOT block generation — the user can always proceed.
  - The proposal status shall display "Discovery Draft (Incomplete Information)" rather than "Draft Ready."
- If all pillars score "High" or "Medium": proceed to generation normally.

### 5.3 Rate Card Management

**FR-RC-001: Service Definition**
- The owner shall be able to create, edit, and delete services in their Rate Card.
- Each service entry shall include: name, description, pricing model (fixed / hourly / recurring), base price, unit label (if hourly), and currency (USD or ILS).
- Services shall be organizable into categories defined by the owner.

**FR-RC-002: Rate Card Import**
- The system shall allow bulk import of services from a CSV or spreadsheet.
- The system shall allow extraction of services from uploaded past proposals during onboarding.

**FR-RC-003: Price Verification**
- Every price displayed in a generated proposal shall show a "Verified" badge linking to the originating Rate Card entry.
- The system shall NEVER display a price that was generated by the LLM. All prices are computed by the deterministic pricing engine.

**FR-RC-004: Unrecognized Service Handling (Flag & Draft Protocol)**
- When the extraction identifies a service not present in the Rate Card:
  - The system shall generate narrative content (scope, benefits, value proposition) using context.
  - The system shall insert a "[??] Price Required" placeholder — visually highlighted, impossible to miss.
  - The system shall prompt the owner to enter a price manually.
  - Upon price entry, the system shall offer: "Add this to your Rate Card for future use?" (checkbox).
  - The "Send" / "Publish" button shall be disabled until all [??] placeholders are resolved.

**FR-RC-005: Discounts**
- The owner shall be able to apply ad-hoc discounts to any line item or to the proposal total.
- Discount types: percentage-based or flat-dollar amount.
- The discounted price and original price shall both be visible in the pricing table.

**FR-RC-006: Optional Line Items**
- The owner shall be able to mark any line item as "Optional."
- Optional items shall be visually distinguished in the proposal output.
- On the web link, the client shall be able to toggle optional items on/off and see the total update in real time.

**FR-RC-007: Payment Terms**
- The owner shall be able to configure payment terms per proposal.
- Supported structures: single payment, percentage split (e.g., 50/50), milestone-based (custom number of milestones with labels and amounts), Net X days.
- Payment terms shall appear as a dedicated section in the proposal output.

**FR-RC-008: Currency**
- Each proposal shall have a single currency: USD or ILS.
- Currency is selected at proposal creation time.
- All amounts in the proposal display the correct currency symbol and formatting.

**FR-RC-009: Tax Display**
- The owner shall be able to configure a tax label (e.g., "VAT") and percentage (e.g., 17%) in their settings.
- The system shall calculate and display the tax amount on the pricing table as a separate line.
- Tax settings can be overridden per proposal.
- The system does NOT maintain a tax rate database — the owner provides the rate.

### 5.4 Proposal Generation

**FR-PG-001: Generation Trigger**
- After the user confirms the extraction preview, the "Generate Proposal" action triggers the narrative engine.

**FR-PG-002: Generation UX**
- The system shall display a step-by-step progress indicator during generation:
  - "Analyzing context..."
  - "Matching services to your Rate Card..."
  - "Writing executive summary..."
  - "Building scope of work..."
  - (etc., per section)
- Once generation begins producing text, the system shall transition to a streaming preview — the proposal appears section by section in real time.
- The user can begin reading early sections while later sections are still generating.

**FR-PG-003: Proposal Structure**
- The default generated proposal shall include these sections (in order):
  1. Cover page (branded — logo, colors, proposal title, client name, date)
  2. Executive summary / "Why us"
  3. Understanding of client needs (parroting back pain points from context)
  4. Products & services overview
  5. Scope of work (what's included AND what's excluded)
  6. Deliverables & timeline
  7. Investment / pricing table
  8. Payment milestones / terms
  9. General terms & conditions
  10. About us / team
- Section order is configurable. Sections can be removed or new custom sections added.

**FR-PG-004: Tone Control**
- The system shall provide a tone selector with at minimum two modes: "Formal / Corporate" and "Friendly / Collaborative."
- Tone selection affects all narrative sections.
- The tone selector shall be available before generation and changeable during editing (triggers re-generation of narrative sections).
- The system shall adapt narrative sophistication to the detected client type (e.g., a plumbing proposal reads differently than an enterprise consulting proposal).

**FR-PG-005: Language Support**
- The system shall generate proposal output in English or Hebrew.
- Language is selected per proposal.
- Hebrew proposals shall render correctly in RTL layout in both PDF and web link output.
- The LLM shall generate native-quality Hebrew sales copy, not translated English.

### 5.5 Proposal Editor (Review & Refinement)

**FR-PE-001: Structured Editing**
- The proposal shall always be presented as a structured, editable document — never a static blob.
- Each section is independently editable.
- The editor shall support: inline text editing, section reordering (drag and drop), section addition, and section removal.

**FR-PE-002: Confidence Flags**
- Any content the AI is uncertain about shall be highlighted in yellow with a tooltip explaining the uncertainty.
- Example: "Client mentioned a budget but it was unclear — verify this detail."
- Confidence flags shall be clearable by the user once reviewed.

**FR-PE-003: Line Item Controls**
- Each line item from the Rate Card shall have:
  - A "Swap" button — opens a dropdown of the owner's Rate Card services to replace the item instantly.
  - Quantity editing (if unit-based).
  - Price override (manual entry, clears the "Verified" badge and shows "Custom Price").
  - Delete button.
- The pricing table total shall update in real time on any change.

**FR-PE-004: AI Override**
- The user shall be able to select any AI-generated text and replace it with their own content.
- Manually written content shall be preserved across regeneration (the system does not overwrite user edits unless explicitly requested).

### 5.6 Proposal Output & Delivery

**FR-PO-001: PDF Generation**
- The system shall generate a downloadable PDF in A4 format.
- The PDF shall reflect the owner's branding (logo, colors, fonts).
- The PDF shall render correctly in both English (LTR) and Hebrew (RTL).

**FR-PO-002: Web Link**
- The system shall generate a unique, shareable URL for each proposal.
- The web link shall display a branded, responsive, mobile-friendly view of the proposal.
- The web link shall support English and Hebrew (RTL).

**FR-PO-003: Live Document with Publish Gate**
- The web link is a live document. The owner can edit the proposal after sending.
- Edits are NOT visible to the client until the owner clicks "Publish."
- On publish, the client receives a notification: "An updated version of your proposal is available."

**FR-PO-004: Version History**
- The system shall maintain a version history of all published versions.
- Owner view: full audit trail (V1, V2, V3...) with ability to restore any version.
- Client view: only the current active version. No draft visibility.

**FR-PO-005: Accept Button**
- The web link shall include an "Accept" button at the bottom.
- Clicking "Accept" shall change the proposal status to "Accepted" and notify the owner (in-app + email).
- Acceptance shall record: timestamp, client name (if known), and IP address.

**FR-PO-006: Proposal Duplication**
- The owner shall be able to duplicate any existing proposal.
- The duplicate creates a new draft with all content pre-filled, allowing the owner to change the client, modify scope, and adjust pricing.

**FR-PO-007: Branding Customization**
- The owner shall be able to upload their logo, select brand colors (primary and secondary), and choose from available font families.
- Branding is applied globally (all proposals) with per-proposal override capability.
- The system shall provide 3-4 professionally designed base layouts. The owner selects one as their default.

### 5.7 Tracking & Notifications

**FR-TN-001: Open Tracking**
- The system shall track when a web link proposal is opened.
- Tracked data points: opened (yes/no), timestamp of first open, total view count, last viewed timestamp, unique viewer count (IP-based), pricing section viewed (scroll-depth checkpoint).

**FR-TN-002: Owner Notifications**
- On first open: the owner receives an in-app notification and an email.
- On acceptance: the owner receives an in-app notification and an email.
- Notification content includes: proposal name, client name, event type, and timestamp.

**FR-TN-003: Automated Follow-Up Reminders**
- The system shall support configurable automated reminders.
- Default configuration (owner-adjustable):
  - "Not opened" reminder: triggers after 3 days if proposal has not been opened.
  - "Not responded" reminder: triggers after 7 days if proposal has been opened but not accepted.
- Reminders are sent to the owner (not the client) — in-app + email.
- The owner can configure: enable/disable, timing, and reminder frequency.

### 5.8 Dashboard

**FR-DB-001: Proposal Dashboard**
- The system shall display a dashboard as the default landing page after login.
- Dashboard elements:
  - Total proposals by status: Draft, Sent, Opened, Accepted, Expired. Displayed as summary cards.
  - Recent activity feed: chronological list of events (proposal sent, opened, accepted, new draft created).
  - "New Proposal" button: prominently placed, always visible.

**FR-DB-002: Client Directory**
- The system shall maintain a directory of clients.
- Each client entry includes: name, company (optional), email (optional), and a list of linked proposals.
- Clients are auto-created when a proposal is generated with a new client name.
- The directory is searchable and sortable.

### 5.9 Onboarding

**FR-OB-001: Dual-Path Onboarding**
- **Fast Path (~2 minutes):**
  - Company name
  - One-sentence description ("What do you do?")
  - Service list (free-text or structured entry)
  - Skip everything else → generate first proposal immediately.
- **Better Path (~10 minutes):**
  - Everything in Fast Path, plus:
  - Upload 3-5 past proposals (PDF/DOCX)
  - Guided USP questionnaire (5 questions: "What makes you different?", "Who is your ideal client?", "What do clients say about you?", "What's your biggest strength?", "What should clients know before working with you?")
  - Logo upload and brand color selection
- Neither path is gated. User can always switch or upgrade later.

**FR-OB-002: Quality Indicator**
- After onboarding, the system shall display a quality indicator showing the expected output quality based on data provided.
- Visual representation (e.g., "Good" → "Great" → "Excellent" with a progress bar).
- Specific prompt: "Upload past proposals to reach Excellent quality."
- Non-blocking — informational only.

**FR-OB-003: Cold Start Handling**
- If the user has zero past proposals to upload, the system shall still function using:
  - The company description and service list provided in Fast Path.
  - Industry defaults based on the user's self-identified vertical.
  - Verbal description (free-text input: "Tell us about your business in your own words").
- Output quality will be lower and the quality indicator shall reflect this.

**FR-OB-004: Seed Proposal Processing**
- When the user uploads past proposals, the system shall extract:
  - Writing style and tone patterns
  - Common section structures
  - Service descriptions and positioning language
  - USPs and value propositions
  - Client-facing terminology
- This data forms the "Voice Profile" used to personalize all future narrative generation.

### 5.10 Authentication & Multi-Tenancy

**FR-AM-001: Authentication**
- Email + password registration and login.
- Google OAuth ("Sign in with Google").
- Password reset via email.

**FR-AM-002: Multi-Tenant Data Isolation**
- Every data entity shall be scoped to a tenant.
- Tenant A's data (rate card, proposals, clients, voice profile) must never be accessible to Tenant B.
- Isolation shall be enforced at the application layer (query scoping) and validated at the data layer.
- LLM prompts shall never contain data from multiple tenants.

**FR-AM-003: Internal Draft Sharing**
- The owner shall be able to generate a read-only internal link to share a draft with a colleague/partner for review before sending.
- The internal link requires no login (simple token-based access).
- The reviewer can view but not edit.

### 5.11 Subscription & Billing

**FR-SB-001: Tier Structure**
- Three tiers: names TBD (e.g., Starter / Pro / Business).
- Tier 1 (lowest): sub-$10/month. Feature-gated.
- Tiers differentiated by: proposal volume cap, advanced features, branding options.
- Exact pricing, caps, and feature gates are **open decisions** (see Section 11).

**FR-SB-002: Free Tier**
- Permanent free tier with meaningful feature limitations.
- Candidate gates (to be finalized):
  - Limited proposal count per month
  - No PDF download (web link only)
  - No open tracking / analytics
  - Platform branding on proposals ("Powered by QuotesProduct")
  - No custom branding
- Conversion trigger: the moment the user wants to send a professional, unbranded, trackable proposal.

**FR-SB-003: Billing Integration**
- Stripe for subscription management.
- Support for: monthly billing, subscription upgrades/downgrades, payment method management, invoice history.

**FR-SB-004: Usage Metering**
- The system shall track proposals generated per billing cycle per tenant.
- When approaching the tier limit, the system shall display a warning.
- When the limit is reached, the system shall prompt upgrade — not hard-block mid-workflow.

---

## 6. Functional Requirements — Phase 1.1

**FR-1.1-001: Voice Memo Support**
- Upload pre-recorded voice memos (.m4a, .mp3, .wav, .ogg).
- In-app voice recording ("press and talk" interface).
- Speech-to-text processing → feeds into entity extraction pipeline.

**FR-1.1-002: Advanced Pricing Models**
- Tiered/volume pricing (configurable breakpoints and rates).
- Conditional pricing (if X then add Y%).
- Bundled packages (combine services at a package price).
- Rule-based discounts (volume, loyalty, bundle triggers).

**FR-1.1-003: Regenerate Individual Sections**
- The user shall be able to regenerate any single section without regenerating the entire proposal.
- Regeneration respects the current context, rate card state, and any user edits in other sections.

**FR-1.1-004: Inline AI Prompting**
- The user shall be able to highlight any text in the editor and invoke an AI prompt.
- Preset prompts: "Make more professional," "Make more concise," "Emphasize [specific point]," "Rewrite for technical audience."
- Custom prompt: free-text instruction.

**FR-1.1-005: Learning Loop**
- The system shall track owner edits to AI-generated content.
- Patterns in edits (e.g., consistently adding a specific sentence to a service description) shall be incorporated into future generation for that owner.
- The voice profile evolves over time based on accumulated edits.

**FR-1.1-006: E-Signature on Acceptance**
- When a client clicks "Accept," they shall be prompted to provide a digital signature (type name, draw signature, or upload).
- Signed proposals are stored with the signature for the owner's records.

**FR-1.1-007: Website Scraping for Voice Calibration**
- During onboarding or profile setup, the owner can provide their website URL.
- The system scrapes the site for: tone, positioning language, service descriptions, and visual brand cues.
- Scraped data supplements the voice profile.

**FR-1.1-008: Google Suite Integration**
- Export proposal to Google Docs.
- Save PDF to Google Drive.
- Send proposal link via Gmail (pre-composed email with proposal link).

**FR-1.1-009: Dashboard Analytics**
- Revenue pipeline: total quoted value of all sent proposals.
- Win rate: accepted / sent ratio.
- Average time to acceptance.

**FR-1.1-010: Clarification Email Generator**
- For low-confidence proposals, the system generates a professional email template the owner can send to the client to request missing information.
- Pre-filled with the specific gaps identified in the gap analysis.

**FR-1.1-011: AI Interviewer Mode**
- For vague input, the system presents a chat interface.
- The AI asks targeted questions about identified gaps: "I found 70% of what I need, but I'm missing the timeline. Was that discussed, or should I use your standard 4-week default?"
- Answers feed directly into the extraction and trigger regeneration.

---

## 7. Functional Requirements — Phase 2+

### Phase 2 (Months 4-6): Growth Features

**FR-2-001: Team Seats & Roles**
- Multiple users per account with role-based access: Owner (full access), Manager (edit + send, configurable approval threshold), Member (draft only, requires approval to send).
- Per-seat pricing addition to subscription.

**FR-2-002: Approval Workflows**
- Configurable rules: proposals above $X or for specific service types require manager/owner approval before sending.
- Approval request → notification → approve/reject/comment flow.

**FR-2-003: Meeting Transcript Support**
- Upload transcript files from Zoom (.vtt), Google Meet (.sbv), Teams (.docx).
- Noise filtering to strip small talk and extract project-relevant content.

**FR-2-004: CRM Integration**
- Direction and platform TBD (see Open Questions).
- Minimum: push proposal data (status, value, client) to CRM on key events.

**FR-2-005: Calendar Integration**
- Bi-directional sync with Google Calendar and Outlook.
- "Book a Strategy Call" CTA embedded in web link proposals (shows owner's available slots).
- Smart nudge: if client opens 3+ times without accepting, trigger notification to owner suggesting outreach or offer client a "15-minute Q&A" booking slot.

**FR-2-006: Payment Integration**
- Stripe Connect for "Accept and Pay Deposit" directly from web link.
- Owner configures deposit amount (fixed or percentage of total).

**FR-2-007: Advanced Analytics**
- Detailed engagement analytics: time per section, device type, full scroll tracking.
- Revenue by service type, close rate by client segment, proposal performance comparison.
- SMS / Slack / push notification channels.

**FR-2-008: Images & Media in Proposals**
- Support for embedded images in proposal sections (portfolio pieces, mockups, diagrams).
- Image upload and placement within the structured editor.

### Phase 3 (Months 6-12): Platform Play

**FR-3-001: Embedded Lead Capture Widget**
- Embeddable widget for the owner's website ("Tailor Your Project" / "Get a Custom Proposal").
- 4-step conversational client journey:
  1. **Engagement:** Client clicks widget.
  2. **Context Capture:** Conversational UI — "What are you trying to achieve?" / "What is your biggest frustration?" (text or voice-to-text).
  3. **Teaser Output:** Widget summarizes needs back to client: "It sounds like you need [X] to solve [Y]. I've sent this to [Owner] to finalize your custom strategy."
  4. **Handoff:** Client sees "Thank You" screen. Owner gets notification: "New High-Context Lead. Proposal Draft Ready for Review."
- No instant pricing unless owner has pre-authorized fixed packages.
- Widget styled to owner's brand (slide-out or modal).
- Automated "draft preview" email to client 5 minutes after submission.

**FR-3-002: Conditional Routing Logic**
- If quote value < owner's threshold AND matches a standard package → auto-send to keep lead hot.
- If quote value > threshold OR involves custom work OR owner is flagged "In-Office" → hold as draft for manual review.
- "Out of Office" buffer: send "Preliminary Estimate" with disclaimer, auto-schedule follow-up call.

**FR-3-003: The Meeting-to-Context Flywheel**
- If owner uses integrated calendar for follow-up calls, the platform offers to record the call (with permission).
- Recording becomes context for the next proposal version or final contract.
- Turns the platform from a one-off tool into a continuous sales loop.

**FR-3-004: Public API**
- RESTful API for power users and agencies to integrate proposal generation into their own systems.
- API key management, rate limiting, webhook support.

**FR-3-005: Native Mobile App**
- iOS and Android apps for proposal review, approval, and sending on the go.
- Push notifications for opens, acceptances, and new leads.

---

## 8. Non-Functional Requirements

### 8.1 Performance

| Metric | Target | Notes |
|---|---|---|
| Proposal generation time | < 30 seconds for a standard proposal | User sees streaming output within 5 seconds |
| Page load time | < 2 seconds | Dashboard and editor |
| PDF generation | < 10 seconds | After proposal is finalized |
| Search / filter in dashboard | < 500ms | Client directory, proposal list |
| Web link proposal load time | < 3 seconds | Client-facing — first impressions matter |
| Extraction preview | < 15 seconds | From context submission to preview display |

### 8.2 Scalability

| Stage | Scale | Priority |
|---|---|---|
| MVP | 1-100 tenants, <500 proposals/month | Optimize for development speed |
| Traction | 100-10K tenants, <50K proposals/month | Optimize for reliability |
| Scale | 10K-100K tenants, <500K proposals/month | Optimize for performance |
| Mass | 100K+ tenants, 1M+ proposals/month | Optimize for cost efficiency |

- The architecture shall support horizontal scaling without application-level changes.
- Background processing (document parsing, LLM calls, PDF generation) shall be decoupled from the request-response cycle.

### 8.3 Availability

- Target uptime: 99.9% (approximately 8.7 hours downtime/year).
- Proposal web links shall be available 99.95% — these are client-facing and time-sensitive.
- Graceful degradation: if LLM services are unavailable, the editor and Rate Card management shall remain functional.

### 8.4 Security

- All data encrypted in transit (TLS 1.3) and at rest (AES-256).
- Multi-tenant isolation enforced at application, query, and LLM prompt layers.
- LLM data privacy: client transcripts and business pricing data shall NOT be used for model training. Select model providers and deployment options that contractually guarantee this.
- Authentication: bcrypt password hashing, OAuth 2.0 for Google SSO, JWT session tokens.
- Rate limiting on all API endpoints.
- CORS properly configured for web link proposals.

### 8.5 Compliance

- **MVP:** Privacy policy, terms of service, secure data handling practices, cookie consent.
- **Phase 1.1:** GDPR compliance (data export, deletion requests, consent management) — required for EU-based SMBs.
- **Phase 2:** SOC 2 Type II preparation. Data residency options flagged for future evaluation.
- **Ongoing:** Architecture designed to accommodate sector-specific compliance (construction bids, healthcare disclaimers) via configurable proposal sections and terms templates.

### 8.6 Accessibility

- WCAG 2.1 AA compliance for the application UI.
- Web link proposals shall be screen-reader compatible.
- Keyboard navigation support throughout the editor.

### 8.7 Localization

- App UI: English only at launch.
- Proposal output: English and Hebrew.
- Hebrew proposals: native RTL rendering in both PDF and web link. Not translated — generated natively.
- Architecture shall support addition of new output languages without structural changes.

### 8.8 Browser Support

- Latest 2 versions of: Chrome, Firefox, Safari, Edge.
- Mobile: Safari (iOS), Chrome (Android).
- No IE11 support.

---

## 9. User Stories & Acceptance Criteria

### Context Ingestion

**US-001: Paste messy notes**
> As Solo Sam, I want to paste my raw meeting notes into the app so that I can generate a proposal without re-typing anything.

Acceptance Criteria:
- [ ] Text area accepts free-form text up to 50,000 characters.
- [ ] Input in English or Hebrew is accepted.
- [ ] Pasting triggers no formatting issues or data loss.
- [ ] A "Continue" action moves to the extraction preview.

**US-002: Upload context documents**
> As Solo Sam, I want to upload a past proposal and a client brief so that the AI has maximum context for generating a new proposal.

Acceptance Criteria:
- [ ] Upload accepts PDF, DOCX, DOC, TXT, RTF.
- [ ] Up to 10 files, each up to 25MB.
- [ ] Each file displays a success/failure indicator after processing.
- [ ] Uploaded content is merged with any other input sources.

**US-003: Use a guided form**
> As Solo Sam, I want to answer guided questions about the project so that the AI can generate a proposal even when I don't have notes to paste.

Acceptance Criteria:
- [ ] Form presents contextual questions (client, project, requirements, timeline, budget).
- [ ] All fields are optional.
- [ ] Form adapts based on owner's industry.
- [ ] Submission moves to extraction preview.

**US-004: Combine multiple input sources**
> As Solo Sam, I want to paste my notes AND upload the client's brief AND fill in some form fields so that the AI has the richest possible context.

Acceptance Criteria:
- [ ] All three input types can be used for a single proposal.
- [ ] Extraction merges information from all sources.
- [ ] Conflicting information across sources is flagged in the extraction preview.

### Extraction & Preview

**US-005: Review extracted points**
> As Solo Sam, I want to see what the AI understood from my input before it generates a proposal so that I can catch errors early.

Acceptance Criteria:
- [ ] Extraction preview displays: client identity, needs/requirements, pain points, matched services, timeline, budget signals.
- [ ] Each point is editable and deletable.
- [ ] User can add missed points.
- [ ] Confidence score (Identity / Scope / Investment) is visible.
- [ ] "Generate Proposal" button appears after review.

**US-006: Handle vague input**
> As Solo Sam, I want the system to tell me what's missing from my input instead of generating a bad proposal so that I can either fill the gaps or send a useful "discovery" document.

Acceptance Criteria:
- [ ] Low-confidence pillar triggers "Missing Context" bulleted list.
- [ ] System offers "Discovery Draft" option.
- [ ] User can proceed to generate anyway.
- [ ] Status displays "Discovery Draft (Incomplete Information)" for low-confidence outputs.

### Rate Card

**US-007: Set up my services and pricing**
> As Solo Sam, I want to define my services with their prices so that every proposal uses my real pricing.

Acceptance Criteria:
- [ ] User can create services with: name, description, pricing model (fixed/hourly/recurring), price, currency.
- [ ] Services can be organized into categories.
- [ ] Services can be edited and deleted.
- [ ] Bulk import from CSV is supported.

**US-008: Handle services not in my rate card**
> As Solo Sam, I want to be told when the AI found a service I haven't priced yet so that I don't accidentally send a proposal with wrong numbers.

Acceptance Criteria:
- [ ] Unrecognized services display [??] placeholder with "Action Required" highlight.
- [ ] Owner can enter a price manually.
- [ ] System offers "Add to Rate Card" checkbox.
- [ ] Send/Publish is blocked until all [??] are resolved.

### Proposal Generation & Editing

**US-009: Generate a proposal**
> As Solo Sam, I want to click one button and watch a professional proposal appear section by section so that I go from raw notes to a polished document in under a minute.

Acceptance Criteria:
- [ ] Step-by-step progress indicator displays during generation.
- [ ] Proposal streams in section by section within 30 seconds.
- [ ] All sections from the default template are generated.
- [ ] All prices come from the Rate Card (Verified badge visible).
- [ ] Tone matches the selected setting (formal/collaborative).

**US-010: Edit and refine the proposal**
> As Solo Sam, I want to tweak the AI's output easily so that the final proposal sounds exactly like me.

Acceptance Criteria:
- [ ] Every section is inline-editable.
- [ ] Sections can be reordered via drag and drop.
- [ ] Sections can be added or removed.
- [ ] Line items have a "Swap" button to replace with another Rate Card service.
- [ ] Line item quantities are editable; total updates in real time.
- [ ] User can override any AI text with their own.
- [ ] Confidence-flagged sections are highlighted in yellow with explanatory tooltips.

**US-011: Control the tone**
> As Solo Sam, I want to switch the proposal tone from formal to friendly so that it matches my client's style.

Acceptance Criteria:
- [ ] Tone selector is available before generation and during editing.
- [ ] Selecting a new tone regenerates narrative sections (preserving user edits unless explicitly regenerated).
- [ ] At least two tone presets: "Formal / Corporate" and "Friendly / Collaborative."

### Proposal Delivery

**US-012: Send a proposal as PDF**
> As Solo Sam, I want to download a polished PDF of my proposal so that I can email it or print it.

Acceptance Criteria:
- [ ] PDF is generated in A4 format within 10 seconds.
- [ ] PDF reflects owner branding (logo, colors, fonts).
- [ ] PDF renders correctly for both English and Hebrew proposals.
- [ ] PDF is downloadable with a single click.

**US-013: Share a proposal via web link**
> As Solo Sam, I want to send my client a link to a beautiful web version of my proposal so that they can view it on any device and I can track if they opened it.

Acceptance Criteria:
- [ ] Unique URL is generated per proposal.
- [ ] Web page is branded, responsive, and mobile-friendly.
- [ ] Supports English (LTR) and Hebrew (RTL).
- [ ] Includes "Accept" button.
- [ ] Open tracking is active from the moment the link is shared.

**US-014: Update a proposal after sending**
> As Solo Sam, I want to edit a proposal after sending it and have the client see the updated version so that I can handle change requests without starting over.

Acceptance Criteria:
- [ ] Owner can edit a sent proposal.
- [ ] Edits are NOT visible to client until "Publish" is clicked.
- [ ] Client receives a notification on new version publish.
- [ ] Version history is accessible to owner (all versions).
- [ ] Client only sees the current active version.

**US-015: Duplicate a past proposal**
> As Solo Sam, I want to clone an old proposal and modify it for a new client so that I don't start from scratch on similar projects.

Acceptance Criteria:
- [ ] Any proposal can be duplicated.
- [ ] Duplicate creates a new draft with all content pre-filled.
- [ ] Client name, pricing, and scope are editable in the duplicate.

### Tracking & Notifications

**US-016: Know when my proposal is opened**
> As Solo Sam, I want to be notified the moment my client opens my proposal so that I can follow up at the perfect time.

Acceptance Criteria:
- [ ] In-app notification fires on first open.
- [ ] Email notification fires on first open.
- [ ] Dashboard shows: opened (yes/no), view count, last viewed, unique viewers, pricing section viewed.

**US-017: Get follow-up reminders**
> As Solo Sam, I want the system to remind me if my proposal hasn't been opened or responded to so that no deal falls through the cracks.

Acceptance Criteria:
- [ ] Default "not opened" reminder at 3 days (configurable).
- [ ] Default "not responded" reminder at 7 days (configurable).
- [ ] Reminders go to the owner, not the client.
- [ ] Reminders can be enabled/disabled and timing adjusted.

### Onboarding

**US-018: Get started in 2 minutes**
> As Solo Sam, I want to sign up and generate my first proposal in under 2 minutes so that I can see the value immediately.

Acceptance Criteria:
- [ ] Fast path requires only: company name, one-sentence description, service list.
- [ ] First proposal can be generated immediately after fast path.
- [ ] Quality indicator shows "Good" with prompt to improve.

**US-019: Teach the AI my voice**
> As Solo Sam, I want to upload my best past proposals so that the AI writes in my style, not generic AI-speak.

Acceptance Criteria:
- [ ] Upload supports 3-5 past proposals (PDF/DOCX).
- [ ] System extracts style, tone, structure, and positioning.
- [ ] Subsequent proposals reflect the owner's writing patterns.
- [ ] Quality indicator upgrades after seed proposals are processed.

### Dashboard

**US-020: See my proposal pipeline at a glance**
> As Solo Sam, I want to see all my proposals and their statuses on one screen so that I know what needs attention.

Acceptance Criteria:
- [ ] Dashboard shows totals by status: Draft, Sent, Opened, Accepted, Expired.
- [ ] Recent activity feed displays chronological events.
- [ ] "New Proposal" button is always visible and prominent.
- [ ] Client directory is accessible with linked proposals.

### Internal Sharing

**US-021: Get a second opinion before sending**
> As Team Lead Tara, I want to share a draft with my business partner for review before sending it to the client so that we're aligned.

Acceptance Criteria:
- [ ] A read-only internal link can be generated for any draft.
- [ ] Link requires no login (token-based access).
- [ ] Reviewer can view all sections but cannot edit.

---

## 10. Out of Scope

The following are explicitly **not** included in MVP or Phase 1.1:

| Item | Rationale |
|---|---|
| Native mobile app (iOS/Android) | Mobile-responsive web is sufficient. Revisit Phase 3. |
| CRM integration (HubSpot, Salesforce) | Too early to commit to a platform. Phase 2. |
| Payment / deposit collection | Requires Stripe Connect complexity. Phase 2. |
| Embedded lead capture widget | Phase 3 platform play. |
| Public API | Phase 3. |
| Team seats and approval workflows | Single-user (+ read-only sharing) is sufficient for Phase 1. |
| Advanced engagement analytics (time per section, device type) | Basic tracking (opens, views, pricing scroll) covers Phase 1. |
| Images/media in proposals | Text + pricing tables for Phase 1. |
| Meeting transcript ingestion | Document upload and text input cover Phase 1. |
| Live call recording | Phase 2+. |
| Market baseline price suggestions | Requires market data infrastructure. Phase 2+. |
| Full Hebrew app UI | App stays English. Only proposal output supports Hebrew. |
| Multi-language beyond English/Hebrew | Deferred. Architecture supports future languages. |
| AI-powered contract generation post-acceptance | We are the "Closing Engine," not the "Project Manager." |
| In-app client-to-owner chat | All negotiation happens via owner's existing channels in Phase 1. |

---

## 11. Open Questions & Deferred Decisions

| # | Decision | Impact | Deadline |
|---|---|---|---|
| OQ-1 | Exact subscription tier names, pricing, and feature gates | Billing architecture, marketing, onboarding flow | Before MVP launch |
| OQ-2 | Free tier feature gate specifics (candidates: PDF download, send capability, tracking, branding removal, proposal cap) | Conversion funnel, perceived value | Before MVP launch |
| OQ-3 | "First X proposals free" vs. "14-day trial" vs. permanent free tier conversion mechanism | PLG strategy | Before MVP launch |
| OQ-4 | Formal GTM strategy beyond friends & family | Growth trajectory | Month 2-3 |
| OQ-5 | Revenue target ($MRR) at 6 and 12 months | Resource planning, investment decisions | Month 1 |
| OQ-6 | CRM platform priority (HubSpot? Salesforce? Both?) | Phase 2 integration roadmap | Month 3-4 |
| OQ-7 | E-signature build vs. buy (native, DocuSign, HelloSign) | Phase 1.1 scope and cost | Month 3 |
| OQ-8 | Speech-to-text provider for voice memo support (Whisper, Deepgram, AssemblyAI) | Phase 1.1 audio pipeline | Month 3 |
| OQ-9 | Hebrew voice calibration strategy when seed proposals are in English | LLM prompt engineering, quality assurance | Before MVP launch |
| OQ-10 | Number and design of base proposal layouts (targeting 3-4) | Requires designer involvement | Before MVP launch |
| OQ-11 | Widget pricing authorization rules (threshold configuration, default behavior) | Phase 3 widget design | Month 5-6 |

### Assumptions

| # | Assumption | Risk if Wrong |
|---|---|---|
| A-1 | SMB owners are willing to pay sub-$10/mo for a basic proposal tool | Pricing is too low to sustain unit economics; may need higher floor |
| A-2 | Copy-paste text is sufficient context for generating useful proposals in MVP | Users may need voice or document upload from day one for adequate quality |
| A-3 | 3-5 seed proposals are enough for meaningful voice calibration | May need more data or a different approach for tone accuracy |
| A-4 | Low-tech users (email + browser comfort) can navigate a structured editor | Editor may need to be even simpler; user testing will validate |
| A-5 | Hebrew LLM output quality is sufficient for professional sales copy | May need Hebrew-specific fine-tuning or model selection |
| A-6 | IP-based unique viewer detection is accurate enough to be useful | VPNs, shared office IPs may skew data; acceptable for Phase 1 |
| A-7 | "Live document + Publish gate" model doesn't confuse users vs. simpler frozen-link approach | User testing required to validate the mental model |

---

## 12. Success Metrics & KPIs

### North Star Metric
**Proposals sent per active user per month** — measures core engagement and value delivery.

### MVP Success Criteria (Month 3)

| Metric | Target | Rationale |
|---|---|---|
| Registered users | 50+ | Friends, family, network seeding |
| Users who generated ≥1 proposal | 30+ (60% activation) | Proves onboarding works |
| Users who sent ≥1 proposal to a client | 20+ (40% of registered) | Proves end-to-end value |
| Time from signup to first generated proposal | < 5 minutes | Validates low-friction onboarding |
| Time from context input to send-ready proposal | < 5 minutes (including edits) | Validates "faster than Word template" promise |
| User-reported quality (survey) | ≥ 7/10 | "Would you use this instead of your current process?" |

### Phase 1 Success Criteria (Month 6)

| Metric | Target | Rationale |
|---|---|---|
| Active paying users | 200+ | Validates willingness to pay |
| Total active users (free + paid) | 1,000 | Founder's stated goal |
| Proposals sent per active user per month | ≥ 4 | Confirms habitual use |
| Free-to-paid conversion rate | ≥ 5% | Validates free tier gates |
| Monthly churn rate | < 8% | Proves retention via Rate Card lock-in |
| Proposal acceptance rate (client clicks Accept) | Tracked, no target yet | Baseline establishment |
| NPS | ≥ 40 | Strong product-market fit signal |

### Phase 2 Success Criteria (Month 12)

| Metric | Target | Rationale |
|---|---|---|
| Active paying users | 1,000+ | Scaling validation |
| MRR | TBD (depends on pricing decision OQ-1) | Revenue sustainability |
| Team accounts (≥2 seats) | 10% of paid base | Validates expansion into teams |
| Proposal open rate (% of sent proposals opened by client) | ≥ 70% | Validates web link delivery model |
| Average proposals per user per month | ≥ 6 | Deepening engagement |

---

## 13. Risk Register

| # | Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|---|
| R-1 | LLM output quality too generic — users feel they're "babysitting" the AI | High | Critical | Voice calibration from seed proposals, industry-specific tuning, continuous feedback loop from edits. Invest heavily in prompt engineering before launch. |
| R-2 | Hebrew proposal quality is noticeably worse than English | Medium | High | Test Hebrew output quality early with native speakers. Evaluate Hebrew-specific models or fine-tuning. Have a fallback: generate in English, offer "professional translation" as a future feature. |
| R-3 | Rate card complexity exceeds what users are willing to set up | Medium | High | Default to simple (fixed + hourly). Import from CSV/past proposals. Guide users with templates. Make Rate Card setup feel like "telling us your prices" not "configuring a database." |
| R-4 | LLM costs make sub-$10 tier unprofitable | Medium | High | Model per-proposal cost early. Implement caching, use smaller models for extraction, reserve large models for narrative. Set proposal caps per tier. |
| R-5 | Users expect instant results but generation takes 20-30 seconds | Medium | Medium | Streaming preview mitigates perceived wait time. Optimize pipeline for speed. Consider pre-computing extractable elements. |
| R-6 | Multi-tenant data leak (tenant A sees tenant B's data) | Low | Critical | Enforce tenant scoping at every layer. Automated isolation tests in CI. Penetration testing before launch. LLM prompt injection protection. |
| R-7 | Users don't upload seed proposals, leading to poor output quality | High | Medium | Quality indicator creates positive pressure. Cold start mechanisms (website scraping, industry defaults, verbal description) provide fallback. First-use experience must be good enough without seeds. |
| R-8 | Free tier is too generous — no conversion pressure | Medium | Medium | Gate the features that matter most to paying customers: tracking, PDF, branding. Test and iterate on gates based on conversion data. |
| R-9 | Price placeholder ([??]) workflow is confusing or annoying | Medium | Medium | Make the UX delightful, not punitive. The placeholder should feel like a helpful prompt, not an error state. User testing before launch. |
| R-10 | Competitor (PandaDoc, Proposify) adds AI features before we gain traction | Medium | Medium | Our moat is at the ingestion layer, not the formatting layer. Competitors would need to rebuild their architecture. Speed to market and voice calibration quality are our defensible advantages. Move fast. |

---

*End of PRD v1.0*
