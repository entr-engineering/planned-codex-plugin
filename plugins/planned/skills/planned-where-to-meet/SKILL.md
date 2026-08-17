---
name: planned-where-to-meet
description: Recommend or compare meeting cities with Planned and optionally hand the selected result into event creation. Use for distributed-team location ideation, named-city comparisons, or refinement of a previous Where to Meet recommendation.
---

# Planned Where to Meet

Use `meeting_locations_recommend` for the recommendation. It is stateless and does not read, create, or change a Planned event.

## Recommend a location

1. Include every known origin and attendee count, exact dates or date flexibility, up to five named candidates, constraints, and preferences in `context`.
2. Never invent or silently default a requested input.
3. If the result is `clarification_required`, ask every returned `question`, in order, in the next response. Do not omit, combine, reword, or answer a question for the user.
4. Track which returned questions the user answered. If an answer is partial, ask only the unanswered questions. Do not call the tool again until every returned question has an answer. If the user cannot provide one, state that the recommendation cannot continue without it.
5. Call the tool again with `previousState` and all newly supplied answers. Repeat steps 3–5 for every subsequent `clarification_required` result.
6. If a `recommendations` result contains `conversation.nextQuestions`, treat those as requested inputs. Present the directional recommendations, then ask every `nextQuestion` in order. Do not offer event creation yet. Refine with `previousState` after all answers are supplied.
7. Present the final recommendations with their evidence, tradeoffs, and uncertainty only when no returned questions remain unanswered. Do not claim route details, travel times, prices, or suitability that the result does not support.
8. If no candidate qualifies, explain the returned reasons. Do not turn missing soft evidence into a disqualification.

## Offer event creation

Follow this sequence exactly when the result status is `recommendations` and no entry from returned `questions` or `conversation.nextQuestions` remains unanswered:

1. Present the recommendations.
2. End the response with: **“Would you like me to create a Planned event for one of these recommendations?”**
3. Do not call `event_create` until the user answers yes.
4. If the user says yes without choosing a city, ask them to choose one of the returned recommendations. Do not choose for them.
5. Collect any event details required by `event_create` that are not already known. Reuse the selected recommendation's city and the dates and guest count already confirmed during the recommendation flow.
6. Present one final proposal containing the event name, city, dates, and guest count. End with: **“Should I create this Planned event?”**
7. Call `event_create` only after the user explicitly confirms that final proposal. Include only user-supplied or recommendation-backed facts.
8. If `event_create` returns `clarification_required`, ask the returned questions and continue with `previousState`. If an answer changes the final proposal, present it again and repeat step 6 before retrying.
9. Report whether Planned created a published event or draft and include the returned URL.

Do not offer event creation for `clarification_required` or `no_valid_candidates`. Never treat selection of a recommendation as confirmation to create an event.
