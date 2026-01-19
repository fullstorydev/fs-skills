---
name: mobile-rn-orchestrator
version: 1.0.0
description: A high-level decision engine and implementation roadmap for sequencing Fullstory React Native skills. Ensures that data capture follows a logical sequence (Compliance -> Identity -> Navigation -> Interaction -> Diagnostics) and provides guidance on when and in what order to utilize the React Native skills library.
related_skills:
  - meta-mobile-instrumentation
  - mobile-react-native-pages
  - mobile-react-native-privacy
  - mobile-react-native-identity
  - mobile-react-native-track
  - mobile-react-native-element-attributes
  - mobile-react-native-errors
---

# Mobile React Native Orchestrator

## Overview

The mobile-rn-orchestrator skill provides the logic for "When" and "In what order" to utilize the React Native skills library. It ensures that data capture follows a logical sequence: Compliance → Identity → Navigation → Interaction → Diagnostics.

---

## Core Concepts

### The Implementation Timeline

Instrumentation must follow a specific sequence to ensure data integrity and user privacy.

```
[ STAGE 1: INIT ] -> [ STAGE 2: AUTH ] -> [ STAGE 3: NAV ] -> [ STAGE 4: ACTION ]
       |                   |                  |                   |
       v                   v                  v                   v
+--------------+    +--------------+    +--------------+    +--------------+
| Privacy      |    | Identity     |    | Pages        |    | Track        |
| (Masking)    |    | (UID/Vars)   |    | (Lifecycles) |    | (Events)     |
+--------------+    +--------------+    +--------------+    +--------------+
       |                   |                  |                   v
       +-------------------/                  |            +--------------+
                                              +----------> | Errors       |
                                                           | (Catch/Track)|
                                                           +--------------+
```

### The Decision Matrix

| Trigger | Primary Skill | Secondary Skill |
|---------|---------------|-----------------|
| Application Boot | mobile-react-native-privacy | mobile-react-native-identity |
| Successful Login | mobile-react-native-identity | mobile-react-native-track |
| Screen Transition | mobile-react-native-pages | mobile-react-native-element-attributes |
| User Interaction | mobile-react-native-track | mobile-react-native-element-attributes |
| Background Failure | mobile-react-native-errors | mobile-react-native-track |

---

## Implementation Standards

### ✅ GOOD IMPLEMENTATION: The Sequential Flow

This example demonstrates the correct sequence of operations during a typical "Checkout" flow.

```javascript
import React, { useEffect } from 'react';
import { View, TextInput } from 'react-native';
import FullStory from '@fullstory/react-native';

const CheckoutProcess = ({ user, orderData }) => {
  useEffect(() => {
    // STEP 1: Privacy (Set before page start to ensure data is masked immediately)
    // Skill: mobile-react-native-privacy

    // STEP 2: Page Lifecycle
    // Skill: mobile-react-native-pages
    const pageHandle = FullStory.page.start('Checkout Review', {
      cart_value_num: orderData.total
    });

    return () => FullStory.page.end(pageHandle);
  }, []);

  const onConfirm = async () => {
    try {
      // STEP 3: Business Logic Event
      // Skill: mobile-react-native-track
      FullStory.track('Order Completed', { order_id_str: orderData.id });
    } catch (e) {
      // STEP 4: Error Capture
      // Skill: mobile-react-native-errors
      FullStory.track('API Error', { status_code_num: 500 });
    }
  };

  return (
    <View data-component="checkout-view">
      {/* STEP 5: Element Naming (CUA Visibility) */}
      <TextInput fs-mask={true} data-element="cc-input" />
    </View>
  );
};
```

### ❌ BAD IMPLEMENTATION: Out-of-Order Execution

Tracking interactions without establishing location or identity context.

```javascript
// ❌ WRONG: Tracking an event without starting a page or identifying
const BadComponent = () => {
  const handleTap = () => {
    // ERROR: This event is "homeless" because no page.start() was called.
    FullStory.track('Button Tapped', { label_str: 'Go' });
  };
  
  return <Button onPress={handleTap} title="Go" />;
};
```

---

## Key Takeaways for Agent

1. **Priority 1 (Privacy)**: Never track anything until privacy masking (baseline or advanced) has been applied to sensitive views.

2. **Priority 2 (Identity)**: Ensure `identify` is called as soon as auth state is confirmed to avoid "anonymous" session segments.

3. **Priority 3 (Pages)**: Every `track` event should exist within a defined `page.start()` and `page.end()` block.

4. **Diagnostic Safety**: Ensure every `FS.track` for an interaction is paired with a corresponding `FS.track` for a potential error in the `catch` block.

5. **Standard Check**: Always verify the `meta-mobile-instrumentation` skill before finalizing any code to ensure naming and suffixing compliance.
