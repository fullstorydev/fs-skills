# Fullstory Skills Repository (FSR)

> A comprehensive collection of Agent Skills for expert-level Fullstory semantic decoration guidance.

## 🎯 What is FSR?

The **Fullstory Skills Repository (FSR)** is a structured collection of AI Agent Skills that provide expert-level guidance for Fullstory semantic decoration for digital experiences (FSD). FSD enables AI coding assistants to decorate digital experiences with semantic meaning. This exposes behavioral digital experience usage to Computer Use Agents and unlocks high fidelity analytic measurement directly in the DOM or mobile app view tree:

- Guide developers through API implementation with good/bad examples
- Apply industry-specific privacy requirements automatically
- Reference regulatory compliance patterns (HIPAA, PCI, GDPR, etc.)
- Recommend the right API for each use case

---

## 🤝 How to Contribute to FSR

We welcome contributions to expand and improve the Fullstory Skills Repository! Here's how to add or update skills:

### Adding a New Skill

1. **Choose the right category:**
   - `core/` - For specific Fullstory API implementations
   - `meta/` - For strategic guidance and decision frameworks
   - `industry/` - For industry-specific semantic decoration guidance
   - `framework/` - For frontend framework integration patterns

2. **Create the folder and file:**
   ```
   skills/[category]/[skill-name]/SKILL.md
   ```

3. **Use the standard skill template:**

```yaml
---
name: fullstory-your-skill-name
version: v2
description: Clear, comprehensive description of what this skill covers and when to use it.
related_skills:
  - fullstory-related-skill-1
  - fullstory-related-skill-2
---

# Fullstory [Skill Title]

> ⚠️ **LEGAL DISCLAIMER** (for industry skills only): This guidance is for educational purposes only...

## Overview

Brief explanation of what this skill covers and when developers should use it.

## Core Concepts

Key principles with visual diagrams (use ASCII art for compatibility):

```
┌─────────────────────────────┐
│  Visual concept diagrams    │
│  help Agent understand     │
└─────────────────────────────┘
```

## API Reference

### Basic Syntax

\`\`\`javascript
FS('methodName', {
  param1: 'value',
  param2: 'value'
});
\`\`\`

### Parameters

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `param1` | string | **Yes** | What it does |
| `param2` | object | No | Optional parameter |

---

## ✅ GOOD IMPLEMENTATION EXAMPLES

### Example 1: [Descriptive Title]

\`\`\`javascript
// GOOD: Explanation of why this is good
[code example]
\`\`\`

**Why this is good:**
- ✅ Reason 1
- ✅ Reason 2

---

## ❌ BAD IMPLEMENTATION EXAMPLES

### Example 1: [What's Wrong]

\`\`\`javascript
// BAD: Explanation of what's wrong
[bad code example]
\`\`\`

**Why this is bad:**
- ❌ Problem 1
- ❌ Problem 2

**CORRECTED VERSION:**
\`\`\`javascript
[corrected code]
\`\`\`

---

## TROUBLESHOOTING

### [Common Issue Title]

**Symptom**: What the developer sees
**Common Causes**: 
1. Cause 1
2. Cause 2

**Solutions**:
- ✅ Solution 1
- ✅ Solution 2

---

## KEY TAKEAWAYS FOR CLAUDE

When helping developers with [this topic]:

1. **Always emphasize**:
   - Key point 1
   - Key point 2

2. **Common mistakes to watch for**:
   - Mistake 1
   - Mistake 2

3. **Questions to ask developers**:
   - Question 1?
   - Question 2?

---

## REFERENCE LINKS

- **Official Docs**: https://developer.fullstory.com/...
- **Help Center**: https://help.fullstory.com/...

---

*This skill document was created to help Agent understand and guide developers...*
```

### Contribution Checklist

Before submitting your skill:

- [ ] YAML front matter includes `name`, `version: v2`, `description`, and `related_skills`
- [ ] Overview section clearly explains the skill's purpose
- [ ] Core Concepts include visual diagrams where helpful
- [ ] API Reference matches [official Fullstory documentation](https://developer.fullstory.com/)
- [ ] At least 3 GOOD examples with explanations
- [ ] At least 3 BAD examples with corrections
- [ ] Troubleshooting section covers common issues
- [ ] "Key Takeaways for Agent" section summarizes guidance
- [ ] Reference Links point to valid, official URLs
- [ ] Cross-references added to related skills (update their `related_skills` too!)
- [ ] Legal disclaimer included (for industry skills)
- [ ] Build tool warnings included (for CSS class-based features)

### Updating Existing Skills

1. **Always verify API syntax** against official documentation first
2. **Add examples** for new use cases or patterns
3. **Update `related_skills`** if new relevant skills exist
4. **Check reference links** are still valid
5. **Test examples** to ensure they work correctly

### Style Guidelines

- Use clear, descriptive section headers
- Include both code AND explanations
- Use tables for parameter documentation
- Use ASCII diagrams for visual concepts
- Use ✅/❌ emojis in good/bad examples
- Keep examples realistic and industry-relevant
- Avoid hallucinating API features - verify everything!

---

## 📚 Skill Categories

### Core API Skills (12)

Technical implementation guides for each Fullstory Browser API:

| Skill | Purpose |
|-------|---------|
| `fullstory-identify-users` | Link sessions to users via `setIdentity` |
| `fullstory-anonymize-users` | End identified sessions, handle logout |
| `fullstory-user-properties` | Set user attributes for segmentation |
| `fullstory-page-properties` | Set page context for Journeys |
| `fullstory-element-properties` | Capture interaction-level data |
| `fullstory-analytics-events` | Track discrete business events |
| `fullstory-privacy-controls` | Implement `fs-exclude`, `fs-mask`, `fs-unmask` |
| `fullstory-user-consent` | GDPR/CCPA consent management |
| `fullstory-capture-control` | Pause/resume recording |
| `fullstory-observe-callbacks` | Session URL and lifecycle events |
| `fullstory-logging` | Error and debug logging |
| `fullstory-async-methods` | Promise-based API patterns |

### Meta/Strategy Skills (3)

Strategic guidance for implementation planning:

| Skill | Purpose |
|-------|---------|
| `fullstory-getting-started` | Entry point with complete skill inventory and industry comparisons |
| `fullstory-privacy-strategy` | Decision framework for data privacy |
| `universal-data-scoping-and-decoration` | Where to put data (user vs page vs element vs event) |

### Industry-Specific Skills (7)

Tailored guidance for specific verticals:

| Industry | Skill | Key Focus Areas |
|----------|-------|-----------------|
| **Banking & Financial Services** | `fullstory-banking` | PCI DSS, GLBA, SOX; transaction masking; MFA flows |
| **E-commerce & Retail** | `fullstory-ecommerce` | Conversion funnels; cart abandonment; product tracking |
| **Gambling & Gaming** | `fullstory-gambling` | Responsible gambling; KYC/AML; ethical considerations |
| **Healthcare** | `fullstory-healthcare` | HIPAA; PHI exclusion; BAA requirements |
| **B2B SaaS** | `fullstory-saas` | Feature adoption; onboarding; churn prediction |
| **Travel & Hospitality** | `fullstory-travel` | Booking funnels; ancillaries; passport/ID exclusion |
| **Media & Entertainment** | `fullstory-media-entertainment` | Video tracking; subscriptions; COPPA compliance |

### Framework Integration Skills (1)

| Skill | Purpose |
|-------|---------|
| `fullstory-stable-selectors` | Universal pattern for stable `data-*` attributes across ALL frameworks |

> **Why Stable Selectors?** Modern CSS tools (CSS Modules, styled-components, Tailwind) generate dynamic class names that change every build. Stable `data-*` attributes ensure Fullstory searches, defined elements, and click maps work reliably across deployments.

This single skill covers implementation patterns for:
- React, Angular, Vue, Svelte
- Vanilla JavaScript & Web Components
- Server-side templates (PHP, Django, Rails, etc.)

No external plugins required—just add `data-component` and `data-element` attributes to your markup.

---

## 🏭 Industry Privacy Comparison

Different industries have vastly different requirements for Fullstory implementation:

### Privacy Defaults by Industry

| Industry | Default Privacy Mode | Financial Data | User Content | Conversion Tracking | Primary Concern |
|----------|---------------------|----------------|--------------|---------------------|-----------------|
| **Banking** | Exclude | Exclude (ranges only) | Exclude | Limited | Regulatory (PCI, GLBA) |
| **E-commerce** | Unmask | Capture (orders) | Mostly capture | Rich | Conversion optimization |
| **Gambling** | Mixed | Exclude (ranges only) | Exclude | Careful | Responsible gambling |
| **Healthcare** | Exclude | Exclude | Exclude | Very limited | HIPAA compliance |
| **SaaS** | Unmask | Usually OK | Mask/Consider | Rich | Feature adoption |
| **Travel** | Unmask | Capture (bookings) | Mask | Rich | Booking optimization |
| **Media** | Unmask | N/A | Capture | Rich | Engagement metrics |

### What to Capture by Industry

| Data Type | Banking | E-commerce | Gambling | Healthcare | SaaS | Travel | Media |
|-----------|---------|------------|----------|------------|------|--------|-------|
| User names | ❌ | ⚠️ Mask | ⚠️ Mask | ❌ | ⚠️ Mask | ⚠️ Mask | ⚠️ Mask |
| Email | ❌ | ⚠️ Hash | ⚠️ Hash | ❌ | ⚠️ Consider | ⚠️ Mask | ⚠️ Consider |
| Product/content names | N/A | ✅ | ✅ | N/A | ✅ | ✅ | ✅ |
| Prices/amounts | ❌ Ranges | ✅ | ❌ Ranges | ❌ | ✅ | ✅ | ✅ |
| Account balance | ❌ | N/A | ❌ | N/A | N/A | N/A | N/A |
| Transaction details | ❌ | ✅ | ❌ | ❌ | ✅ | ✅ | ✅ |
| Search queries | ⚠️ | ✅ | ⚠️ | ❌ | ✅ | ✅ | ✅ |
| Payment cards | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| Government IDs | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

**Legend**: ✅ = Capture, ⚠️ = Consider/Mask, ❌ = Exclude

### Key Regulatory Requirements

| Industry | Primary Regulations | Fullstory Requirements |
|----------|--------------------|-----------------------|
| **Banking** | PCI DSS, GLBA, SOX | Exclude all financial data; use ranges |
| **E-commerce** | PCI DSS, CCPA, GDPR | Exclude payment fields; consent for EU |
| **Gambling** | Gaming licenses, AML/KYC | Never analyze gambling patterns; exclude amounts |
| **Healthcare** | HIPAA, HITECH | BAA required; exclude ALL PHI; masking insufficient |
| **SaaS** | SOC 2, GDPR | Enterprise privacy options; consent for EU |
| **Travel** | PCI DSS, GDPR | Exclude passport/ID numbers; payment exclusion |
| **Media** | COPPA, GDPR | Never track children's profiles; consent |

---

## 📁 Repository Structure

```
skills/
├── README.md                          # This file
│
├── core/                              # 12 Core API Skills
│   ├── fullstory-analytics-events/
│   ├── fullstory-anonymize-users/
│   ├── fullstory-async-methods/
│   ├── fullstory-capture-control/
│   ├── fullstory-element-properties/
│   ├── fullstory-identify-users/
│   ├── fullstory-logging/
│   ├── fullstory-observe-callbacks/
│   ├── fullstory-page-properties/
│   ├── fullstory-privacy-controls/
│   ├── fullstory-user-consent/
│   └── fullstory-user-properties/
│
├── meta/                              # 3 Meta/Strategy Skills
│   ├── fullstory-getting-started/
│   ├── fullstory-privacy-strategy/
│   └── universal-data-scoping-and-decoration/
│
├── industry/                          # 7 Industry-Specific Skills
│   ├── fullstory-banking/
│   ├── fullstory-ecommerce/
│   ├── fullstory-gambling/
│   ├── fullstory-healthcare/
│   ├── fullstory-media-entertainment/
│   ├── fullstory-saas/
│   └── fullstory-travel/
│
└── framework/                         # 1 Framework Integration Skill
    └── fullstory-stable-selectors/    # Universal pattern for any framework
```

---

## 📖 Skill Format

All FSR skills follow a consistent format:

```yaml
---
name: skill-name
version: v2
description: Comprehensive description of the skill's purpose
related_skills:
  - other-skill-1
  - other-skill-2
---
```

### Standard Sections

1. **Overview** - What the API/concept does and when to use it
2. **Core Concepts** - Key principles with visual diagrams
3. **API Reference** - Syntax, parameters, limits
4. **✅ GOOD Examples** - Correct implementations with explanations
5. **❌ BAD Examples** - Anti-patterns with corrections
6. **Platform-Specific** - Web, iOS, Android guidance (where applicable)
7. **Troubleshooting** - Common issues and solutions
8. **Key Takeaways for Agent** - Summary guidance for AI assistance
9. **Reference Links** - Official documentation links

---

## 🚀 Getting Started

1. **New to Fullstory?** Start with `meta/fullstory-getting-started/SKILL.md`
2. **Know your industry?** Jump to the relevant `industry/` skill
3. **Need specific API help?** Check the relevant `core/` skill
4. **Planning privacy?** See `meta/fullstory-privacy-strategy/SKILL.md`

### Quick Decision Tree

```
What do you need help with?
│
├─ "Which API should I use?"
│  └─ → meta/universal-data-scoping-and-decoration
│
├─ "How do I identify users?"
│  └─ → core/fullstory-identify-users
│
├─ "What should I mask vs exclude?"
│  └─ → core/fullstory-privacy-controls
│
├─ "I work in [industry]"
│  └─ → industry/fullstory-[industry]
│
├─ "My CSS class names keep changing"
│  └─ → framework/fullstory-stable-selectors
│
├─ "How do I annotate elements for any framework?"
│  └─ → framework/fullstory-stable-selectors
│
└─ "I'm just getting started"
   └─ → meta/fullstory-getting-started
```

---

## 🔗 Cross-References

Skills are interconnected via the `related_skills` field in their YAML front matter. This enables Agent to:

- Navigate between related concepts
- Suggest additional relevant skills
- Build comprehensive implementation guidance

Example connections:
- `fullstory-identify-users` → `fullstory-anonymize-users`, `fullstory-privacy-strategy`
- `fullstory-privacy-controls` → `fullstory-banking`, `fullstory-healthcare`
- `fullstory-analytics-events` → `fullstory-ecommerce`, `fullstory-saas`

---

## 📊 Skill Statistics

| Category | Count | Focus |
|----------|-------|-------|
| Core API | 12 | Technical implementation |
| Meta/Strategy | 3 | Planning and architecture |
| Industry | 7 | Vertical-specific guidance |
| Framework | 1 | Stable selectors for any framework |
| **Total** | **23** | Complete Fullstory coverage |

---

## ⚠️ Important Notes

### API Verification

This repository documents Fullstory Browser API **v2**. Always verify API syntax against the [official documentation](https://developer.fullstory.com/browser/getting-started/) as the API may evolve.

**Official Documentation Links:**
- [Identify Users](https://developer.fullstory.com/browser/identification/identify-users/)
- [Anonymize Users](https://developer.fullstory.com/browser/identification/anonymize-users/)
- [Set User Properties](https://developer.fullstory.com/browser/identification/set-user-properties/)
- [Analytics Events](https://developer.fullstory.com/browser/capture-events/analytics-events/)
- [Set Page Properties](https://developer.fullstory.com/browser/set-page-properties/)
- [Capture Data (shutdown/restart)](https://developer.fullstory.com/browser/fullcapture/capture-data/)
- [User Consent](https://developer.fullstory.com/browser/fullcapture/user-consent/)
- [Callbacks and Delegates](https://developer.fullstory.com/browser/fullcapture/callbacks-and-delegates/)
- [Logging](https://developer.fullstory.com/browser/fullcapture/logging/)
- [Asynchronous Methods](https://developer.fullstory.com/browser/asynchronous-methods/)
- [Privacy (Help Center)](https://help.fullstory.com/hc/en-us/articles/360020623574)

### Legal Disclaimers

All industry-specific skills contain regulatory guidance for educational purposes only. This guidance **does not constitute legal advice**. Regulations (PCI DSS, HIPAA, GDPR, GLBA, etc.) are:
- Complex and nuanced
- Jurisdiction-specific
- Subject to change

**Always consult with your legal and compliance teams** before implementing data capture solutions.

### Build Tool Warning

Modern CSS build tools (Tailwind, PostCSS purge, CSS Modules) may remove "unused" classes. Privacy classes like `.fs-exclude`, `.fs-mask`, `.fs-unmask` must be:
1. Safelisted in your build configuration
2. Verified in production HTML
3. Tested in Fullstory to confirm they work

### Security Notes

- **Client-side hashing** is NOT a security control - it's a privacy measure only
- CSS classes can be inspected by users
- Always use server-side processing for true security requirements

---

## 📜 License

Internal use only - Fullstory Solutions Engineering

---

*FSR - Enabling expert-level Fullstory semantic decoration guidance through Agent Skills*

