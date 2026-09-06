# Hair-Transplant Instagram Content Skill

The `hair-transplant-instagram-content` skill is a provider-neutral workflow for creating Instagram copy and creative briefs for hair-transplant clinics. It supports static posts, carousels, Reels, and Stories while keeping clinical claims, consent, accessibility, and human approval explicit.

## Install

From a checkout of this repository, copy the skill into the host agent's skills directory:

```bash
cp -R skills/hair-transplant-instagram-content /path/to/your/agent/skills/
```

For a packaged install, use `skills/hair-transplant-instagram-content.zip` and extract the directory into the host's skills directory. The host agent must load `SKILL.md` as instructions; no runtime dependency is required.

## Invoke

Ask the host agent to use `hair-transplant-instagram-content` and provide a JSON input object or an equivalent natural-language request. For deterministic integrations, send the JSON contract documented in [the skill definition](../skills/hair-transplant-instagram-content/SKILL.md).

At minimum provide:

- Clinic name and locale
- Campaign topic, objective, and audience
- Approved facts and claims
- Requested format (`image`, `carousel`, `video`, `reel`, `story`, or `auto`)
- Asset and patient-consent state

## GenAI_Agents adapter boundary

The skill produces a JSON content package. It does not call GenAI_Agents, an image/video provider, Instagram, a moderation service, or a database. A host adapter must:

1. Submit validated input and validate the returned `schemaVersion`, `contentType`, route-specific field, and `compliance` object.
2. Render the returned creative brief with the host's documented model and asset APIs.
3. Stop for `blocked`; require explicit human approval for `review_required`.
4. Keep generated media, moderation results, consent evidence, and publication records outside this skill's output unless the host contract defines those fields.

No endpoint, SDK, model name, authentication method, or asset URI convention is assumed. Use a versioned field mapping if GenAI_Agents uses different names; do not silently change the contract.

## Example request

```json
{
  "clinic": {
    "name": "Northstar Hair Clinic",
    "location": "Manchester",
    "approvedFacts": [
      "FUE consultations are available",
      "A clinician assesses candidacy before treatment"
    ],
    "approvedClaims": []
  },
  "campaign": {
    "topic": "What happens during an FUE consultation",
    "objective": "educate",
    "audience": "Adults researching hair restoration",
    "tone": "calm, clear, reassuring",
    "locale": "en-GB",
    "callToAction": "Book a consultation"
  },
  "format": "carousel",
  "assets": {
    "patientBeforeAfter": false,
    "patientConsentConfirmed": false
  }
}
```

## Output and review

The returned package includes a route-specific `imageBrief`, `slidePlan`, or `shotList`, plus `caption`, `hashtags`, `altText`, and `compliance`. `publishReady` should remain `false` until the host's human approval process completes. The skill's compliance guidance is operational content guidance, not legal advice; clinics remain responsible for local advertising, medical, privacy, consent, and platform requirements.
