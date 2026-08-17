---
name: planned-payment-facilitation
description: Prepare and confirm a Planned payment facilitation request from a contract attachment, resolving or explicitly creating the required event first. Use when a user wants Planned to facilitate payment for an event contract.
---

# Planned Payment Facilitation

Use `payment_facilitation_initiate` for the payment request. It requires an exact existing event and does not find or create one.

## Resolve the event

1. Use `event_get` when the event ID is known.
2. Otherwise use `event_list` to find accessible events. Client Admins should pass the most specific known filter; other roles must omit filters and receive only events they collaborate on.
3. Confirm that one exact event matches the contract. Never guess or silently attach a contract to the closest result.

If no suitable event exists, propose creating one. Show the exact known name, city, dates, and guest count, then ask the user whether to create it.

Never call `event_create` before explicit confirmation. A request for payment facilitation, a supplied contract, or approval of the payment preview is not confirmation to create an event.

After confirmation, call `event_create` with only supplied facts. If it returns `clarification_required`, ask its questions and continue with `previousState`; reconfirm the updated event proposal before any retry that may create it. Report and retain the exact created event ID.

## Prepare the request

1. Require the exact event ID and the contract attachment supplied by the harness.
2. Generate one stable `idempotencyKey` and reuse it for every follow-up or retry.
3. Call `payment_facilitation_initiate` with `action: prepare` and `document` on the first call.
4. If the result is `not_available`, stop and report its message.
5. For `confirmation_required`, present the exact event, contract filename, signing entity, defaults, and `preview.message`. Make clear that nothing has been sent.

## Confirm the request

Treat payment confirmation as a separate approval from event creation.

1. Ask the user to approve the exact payment preview.
2. If they request a message change, prepare a new preview using the returned `attachment`; do not confirm the old preview.
3. Only after explicit approval, call the tool with `action: confirm`, the same event ID and idempotency key, the returned `attachment`, and exact copies of `preview.message` and `previewHash`. Do not resend `document`.
4. Do not reconstruct or modify the approved message or hash.
5. Report `initiated`, the event URL, source document, and whether the request was reused.

On an ambiguous failure, keep the idempotency key and reconcile before retrying. Never create another event or send a second request automatically.
