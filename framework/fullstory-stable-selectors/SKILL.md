---
name: fullstory-stable-selectors
version: v2
description: Framework-agnostic guide for implementing stable selectors in any web application. Solves the dynamic class name problem caused by CSS-in-JS, CSS Modules, and build tools by teaching developers to add semantic data-* attributes. Includes implementation patterns for React, Angular, Vue, Svelte, and vanilla JavaScript. This skill replaces the need for framework-specific plugins.
related_skills:
  - fullstory-element-properties
  - fullstory-privacy-controls
  - fullstory-getting-started
  - universal-data-scoping-and-decoration
---

# Fullstory Stable Selectors

## Overview

Modern web applications use build tools and CSS methodologies that generate dynamic, unpredictable class names. This creates a challenge for Fullstory: how do you reliably target elements for search, defined elements, and click maps when class names change on every build?

**The Solution**: Add stable, semantic `data-*` attributes that describe **what** the element is, not how it's styled.

This skill teaches you how to implement stable selectors in **any framework** without requiring external plugins.

---

## The Problem

```html
<!-- What your code looks like -->
<button className={styles.primaryButton}>Add to Cart</button>

<!-- What renders in the browser -->
<button class="Button_primaryButton__x7Ks2">Add to Cart</button>
                                    ↑
                        This hash changes every build!
```

**Dynamic class names come from:**
- ❌ CSS Modules (hash suffixes)
- ❌ styled-components / Emotion (random class names)
- ❌ Tailwind CSS (class purging changes the set)
- ❌ Build optimizations (minification, renaming)
- ❌ Component libraries (internal naming conventions)
- ❌ Shadow DOM / Web Components (encapsulated styles)

**Impact on Fullstory:**
- ❌ Searches by class name break after deployments
- ❌ Defined elements stop matching
- ❌ Click maps lose data continuity
- ❌ Segments and metrics become unreliable

---

## The Solution

Add stable `data-*` attributes that survive build changes:

```html
<!-- Before: Brittle selector -->
<button class="Button_primaryButton__x7Ks2">Add to Cart</button>

<!-- After: Stable selector -->
<button 
  class="Button_primaryButton__x7Ks2"
  data-component="ProductCard"
  data-element="add-to-cart-button"
>
  Add to Cart
</button>
```

**Benefits:**
- ✅ Survives all build changes
- ✅ Semantic and self-documenting
- ✅ Works in ANY framework
- ✅ Enables reliable Fullstory searches
- ✅ Powers defined elements and click maps
- ✅ No external plugins required

---

## Core Concepts

### The Three Standard Attributes

| Attribute | Purpose | When to Use |
|-----------|---------|-------------|
| `data-component` | Names the component that owns this element | Root element of each component |
| `data-element` | Names the element's role within the component | Interactive elements, key UI parts |
| `data-source-file` | References the source file (optional) | Debugging, development |

### Attribute Hierarchy

```
┌─────────────────────────────────────────────────────────────────┐
│  data-component                                                  │
│  • Identifies which component this element belongs to            │
│  • Set on the root element of each component                     │
│  • Example: "ProductCard", "CheckoutForm", "NavBar"              │
├─────────────────────────────────────────────────────────────────┤
│  data-element                                                    │
│  • Identifies the element's purpose within the component         │
│  • Set on interactive elements and key UI elements               │
│  • Example: "add-to-cart", "search-input", "price-display"       │
├─────────────────────────────────────────────────────────────────┤
│  data-source-file (Optional)                                     │
│  • References the source file for debugging                      │
│  • Can be stripped in production builds                          │
│  • Example: "ProductCard.tsx", "checkout/Form.vue"               │
└─────────────────────────────────────────────────────────────────┘
```

---

## Naming Conventions

### Component Names (`data-component`)

Use **PascalCase** matching your component/class names:

```html
<!-- ✅ GOOD: Matches component names -->
<div data-component="ProductCard">
<div data-component="CheckoutForm">
<div data-component="NavigationHeader">
<div data-component="UserProfileDropdown">

<!-- ❌ BAD: Generic names -->
<div data-component="Container">
<div data-component="Wrapper">
<div data-component="Component">
<div data-component="Box">
```

### Element Names (`data-element`)

Use **kebab-case** describing the element's purpose:

```html
<!-- ✅ GOOD: Describes purpose -->
<button data-element="add-to-cart">
<input data-element="email-input">
<div data-element="product-image">
<span data-element="price-display">
<nav data-element="main-navigation">

<!-- ❌ BAD: Describes appearance or position -->
<button data-element="blue-button">
<button data-element="big-button">
<button data-element="button-1">
<button data-element="first-button">
<div data-element="left-sidebar">
```

### What to Annotate

**Always annotate:**
- ✅ Buttons and clickable elements
- ✅ Form inputs (text, select, checkbox, etc.)
- ✅ Links and navigation items
- ✅ Cards and list items in repeating content
- ✅ Modals and dialog triggers
- ✅ Tab and accordion controls

**Skip annotation for:**
- ❌ Pure layout wrappers (unless interactive)
- ❌ Styling containers
- ❌ Text-only elements (unless key content)

---

## Implementation by Framework

### React

```jsx
// ProductCard.jsx
function ProductCard({ product, onAddToCart }) {
  return (
    <div 
      data-component="ProductCard"
      data-element="card"
      className={styles.card}
    >
      <img 
        src={product.image} 
        alt={product.name}
        data-element="product-image"
      />
      
      <h3 data-element="product-name">{product.name}</h3>
      
      <span data-element="price">${product.price}</span>
      
      <button 
        data-element="add-to-cart"
        onClick={() => onAddToCart(product)}
      >
        Add to Cart
      </button>
    </div>
  );
}
```

#### React Helper (Optional)

```jsx
// useStableSelector.js
export function useStableSelector(componentName) {
  return {
    root: {
      'data-component': componentName,
    },
    element: (name) => ({
      'data-element': name,
    }),
  };
}

// Usage
function ProductCard({ product }) {
  const sel = useStableSelector('ProductCard');
  
  return (
    <div {...sel.root} {...sel.element('card')}>
      <button {...sel.element('add-to-cart')}>Add to Cart</button>
    </div>
  );
}
```

---

### Angular

```html
<!-- product-card.component.html -->
<article 
  data-component="ProductCard"
  data-element="card"
  class="product-card"
>
  <img 
    [src]="product.image" 
    [alt]="product.name"
    data-element="product-image"
  />
  
  <h3 data-element="product-name">{{ product.name }}</h3>
  
  <span data-element="price">{{ product.price | currency }}</span>
  
  <button 
    data-element="add-to-cart"
    (click)="addToCart()"
  >
    Add to Cart
  </button>
</article>
```

#### Angular Directive (Optional)

```typescript
// stable-selector.directive.ts
import { Directive, ElementRef, Input, OnInit } from '@angular/core';

@Directive({
  selector: '[fsComponent], [fsElement]'
})
export class StableSelectorDirective implements OnInit {
  @Input() fsComponent: string;
  @Input() fsElement: string;
  
  constructor(private el: ElementRef) {}
  
  ngOnInit() {
    if (this.fsComponent) {
      this.el.nativeElement.setAttribute('data-component', this.fsComponent);
    }
    if (this.fsElement) {
      this.el.nativeElement.setAttribute('data-element', this.fsElement);
    }
  }
}

// Usage in template
<div fsComponent="ProductCard" fsElement="card">
  <button fsElement="add-to-cart">Add to Cart</button>
</div>
```

---

### Vue

```vue
<!-- ProductCard.vue -->
<template>
  <article 
    data-component="ProductCard"
    data-element="card"
    class="product-card"
  >
    <img 
      :src="product.image" 
      :alt="product.name"
      data-element="product-image"
    />
    
    <h3 data-element="product-name">{{ product.name }}</h3>
    
    <span data-element="price">{{ formatPrice(product.price) }}</span>
    
    <button 
      data-element="add-to-cart"
      @click="$emit('add-to-cart', product)"
    >
      Add to Cart
    </button>
  </article>
</template>

<script setup>
defineProps(['product']);
defineEmits(['add-to-cart']);
</script>
```

#### Vue Directive (Optional)

```javascript
// main.js
app.directive('fs', {
  mounted(el, binding) {
    const { component, element } = binding.value;
    if (component) el.setAttribute('data-component', component);
    if (element) el.setAttribute('data-element', element);
  }
});

// Usage in template
<div v-fs="{ component: 'ProductCard', element: 'card' }">
  <button v-fs="{ element: 'add-to-cart' }">Add to Cart</button>
</div>
```

---

### Svelte

```svelte
<!-- ProductCard.svelte -->
<article 
  data-component="ProductCard"
  data-element="card"
  class="product-card"
>
  <img 
    src={product.image} 
    alt={product.name}
    data-element="product-image"
  />
  
  <h3 data-element="product-name">{product.name}</h3>
  
  <span data-element="price">${product.price}</span>
  
  <button 
    data-element="add-to-cart"
    on:click={() => dispatch('addToCart', product)}
  >
    Add to Cart
  </button>
</article>

<script>
  import { createEventDispatcher } from 'svelte';
  export let product;
  const dispatch = createEventDispatcher();
</script>
```

---

### Vanilla JavaScript / Web Components

```javascript
// product-card.js
class ProductCard extends HTMLElement {
  connectedCallback() {
    const product = JSON.parse(this.getAttribute('product'));
    
    this.innerHTML = `
      <article data-component="ProductCard" data-element="card">
        <img 
          src="${product.image}" 
          alt="${product.name}"
          data-element="product-image"
        />
        <h3 data-element="product-name">${product.name}</h3>
        <span data-element="price">$${product.price}</span>
        <button data-element="add-to-cart">Add to Cart</button>
      </article>
    `;
    
    this.querySelector('[data-element="add-to-cart"]')
      .addEventListener('click', () => this.handleAddToCart(product));
  }
}

customElements.define('product-card', ProductCard);
```

---

### Server-Side Templates (PHP, Django, Rails, etc.)

```html
<!-- PHP/Blade -->
<article data-component="ProductCard" data-element="card">
  <img src="{{ $product->image }}" data-element="product-image" />
  <h3 data-element="product-name">{{ $product->name }}</h3>
  <button data-element="add-to-cart">Add to Cart</button>
</article>

<!-- Django -->
<article data-component="ProductCard" data-element="card">
  <img src="{{ product.image }}" data-element="product-image" />
  <h3 data-element="product-name">{{ product.name }}</h3>
  <button data-element="add-to-cart">Add to Cart</button>
</article>

<!-- Rails ERB -->
<article data-component="ProductCard" data-element="card">
  <img src="<%= product.image %>" data-element="product-image" />
  <h3 data-element="product-name"><%= product.name %></h3>
  <button data-element="add-to-cart">Add to Cart</button>
</article>
```

---

## Using Stable Selectors in Fullstory

### Searching by Selector

```
# Find all ProductCard components
css selector: [data-component="ProductCard"]

# Find add-to-cart buttons
css selector: [data-element="add-to-cart"]

# Find add-to-cart within ProductCard
css selector: [data-component="ProductCard"] [data-element="add-to-cart"]
```

### Creating Defined Elements

When creating defined elements in Fullstory, use stable selectors:

| Element Name | Selector |
|--------------|----------|
| Add to Cart Button | `[data-element="add-to-cart"]` |
| Product Card | `[data-component="ProductCard"]` |
| Search Input | `[data-element="search-input"]` |
| Checkout Submit | `[data-component="CheckoutForm"] [data-element="submit-button"]` |

### Combining with Element Properties

Stable selectors and Element Properties work together:

```html
<div 
  data-component="ProductCard"
  data-element="card"
  data-fs-element="Product Card"
  data-fs-properties-schema='{"product_id":"string","price":"real"}'
  data-product-id="SKU-123"
  data-price="99.99"
>
  <!-- content -->
</div>
```

| Attribute | Purpose |
|-----------|---------|
| `data-component` | Stable selector for searching |
| `data-element` | Stable selector for specific element |
| `data-fs-element` | Fullstory defined element name |
| `data-fs-properties-schema` | Fullstory element properties schema |

---

## ✅ GOOD Implementation Examples

### Example 1: E-commerce Product Grid

```html
<section data-component="ProductGrid" data-element="grid">
  <h2 data-element="section-title">Featured Products</h2>
  
  <div data-element="product-list">
    <!-- Each product card -->
    <article data-component="ProductCard" data-element="card">
      <img src="..." data-element="product-image" />
      <h3 data-element="product-name">Wireless Headphones</h3>
      <div data-element="pricing">
        <span data-element="current-price">$149.99</span>
        <span data-element="original-price">$199.99</span>
      </div>
      <div data-element="actions">
        <button data-element="add-to-cart">Add to Cart</button>
        <button data-element="wishlist">♡</button>
      </div>
    </article>
    
    <!-- More product cards... -->
  </div>
  
  <nav data-element="pagination">
    <button data-element="prev-page">Previous</button>
    <button data-element="next-page">Next</button>
  </nav>
</section>
```

### Example 2: Multi-Step Form

```html
<form data-component="CheckoutForm" data-element="form">
  <!-- Progress indicator -->
  <nav data-element="step-indicator">
    <span data-element="step" data-step="shipping">Shipping</span>
    <span data-element="step" data-step="payment">Payment</span>
    <span data-element="step" data-step="review">Review</span>
  </nav>
  
  <!-- Shipping step -->
  <fieldset data-element="shipping-step">
    <div data-element="name-field">
      <label>Full Name</label>
      <input type="text" data-element="name-input" />
    </div>
    <div data-element="address-field">
      <label>Address</label>
      <input type="text" data-element="address-input" />
    </div>
  </fieldset>
  
  <!-- Payment step (with privacy) -->
  <fieldset data-element="payment-step" class="fs-exclude">
    <div data-element="card-field">
      <label>Card Number</label>
      <input type="text" data-element="card-input" />
    </div>
  </fieldset>
  
  <!-- Actions -->
  <div data-element="form-actions">
    <button type="button" data-element="back-button">Back</button>
    <button type="submit" data-element="submit-button">Continue</button>
  </div>
</form>
```

### Example 3: Navigation with Dropdowns

```html
<header data-component="SiteHeader" data-element="header">
  <a href="/" data-element="logo">
    <img src="logo.svg" alt="Company" />
  </a>
  
  <nav data-component="MainNav" data-element="navigation">
    <ul data-element="nav-list">
      <li data-element="nav-item">
        <a href="/products" data-element="nav-link">Products</a>
        <ul data-element="dropdown-menu">
          <li><a href="/products/shoes" data-element="dropdown-item">Shoes</a></li>
          <li><a href="/products/bags" data-element="dropdown-item">Bags</a></li>
        </ul>
      </li>
      <li data-element="nav-item">
        <a href="/about" data-element="nav-link">About</a>
      </li>
    </ul>
  </nav>
  
  <div data-element="header-actions">
    <button data-element="search-toggle">🔍</button>
    <a href="/cart" data-element="cart-link">
      Cart (<span data-element="cart-count">3</span>)
    </a>
    <button data-element="account-menu">Account</button>
  </div>
</header>
```

---

## ❌ BAD Implementation Examples

### Example 1: Generic Names

```html
<!-- ❌ BAD: Names are too generic -->
<div data-component="Component">
  <img data-element="image" />
  <span data-element="text" />
  <button data-element="button">Click</button>
</div>
```

**Why it's bad:** Every component has "image", "text", "button" - searches return everything.

**✅ CORRECTED:**
```html
<div data-component="ProductCard">
  <img data-element="product-image" />
  <span data-element="product-name" />
  <button data-element="add-to-cart">Click</button>
</div>
```

### Example 2: Position-Based Names

```html
<!-- ❌ BAD: Position-based naming -->
<div data-component="ProductList">
  <div data-element="item-0">First product</div>
  <div data-element="item-1">Second product</div>
  <div data-element="item-2">Third product</div>
</div>
```

**Why it's bad:** If sort order changes, "item-0" is now a different product.

**✅ CORRECTED:**
```html
<div data-component="ProductList">
  <div data-element="product-item" data-product-id="SKU-A">First product</div>
  <div data-element="product-item" data-product-id="SKU-B">Second product</div>
  <div data-element="product-item" data-product-id="SKU-C">Third product</div>
</div>
```

### Example 3: Appearance-Based Names

```html
<!-- ❌ BAD: Named by appearance -->
<button data-element="blue-button">Primary Action</button>
<button data-element="gray-button">Secondary Action</button>
<div data-element="left-sidebar">Navigation</div>
```

**Why it's bad:** If design changes (blue → green, sidebar moves right), names become wrong.

**✅ CORRECTED:**
```html
<button data-element="primary-action">Primary Action</button>
<button data-element="secondary-action">Secondary Action</button>
<div data-element="side-navigation">Navigation</div>
```

---

## Best Practices

### 1. Annotate at Development Time

Add annotations as you write components, not as an afterthought:

```jsx
// ✅ Good habit: Add annotations as you code
function ProductCard({ product }) {
  return (
    <div data-component="ProductCard">
      <button data-element="add-to-cart">Add</button>
    </div>
  );
}
```

### 2. Document Your Conventions

Create a team style guide:

```markdown
## Stable Selector Conventions

### Component Names
- Use PascalCase: `ProductCard`, `CheckoutForm`
- Match your component file/class name

### Element Names
- Use kebab-case: `add-to-cart`, `search-input`
- Describe purpose, not appearance
- Be specific: `product-name` not `name`

### Required Annotations
- All buttons and links
- All form inputs
- All cards in lists
- Modal and dropdown triggers
```

### 3. Combine with Privacy Controls

```html
<!-- Annotate, but respect privacy -->
<form data-component="PaymentForm">
  <div data-element="card-field" class="fs-exclude">
    <input data-element="card-input" type="text" />
  </div>
  <button data-element="submit-payment">Pay Now</button>
</form>
```

### 4. Use Consistent Depth

Don't over-nest annotations:

```html
<!-- ✅ GOOD: Flat, specific selectors -->
<div data-component="ProductCard">
  <button data-element="add-to-cart">Add</button>
</div>

<!-- ❌ BAD: Deep nesting (unnecessary) -->
<div data-component="App">
  <div data-component="MainContent">
    <div data-component="ProductSection">
      <div data-component="ProductCard">
        <button data-element="add-to-cart">Add</button>
      </div>
    </div>
  </div>
</div>
```

---

## Troubleshooting

### Selectors Not Working in Fullstory

**Check in browser DevTools:**
1. Inspect the element
2. Verify `data-component` and `data-element` attributes exist
3. Check for typos in attribute names

**Common issues:**
- Framework stripping data attributes in production
- SSR/hydration mismatch
- Conditional rendering removing the element

### Too Many Search Results

**Problem:** Searching `[data-element="button"]` returns hundreds of results

**Solution:** Be more specific:
```
[data-component="ProductCard"] [data-element="add-to-cart"]
```

### Attributes Stripped in Production

Check your build tool configuration:

```javascript
// webpack.config.js - DON'T strip data-* attributes
optimization: {
  minimizer: [
    new HtmlWebpackPlugin({
      minify: {
        // Keep data-* attributes
        removeDataAttributes: false
      }
    })
  ]
}
```

---

## KEY TAKEAWAYS FOR CLAUDE

When helping developers implement stable selectors:

1. **Framework-agnostic solution**: Works in React, Angular, Vue, Svelte, vanilla JS, server-side templates
2. **Two attributes are key**: `data-component` (PascalCase) and `data-element` (kebab-case)
3. **Name by purpose, not appearance**: "add-to-cart" not "blue-button"
4. **Annotate interactive elements**: Buttons, inputs, links, cards in lists
5. **Combine with Element Properties**: Stable selectors for search, Element Properties for analytics data
6. **No plugins required**: Manual annotation works everywhere

### Questions to Ask Developers

1. "What framework are you using?"
2. "Are your class names dynamic? (CSS Modules, styled-components, etc.)"
3. "What elements do you need to reliably search for in Fullstory?"
4. "Do you have a component naming convention already?"

### Implementation Checklist

```markdown
□ Identify interactive elements that need tracking
□ Add data-component to component root elements
□ Add data-element to buttons, inputs, links, cards
□ Use specific, purpose-based names
□ Test selectors in browser DevTools
□ Verify attributes survive production build
□ Create defined elements in Fullstory using data-* selectors
```

---

## REFERENCE LINKS

- **Fullstory CSS Selectors**: https://help.fullstory.com/hc/en-us/articles/360020623294
- **Defined Elements**: https://help.fullstory.com/hc/en-us/articles/360020828113
- **Element Properties**: ../core/fullstory-element-properties/SKILL.md

---

*This skill provides a universal pattern for stable selectors that works in any framework. No external plugins required.*
