# Seedlink AI Abstraction Layer API (OpenAPI Contract)

This repository contains the official **OpenAPI specification** for the Seedlink **AI Abstraction Layer**.

The AI Abstraction Layer is a **stable orchestration API** that sits between AI channels (WhatsApp, webchat, call-centre tools) and the evolving Seedlink platform services (Commerce, LMS, Logistics/Drovvi).

It provides **AI-safe, permissioned, audited endpoints** for:

- Unified search
- Verified user context retrieval
- Safe, controlled actions
- Coaching slot reservations (10-minute hold model)
- Human handoff workflows
- Realtime event streaming

---

## Primary API Contract

```
openapi/seedlink-ai-abstraction-v1.yaml
```

This file defines all endpoints, schemas, and request/response contracts for:
---

## Core Platform Compatibility

This AI Abstraction Layer is designed to operate against:
Seedlink Core Platform API v1.4.0

It intentionally exposes a reduced, AI-safe surface area of the Core Platform.
It does not mirror the full Core contract.


- AI middleware (WhatsApp / webchat / call integrations)
- Future AI agents
- Human-agent handoff systems (CRM/helpdesk)
- Realtime notification systems
- Orchestrated access to Commerce + LMS + Drovvi

---

# Repository Structure

```
seedlink-ai-abstraction-api/
│
├── README.md
└── openapi/
    └── seedlink-ai-abstraction-v1.yaml
```

This repository contains the API contract only.  
It does not contain implementation code.

---

# Purpose of This Repository

This repository exists to:

1. Provide a **single source of truth** for AI integration contracts.
2. Decouple AI development timelines from Commerce/LMS/Drovvi implementation changes.
3. Standardise how AI reads and acts on realtime platform truth (prices, stock, orders, entitlements, bookings, deliveries).
4. Provide a controlled surface for auditing, permissions, and safe actions.
5. Enable SDK generation and contract-based testing.

This repository does **not** contain application code.  
It contains the **AI integration API contract only**.

---

# What the AI Abstraction Layer Does

## 1) Unified Search (AI-Friendly)

- Products and variants (Commerce)
- Courses/modules/coaching offers (LMS)
- Optional safe pointers (FAQs, policies)

Search results return AI-safe summaries, not raw database objects.

---

## 2) Verified User Context (AI-Safe)

When a user is verified (via JWT/OTP):

- Profile summary
- Recent orders + status
- Delivery/tracking summary (via Drovvi/logistics provider)
- Enrollments + progress summary
- Entitlements summary (courses/modules/coaching packs/discount access)

No sensitive information is returned without verified identity.

---

## 3) Safe, Audited Actions

The abstraction layer allows AI to trigger controlled platform actions:

- Create/update draft cart (optional)
- Start hosted checkout (optional)
- Reserve coaching slots (10-minute HOLD)
- Confirm coaching booking after verified payment
- Create human handoff tickets
- Trigger notifications

All actions are logged and auditable.

---

## 4) Realtime Events (SSE)

The AI middleware can subscribe to:

```
GET /ai/events/stream
```

Supported filters:

- `topics` (comma-separated)
- `event_types` (comma-separated)
- `since` (RFC3339 timestamp)

This supports realtime notifications such as:

- “Your order has been paid and is now being processed.”
- “Your delivery is in transit.”
- “Your course access has been granted.”
- “Your coaching session has been confirmed.”

---

# What This Layer Does NOT Do

- It is not a knowledge base.
- It is not the primary Commerce API.
- It is not the primary LMS API.
- It does not store full product catalogues.
- It does not expose raw internal service schemas.
- It does not allow high-risk operations without strict permission checks.

It is an orchestration and safety boundary.

---

# Security Model

Authentication:

- **Bearer JWT** for authenticated calls.
- Short-lived user-bound tokens for verified identity.

Auditing:

- Every AI call logs:
  - `conversation_id`
  - `channel`
  - `agent_id` (if applicable)
  - `request_id`
  - action outcome

Recommended headers:

- `Authorization: Bearer <token>`
- `X-Conversation-Id`
- `X-Channel`

---

# Coaching Booking Model (10-Minute Hold)

The abstraction layer enforces a safe booking lifecycle:

1. User selects slot → `/ai/coaching/reserve`
2. System places a HOLD (default 10 minutes)
3. If payment not confirmed:
   - Hold automatically expires
4. Payment confirmed → `/ai/coaching/confirm`
5. Booking is confirmed and meeting details (Zoom/Teams) are issued

This prevents double booking and ensures payment-first confirmation.

---

# View API Documentation Locally (Swagger UI)

## Option A — Docker (Recommended)

From repository root:

```bash
docker run --rm -p 8080:8080 \
  -e SWAGGER_JSON=/spec/seedlink-ai-abstraction-v1.yaml \
  -v "$(pwd)/openapi:/spec" \
  swaggerapi/swagger-ui
```

Open:

```
http://localhost:8080
```

---

## Option B — Redocly CLI

Install:

```bash
npm i -g @redocly/cli
```

Preview documentation:

```bash
redocly preview-docs openapi/seedlink-ai-abstraction-v1.yaml
```

---

# Validate the OpenAPI Specification

```bash
redocly lint openapi/seedlink-ai-abstraction-v1.yaml
```

Or:

```bash
swagger-cli validate openapi/seedlink-ai-abstraction-v1.yaml
```

---

# Generate SDKs

## TypeScript

```bash
npx @openapitools/openapi-generator-cli generate \
  -i openapi/seedlink-ai-abstraction-v1.yaml \
  -g typescript-fetch \
  -o sdk/typescript
```

## Kotlin

```bash
npx @openapitools/openapi-generator-cli generate \
  -i openapi/seedlink-ai-abstraction-v1.yaml \
  -g kotlin \
  -o sdk/kotlin
```

## Swift

```bash
npx @openapitools/openapi-generator-cli generate \
  -i openapi/seedlink-ai-abstraction-v1.yaml \
  -g swift5 \
  -o sdk/swift
```

---

# Governance Rules

1. All changes via Pull Request.
2. OpenAPI must pass lint validation before merge.
3. No breaking changes without version bump.
4. Keep repository private.
5. Avoid exposing sensitive internal data structures.

---

# Ownership

Seedlink Platform Architecture — AI Integration Contract

All contract modifications require architectural review.

---

End of README.

