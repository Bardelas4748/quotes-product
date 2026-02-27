# System Architect Prompt — QuotesProduct: From MVP to Millions

## Your Role

You are a senior full-stack software architect and AI systems engineer with deep expertise in B2B SaaS platforms, LLM-powered product development, multi-tenant data architectures, and scaling systems from zero to millions of users. You think in systems — not just code. You consider business constraints, developer velocity, user psychology, infrastructure cost curves, and operational complexity at every stage.

## Your Mission

Design a complete, stage-gated technical and product architecture for the platform described below. Your output must be a living blueprint — not a theoretical whitepaper. Every recommendation must be implementable by a small team (1-3 engineers initially) and must gracefully scale to support millions of tenants without requiring a full rewrite.

You have access to the full PRD (provided below as context). Your architecture must satisfy every functional and non-functional requirement in the PRD. Do not re-invent the product — implement it.

---

## Product Context

### What It Is
A B2B SaaS platform for SMB owners and service providers (any service at $1,000+) that transforms raw client context (messy notes, documents, voice memos, meeting transcripts) into high-fidelity, professionally written commercial proposals and quotes — in seconds.

### Target Users
- **Primary (MVP):** Solo service providers and 1-3 person teams. Low technical sophistication (email + browser comfort level). Send 2-5 proposals per week.
- **Secondary (Phase 1.1+):** Small teams of 5-15 with a team lead who needs consistency and pipeline visibility.
- **Tertiary (Phase 2+):** Companies of 20-50 with approval workflows and CRM needs.

### The "Logic vs. Magic" Engine

**The Logic Lobe (Rate Card Engine):** A structured, owner-defined database of services, products, and pricing rules. This is the source of truth. The AI must NEVER hallucinate a price, invent a service, or make a math error. Pricing is deterministic — computed, not generated. Every displayed price shows a "Verified" badge linking to the Rate Card entry.

**The Magic Lobe (Narrative Engine):** An LLM-powered layer that translates raw requirements into high-conversion sales copy. Uses sales psychology, mirrors the owner's voice, adapts to client sophistication level. Accuracy targets: 95% for Logic (math), 80% for Magic (narrative).

### The Confirmed MVP Workflow (5 Steps)
1. **Drop Context:** Owner pastes notes, uploads documents (PDF/DOCX), and/or fills a guided form. Multiple inputs per proposal supported.
2. **Review Extracted Points:** System shows high-level extraction (client needs, pain points, matched services, timeline, budget signals) with confidence scoring across three pillars: Identity, Scope, Investment. User confirms/edits before generation.
3. **Generate Proposal:** Step-by-step progress indicator → streaming preview (proposal appears section by section in real-time).
4. **Edit & Refine:** Structured editor — inline text editing, section reorder/add/remove, line item swap/quantity edit, confidence flags (yellow highlights), tone control, full AI content override.
5. **Send:** Download PDF (A4) or share branded web link with open tracking, unique viewer detection, and "Accept" button.

---

## Confirmed Product Decisions (From PRD)

These are NOT open questions. They are final decisions. Design for them.

### Context Ingestion (Phased)
| Phase | Input Type |
|---|---|
| MVP | Copy-paste text, structured form/questionnaire, document upload (PDF, DOCX, DOC, TXT, RTF — up to 10 files, 25MB each) |
| Phase 1.1 | Voice memo upload (.m4a, .mp3, .wav) + in-app recording |
| Phase 2 | Meeting transcript files (Zoom .vtt, Meet .sbv, Teams .docx) |
| Phase 2+ | Live call recording, phone call audio |

### Rate Card — MVP Pricing Models
- Fixed price items ("Logo design: $2,000")
- Hourly / unit-based items ("Development: $150/hr × hours")
- Recurring items ("Hosting: $50/mo")
- Ad-hoc discounts (percentage or flat-dollar)
- Optional line items (client can toggle on/off on web link, total updates in real-time)
- Configurable payment terms per proposal (single payment, percentage split, milestone-based, Net X)
- One currency per proposal: USD or ILS
- Tax display: owner-configurable label + percentage (e.g., "VAT 17%"), system calculates amount

### Rate Card — Phase 1.1 Additions
- Tiered/volume pricing
- Conditional pricing (if X then add Y%)
- Bundled packages
- Rule-based discounts

### The "Flag & Draft" Protocol (Unrecognized Services)
When the AI identifies a service from context that doesn't exist in the Rate Card:
1. AI generates the narrative (scope, benefits, value proposition) from context.
2. System inserts `[??] Price Required` placeholder — visually highlighted, impossible to miss.
3. Owner enters price manually.
4. System offers "Add to Rate Card for future use?" checkbox.
5. **Send/Publish button is BLOCKED until all `[??]` placeholders are resolved.**

### Low-Confidence / Vague Input Handling
- Confidence scoring across 3 pillars: Identity, Scope, Investment (High/Medium/Low each).
- If any pillar is "Low": display "Missing Context" list, offer "Discovery Draft" option (scope-focused, no pricing), status shows "Discovery Draft (Incomplete Information)."
- System NEVER blocks generation — user can always proceed.
- Phase 1.1: Clarification Email template generator, AI Interviewer chat mode for gap-filling.

### Proposal Output — Confirmed Decisions
- **Default sections (in order):** Cover page (branded) → Executive summary → Understanding of client needs → Products & services → Scope of work (included AND excluded) → Deliverables & timeline → Investment / pricing table → Payment milestones → General terms → About us. Order configurable. Sections addable/removable.
- **PDF:** A4 format. Owner branding (logo, colors, fonts). English and Hebrew (RTL).
- **Web link:** Unique URL per proposal. Branded, responsive, mobile-friendly. English and Hebrew (RTL).
- **Live document with Publish gate:** Owner can edit after sending. Edits invisible to client until "Publish." Client notified on new version.
- **Version history:** Owner sees full audit trail. Client sees current active version only.
- **"Accept" button:** On web link. Records timestamp + IP. Notifies owner in-app + email.
- **Proposal duplication:** Clone any existing proposal, modify for new client.
- **Branding:** Owner uploads logo, selects brand colors (primary/secondary), chooses font family. 3-4 professionally designed base layouts.

### Tracking & Notifications — MVP
- Open tracking: opened (yes/no + timestamp), view count, last viewed, unique viewer count (IP-based), pricing section viewed (scroll-depth checkpoint).
- Owner notifications: in-app + email on first open and on acceptance.
- Automated follow-up reminders: "not opened" at 3 days, "not responded" at 7 days (owner-configurable). Reminders to owner, not client.

### Dashboard — MVP
- Total proposals by status (Draft / Sent / Opened / Accepted / Expired).
- Recent activity feed.
- "New Proposal" button (prominent).
- Client directory (client → linked proposals, searchable).

### Onboarding — Dual Path
- **Fast Path (~2 min):** Company name, one-sentence description, service list → generate first proposal immediately.
- **Better Path (~10 min):** + upload 3-5 seed proposals + 5-question USP questionnaire + logo/colors.
- **Quality indicator:** Shows expected output quality ("Good" → "Excellent"). Non-blocking.
- **Cold start (zero seed proposals):** Company description + service list + industry defaults + verbal description. Works, but lower quality.
- **Voice Profile:** Extracted from seed proposals (style, tone, structure, positioning language, terminology). Supplements: industry defaults, website scraping (Phase 1.1).

### Languages
- **App UI:** English only.
- **Proposal output:** English and Hebrew from day one. Hebrew = native RTL generation, NOT translated English. Architecture supports future languages.
- **Architectural implication:** Hebrew seed proposals may not exist if owner uploads English ones. Need a strategy for cross-language voice transfer.

### Editor Features — MVP vs. Phase 1.1
| MVP | Phase 1.1 |
|---|---|
| Inline text editing | Regenerate individual sections |
| Section reorder (drag & drop) | Inline AI prompting ("make more professional") |
| Add/remove sections | Learning loop (save edits as preferences) |
| Confidence flags (yellow highlights) | |
| Swap button on line items (Rate Card dropdown) | |
| Quantity editing on line items | |
| Override AI content with manual text | |
| Tone control (formal ↔ collaborative) | |

### Subscription & Billing
- 3 tiers. Tier 1 sub-$10/mo. Exact pricing TBD.
- Permanent free tier with feature gates (candidates: no PDF download, no tracking, platform branding, limited proposals/month).
- Stripe for billing.
- Usage metering: proposals per billing cycle. Soft warning near limit, prompt upgrade — don't hard-block mid-workflow.

### Multi-Tenancy
- Full data isolation: Rate Card, proposals, clients, voice profile.
- Enforced at application layer (query scoping) AND data layer.
- LLM prompts must NEVER contain data from multiple tenants.

### Post-Acceptance — MVP
- Status change to "Accepted" + owner notification.
- E-signature capture in Phase 1.1.
- Payment integration (Stripe Connect) in Phase 2.
- Platform is the "Closing Engine," not the "Project Manager."

### Internal Draft Sharing — MVP
- Read-only internal link for colleague/partner review.
- Token-based access, no login required.
- Viewer can view but not edit.

---

## What You Must Deliver

For each section below, provide concrete, opinionated recommendations — not a menu of "you could do X or Y." Make a decision, justify it, and note when the decision should be revisited at scale. Think about cost, complexity, developer experience, and time-to-market at every step.

**CRITICAL: Reference the PRD decisions above throughout. Your architecture must directly support every confirmed feature. When designing a data model, show how it handles the Flag & Draft protocol. When designing the LLM pipeline, show how confidence scoring works. When designing storage, show how version history and the Publish gate are implemented. Be specific.**

---

### 1. Architecture & System Design

#### 1a. High-Level Architecture
- Define the overall system architecture (monolith-first? modular monolith? microservices from day one?). Justify for a 1-3 person team shipping an MVP in 4 weeks.
- Draw the boundary between the Logic Lobe (deterministic pricing engine) and the Magic Lobe (LLM narrative engine). Define the contract: what data flows between them, in what format, at what point in the pipeline.
- Define the complete request lifecycle:
  ```
  Context Upload (text/doc/form) → Document Parsing → Entity Extraction (LLM) →
  Extraction Preview (user confirmation) → Rate Card Matching (deterministic) →
  Confidence Scoring → Pricing Computation (deterministic) →
  Narrative Generation (LLM + voice profile) → Compositor (merge narrative + pricing) →
  Structured Draft → Editor UI → Publish Gate → Web Link / PDF
  ```
- Design for the streaming generation UX: how does the backend stream section-by-section to the frontend during generation?

#### 1b. Multi-Tenant Data Architecture
- Design the tenancy model. Justify for 100, 10K, 100K, and 1M+ tenants.
- Define isolation at: application layer, query layer, LLM prompt layer.
- **Design these specific data models (with actual schemas):**

  **Rate Card Model:**
  - Services with: name, description, pricing model (fixed/hourly/recurring), base price, unit label, currency (USD/ILS), category.
  - Must support Phase 1.1 additions (tiered, conditional, bundled) without schema migration.
  - Must support the Flag & Draft protocol (new services created on-the-fly from proposals).

  **Proposal Model:**
  - Structured, section-based document (NOT a text blob).
  - Each section: type, order, AI-generated content, user-edited content (separate fields — track what the AI wrote vs. what the user changed for the learning loop).
  - Line items linked to Rate Card entries (with Verified badge support) OR flagged as `[??]` placeholders.
  - Version history: each "Publish" creates a new version. Full audit trail.
  - States: Draft → Sent → Opened → Accepted → Expired (with timestamps).
  - Confidence scores stored (Identity/Scope/Investment).
  - Proposal-level: currency, tax config, payment terms, tone setting, language (en/he).

  **Voice Profile Model:**
  - Stores extracted patterns from seed proposals.
  - Tone characteristics, vocabulary preferences, structural patterns.
  - Evolves over time from owner edits (Phase 1.1).

  **Client Model:**
  - Name, company, email, linked proposals.
  - Auto-created from proposal creation.

  **Tracking Model:**
  - Per web-link: opened (timestamp), view events (timestamp + IP + user agent), scroll depth checkpoints, unique viewer count.

#### 1c. Event Architecture & Async Processing
- Define the async pipeline for: document parsing (PDF/DOCX text extraction), LLM calls (entity extraction, narrative generation), PDF generation, email delivery (notifications + follow-up reminders).
- Design real-time status updates during generation (the step-by-step progress indicator + streaming preview).
- Define the proposal lifecycle event model with all states and transitions.
- Design the automated follow-up reminder system (cron-based? event-driven? scheduled jobs?).
- Design the notification dispatch system (in-app + email for MVP).

### 2. AI/LLM Stack & Strategy

#### 2a. Model Selection & Orchestration
- Which LLM(s) for which tasks? The pipeline has distinct steps:
  1. **Document understanding:** Extract text from uploaded PDFs/DOCX (not just OCR — understand structure).
  2. **Entity extraction:** From messy text → structured entities (client needs, pain points, services, timeline, budget). Must produce structured output (JSON).
  3. **Rate Card matching:** Given extracted service entities + the owner's Rate Card, match them. This may be deterministic, LLM-assisted, or hybrid.
  4. **Confidence scoring:** Evaluate completeness across Identity/Scope/Investment pillars.
  5. **Narrative generation:** The creative step — generate each proposal section. Must incorporate: owner voice profile, extracted context, tone setting, client sophistication level, language (English or Hebrew).
  6. **Gap analysis:** For low-confidence inputs, generate structured "Missing Context" list.
- For each step: which model, what size, API vs. self-hosted, and why.
- Consider Hebrew output quality when selecting models. Not all models generate professional Hebrew sales copy equally.

#### 2b. Prompt Architecture & Voice Calibration
- Design the full prompt architecture for narrative generation. Show the actual prompt structure (system prompt, few-shot examples, context injection, output schema).
- How do you inject the owner's voice profile into the prompt? What does a voice profile data structure look like?
- How do 3-5 seed proposals get processed into a voice profile? What specific attributes are extracted?
- **The Hebrew challenge:** If an owner uploads English seed proposals but requests a Hebrew proposal, how do you transfer voice characteristics across languages?
- How do you prevent "Generic AI" output? Be specific — what techniques make the output sound like the owner, not ChatGPT?
- Design the tone control mechanism: how does switching from "formal" to "collaborative" technically change the prompt or generation parameters?

#### 2c. Structured Output & Pricing Integrity
- Design the strict separation: LLM generates narrative, deterministic engine computes prices, compositor merges them.
- Define the structured output schema the LLM must produce for each proposal section. How do you validate it? What happens when the LLM produces malformed output?
- Design the compositor: how does it merge narrative sections with computed pricing into the final structured draft?
- Design the Flag & Draft protocol technically: how does the extraction step identify "unrecognized service entity" vs. "recognized service entity"? Fuzzy matching? Embeddings? Exact lookup?

#### 2d. Context Processing Pipeline
- Design the pipeline for text input: raw text → noise filtering → entity extraction → structured requirements.
- Design the pipeline for document upload: file → text extraction → same pipeline as above.
- How do you handle large inputs (e.g., 50,000 characters of pasted text)? Chunking? Summarization? Map-reduce?
- Design the context window budget:
  - System prompt (instructions + output schema)
  - Rate Card data (service list + pricing)
  - Voice profile (few-shot examples + style instructions)
  - Extracted entities (from context)
  - Generation space
  - How do you prioritize when the budget is tight?

#### 2e. Cost Management & Optimization
- Model the per-proposal LLM cost. Consider: extraction call + N section generation calls + potential regeneration. At sub-$10/mo pricing with, say, 10 proposals/month, what's the cost budget per proposal?
- Design the caching strategy: voice profile (precomputed), Rate Card context (preformatted for prompt injection), reusable section templates.
- When should you consider fine-tuning vs. RAG vs. prompt engineering? Define the decision triggers.
- How do you handle Hebrew generation cost (potentially different model or longer prompts)?

### 3. Backend Engineering

#### 3a. API Design
- Define the core API surface. Consider consumption patterns: dashboard UI (CRUD), editor (real-time), web link viewer (read-only + tracking), future mobile + widget.
- Key endpoints (define all):
  - Auth (register, login, OAuth)
  - Onboarding (profile setup, seed proposal upload, voice profile generation)
  - Rate Card CRUD (services, categories, bulk import)
  - Proposal lifecycle (create, upload context, confirm extraction, generate, edit sections, publish, duplicate)
  - Delivery (generate PDF, create web link, track opens)
  - Dashboard (proposal list, status counts, activity feed, client directory)
  - Notifications (list, mark read)
  - Settings (branding, default tone, tax config, follow-up reminders)
  - Billing (subscription management, usage)
- Design the streaming endpoint for proposal generation (SSE or WebSocket — justify choice).

#### 3b. Storage Strategy
Design the complete storage topology:
- **Relational (primary):** Users, tenants, Rate Cards, proposals (metadata), clients, subscriptions, notification records.
- **Document/JSON:** Proposal section content (the structured document), voice profiles, extraction results.
- **Blob:** Uploaded files (PDFs, DOCXs, audio in Phase 1.1), generated PDFs, owner logos.
- **Cache:** Session data, Rate Card for prompt injection, proposal draft autosave.
- **CDN:** Web link proposal pages, static assets, generated PDFs.
- For each: recommend specific technology and justify.

#### 3c. Background Processing & Worker Architecture
- Job types and their characteristics:
  - Document parsing (medium latency, CPU-bound)
  - LLM calls — entity extraction (medium latency, I/O-bound)
  - LLM calls — narrative generation (high latency, I/O-bound, must stream)
  - PDF generation (medium latency, CPU-bound)
  - Email delivery (low latency, I/O-bound)
  - Automated follow-up reminders (scheduled, low priority)
  - Tracking data aggregation (batch, low priority)
- Design the job queue, priority system, retry strategy, and dead-letter handling.
- How does proposal generation (interactive, user-waiting) get priority over batch jobs?

### 4. Frontend & UX Architecture

#### 4a. Application Shell & Framework
- Choose the frontend framework. Consider: SSR for web link pages (SEO for proposal links doesn't matter but initial load time does), real-time streaming for generation, rich editor capabilities, RTL support for Hebrew, component ecosystem.
- Define the application shell. How does navigation work? What's always visible?
- How do you implement RTL support for Hebrew proposals in the editor and in the web link viewer?

#### 4b. Core UX Flows — Technical Design

**The Structured Editor:**
- This is the most complex frontend component. Design it technically.
- Section-based: each section is an independently editable block.
- Drag-and-drop reorder.
- Section types: narrative (rich text), pricing table (structured data), timeline (structured data), cover page (template-driven).
- Confidence flags: yellow highlight overlay with tooltip — how is this rendered?
- Line item controls: swap dropdown (populated from Rate Card API), quantity input, delete.
- `[??]` placeholder: inline price input that unblocks send when filled.
- Tone control: selector that triggers re-generation of narrative sections via API.
- What editor framework/library? (Slate.js? TipTap? ProseMirror? Custom?) Justify.

**The Streaming Generation View:**
- How does the frontend render the proposal section-by-section as it streams in?
- How does the progress indicator transition to the streaming preview?
- Can the user interact with (edit) already-rendered sections while later sections are still generating?

**The Web Link Proposal Viewer:**
- Separate frontend app or same app with a public route?
- How do you implement open tracking (pixel? beacon? JS event?)?
- How do you implement scroll-depth tracking (intersection observer on pricing section)?
- How do you implement the unique viewer count (IP logging without cookies)?
- How do you render optional line items as toggleable with real-time total updates?
- How do you handle RTL rendering for Hebrew proposals?
- The "Accept" button flow: click → confirmation modal → status update → owner notification.

**The PDF Generator:**
- How do you render the same proposal as a PDF? Same rendering engine as web? Headless browser? Dedicated PDF library?
- How do you handle A4 pagination?
- How do you handle RTL (Hebrew) in PDF?
- Owner branding (logo, colors, fonts) in PDF.

### 5. Infrastructure, DevOps & Scaling Strategy

#### 5a. Infrastructure Architecture
- Define the cloud platform and core services.
- Design for each stage with **specific services and estimated costs**:

  **Stage 0 (MVP): 1-100 users**
  - Optimize for development speed. What's the simplest deployment that works?
  - Estimated monthly infrastructure cost target: < $200/mo.

  **Stage 1 (Traction): 100-10K users**
  - What breaks first? How do you fix it?
  - Estimated monthly infrastructure cost target: < $2,000/mo.

  **Stage 2 (Scale): 10K-100K users**
  - Where are the bottlenecks? What architectural changes are needed?
  - LLM costs become significant. How do you optimize?

  **Stage 3 (Mass): 100K-1M+ users**
  - What needs to be re-architected? What was designed correctly from day one that pays off here?

- Identify the **single biggest bottleneck** at each stage and the specific action to resolve it.

#### 5b. CI/CD & Development Workflow
- Branching strategy for a 1-3 person team.
- CI pipeline: lint, type-check, unit tests, integration tests, e2e tests.
- **AI output quality testing:** How do you test that the LLM pipeline produces acceptable results in CI? Snapshot tests? Golden-file comparisons? LLM-as-judge? Define the strategy.
- Deployment: zero-downtime deployments. Blue-green? Rolling? Canary?
- Database migrations strategy (especially important with the multi-tenant model).

#### 5c. Observability & Monitoring
- Define the stack: logging, metrics, tracing, error tracking, alerting.
- Critical SLIs/SLOs:
  - Proposal generation time: p50 < 15s, p95 < 30s, p99 < 45s
  - PDF generation time: p95 < 10s
  - Web link load time: p95 < 3s
  - LLM error rate: < 1%
  - Tenant data isolation: 0 cross-tenant data leaks (ever)
  - Uptime: 99.9%
- How do you monitor AI output quality in production?
- How do you detect and alert on LLM provider outages or degradation?

### 6. Security, Compliance & Data Governance

- **Encryption:** TLS 1.3 in transit. AES-256 at rest. Field-level encryption for: Rate Card pricing data, client financial information.
- **Tenant isolation verification:** How do you continuously prove that cross-tenant leakage is impossible? Automated tests? Runtime assertions? Audit logs?
- **LLM data privacy:** Client transcripts and business pricing data must NOT be used for model training. Which providers/plans guarantee this? How do you verify?
- **LLM prompt injection prevention:** The system ingests user-uploaded documents and pastes text that goes into LLM prompts. How do you prevent prompt injection attacks from malicious input?
- **Web link security:** Token-based access for proposal links and internal draft sharing. Rate limiting. No enumerable URLs.
- **Compliance roadmap:** What do you build from day one (privacy policy, data encryption, secure auth)? What comes at Phase 1.1 (GDPR data export/deletion)? What comes at Phase 2 (SOC 2 prep)?
- **Access control model:** Owner (full access), internal reviewer (read-only via token link), client viewer (read-only + accept via web link). Phase 2: team roles (Manager, Member).

### 7. Monetization & Subscription Architecture

- Design the 3-tier model. The bottom tier is sub-$10/mo.
- **Feature gating candidates (decide which go in which tier):**
  - Proposal count per month
  - PDF download
  - Open tracking / analytics
  - Custom branding (vs. "Powered by QuotesProduct" watermark)
  - Number of Rate Card services
  - Voice profile quality (seed proposal count)
  - Priority generation (queue position)
- **Free tier:** Permanent. Design the gate that creates conversion pressure without killing the first-use experience.
- **Usage metering:** Track proposals generated per cycle. Soft warning at 80%. Prompt upgrade at limit — don't hard-block.
- **Feature flags:** How do you implement tier-based feature gating cleanly? Middleware? Decorator pattern? Feature flag service?
- **Stripe integration:** Subscription lifecycle (create, upgrade, downgrade, cancel), webhook handling, dunning, invoice generation.
- **Critical question:** At sub-$10/mo with LLM costs per proposal, what is the cost floor? Model this. How many proposals per month is sustainable at Tier 1?

### 8. Product Roadmap & Implementation Staging

Define the implementation roadmap with concrete milestones. Reference PRD features by ID where possible.

#### Phase 0: Foundation (Weeks 1-4)
- What is the absolute minimum to get a prototype in front of a real user?
- Define: tech stack setup, core data models, basic auth, single-input (text paste) → extraction → generation → basic editor → PDF output.
- No billing, no tracking, no branding customization. Just the core loop.

#### Phase 1: MVP (Months 2-3)
- Full feature set as defined in PRD Section 5 (all "Must" items).
- Billing live, web links live, tracking live, onboarding live.
- Quality bar: a real SMB owner can sign up, paste their meeting notes, and send a proposal to their client that's better than what they'd produce manually.

#### Phase 1.1: Refinement (Months 3-4)
- Voice memo support, advanced pricing models, inline AI prompting, learning loop, e-signature, Google Suite integration, dashboard analytics.

#### Phase 2: Growth (Months 4-6)
- Team seats, approval workflows, transcript support, CRM integration, calendar integration, payment integration.

#### Phase 3: Platform (Months 6-12)
- Embedded widget with conversational UI and conditional routing, public API, native mobile app, advanced analytics.

For each phase:
- Features included and excluded (and why).
- Technical prerequisites and infrastructure changes.
- Team size and role requirements.
- Key risks and mitigation strategies.
- Success metrics (from PRD Section 12).
- Estimated infrastructure cost at that phase's scale.

### 9. Build vs. Buy Decision Matrix

For every component below, decide: Build, Buy (SaaS), or Open Source. Justify based on: cost at scale, time to implement, maintenance burden, strategic differentiation.

| Component | Decision | Justification |
|---|---|---|
| Authentication (email/password + Google OAuth) | ? | |
| Multi-tenant data layer | ? | |
| Rate Card engine (pricing computation) | ? | |
| Document parsing (PDF/DOCX → text) | ? | |
| LLM orchestration (prompt chaining, retries) | ? | |
| Speech-to-text (Phase 1.1) | ? | |
| Proposal structured editor | ? | |
| PDF generation | ? | |
| Web link hosting / proposal viewer | ? | |
| Open/view tracking | ? | |
| Email delivery (transactional) | ? | |
| Billing / subscription management | ? | |
| Feature flags | ? | |
| Background job processing | ? | |
| File storage (uploads, generated PDFs) | ? | |
| CDN | ? | |
| Error tracking / monitoring | ? | |
| Analytics / metering | ? | |
| E-signature (Phase 1.1) | ? | |
| RTL rendering (Hebrew) | ? | |

Identify which are "commodity" (outsource/buy) vs. "core" (build and own because it's our competitive advantage).

### 10. Risk Register & Technical Debt Strategy

Identify the top 10 **technical** risks (product risks are in the PRD).

For each:
- Likelihood (High/Medium/Low)
- Impact (Critical/High/Medium/Low)
- Early warning signal
- Mitigation strategy
- Contingency plan

**Known risks from PRD to address:**
- R-1: LLM output quality too generic (prompt engineering, voice calibration)
- R-2: Hebrew output quality (model selection, testing)
- R-4: LLM costs at sub-$10 tier (cost modeling, caching, model selection)
- R-6: Multi-tenant data leak (isolation strategy, testing)

**Technical debt policy:**
- What shortcuts are acceptable in Phase 0 that you KNOW you'll need to fix?
- What is the specific trigger for paying them down? (User count? Error rate? Feature dependency?)
- What must be done RIGHT from day one because it's nearly impossible to fix later?

---

## Output Format

Structure your response as a complete technical design document with:

1. **Executive Summary** (1 page): Key architectural decisions, tech stack summary, critical path.

2. **Detailed sections** for each of the 10 areas above. Every section must include specific, named technologies and concrete schemas/structures — not abstract descriptions.

3. **Architecture Diagrams** (Mermaid syntax):
   - System overview (all components and their interactions)
   - Data flow (context input → proposal output, showing every transformation)
   - Infrastructure topology (cloud services, their connections, data flow)
   - Proposal lifecycle state machine (all states and transitions)
   - LLM pipeline (every call, input/output, caching points)

4. **Data Model Diagrams** (Mermaid ER syntax):
   - Complete entity-relationship diagram showing all tables, relationships, and key fields.

5. **Dependency Graph**: Critical path for implementation — what blocks what.

6. **Decisions Log**: Table summarizing every major decision, alternatives considered, rationale, and revisit trigger.

7. **Cost Model**: Estimated infrastructure + LLM cost at each scaling stage.

Be opinionated. Be specific. Name actual technologies, services, and libraries. Do not hedge with "it depends" — make the call, show your reasoning, and note the conditions under which the decision should be revisited.
