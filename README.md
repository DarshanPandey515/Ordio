# Ordio

Ordio is a voice-first grocery ordering system. Customers place their grocery order by calling a phone
number and speaking it out, in a normal conversation, instead of filling in an app form.

> **Status:** the capabilities described below are intended behaviour, not shipped features. This
> repository contains project documentation only; no application code exists yet.

## Introduction

Many customers find app-based grocery ordering inconvenient: the download, the account setup, the search
box, the cart, and the checkout flow. At the same time, a large share of neighborhood grocery stores
already take orders by phone and have customers who prefer to place them that way.

Ordio is built around that overlap. The system sits between an inbound phone call and a merchant's
product catalog, and its purpose is to turn a natural spoken request into a structured, confirmed,
priced grocery order.

The value proposition is narrow and concrete:

- A customer can describe what they need in ordinary language rather than in a fixed form.
- The conversation is resolved against a real catalog, so the resulting order contains valid SKUs,
  quantities, and prices.
- Nothing is submitted until the customer has heard the full basket, the total, and the fulfilment
  details, and has explicitly confirmed them.

## How It Works

The intended order flow:

1. **The customer calls.** A call arrives at a phone number assigned to a merchant.
2. **The agent captures the request.** It records items, quantities, brand preferences, and fulfilment
   details from what the customer says.
3. **The catalog is consulted.** The captured items are matched against the merchant's catalog. Pricing
   and availability come from that catalog, not from the conversation.
4. **The agent clarifies.** When a reference is ambiguous or a match is uncertain, the agent asks a
   question rather than choosing silently. The customer can correct items, quantities, or brands at any
   point in the call.
5. **The basket is read back.** The agent reads out every line item, the total, and the delivery or
   pickup details, and waits for confirmation.
6. **The customer confirms.** An order is created only after an explicit spoken confirmation. Without
   it, no order is placed.
7. **The order is delivered.** The merchant receives the structured order on an order list, and the
   customer receives a confirmation by message where messaging is available.

Steps 1 through 7 describe the intended behaviour of the system. None of this flow is implemented in the
repository at present.

## Core Capabilities

The following capabilities are planned and not yet implemented:

- Natural-language grocery ordering over ordinary phone calls.
- Product matching of spoken requests against a merchant-provided catalog.
- Handling of quantities, brands, additions, removals, and corrections during the call.
- Catalog-backed pricing and stock validation.
- Explicit customer confirmation as a precondition for order creation.
- Persistent order records with a merchant-facing order list.
- Human handoff, or message taking, when the automated flow cannot complete a request.
- Call logging with transcripts, outcomes, and latency measurements.
- Support for Hindi-English code-switching, treated as a design goal rather than a solved problem.

## Design Principles

These constraints are intended to hold regardless of the underlying model or vendor chosen:

- **Catalog data is the source of truth.** Prices, availability, and totals come from validated catalog
  data. The conversational layer never invents them.
- **Ambiguity gets a question.** An unclear or low-confidence product reference is resolved by asking, not
  by silently picking one item.
- **Substitution needs consent.** Replacing an unavailable item with another is a suggestion that requires
  the customer's explicit agreement.
- **Confirmation is mandatory.** Order placement is preceded by a complete readback of the basket and an
  explicit spoken confirmation.
- **No card or UPI details over voice.** Payment card numbers, CVVs, and UPI credentials are never
  collected through the voice channel. Payment happens outside the call.
- **A human remains reachable.** Escalation to a person, or taking a message for later callback, must be
  available whenever the automated flow cannot proceed.
- **Recording is disclosed.** Call recording is announced to the caller, and personal data is collected,
  retained, and deleted only as far as the stated purpose requires.

## Architecture Overview

A conceptual decomposition of the system, independent of any specific provider:

| Component | Responsibility |
| --- | --- |
| Telephony | Inbound call handling, session lifecycle, media streaming, and call disposition. |
| Speech recognition | Streaming audio to text across the languages a merchant's customers actually speak. |
| Speech synthesis | Text to speech for prompts, questions, and the readback. |
| Conversational agent | Dialogue state, intent handling, and orchestration of the validated tools. |
| Validated tools | Catalog lookup, availability check, pricing, and order creation. Every tool call returns catalog-derived data. |
| Catalog and order store | Merchant catalogs, pricing, stock, order records, and the merchant order list. |
| Observability | Call logs, transcripts, outcomes, latency, and error capture. |

No telephony, speech, model, or hosting vendor has been selected. Provider names that appear in product
planning are candidates for evaluation, not dependencies of this repository.

## Project Status

The repository is at its initial setup and documentation stage. It contains this README and Git
configuration only. There is no application code, no dependency manifest, no database schema, and no test
suite yet. No release has been published, no merchant integration has been completed, and no performance
or accuracy results have been measured.

## Development Setup

No development environment is defined yet, because there is no application code to run. Implementation,
local development instructions, and tooling will be documented here as the codebase is established.

The only requirement at this point is Git:

```sh
git clone https://github.com/DarshanPandey515/Ordio.git
cd Ordio
```

## Repository Structure

```text
.
└── README.md
```

## Security and Privacy

- **Consent.** Call handling is disclosed to the caller at the start of the call, including that the call
  may be recorded. A caller who declines is not required to proceed through the automated flow.
- **Personal data.** Ordio intends to collect only what an order requires: the items, fulfilment details,
  and the minimum contact information needed to deliver or confirm the order. Data is retained only as
  long as the order and its audit trail require.
- **Call recordings and transcripts.** Recordings and transcripts may contain personal information. Access
  is restricted to what the ordering workflow and support of an order need.
- **Sensitive payment data.** Card numbers, CVVs, bank credentials, and UPI secrets are never solicited
  by the voice agent, never logged, and never stored. Payment is completed through a channel outside the
  automated call flow.
- **Order data.** Order records belong to the merchant and are used to fulfil the order and resolve
  disputes about it.

## License

Licensing has not yet been established. A license will be added here before any distribution of the
code.
