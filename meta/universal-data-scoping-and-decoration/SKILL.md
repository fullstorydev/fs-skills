---
name: fullstory-data-scoping-decoration
version: v2
description: Strategic meta-skill for Fullstory data semantic decoration. Teaches when to use page properties vs element properties vs user properties vs events. Provides a deterministic framework for scoping data at the right level to maximize searchability, avoid duplication, and maintain consistency. Essential foundation for proper Fullstory implementation across all API types.
related_skills:
  - fullstory-page-properties
  - fullstory-element-properties
  - fullstory-user-properties
  - fullstory-analytics-events
  - fullstory-identify-users
  - fullstory-getting-started
  - fullstory-privacy-controls
  - fullstory-privacy-strategy
---

# Universal Data Scoping and Decoration: The Unified Model (v4.0)

> A deterministic strategy for applying high-signal data attributes across web interfaces. Separate user context from page context from element context to maximize efficiency, searchability, and consistency.

---

## Overview

This meta-skill provides the strategic framework for deciding **where** to capture data in FullStory. Before implementing any FullStory API, use this guide to determine the appropriate scope for your data.

### The Data Hierarchy

```
┌─────────────────────────────────────────────────────────────┐
│  USER PROPERTIES (FS setIdentity / setProperties)           │
│  Scope: Across all sessions for this user                   │
│  Examples: plan, role, company, signup_date                 │
├─────────────────────────────────────────────────────────────┤
│  PAGE PROPERTIES (FS setProperties type: 'page')            │
│  Scope: Current page until URL path changes                 │
│  Examples: pageName, searchTerm, filters, productId         │
├─────────────────────────────────────────────────────────────┤
│  ELEMENT PROPERTIES (data-fs-properties-schema)             │
│  Scope: Individual element interactions                     │
│  Examples: itemId, position, variant, price                 │
├─────────────────────────────────────────────────────────────┤
│  EVENT PROPERTIES (FS trackEvent)                           │
│  Scope: Single discrete action                              │
│  Examples: orderId, revenue, source, action                 │
└─────────────────────────────────────────────────────────────┘
```

---

## I. Core Principles: Data Scope and Responsibility

### Rule 1: Capture Data at the Highest Relevant Scope

**Why?** 
- Reduces duplication
- Improves searchability
- Makes data inheritance work correctly
- Simplifies semantic decoration

**Decision Matrix:**

| Data Characteristic | Scope to Use | API |
|---------------------|--------------|-----|
| Same for all user sessions | User Properties | `setIdentity` / `setProperties(user)` |
| Same for entire page | Page Properties | `setProperties(page)` |
| Different per element on page | Element Properties | `data-fs-*` attributes |
| Discrete action/moment | Event | `trackEvent` |

### Rule 2: Never Duplicate Across Scopes

❌ **BAD**: Product ID on page AND on every element
```javascript
// Page properties
FS('setProperties', { type: 'page', properties: { productId: 'SKU-123' }});

// Also on element (REDUNDANT!)
<button data-product-id="SKU-123">Add to Cart</button>
```

✅ **GOOD**: Product ID at page level only
```javascript
// Page properties (single source of truth)
FS('setProperties', { type: 'page', properties: { productId: 'SKU-123' }});

// Element just has element-specific data
<button data-fs-element="Add to Cart Button">Add to Cart</button>
```

### Rule 3: Let Inheritance Work

FullStory's property inheritance:
- **User properties** → Available on all sessions for that user
- **Page properties** → Available on all elements/events on that page
- **Element properties** → Inherited by child elements

---

## II. Scope Selection by Scenario

### Scenario A: Single-Entity Detail Pages (1:1)

**Definition**: A page dedicated to one unique entity (product detail, flight itinerary, policy document).

**Strategy**: Entity attributes become **Page Properties**

```javascript
// ✅ CORRECT: Entity data at page level
FS('setProperties', {
  type: 'page',
  properties: {
    pageName: 'Product Detail',
    productId: 'SKU-123',
    productName: 'Wireless Headphones',
    category: 'Electronics',
    price: 199.99,
    inStock: true
  }
});

// ✅ CORRECT: Elements just have element-specific data
<button data-fs-element="Add to Cart">Add to Cart</button>
<button data-fs-element="Buy Now">Buy Now</button>
```

```javascript
// ❌ WRONG: Entity data duplicated on elements
<button 
  data-fs-element="Add to Cart"
  data-product-id="SKU-123"       // REDUNDANT - already at page level
  data-product-name="Wireless..."  // REDUNDANT
>Add to Cart</button>
```

**Why This Works:**
- All interactions inherit product context
- Search by "clicks on Product Detail page where price > 100" works
- No duplication, cleaner implementation

---

### Scenario B: Multi-Entity Listing Pages (1:Many)

**Definition**: Pages showing multiple distinct entities (search results, product grid, job listings).

**Strategy**: 
- Search/filter context → **Page Properties**
- Individual item context → **Element Properties**

```javascript
// ✅ CORRECT: Search context at page level
FS('setProperties', {
  type: 'page',
  properties: {
    pageName: 'Search Results',
    searchTerm: 'wireless headphones',
    resultsCount: 50,
    sortBy: 'relevance',
    activeFilters: ['Electronics', 'In Stock']
  }
});
```

```html
<!-- ✅ CORRECT: Item-specific data on elements -->
<div 
  data-product-id="SKU-123"
  data-product-name="Wireless Headphones"
  data-price="199.99"
  data-position="1"
  data-fs-properties-schema='{
    "data-product-id": {"type": "str", "name": "productId"},
    "data-product-name": {"type": "str", "name": "productName"},
    "data-price": {"type": "real", "name": "price"},
    "data-position": {"type": "int", "name": "position"}
  }'
  data-fs-element="Product Card"
>
  ...
</div>
```

```html
<!-- ❌ WRONG: Search context duplicated on elements -->
<div 
  data-product-id="SKU-123"
  data-search-term="wireless headphones"  <!-- REDUNDANT - page level -->
  data-results-count="50"                  <!-- REDUNDANT - page level -->
>
```

---

### Scenario C: User Attributes

**Definition**: Data about WHO the user is (not what they're doing).

**Strategy**: Use **User Properties** via `setIdentity` or `setProperties(user)`

```javascript
// ✅ CORRECT: User attributes at user level
FS('setIdentity', {
  uid: user.id,
  properties: {
    displayName: user.name,
    email: user.email,
    plan: 'enterprise',
    role: 'admin',
    companyName: user.company,
    signupDate: user.createdAt
  }
});
```

```javascript
// ❌ WRONG: User attributes in page/element properties
FS('setProperties', {
  type: 'page',
  properties: {
    userPlan: 'enterprise',  // WRONG SCOPE - use user properties
    userRole: 'admin'        // WRONG SCOPE
  }
});
```

---

### Scenario D: Discrete Actions (Events)

**Definition**: Something that **happened** at a point in time.

**Strategy**: Use **trackEvent** with action-specific properties

```javascript
// ✅ CORRECT: Action captured as event
FS('trackEvent', {
  name: 'Product Added to Cart',
  properties: {
    quantity: 2,              // Action-specific
    addedFrom: 'quick-view',  // Action-specific
    // productId inherited from page properties
    // userId inherited from user properties
  }
});
```

```javascript
// ❌ WRONG: Trying to track actions via properties
FS('setProperties', {
  type: 'user',
  properties: {
    lastAddedProduct: 'SKU-123',    // Events shouldn't be properties
    lastAddedQuantity: 2,
    lastAddedTime: Date.now()
  }
});
```

---

## III. Decision Flowchart

```
START: You have data to capture
          │
          ▼
    Is this data about WHO the user is?
    (plan, role, company, signup date)
          │
    YES ──┴── NO
     │         │
     ▼         ▼
  USER      Is this a discrete action/moment?
  PROPS     (purchase, signup, feature used)
               │
         YES ──┴── NO
          │         │
          ▼         ▼
       EVENT    Is this data the same for the entire page?
                (search term, product on detail page)
                      │
                YES ──┴── NO
                 │         │
                 ▼         ▼
              PAGE      ELEMENT
              PROPS     PROPS
```

---

## IV. Common Patterns by Industry

### E-commerce

| Data Point | Scope | Implementation |
|------------|-------|----------------|
| User's plan/loyalty tier | User Property | `setIdentity` |
| Search term | Page Property | `setProperties(page)` |
| Product ID (on PDP) | Page Property | `setProperties(page)` |
| Product ID (in grid) | Element Property | `data-fs-*` |
| Cart value | Page Property | `setProperties(page)` |
| Purchase completed | Event | `trackEvent` |

### SaaS

| Data Point | Scope | Implementation |
|------------|-------|----------------|
| User role | User Property | `setIdentity` |
| Team/org ID | User Property | `setIdentity` |
| Dashboard being viewed | Page Property | `setProperties(page)` |
| Report ID in list | Element Property | `data-fs-*` |
| Feature used | Event | `trackEvent` |
| Setting changed | Event | `trackEvent` |

### Content/Media

| Data Point | Scope | Implementation |
|------------|-------|----------------|
| Subscriber status | User Property | `setIdentity` |
| Article category | Page Property | `setProperties(page)` |
| Article ID (on detail) | Page Property | `setProperties(page)` |
| Related article IDs | Element Property | `data-fs-*` |
| Video played | Event | `trackEvent` |
| Content shared | Event | `trackEvent` |

---

## V. Anti-Patterns to Avoid

### Anti-Pattern 1: Everything as User Properties

```javascript
// ❌ BAD: Transient state as user property
FS('setProperties', {
  type: 'user',
  properties: {
    currentPage: '/checkout',        // Should be page property
    cartItems: 5,                    // Should be page property
    lastClickedButton: 'submit'      // Should be event
  }
});
```

### Anti-Pattern 2: Everything as Events

```javascript
// ❌ BAD: State as events
FS('trackEvent', { name: 'User Is Premium', properties: {} });    // User property
FS('trackEvent', { name: 'Page Has 5 Results', properties: {} }); // Page property
```

### Anti-Pattern 3: Duplicating Hierarchy

```javascript
// ❌ BAD: Same data at multiple levels
FS('setIdentity', { uid: '123', properties: { plan: 'premium' }});
FS('setProperties', { type: 'page', properties: { userPlan: 'premium' }}); // DUP
FS('trackEvent', { name: 'Click', properties: { userPlan: 'premium' }});   // DUP
```

### Anti-Pattern 4: Over-Granular Element Properties

```javascript
// ❌ BAD: Too much data on elements when page context would work
<button 
  data-page-name="Checkout"          // Should be page property
  data-user-id="123"                 // Should be user property
  data-cart-total="99.99"            // Should be page property
  data-fs-element="Submit">
```

---

## VI. Implementation Checklist

Before implementing, answer these questions:

- [ ] **Who is this data about?**
  - The user → User Properties
  - The page/context → Page Properties
  - A specific element → Element Properties
  - An action → Event

- [ ] **How long is this data relevant?**
  - Entire user lifetime → User Properties
  - This page view → Page Properties
  - This interaction → Element/Event Properties

- [ ] **Is this data already available at a higher scope?**
  - If yes → Don't duplicate, let inheritance work
  - If no → Add at appropriate scope

- [ ] **Can this data be searched/segmented?**
  - User properties → Segment users
  - Page properties → Find sessions with this page context
  - Element properties → Find clicks on elements with this data
  - Events → Funnel analysis, conversion tracking

---

## VII. Related Core Skills

For detailed implementation guidance on each API:

| API | Skill Document |
|-----|----------------|
| User Identification | `fullstory-identify-users` |
| User Properties | `fullstory-user-properties` |
| Page Properties | `fullstory-page-properties` |
| Element Properties | `fullstory-element-properties` |
| Events | `fullstory-analytics-events` |

---

## VIII. Version History

- **4.0** (Current)
  - Added YAML front matter for skill metadata
  - Integrated with core skill documents
  - Added decision flowchart
  - Expanded industry-specific patterns
  - Added explicit anti-patterns section
  - Aligned format with element-properties skill
  
- **3.0**
  - Generalized naming and examples
  - Added explicit Good/Bad implementation guides
  - Renamed properties for universal application

- **2.0**
  - Merged Scoping and Element Naming into one unified document

---

## Key Takeaways for Agent

When helping developers with data scoping:

1. **Always ask first**:
   - What data are you trying to capture?
   - Is this about the user, the page, an element, or an action?
   - Will this data be the same across multiple elements?

2. **Common mistakes to watch for**:
   - User data in page properties
   - Page-level data duplicated on elements
   - Actions captured as properties instead of events
   - Same data at multiple scopes

3. **Golden rules**:
   - Capture at the HIGHEST relevant scope
   - Let inheritance do the work
   - User → Page → Element → Event (hierarchy)
   - Don't duplicate across scopes

4. **Quick decision**:
   - WHO = User Properties
   - WHERE = Page Properties
   - WHAT (specific item) = Element Properties
   - WHAT HAPPENED = Events

---

*This meta-skill document provides strategic guidance for FullStory data semantic decoration. Refer to individual API skill documents for detailed implementation examples.*
