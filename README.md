# Planned Codex plugin

Use [Planned](https://planned.com/) from Codex to choose where a distributed team should meet and to initiate payment facilitation for an event contract.

## What it does

### Recommend where to meet

The plugin gathers attendee origins, dates, candidate cities, constraints, and preferences before making a recommendation. It can:

- Compare up to five candidate cities.
- Explain the evidence, tradeoffs, and uncertainty behind each recommendation.
- Refine a recommendation as you provide more context.
- Create a Planned event for the selected city after you review and explicitly confirm the event details.

The recommendation itself is stateless: it does not read or modify an existing Planned event.

### Facilitate a contract payment

Attach an event contract and ask Planned to facilitate the payment. The plugin can:

- Resolve the contract to one exact Planned event.
- Propose creating an event when no suitable event exists.
- Prepare a payment-facilitation preview showing the event, contract, signing entity, defaults, and outgoing message.
- Initiate the request only after you explicitly approve that preview.

Event creation and payment initiation use separate confirmation steps. Preparing a preview does not send a request.

## Example prompts

- “Recommend where my distributed team should meet.”
- “Compare Toronto, Chicago, and New York for our October offsite.”
- “Start payment facilitation for this event contract.”

## Requirements

- A Planned account with access to the relevant events.
- Authentication with Planned when the plugin is installed.
- A contract attachment when initiating payment facilitation.

## Included workflows

The production plugin contains two guided skills:

- `planned-where-to-meet`
- `planned-payment-facilitation`

Both workflows use Planned's production MCP server and require explicit confirmation before actions that create an event or initiate a payment request.
