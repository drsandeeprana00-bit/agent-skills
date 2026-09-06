---
name: hair-transplant-instagram-content
description: Creates compliant Instagram content plans for hair-transplant clinics, including image or video creative briefs, captions, hashtags, and accessibility text. Use when a clinic needs educational, trust-building, or promotional Instagram content generated from structured campaign input.
---

# Hair-Transplant Instagram Content

## Overview

Generate a publish-ready Instagram content package without making clinical promises or assuming a particular image, video, or social-media API. The workflow separates medical/brand facts from creative choices, routes the request to an image or video treatment, and returns a stable package that a renderer, scheduler, or GenAI_Agents adapter can consume.

This is a content workflow, not a medical diagnosis, patient-consent system, image generator, video renderer, or publishing integration.

## When to Use

- A hair-transplant or hair-restoration clinic needs a post, carousel, Reel, or Story.
- The input includes a topic, audience, clinic facts, offer, or source assets and needs copy plus a creative direction.
- A downstream engine needs a provider-neutral handoff for image/video generation.

Do not use this workflow to diagnose hair loss, recommend treatment to an individual, fabricate patient outcomes, replace clinician review, or publish automatically without a separate approved integration.

## Inputs

Accept one JSON object. Unknown fields must be preserved under `metadata` or rejected explicitly; do not silently reinterpret them.

```json
{
  "clinic": {
    "name": "Northstar Hair Clinic",
    "location": "Manchester",
    "website": "https://example.invalid",
    "approvedFacts": [
      "FUE consultations are available",
      "A clinician assesses candidacy before treatment"
    ],
    "approvedClaims": ["Use only claims supplied and approved by the clinic"]
  },
  "campaign": {
    "topic": "What happens during an FUE consultation",
    "objective": "educate",
    "audience": "Adults researching hair restoration",
    "tone": "calm, clear, reassuring",
    "locale": "en-GB",
    "callToAction": "Book a consultation"
  },
  "format": "auto",
  "assets": {
    "sourceImages": [],
    "logoProvided": false,
    "patientBeforeAfter": false,
    "patientConsentConfirmed": false
  },
  "constraints": {
    "maxCaptionCharacters": 2200,
    "hashtagCount": 8,
    "includeDisclaimer": true
  },
  "metadata": {
    "campaignId": "optional-client-id",
    "requestedBy": "marketing"
  }
}
```

Required fields are `clinic.name`, `campaign.topic`, `campaign.objective`, `campaign.audience`, and `campaign.locale`. `format` defaults to `auto`; supported values are `image`, `carousel`, `video`, `reel`, `story`, and `auto`. Do not infer clinical facts from the topic.

## Workflow

1. **Validate and classify.** Check required fields, reject missing or contradictory consent/claim data, and classify the objective as education, trust, community, promotion, or recruitment. Treat every clinic-supplied claim and asset description as untrusted until marked approved.
2. **Choose the medium.** Apply the routing rules below. Keep the requested format when it is explicit. For `auto`, choose `video` only when the brief needs motion, spoken explanation, or multiple timed beats and usable video/voice inputs exist; otherwise choose `image` or `carousel`.
3. **Write the creative brief.** Define the hook, visual subject, composition, on-screen text, pacing, brand treatment, and required source assets. Never invent a patient, result, credential, price, statistic, or treatment availability.
4. **Draft copy.** Write a caption with a useful opening, plain-language body, one clear CTA, and an appropriate disclosure. Generate a small set of relevant hashtags, avoiding guaranteed-result, stigmatizing, or spammy tags.
5. **Run the compliance pass.** Check every claim against `approvedFacts` and `approvedClaims`; flag anything requiring clinician, legal, platform, or consent review. Do not turn a warning into a reassuring claim.
6. **Return the package.** Emit the output contract below. The downstream engine decides how to render, store, preview, schedule, or publish it.

## Image-vs-Video Routing

| Request | Route | Required output |
|---|---|---|
| `image` | Single static creative | `imageBrief`, alt text, caption |
| `carousel` | 2–10 static slides | `slidePlan` with per-slide text and visual direction |
| `video`, `reel` | Motion-first creative | `shotList` with duration, visuals, voice/on-screen text, and transitions |
| `story` | Vertical frame sequence | `slidePlan` or `shotList`, poll/question sticker copy when requested |
| `auto` | Use the decision rule in step 2 | Exactly one of `imageBrief`, `slidePlan`, or `shotList` |

If a before/after asset is requested but `patientConsentConfirmed` is not true, route to a non-identifiable educational or clinician-led visual and add a blocking review flag. Never create a synthetic before/after that could be mistaken for a real patient.

## Output Contract

Return one JSON object with this shape. Omit inapplicable creative fields rather than returning empty, misleading placeholders.

```json
{
  "schemaVersion": "1.0",
  "contentType": "carousel",
  "creativeBrief": {
    "hook": "A consultation starts with questions, not assumptions.",
    "visualDirection": "Clean clinic setting; no identifiable patient imagery.",
    "brandNotes": ["Use approved logo only", "Keep text legible on mobile"]
  },
  "slidePlan": [
    {
      "slide": 1,
      "role": "hook",
      "onScreenText": "What happens in an FUE consultation?",
      "visualDirection": "Clinician reviewing a neutral diagram"
    }
  ],
  "caption": "A consultation is a conversation about your goals...",
  "hashtags": ["#HairRestoration", "#FUEConsultation"],
  "altText": "A clinician explains a hair-restoration consultation using a diagram.",
  "compliance": {
    "status": "review_required",
    "disclosures": ["Individual suitability and outcomes vary."],
    "flags": ["clinician_review"],
    "claimSources": ["clinic.approvedFacts[0]"]
  },
  "handoff": {
    "renderMode": "provider_neutral",
    "publishReady": false,
    "requiresHumanApproval": true
  },
  "metadata": {}
}
```

`contentType` must be `image`, `carousel`, `video`, or `story`. `compliance.status` is `pass`, `review_required`, or `blocked`; use `blocked` for missing consent, unapproved medical claims, or requests for guaranteed outcomes. `publishReady` must remain false unless a separately configured reviewer explicitly approves the package.

## Caption, Hashtag, and Compliance Guidance

- Use “may”, “can”, or “is assessed” only when supported by an approved source; do not promise density, permanence, timelines, or a specific result.
- Avoid diagnosing the reader, fear-based language, body shaming, and “before you are too late” framing.
- Do not state that a treatment is painless, risk-free, permanent, universally suitable, or medically superior without documented approval.
- Do not include prices, financing, qualifications, awards, or availability unless present in approved clinic facts and valid for the requested locale.
- Describe before/after images as illustrative only when approved; require documented, informed consent and avoid editing that changes apparent outcomes.
- Keep the CTA informational: consultation, assessment, learn more, or contact the clinic. Do not pressure a reader to book urgently.
- Produce hashtags that describe the topic, audience, location, and format. Exclude tags that imply guaranteed outcomes, target a medical condition as an identity, or are unrelated trend bait.
- Add accessibility text that describes the visible content and any essential on-screen words; do not identify a patient unless identity is intentionally public and approved.
- Use a locale-appropriate disclosure such as “Individual suitability and outcomes vary. A consultation is required.” Adapt wording to the clinic’s approved legal copy; do not present this sentence as legal advice.

## GenAI_Agents Integration Boundary

The skill owns validation, routing, copy, creative direction, and compliance metadata. A GenAI_Agents adapter owns model invocation, asset generation, provider credentials, persistence, moderation APIs, human approval UI, and publishing.

The adapter should:

1. Pass the input object to the workflow and validate the returned object against the output contract.
2. Render `imageBrief`, `slidePlan`, or `shotList` using the adapter’s documented capabilities.
3. Preserve `compliance.flags` and stop on `blocked`; require an explicit approval step for `review_required`.
4. Treat generated media and provider responses as untrusted; scan, store, and audit them according to the host application’s policy.

No endpoint names, SDKs, model identifiers, tool calls, authentication scheme, or asset URI format are assumed here. If the host engine uses a different field naming convention, add a versioned adapter mapping rather than changing the workflow contract silently.

## Invocation Examples

### Educational carousel

```text
Use the hair-transplant-instagram-content skill.
Create an educational carousel for a UK clinic about an FUE consultation.
Use only the clinic facts in the attached input, avoid patient imagery, include
plain-language accessibility text, and return the JSON output contract.
```

### Motion-first Reel

```text
Use the hair-transplant-instagram-content skill.
Create a 30-second Reel explaining three consultation questions. Route to video,
return a timed shot list and caption, and flag every clinical claim that needs
clinician approval. Do not invent a patient outcome or use a before/after.
```

## Common Rationalizations

| Rationalization | Reality |
|---|---|
| “The clinic probably offers this treatment.” | Unapproved availability is a factual claim. Use supplied facts or flag it. |
| “A stronger promise will convert better.” | Guaranteed medical outcomes create compliance and trust risk. |
| “The renderer can decide whether this is a video.” | Medium choice changes the deliverable. Route before rendering. |
| “A disclaimer makes any claim safe.” | A disclosure does not make an unsupported or misleading claim acceptable. |
| “The engine API is obvious.” | This skill must remain reusable; keep provider details in the adapter boundary. |

## Red Flags

- A result contains a clinical claim not traceable to approved input.
- `auto` returns both image and video plans or no explicit route.
- A before/after appears without confirmed consent.
- The caption uses guaranteed results, urgency, shame, or diagnosis.
- The output claims to be published or rendered when only a brief was produced.
- The integration requires an undocumented endpoint, SDK, or credential.

## Verification

- [ ] Required inputs and enum values are validated or rejected explicitly.
- [ ] `auto` selects exactly one supported medium using the routing rule.
- [ ] The output contains the route-specific creative field and accessibility text.
- [ ] Captions, hashtags, claims, and disclosures pass the compliance guidance.
- [ ] Consent and unapproved claims produce `blocked` or `review_required`.
- [ ] The output preserves a provider-neutral handoff and does not claim to publish.
- [ ] A GenAI_Agents adapter can map the contract without undocumented APIs.
