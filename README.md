# Fullstory Skills Repository (FSR)

> A comprehensive collection of Agent Skills for expert-level Fullstory semantic decoration guidance.

## 🎯 What is FSR?

The **Fullstory Skills Repository (FSR)** is a structured collection of AI Agent Skills that provide expert-level guidance for Fullstory semantic decoration for digital experiences (FSD). FSD enables AI coding assistants to decorate digital experiences with semantic meaning. This exposes behavioral digital experience usage to Computer Use Agents and unlocks high fidelity analytic measurement directly in the DOM or mobile app view tree:

- Guide developers through API implementation with good/bad examples
- Apply industry-specific privacy requirements automatically
- Reference regulatory compliance patterns (HIPAA, PCI, GDPR, etc.)
- Recommend the right API for each use case

---

## 🆕 Latest Enhancements

### Platform-Split Architecture (NEW)
All **core skills** and **framework skills** now use a three-file structure:
- **`SKILL.md`** — Platform-agnostic core concepts, API parameters, best practices (START HERE)
- **`SKILL-WEB.md`** — JavaScript/TypeScript implementation for web
- **`SKILL-MOBILE.md`** — iOS (Swift), Android (Kotlin), Flutter (Dart), React Native

**Always read SKILL.md first** for concepts, then the platform-specific file for implementation code.

### SDK Installation vs API Usage
The skills repository covers **API usage** (how to identify users, track events, etc.). For **SDK installation**, see official Fullstory documentation:
- **Web**: [Getting Started with Web](https://developer.fullstory.com/browser/getting-started/)
- **Mobile**: See `meta/fullstory-getting-started/SKILL.md` for links to all mobile platform installation guides

### Mobile Instrumentation Orchestrator (NEW)
The new `mobile-instrumentation-orchestrator` skill provides sequencing logic for mobile SDK implementation:
- **Privacy → Identity → Navigation → Interaction → Diagnostics**
- Routes to appropriate `SKILL-MOBILE.md` files for each platform

### Stable Selectors — Now Cross-Platform
The stable selectors skill now covers **both web and mobile**:
- **Web**: `data-component`, `data-element`, `data-action` attributes
- **iOS**: `accessibilityIdentifier`
- **Android**: `testTag`, `contentDescription`
- **React Native**: `testID`
- **Flutter**: `Key`, `Semantics`

### Privacy & Cookie Documentation
- **Private by Default Mode**: Complete documentation for Fullstory's privacy-first capture mode
- **First-Party Cookie Architecture**: `fs_uid` cookie behavior, session merging, and identity persistence
- **Anonymous User Support**: Clarified that user properties work before identification

### Industry-Specific Updates
| Industry | New Content |
|----------|-------------|
| **Banking** | Open Banking/PSD2 requirements |
| **Healthcare** | HIPAA de-identification standards (18 Safe Harbor identifiers) |
| **E-commerce** | Marketplace/multi-vendor considerations |
| **Gaming** | Fraud detection (7 types), game iframe decoration, responsible gaming compliance |
| **SaaS** | AI/ML feature tracking patterns |
| **Travel** | TSA Secure Flight requirements |
| **Media** | Accessibility feature tracking (WCAG compliance) |

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

## KEY TAKEAWAYS FOR AGENT

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

### Core API Skills (12 skills × 3 files = 36 files)

Technical implementation guides for each Fullstory API. **Each skill has three files:**

| File | Content |
|------|---------|
| `SKILL.md` | Core concepts, API parameters, best practices (platform-agnostic) |
| `SKILL-WEB.md` | JavaScript/TypeScript implementation |
| `SKILL-MOBILE.md` | iOS, Android, Flutter, React Native implementation |

| Skill | Purpose | Platforms |
|-------|---------|-----------|
| `fullstory-identify-users` | Link sessions to users | Web + Mobile |
| `fullstory-anonymize-users` | End identified sessions | Web + Mobile |
| `fullstory-user-properties` | Set user attributes | Web + Mobile |
| `fullstory-page-properties` | Set page/screen context | Web + Mobile |
| `fullstory-element-properties` | Capture interaction-level data | Web + Mobile |
| `fullstory-analytics-events` | Track discrete business events | Web + Mobile |
| `fullstory-privacy-controls` | Implement masking/exclusion | Web + Mobile |
| `fullstory-user-consent` | GDPR/CCPA consent management | Web + Mobile |
| `fullstory-capture-control` | Pause/resume recording | Web + Mobile |
| `fullstory-observe-callbacks` | Session URL and lifecycle events | Web only* |
| `fullstory-logging` | Error and debug logging | Web + Mobile |
| `fullstory-async-methods` | Promise-based API patterns | Web only* |

*Web-only APIs have mobile equivalent patterns documented in their `SKILL-MOBILE.md` files.

### Meta/Strategy Skills (4)

Strategic guidance for implementation planning. Meta skills **orchestrate** which core skills to use and in what order.

| Skill | Purpose |
|-------|---------|
| `fullstory-getting-started` | **THE definitive entry point** — skill architecture, platform routing, SDK installation links |
| `fullstory-privacy-strategy` | Decision framework for data privacy |
| `universal-data-scoping-and-decoration` | Where to put data (user vs page vs element vs event) |
| `mobile-instrumentation-orchestrator` | Sequencing logic for mobile SDK implementation (Privacy → Identity → Navigation → Events) |

### Industry-Specific Skills (7)

Tailored guidance for specific verticals:

| Industry | Skill | Key Focus Areas |
|----------|-------|-----------------|
| **Banking & Financial Services** | `fullstory-banking` | PCI DSS, GLBA, SOX; transaction masking; MFA flows |
| **E-commerce & Retail** | `fullstory-ecommerce` | Conversion funnels; cart abandonment; product tracking |
| **Gaming** | `fullstory-gaming` | Fraud detection; responsible gaming compliance; game iframe decoration; KYC/AML |
| **Healthcare** | `fullstory-healthcare` | HIPAA; PHI exclusion; BAA requirements |
| **B2B SaaS** | `fullstory-saas` | Feature adoption; onboarding; churn prediction |
| **Travel & Hospitality** | `fullstory-travel` | Booking funnels; ancillaries; passport/ID exclusion |
| **Media & Entertainment** | `fullstory-media-entertainment` | Video tracking; subscriptions; COPPA compliance |

### Framework Integration Skills (2 skills × 3 files = 6 files)

| Skill | Purpose |
|-------|---------|
| `fullstory-stable-selectors` | Universal pattern for stable identifiers across ALL platforms |
| `fullstory-test-automation` | Test script generation leveraging Fullstory decoration |

**File Structure (same three-file pattern as core skills):**
| File | Content |
|------|---------|
| `SKILL.md` | Core concepts (platform-agnostic) |
| `SKILL-WEB.md` | Web implementation (JavaScript/TypeScript) |
| `SKILL-MOBILE.md` | Mobile implementation (iOS, Android, Flutter, React Native) |

> **Why Stable Selectors?** Modern build tools generate dynamic identifiers that change every build—CSS class hashes on web, auto-generated view IDs on mobile. Stable semantic identifiers ensure Fullstory searches, defined elements, and click maps work reliably across deployments.

#### CUA Readiness (Computer User Agents)

The stable selectors skill prepares your application for AI-driven automation:

```
┌─────────────────────────────────────────────────────────────────┐
│  Without Stable Selectors (Brittle)                              │
│  Web: <button class="sc-abc123 xyz789">                          │
│  iOS: UIButton at 0x7f8b4c0123a0                                │
│  → Identifiers change every build/launch, AI navigation breaks  │
├─────────────────────────────────────────────────────────────────┤
│  With Stable Selectors (Resilient)                               │
│  Web: <button data-component="Checkout" data-element="pay">     │
│  iOS: accessibilityIdentifier = "Checkout.pay"                  │
│  → Semantic, stable, machine-readable forever                   │
└─────────────────────────────────────────────────────────────────┘
```

#### Platform Coverage

| Platform | Stable Identifier Mechanism |
|----------|----------------------------|
| React / Next.js / Vue / Angular | `data-component`, `data-element` attributes |
| Svelte / Solid / Astro | `data-component`, `data-element` attributes |
| iOS (Swift / SwiftUI) | `accessibilityIdentifier` |
| Android (Kotlin / Compose) | `contentDescription`, `testTag` |
| React Native | `testID` |
| Flutter | `Key`, `Semantics` |

No external plugins required—use native platform mechanisms with consistent naming conventions.

---

## 🏭 Industry Privacy Comparison

Different industries have vastly different requirements for Fullstory implementation:

### Private by Default Recommendation

Fullstory offers a **Private by Default mode** that inverts the capture default—everything is masked unless explicitly unmasked. This is the recommended approach for high-sensitivity industries:

| Industry | Private by Default? | Rationale |
|----------|---------------------|-----------|
| **Banking** | ✅ **Highly recommended** | Financial data, regulatory requirements |
| **Healthcare** | ✅ **Required** | HIPAA PHI protection |
| **SaaS (Enterprise)** | ⚠️ **Recommended** | Customer data in multi-tenant apps |
| **Gaming** | ⚠️ **Consider** | Financial + responsible gaming data |
| **Travel** | ⚠️ **Consider** | Passport, payment data |
| **E-commerce** | ❌ Usually not needed | Product data should be visible |
| **Media** | ❌ Usually not needed | Content data is the analytics |

> **Enable Private by Default**: Contact [Fullstory Support](https://help.fullstory.com/hc/en-us/requests/new) or select during account setup.
> 
> **Reference**: [Fullstory Private by Default](https://help.fullstory.com/hc/en-us/articles/360044349073-Fullstory-Private-by-Default)

### Privacy Defaults by Industry

| Industry | Default Privacy Mode | Financial Data | User Content | Conversion Tracking | Primary Concern |
|----------|---------------------|----------------|--------------|---------------------|-----------------|
| **Banking** | Exclude | Exclude (ranges only) | Exclude | Limited | Regulatory (PCI, GLBA) |
| **E-commerce** | Unmask | Capture (orders) | Mostly capture | Rich | Conversion optimization |
| **Gaming** | Mixed | Exclude (ranges only) | Exclude | Careful | Responsible gaming |
| **Healthcare** | Exclude | Exclude | Exclude | Very limited | HIPAA compliance |
| **SaaS** | Unmask | Usually OK | Mask/Consider | Rich | Feature adoption |
| **Travel** | Unmask | Capture (bookings) | Mask | Rich | Booking optimization |
| **Media** | Unmask | N/A | Capture | Rich | Engagement metrics |

### What to Capture by Industry

| Data Type | Banking | E-commerce | Gaming | Healthcare | SaaS | Travel | Media |
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
| **Gaming** | Gaming licenses, AML/KYC | Never analyze gaming patterns; exclude amounts |
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
├── core/                              # 12 Core API Skills (36 files)
│   ├── fullstory-analytics-events/
│   │   ├── SKILL.md                   # Core concepts (platform-agnostic)
│   │   ├── SKILL-WEB.md               # JavaScript/TypeScript implementation
│   │   └── SKILL-MOBILE.md            # iOS, Android, Flutter, React Native
│   ├── fullstory-anonymize-users/
│   │   ├── SKILL.md
│   │   ├── SKILL-WEB.md
│   │   └── SKILL-MOBILE.md
│   ├── fullstory-async-methods/       # (web-only API)
│   ├── fullstory-capture-control/
│   ├── fullstory-element-properties/
│   ├── fullstory-identify-users/
│   ├── fullstory-logging/
│   ├── fullstory-observe-callbacks/   # (web-only API)
│   ├── fullstory-page-properties/
│   ├── fullstory-privacy-controls/
│   ├── fullstory-user-consent/
│   └── fullstory-user-properties/
│
├── meta/                              # 4 Meta/Strategy Skills
│   ├── fullstory-getting-started/
│   ├── fullstory-privacy-strategy/
│   ├── mobile-instrumentation-orchestrator/  # Mobile SDK sequencing
│   └── universal-data-scoping-and-decoration/
│
├── industry/                          # 7 Industry-Specific Skills
│   ├── fullstory-banking/
│   ├── fullstory-ecommerce/
│   ├── fullstory-gaming/
│   ├── fullstory-healthcare/
│   ├── fullstory-media-entertainment/
│   ├── fullstory-saas/
│   └── fullstory-travel/
│
└── framework/                         # 2 Framework Integration Skills (6 files)
    ├── fullstory-stable-selectors/
    │   ├── SKILL.md                   # Core concepts (platform-agnostic)
    │   ├── SKILL-WEB.md               # Web: data-* attributes
    │   └── SKILL-MOBILE.md            # Mobile: accessibilityIdentifier, testID, Key
    └── fullstory-test-automation/
        ├── SKILL.md                   # Core concepts (platform-agnostic)
        ├── SKILL-WEB.md               # Web test generation
        └── SKILL-MOBILE.md            # Mobile test generation
```

---

## 📖 Skill Format

### Platform-Split Structure (Core Skills)

Core skills use a **three-file structure** for platform-specific implementation:

```
fullstory-[skill-name]/
├── SKILL.md           # Core concepts (platform-agnostic)
├── SKILL-WEB.md       # Web implementation (JavaScript/TypeScript)
└── SKILL-MOBILE.md    # Mobile implementation (iOS, Android, Flutter, RN)
```

**SKILL.md Frontmatter:**
```yaml
---
name: fullstory-skill-name
version: v3
description: Core concepts for...
platforms: [web, ios, android, flutter, react-native]
implementation_files: [SKILL-WEB.md, SKILL-MOBILE.md]
related_skills:
  - other-skill-1
---
```

**SKILL-WEB.md / SKILL-MOBILE.md Frontmatter:**
```yaml
---
name: fullstory-skill-name-web  # or -mobile
version: v3
platform: web  # or platforms: [ios, android, flutter, react-native]
parent_skill: fullstory-skill-name
related_skills:
  - other-skill-1
---
```

### Standard Sections

**SKILL.md (Core):**
1. **Overview** - What the API/concept does
2. **Core Concepts** - Key principles (platform-agnostic)
3. **API Parameters** - Parameters, types, limits
4. **Best Practices** - Universal guidance
5. **Troubleshooting** - Common issues
6. **Key Takeaways for Agent** - Platform routing logic
7. **Reference Links** - All platform docs

**SKILL-WEB.md / SKILL-MOBILE.md (Implementation):**
1. **Navigation blockquote** - Links to sibling files
2. **API Reference** - Platform-specific syntax
3. **✅ GOOD Examples** - Correct implementations
4. **❌ BAD Examples** - Anti-patterns with corrections
5. **Common Patterns** - Framework-specific (React, Vue, etc. OR iOS, Android, etc.)
6. **Troubleshooting** - Platform-specific issues

---

## 🚀 Getting Started

### The Golden Rule: SKILL.md First

For any core skill, **always read SKILL.md first** for concepts, then the platform-specific file for implementation:
- `SKILL.md` → Core concepts, API parameters, best practices
- `SKILL-WEB.md` → JavaScript/TypeScript implementation
- `SKILL-MOBILE.md` → iOS, Android, Flutter, React Native implementation

### Where to Start

1. **New to Fullstory?** Start with `meta/fullstory-getting-started/SKILL.md`
2. **Know your industry?** Jump to the relevant `industry/` skill
3. **Need specific API help?** Read core skill's `SKILL.md` first, then platform file
4. **Planning privacy?** See `meta/fullstory-privacy-strategy/SKILL.md`
5. **Building mobile?** See `meta/mobile-instrumentation-orchestrator/SKILL.md` for sequencing

### Quick Decision Tree

```
What do you need help with?
│
├─ "I'm just getting started"
│  └─ → meta/fullstory-getting-started/SKILL.md (THE entry point)
│
├─ "Which API should I use?"
│  └─ → meta/universal-data-scoping-and-decoration/SKILL.md
│
├─ "How do I identify users?"
│  └─ → core/fullstory-identify-users/SKILL.md (concepts first)
│     └─ Then → SKILL-WEB.md or SKILL-MOBILE.md (implementation)
│
├─ "What should I mask vs exclude?"
│  └─ → core/fullstory-privacy-controls/SKILL.md (concepts first)
│     └─ Then → SKILL-WEB.md or SKILL-MOBILE.md (implementation)
│
├─ "I work in [industry]"
│  └─ → industry/fullstory-[industry]/SKILL.md
│
├─ "What order should I implement mobile SDK?"
│  └─ → meta/mobile-instrumentation-orchestrator/SKILL.md
│     (sequences which core SKILL.md files to read)
│
├─ "My CSS class names keep changing" (Web)
│  └─ → framework/fullstory-stable-selectors/SKILL.md → SKILL-WEB.md
│
└─ "My view IDs are unstable" (Mobile)
   └─ → framework/fullstory-stable-selectors/SKILL.md → SKILL-MOBILE.md
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

| Category | Skills | Files | Focus |
|----------|--------|-------|-------|
| Core API | 12 | 36 | Technical implementation (SKILL.md + SKILL-WEB.md + SKILL-MOBILE.md) |
| Meta/Strategy | 4 | 4 | Planning, architecture, orchestration |
| Industry | 7 | 7 | Vertical-specific guidance |
| Framework | 2 | 6 | Stable selectors + test automation (SKILL.md + SKILL-WEB.md + SKILL-MOBILE.md) |
| **Total** | **25** | **53** | Complete Fullstory coverage across all platforms |

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

*FSR - Enabling expert-level Fullstory semantic decoration guidance through Agent Skills*

