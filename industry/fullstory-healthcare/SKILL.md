---
name: fullstory-healthcare
version: v2
description: Industry-specific guide for implementing Fullstory in healthcare applications while maintaining HIPAA compliance. Covers PHI protection, patient portals, telehealth, pharmacy, medical devices, clinical trials, insurance portals, and regulatory requirements (HIPAA, FDA 21 CFR Part 11, COPPA).
related_skills:
  - fullstory-privacy-controls
  - fullstory-privacy-strategy
  - fullstory-user-consent
  - fullstory-identify-users
  - fullstory-capture-control
  - fullstory-analytics-events
---

# Fullstory for Healthcare

> ⚠️ **LEGAL DISCLAIMER**: This guidance is for educational purposes only and does not constitute legal, compliance, or regulatory advice. Healthcare regulations (HIPAA, HITECH, state privacy laws) are complex, jurisdiction-specific, and subject to change. Always consult with your legal, compliance, privacy officer, and security teams before implementing any data capture solution. Your organization is responsible for ensuring compliance with all applicable regulations.

## Table of Contents

1. [Industry Overview](#industry-overview)
2. [HIPAA Framework](#hipaa-framework)
3. [Implementation Architecture](#implementation-architecture)
4. [Core Healthcare Contexts](#core-healthcare-contexts)
   - [Public Health Information Pages](#public-health-information-pages)
   - [Patient Portal Login](#patient-portal-login)
   - [Patient Dashboard](#patient-dashboard)
   - [Appointment Scheduling](#appointment-scheduling)
   - [Telehealth / Virtual Visits](#telehealth--virtual-visits)
   - [Health Records / Test Results](#health-records--test-results)
   - [Patient Messaging](#patient-messaging)
   - [Billing](#billing-healthcare-specific)
5. [Additional Healthcare Contexts](#additional-healthcare-contexts) ⭐ NEW
   - [Pharmacy & Prescription Management](#pharmacy--prescription-management)
   - [Medical Devices & Digital Health Apps](#medical-devices--digital-health-apps)
   - [Clinical Trials & Research](#clinical-trials--research)
   - [Health Insurance Member Portals](#health-insurance-member-portals)
   - [Pediatric Considerations](#pediatric-considerations)
6. [Provider-Side Applications](#provider-side-applications)
7. [Consent Considerations](#consent-considerations)
8. [BAA and Compliance Checklist](#baa-and-compliance-checklist)
9. [Key Takeaways for Agent](#key-takeaways-for-agent)
10. [Reference Links](#reference-links)

## Industry Overview

Healthcare has the most stringent requirements for session analytics due to:

- **HIPAA compliance**: Protected Health Information (PHI) requires strict handling
- **Patient trust**: Breach of medical data is particularly harmful
- **Regulated entities**: Covered entities and business associates have legal obligations
- **BAA requirement**: Business Associate Agreement required with Fullstory

### Critical Understanding

> **In healthcare, the default should be EXCLUDE, not mask or unmask.**
> 
> Even seemingly innocuous data can become PHI when combined with other information. Err on the side of caution.

### Highly Recommended: Private by Default Mode

For healthcare applications, **Fullstory's Private by Default mode is essential**:

```
┌─────────────────────────────────────────────────────────────────┐
│  HEALTHCARE: Enable Private by Default                           │
│                                                                 │
│  • All text masked by default - no accidental PHI capture       │
│  • Selectively unmask ONLY navigation and generic UI            │
│  • Combined with fs-exclude for regulated areas                 │
│  • Contact Fullstory Support to enable                          │
└─────────────────────────────────────────────────────────────────┘
```

> **Reference**: [Fullstory Private by Default](https://help.fullstory.com/hc/en-us/articles/360044349073-Fullstory-Private-by-Default)

### Key Goals for Healthcare Implementations

1. **Improve patient portal UX** without capturing PHI
2. **Optimize appointment scheduling** flows
3. **Reduce friction** in telehealth experiences
4. **Understand navigation patterns** for health content
5. **Never compromise** patient privacy

---

## HIPAA Framework

### What Constitutes PHI?

PHI (Protected Health Information) includes any health information that can be linked to an individual:

| PHI Category | Examples | Fullstory Handling |
|--------------|----------|-------------------|
| **Names** | Patient, provider, family | fs-exclude (not mask!) |
| **Geographic data** | Address, city, ZIP | fs-exclude |
| **Dates** | DOB, admission, discharge, appointment | fs-exclude |
| **Contact info** | Phone, fax, email | fs-exclude |
| **Identifiers** | SSN, MRN, insurance ID | fs-exclude |
| **Health conditions** | Diagnoses, symptoms | fs-exclude |
| **Treatments** | Medications, procedures | fs-exclude |
| **Providers** | Doctor names, specialties | fs-exclude |
| **Test results** | Lab values, imaging | fs-exclude |
| **Images** | Photos, scans, ID documents | fs-exclude |
| **Biometrics** | Height, weight, vitals | fs-exclude |
| **Insurance** | Plan, member ID, claims | fs-exclude |
| **Prescriptions** | Medication names, dosages, pharmacy, prescriber | fs-exclude |
| **Device/wearable data** | Glucose, BP, heart rate, steps (when medical) | fs-exclude |
| **Trial participation** | Study enrollment, participant ID, questionnaires | fs-exclude |
| **Insurance claims** | Diagnoses, procedures, EOBs, provider visits | fs-exclude |

### HIPAA De-Identification Standards

HIPAA provides two methods for de-identification. Understanding these helps clarify what Fullstory can/cannot capture:

| Method | Approach | Fullstory Implication |
|--------|----------|----------------------|
| **Safe Harbor** | Remove 18 specific identifiers | Cannot rely on this—FS captures too much visual data |
| **Expert Determination** | Statistical/scientific analysis | Requires formal expert certification; impractical for session replay |

**Key Point**: Neither de-identification method is practical for session replay. This is why **exclusion (not just masking)** is required for healthcare.

```
The 18 Safe Harbor Identifiers (all require EXCLUSION):
├── Names
├── Geographic data (smaller than state)
├── Dates (except year) - birth, admission, discharge, death
├── Phone numbers
├── Fax numbers
├── Email addresses
├── Social Security numbers
├── Medical record numbers
├── Health plan beneficiary numbers
├── Account numbers
├── Certificate/license numbers
├── Vehicle identifiers
├── Device identifiers
├── Web URLs
├── IP addresses
├── Biometric identifiers
├── Full-face photographs
└── Any other unique identifying characteristic
```

### HIPAA Minimum Necessary Standard

Only capture what is absolutely necessary for UX analysis:

```
┌─────────────────────────────────────────────────────────────────┐
│  WHAT YOU CAN CAPTURE (Limited)                                 │
├─────────────────────────────────────────────────────────────────┤
│  ✓ Navigation patterns (which pages visited)                    │
│  ✓ Error occurrences (not error details)                        │
│  ✓ Form completion rates (not form contents)                    │
│  ✓ Button clicks (which buttons, not data submitted)            │
│  ✓ Page load times                                              │
│  ✓ Device/browser information                                   │
│  ✓ Session duration (generic)                                   │
├─────────────────────────────────────────────────────────────────┤
│  WHAT YOU CANNOT CAPTURE                                        │
├─────────────────────────────────────────────────────────────────┤
│  ✗ Any patient information                                      │
│  ✗ Any provider information                                     │
│  ✗ Any health/medical content                                   │
│  ✗ Appointment details                                          │
│  ✗ Insurance information                                        │
│  ✗ Messages between patient and provider                        │
│  ✗ Test results, diagnoses, medications                         │
│  ✗Images of any kind (could show PHI)                           │
│  ✗ Prescription information (medications, dosages, pharmacy)    │ 
│  ✗Wearable/medical device data (vitals, glucose, activity when medical) │ 
│  ✗Clinical trial participation or responses                     │
│  ✗Insurance claims, diagnoses codes, EOBs                       │
│  ✗Provider searches (reveals health concerns)                   │ 
└─────────────────────────────────────────────────────────────────┘
```

---

## Implementation Architecture

### Privacy Zones for Healthcare

```
┌─────────────────────────────────────────────────────────────────┐
│                    HEALTHCARE APPLICATION                        │
├─────────────────────────────────────────────────────────────────┤
│  LIMITED VISIBLE (fs-unmask) - Be very careful                   │
│  • Main navigation menu                                          │
│  • Generic page titles ("My Appointments" not appointment list)  │
│  • Action buttons (text only, not data)                         │
│  • Generic UI elements                                          │
│  • Public health information pages                               │
├─────────────────────────────────────────────────────────────────┤
│  NEVER USE MASK IN HEALTHCARE                                    │
│  • Masking is NOT sufficient for HIPAA                           │
│  • Even masked text structure could reveal PHI                   │
│  • Example: Masked 3-word name = still identifiable              │
├─────────────────────────────────────────────────────────────────┤
│  MUST EXCLUDE (fs-exclude) - Default for healthcare              │
│  • ALL patient information                                       │
│  • ALL provider information                                      │
│  • ALL appointment details                                       │
│  • ALL medical content                                          │
│  • ALL messaging                                                 │
│  • ALL forms with health data                                    │
│  • ALL test results                                             │
│  • ALL images                                                    │
│  • ALL search queries (could contain symptoms)                   │
└─────────────────────────────────────────────────────────────────┘
```

### Recommended Approach: Default Exclude

```javascript
// Healthcare: Consider using Private by Default mode
// Then selectively unmask ONLY navigation elements

// If not using Private by Default, add fs-exclude to almost everything
```

### User Identification Pattern

```javascript
// Healthcare: Use session-only identification
// DO NOT link sessions to patient identity

// Option 1: Don't identify at all (safest)
// Just use anonymous Fullstory sessions

// Option 2: Session-only identifier
FS('setIdentity', {
  uid: generateSessionId()  // Random per session, no linking
});

// Option 3: Hashed, non-reversible ID (consult legal first)
// Only if you have explicit patient consent
FS('setIdentity', {
  uid: sha256(patient.mrn + salt)  // Irreversible hash
});

// MINIMAL properties - no PHI
FS('setProperties', {
  type: 'user',
  properties: {
    // Only non-PHI operational data
    portal_type: 'patient',  // or "provider", "admin"
    access_method: 'direct',  // or "sso", "mobile_app"
    
    // NOTHING about the patient:
    // No demographics, no conditions, no providers, no appointments
  }
});
```

---

## Page-Specific Implementations

### Public Health Information Pages

```html
<!-- Public Health Content - Can be mostly visible -->
<div class="health-info-page">
  <!-- Navigation - visible -->
  <nav class="main-nav fs-unmask">
    <a href="/">Home</a>
    <a href="/conditions">Health Conditions</a>
    <a href="/services">Our Services</a>
    <a href="/portal">Patient Portal</a>
  </nav>
  
  <!-- Generic content about conditions - visible (it's public info) -->
  <article class="health-article fs-unmask">
    <h1>Understanding Diabetes</h1>
    <p>Diabetes is a chronic condition that affects how your body...</p>
    <!-- Public health education content -->
  </article>
  
  <!-- Search - EXCLUDE (queries could contain symptoms) -->
  <div class="search-box fs-exclude">
    <input type="text" placeholder="Search symptoms or conditions" />
    <button>Search</button>
  </div>
  
  <!-- Call-to-actions - visible -->
  <div class="ctas fs-unmask">
    <a href="/portal/login">Access Patient Portal</a>
    <a href="/appointments">Schedule Appointment</a>
  </div>
</div>
```

### Patient Portal Login

```html
<!-- Patient Portal Login -->
<div class="portal-login">
  <!-- Logo and title - visible -->
  <header class="fs-unmask">
    <img src="hospital-logo.svg" alt="Hospital Name" />
    <h1>Patient Portal</h1>
  </header>
  
  <!-- Login form - EXCLUDE everything -->
  <form class="login-form fs-exclude">
    <!-- Username could be MRN, email, or name -->
    <div class="form-group">
      <label>Username or Medical Record Number</label>
      <input type="text" name="username" />
    </div>
    
    <!-- Password -->
    <div class="form-group">
      <label>Password</label>
      <input type="password" name="password" />
    </div>
    
    <!-- Date of birth (verification) -->
    <div class="form-group">
      <label>Date of Birth</label>
      <input type="date" name="dob" />
    </div>
  </form>
  
  <!-- Action buttons - visible for funnel -->
  <div class="form-actions fs-unmask">
    <button type="submit">Sign In</button>
    <a href="/forgot-password">Forgot Password?</a>
    <a href="/register">New Patient? Register Here</a>
  </div>
</div>
```

```javascript
// Login tracking - NO PHI
FS('trackEvent', {
  name: 'portal_login_attempted',
  properties: {
    portal_type: 'patient',
    login_method: 'username'  // or "sso", "biometric"
    // NEVER: username, MRN, DOB
  }
});

FS('trackEvent', {
  name: 'portal_login_result',
  properties: {
    success: true,
    mfa_required: true
    // NEVER: failure reason (could reveal patient exists)
  }
});
```

### Patient Dashboard

```html
<!-- Patient Dashboard - Almost all excluded -->
<div class="patient-dashboard">
  <!-- Main nav - visible (generic labels only) -->
  <nav class="dashboard-nav fs-unmask">
    <a href="/portal/appointments">My Appointments</a>
    <a href="/portal/messages">Messages</a>
    <a href="/portal/records">Health Records</a>
    <a href="/portal/medications">Medications</a>
    <a href="/portal/billing">Billing</a>
  </nav>
  
  <!-- Welcome banner - EXCLUDE (contains name) -->
  <div class="welcome-banner fs-exclude">
    <h1>Welcome, John Smith</h1>
    <p>Last login: December 1, 2024</p>
  </div>
  
  <!-- Upcoming appointments - EXCLUDE entirely -->
  <section class="appointments-summary fs-exclude">
    <h2>Upcoming Appointments</h2>
    <!-- Contains: dates, provider names, appointment types = ALL PHI -->
    <div class="appointment-card">
      <p class="date">Dec 15, 2024 at 2:00 PM</p>
      <p class="provider">Dr. Sarah Johnson</p>
      <p class="type">Annual Physical</p>
      <p class="location">Main Campus, Room 302</p>
    </div>
  </section>
  
  <!-- Recent messages - EXCLUDE entirely -->
  <section class="messages-summary fs-exclude">
    <h2>Messages</h2>
    <!-- Contains provider names, message subjects = PHI -->
    <div class="message-preview">
      <p class="from">From: Dr. Johnson</p>
      <p class="subject">Re: Test Results</p>
    </div>
  </section>
  
  <!-- Medications - EXCLUDE entirely -->
  <section class="medications-summary fs-exclude">
    <h2>Current Medications</h2>
    <!-- Medications are PHI -->
    <ul>
      <li>Metformin 500mg - 2x daily</li>
      <li>Lisinopril 10mg - 1x daily</li>
    </ul>
  </section>
  
  <!-- Quick actions - visible (just buttons) -->
  <div class="quick-actions fs-unmask">
    <button>Request Appointment</button>
    <button>Message Provider</button>
    <button>Request Refill</button>
    <button>Pay Bill</button>
  </div>
</div>
```

```javascript
// Dashboard tracking - generic only
FS('setProperties', {
  type: 'page',
  properties: {
    page_type: 'patient_dashboard',
    // Only counts, no details
    has_upcoming_appointments: true,  // Boolean only
    has_unread_messages: true,
    // NEVER: appointment count, message count, medication count
    // (could indicate health status)
  }
});
```

### Appointment Scheduling

```html
<!-- Appointment Scheduling - Highly sensitive -->
<div class="appointment-scheduling">
  <h1 class="fs-unmask">Schedule an Appointment</h1>
  
  <!-- Progress steps - visible -->
  <div class="progress-steps fs-unmask">
    <span class="active">1. Service</span>
    <span>2. Provider</span>
    <span>3. Time</span>
    <span>4. Confirm</span>
  </div>
  
  <!-- Service selection - EXCLUDE (reveals health need) -->
  <section class="service-selection fs-exclude">
    <h2>Select Service Type</h2>
    <!-- Service types reveal why patient needs care = PHI context -->
    <div class="services">
      <button>Annual Physical</button>
      <button>Sick Visit</button>
      <button>Mental Health</button>  <!-- Especially sensitive -->
      <button>Follow-up</button>
      <button>Specialist Referral</button>
    </div>
  </section>
  
  <!-- Provider selection - EXCLUDE -->
  <section class="provider-selection fs-exclude">
    <h2>Select Provider</h2>
    <!-- Provider + patient = linkable PHI -->
    <div class="providers">
      <div class="provider-card">
        <img src="provider-photo.jpg" alt="" />
        <h3>Dr. Sarah Johnson, MD</h3>
        <p>Internal Medicine</p>
        <p>Accepting new patients</p>
      </div>
    </div>
  </section>
  
  <!-- Time selection - EXCLUDE -->
  <section class="time-selection fs-exclude">
    <h2>Select Date and Time</h2>
    <div class="calendar">
      <!-- Calendar showing available slots -->
    </div>
    <div class="time-slots">
      <button>9:00 AM</button>
      <button>10:30 AM</button>
      <button>2:00 PM</button>
    </div>
  </section>
  
  <!-- Reason for visit - ABSOLUTELY EXCLUDE -->
  <section class="visit-reason fs-exclude">
    <h2>Reason for Visit</h2>
    <textarea 
      name="reason" 
      placeholder="Please describe your symptoms or reason for visit"
    ></textarea>
  </section>
  
  <!-- Action buttons - visible -->
  <div class="form-actions fs-unmask">
    <button>Back</button>
    <button>Continue</button>
  </div>
</div>
```

```javascript
// Appointment scheduling - track funnel, not details
function trackSchedulingStep(step) {
  FS('trackEvent', {
    name: 'appointment_scheduling_step',
    properties: {
      step_number: step,
      step_name: getStepName(step),  // "service", "provider", "time", "confirm"
      // NEVER: service type, provider name, appointment time
    }
  });
}

FS('trackEvent', {
  name: 'appointment_scheduled',
  properties: {
    scheduling_method: 'online',  // or "phone", "in_person"
    steps_completed: 4,
    // NEVER: appointment details
  }
});
```

### Telehealth / Virtual Visit

```html
<!-- Telehealth Waiting Room -->
<div class="telehealth-waiting">
  <!-- Status - visible (generic) -->
  <div class="wait-status fs-unmask">
    <h1>Virtual Visit Waiting Room</h1>
    <p>Your provider will be with you shortly.</p>
  </div>
  
  <!-- Device check - visible -->
  <div class="device-check fs-unmask">
    <h2>Check Your Setup</h2>
    <div class="check-item">
      <span class="status">✓</span>
      <span>Camera</span>
    </div>
    <div class="check-item">
      <span class="status">✓</span>
      <span>Microphone</span>
    </div>
    <div class="check-item">
      <span class="status">✓</span>
      <span>Speaker</span>
    </div>
  </div>
  
  <!-- Appointment info - EXCLUDE -->
  <div class="appointment-info fs-exclude">
    <!-- Contains provider name, visit type = PHI -->
    <p>Appointment with Dr. Johnson</p>
    <p>Mental Health Follow-up</p>
  </div>
  
  <!-- Actions - visible -->
  <div class="actions fs-unmask">
    <button>Test Audio</button>
    <button>Test Video</button>
    <button>Cancel Visit</button>
  </div>
</div>

<!-- Telehealth Video Session - EXCLUDE ENTIRELY -->
<div class="telehealth-session fs-exclude">
  <!-- NEVER capture video sessions
       - Contains patient image/video
       - Contains provider image/video  
       - Contains medical discussion
       - Contains screen sharing of records
  -->
  <div class="video-container">
    <video id="provider-video"></video>
    <video id="patient-video"></video>
  </div>
  
  <div class="session-controls">
    <button>Mute</button>
    <button>Camera Off</button>
    <button>Share Screen</button>
    <button>End Visit</button>
  </div>
  
  <div class="chat-panel">
    <!-- Chat during visit = PHI -->
  </div>
</div>
```

```javascript
// Telehealth tracking - technical only
FS('trackEvent', {
  name: 'telehealth_session_started',
  properties: {
    connection_type: 'video',  // or "audio_only"
    device_type: getDeviceType(),
    browser: getBrowserName(),
    // Technical quality metrics
    initial_video_quality: 'hd',
    // NEVER: provider name, visit type, session content
  }
});

// Technical issues only
FS('trackEvent', {
  name: 'telehealth_technical_issue',
  properties: {
    issue_type: 'connection_lost',  // or "audio_failed", "video_failed"
    duration_before_issue_seconds: 120,
    // NEVER: what was being discussed
  }
});
```

### Health Records / Test Results

```html
<!-- Health Records - EXCLUDE ENTIRELY -->
<div class="health-records">
  <h1 class="fs-unmask">Health Records</h1>
  
  <!-- Navigation tabs - visible (generic labels) -->
  <nav class="records-nav fs-unmask">
    <a href="#results">Test Results</a>
    <a href="#visits">Visit Summaries</a>
    <a href="#immunizations">Immunizations</a>
    <a href="#allergies">Allergies</a>
  </nav>
  
  <!-- ALL record content - EXCLUDE -->
  <div class="records-content fs-exclude">
    <!-- Test results = PHI -->
    <section id="results">
      <h2>Recent Test Results</h2>
      <div class="result">
        <h3>Complete Blood Count</h3>
        <p>Date: Nov 15, 2024</p>
        <p>Ordered by: Dr. Johnson</p>
        <table class="lab-values">
          <tr><td>WBC</td><td>7.5</td><td>Normal</td></tr>
          <tr><td>RBC</td><td>4.8</td><td>Normal</td></tr>
          <tr><td>Hemoglobin</td><td>14.2</td><td>Normal</td></tr>
        </table>
      </div>
    </section>
    
    <!-- Visit summaries = PHI -->
    <section id="visits">
      <h2>Visit Summaries</h2>
      <div class="visit">
        <p>Nov 10, 2024 - Dr. Johnson</p>
        <p>Diagnosis: Type 2 Diabetes</p>
        <p>Treatment Plan: Diet modification, Metformin 500mg</p>
      </div>
    </section>
    
    <!-- Immunizations = PHI -->
    <section id="immunizations">
      <h2>Immunization Record</h2>
      <ul>
        <li>COVID-19 Booster - Oct 2024</li>
        <li>Flu Shot - Sep 2024</li>
      </ul>
    </section>
    
    <!-- Allergies = PHI -->
    <section id="allergies">
      <h2>Allergies</h2>
      <ul>
        <li>Penicillin - Severe</li>
        <li>Shellfish - Moderate</li>
      </ul>
    </section>
  </div>
</div>
```
---

---
### Patient Messaging

```html
<!-- Patient-Provider Messaging - EXCLUDE ENTIRELY -->
<div class="messaging">
  <h1 class="fs-unmask">Messages</h1>
  
  <!-- Message list - EXCLUDE -->
  <div class="message-list fs-exclude">
    <!-- Message metadata and content = PHI -->
    <div class="message-thread">
      <div class="thread-header">
        <span class="from">Dr. Sarah Johnson</span>
        <span class="subject">Re: Blood Test Results</span>
        <span class="date">Dec 1, 2024</span>
      </div>
      <div class="thread-preview">
        Your recent blood work shows...
      </div>
    </div>
  </div>
  
  <!-- Compose message - EXCLUDE -->
  <div class="compose-message fs-exclude">
    <select name="recipient">
      <option>Dr. Johnson</option>
      <option>Nurse Williams</option>
    </select>
    <input type="text" name="subject" placeholder="Subject" />
    <textarea name="body" placeholder="Type your message"></textarea>
    <button type="submit">Send</button>
  </div>
  
  <!-- Action buttons - visible -->
  <div class="message-actions fs-unmask">
    <button>New Message</button>
    <button>Mark All Read</button>
  </div>
</div>
```

### Billing (Healthcare-Specific)

```html
<!-- Healthcare Billing - Contains PHI -->
<div class="billing-page">
  <h1 class="fs-unmask">Billing</h1>
  
  <!-- Balance overview - EXCLUDE (tied to services received) -->
  <div class="balance-overview fs-exclude">
    <p>Current Balance: $250.00</p>
    <p>Insurance Pending: $500.00</p>
  </div>
  
  <!-- Statement list - EXCLUDE (shows services = PHI) -->
  <div class="statements fs-exclude">
    <div class="statement">
      <p>Nov 15, 2024</p>
      <p>Service: Laboratory Services</p>  <!-- Reveals health data -->
      <p>Provider: Dr. Johnson</p>
      <p>Amount: $150.00</p>
      <p>Insurance: Pending</p>
    </div>
  </div>
  
  <!-- Payment form - mix of concerns -->
  <div class="payment-form">
    <h2 class="fs-unmask">Make a Payment</h2>
    
    <!-- Amount - could reveal service cost = PHI context -->
    <div class="amount-field fs-exclude">
      <label>Payment Amount</label>
      <input type="number" name="amount" />
    </div>
    
    <!-- Card details - EXCLUDE (PCI + general privacy) -->
    <div class="card-details fs-exclude">
      <input type="text" name="cardNumber" />
      <input type="text" name="expiry" />
      <input type="text" name="cvv" />
    </div>
    
    <!-- Submit - visible -->
    <button class="fs-unmask" type="submit">Pay Now</button>
  </div>
</div>
```

---

## Additional Healthcare Contexts

### Pharmacy & Prescription Management

```html
<!-- Pharmacy Portal / Prescription Management -->
<div class="pharmacy-portal">
  <h1 class="fs-unmask">Prescription Management</h1>

  <!-- Prescription list - EXCLUDE entirely -->
  <div class="prescriptions-list fs-exclude">
    <!-- Contains: medication names, dosages, prescriber, pharmacy = ALL PHI -->
    <div class="prescription-card">
      <h3>Metformin 500mg</h3>
      <p>Prescribed by: Dr. Johnson</p>
      <p>Refills remaining: 2</p>
      <p>Pharmacy: CVS Main Street</p>
    </div>
  </div>

  <!-- Refill request form - EXCLUDE -->
  <div class="refill-request fs-exclude">
    <!-- Medication selection = PHI -->
    <select name="prescription">
      <option>Metformin 500mg</option>
      <option>Lisinopril 10mg</option>
    </select>
  </div>

  <!-- Pharmacy selection - EXCLUDE (reveals patient location/preferences) -->
  <div class="pharmacy-selector fs-exclude">
    <h2>Select Pharmacy</h2>
    <!-- Pharmacy choice can indicate location, conditions -->
    <div class="pharmacy-option">
      <p>CVS Pharmacy - Main St</p>
      <p>Specialties: Diabetes, Cardiac care</p>
    </div>
  </div>

  <!-- Action buttons - visible -->
  <div class="actions fs-unmask">
    <button>Request Refill</button>
    <button>Transfer Prescription</button>
  </div>
</div>
```

```javascript
// Pharmacy tracking - generic funnel only
FS('trackEvent', {
  name: 'Refill Request Started',
  properties: {
    request_method: 'online',  // or "phone", "auto_refill"
    // NEVER: medication name, dosage, pharmacy name
  }
});

FS('trackEvent', {
  name: 'Refill Request Completed',
  properties: {
    prescription_count: 2,  // Count only, no details
    delivery_method: 'pickup',  // or "mail"
    // NEVER: specific medications, pharmacy location
  }
});
```

### Medical Devices & Digital Health Apps

```html
<!-- Digital Health Dashboard -->
<div class="health-dashboard">
  <h1 class="fs-unmask">Health Metrics</h1>

  <!-- ALL device data - EXCLUDE -->
  <div class="health-metrics fs-exclude">
    <!-- Wearable/device data = PHI -->
    <div class="metric-card">
      <h3>Blood Glucose</h3>
      <p class="value">120 mg/dL</p>
      <p class="status">In Range</p>
      <p class="timestamp">Last reading: 2 hours ago</p>
    </div>

<div class="metric-card">
  <h3>Blood Pressure</h3>
  <p class="value">118/76 mmHg</p>
  <p class="status">Normal</p>
</div>

<div class="metric-card">
  <h3>Heart Rate</h3>
  <p class="value">72 bpm</p>
</div>

<!-- Activity data when health-related = PHI -->
<div class="metric-card">
  <h3>Daily Steps</h3>
  <p class="value">8,450 steps</p>
  <p>Goal: 10,000</p>
</div>

</div
  <!-- Device management - EXCLUDE (reveals conditions) -->
  <div class="connected-devices fs-exclude">
    <h2>Connected Devices</h2>
    <div class="device">
      <p>Continuous Glucose Monitor</p>
      <p>Model: Dexcom G6</p>
      <p>Last sync: 5 minutes ago</p>
    </div>
  </div>
</div>
```

```javascript
// Device tracking - technical only, NO health data
FS('trackEvent', {
  name: 'Device Data Synced',
  properties: {
    sync_method: 'bluetooth',  // or "wifi", "manual"
    sync_duration_ms: 2500,
    // NEVER: device readings, health metrics, device model (reveals condition)
  }
});

FS('trackEvent', {
  name: 'Device Connection Failed',
  properties: {
    error_type: 'bluetooth_timeout',
    retry_attempted: true,
    // Technical troubleshooting only
  }
});
```

### Clinical Trials & Research Applications

```html
<!-- Clinical Trial Participant Portal -->
<div class="trial-portal">
  <h1 class="fs-unmask">Clinical Trial Portal</h1>

  <!-- Trial enrollment status - EXCLUDE -->
  <div class="enrollment-status fs-exclude">
    <!-- Trial participation is PHI -->
    <p>Study: Phase III Diabetes Treatment Trial</p>
    <p>Your participant ID: PT-8847</p>
    <p>Enrollment date: Oct 15, 2024</p>
    <p>Study coordinator: Dr. Martinez</p>
  </div>

  <!-- Questionnaires - EXCLUDE ENTIRELY -->
  <div class="trial-questionnaire fs-exclude">
    <h2>Weekly Health Assessment</h2>
    <!-- Responses reveal health status -->
    <form>
      <label>Rate your symptoms this week (1-10)</label>
      <input type="range" min="1" max="10" />

  <label>Any new side effects?</label>
  <textarea></textarea>
  
  <label>Medication adherence</label>
  <select>
    <option>Took all doses</option>
    <option>Missed 1-2 doses</option>
  </select>
</form>

</div
  <!-- Visit schedule - EXCLUDE -->
  <div class="visit-schedule fs-exclude">
    <h2>Upcoming Study Visits</h2>
    <!-- Reveals trial participation -->
    <div class="visit">
      <p>Week 12 Assessment</p>
      <p>Dec 20, 2024 at 9:00 AM</p>
      <p>Location: Research Center, Building B</p>
    </div>
  </div>

  <!-- Action buttons - visible -->
  <div class="actions fs-unmask">
    <button>Complete Survey</button>
    <button>View Schedule</button>
    <button>Contact Coordinator</button>
  </div>
</div>
```

```javascript
// Clinical trial tracking - NEVER reveal participation
FS('trackEvent', {
  name: 'Study Survey Started',
  properties: {
    survey_type: 'weekly_assessment',
    delivery_method: 'portal',  // or "email_link", "app"
    // NEVER: study name, participant ID, responses
  }
});

// Adverse event reporting - technical only
FS('trackEvent', {
  name: 'Adverse Event Reported',
  properties: {
    report_method: 'online_form',
    urgency_level: 'routine',  // Generic severity only
    // NEVER: event details, symptoms, participant info
  }
});
```

### Health Insurance Member Portals

```html
<!-- Health Insurance Member Portal -->
<div class="insurance-portal">
  <h1 class="fs-unmask">Member Portal</h1>

  <!-- Coverage summary - EXCLUDE -->
  <div class="coverage-summary fs-exclude">
    <!-- Plan details linked to member = identifiable -->
    <p>Plan: Blue Cross PPO Gold</p>
    <p>Member ID: BC123456789</p>
    <p>Group: Tech Corp Employees</p>
    <p>Deductible: $500 / $1,500</p>
  </div>

  <!-- Claims history - EXCLUDE ENTIRELY (reveals PHI) -->
  <div class="claims-list fs-exclude">
    <!-- Claims = what care was received = PHI -->
    <div class="claim">
      <p>Date of Service: Nov 10, 2024</p>
      <p>Provider: Dr. Johnson, Internal Medicine</p>
      <p>Service: Office Visit - Follow-up</p>
      <p>Diagnosis: Type 2 Diabetes</p>  <!-- PHI! -->
      <p>Billed: $150.00</p>
      <p>Allowed: $120.00</p>
      <p>You owe: $30.00</p>
    </div>
  </div>

  <!-- EOB (Explanation of Benefits) - EXCLUDE -->
  <div class="eob-viewer fs-exclude">
    <!-- EOBs contain diagnosis codes, procedures = PHI -->
    <iframe src="/eob/claim-12345.pdf"></iframe>
  </div>

  <!-- Provider search - EXCLUDE (search reveals health needs) -->
  <div class="provider-search fs-exclude">
    <h2>Find a Provider</h2>
    <input type="text" placeholder="Search by specialty, condition, or name" />
    <!-- Search query = health information seeking = PHI context -->

<div class="search-filters">
  <label>Specialty</label>
  <select>
    <option>Endocrinology</option>  <!-- Reveals suspected condition -->
    <option>Cardiology</option>
    <option>Mental Health</option>
  </select>
</div>

</div
  <!-- ID card - EXCLUDE (contains member ID, group) -->
  <div class="id-card fs-exclude">
    <h2>Digital ID Card</h2>
    <!-- Contains member ID, group number = identifiers -->
    <img src="/id-card/member-123.png" alt="Insurance ID" />
  </div>

  <!-- Action buttons - visible -->
  <div class="actions fs-unmask">
    <button>View Claims</button>
    <button>Find Provider</button>
    <button>Download ID Card</button>
  </div>
</div>
```

```javascript
// Insurance portal tracking - generic only
FS('trackEvent', {
  name: 'Claims History Viewed',
  properties: {
    view_type: 'summary',  // or "detailed", "by_provider"
    date_range: '90_days',
    // NEVER: claim details, diagnoses, providers, amounts
  }
});

FS('trackEvent', {
  name: 'Provider Search Performed',
  properties: {
    search_method: 'specialty_filter',  // or "name", "location"
    results_count: 15,  // Count only
    // NEVER: specialty searched, provider names, location details
  }
});
```

### Pediatric Healthcare Applications

Pediatric applications face **dual compliance**: HIPAA + **COPPA** (Children's Online Privacy Protection Act) for users under 13.

**Additional COPPA requirements:**
- Parental consent required for data collection
- Cannot track children's behavior for targeted advertising
- Stricter notice requirements
- Must allow parents to review and delete child's data

**Fullstory Implications:**

```html
<!-- Pediatric Portal -->
<div class="pediatric-portal">
  <!-- Parent/Guardian interface - careful capture -->
  <div class="parent-section">
    <h1 class="fs-unmask">Parent Dashboard</h1>

<!-- Child's info - EXCLUDE (PHI + COPPA) -->
<div class="child-profile fs-exclude">
  <p>Patient: Emily Smith (Age 8)</p>
  <p>Pediatrician: Dr. Jones</p>
</div>
  
</div
  <!-- Child-facing interface - NEVER CAPTURE -->
  <div class="child-section fs-exclude">
    <!-- COPPA: Cannot track children's behavior -->
    <div class="games-activities">
      <!-- Do not capture any child interactions -->
    </div>
  </div>
</div>
```

**Recommendation:** 
- Use Fullstory **only for parent/guardian interfaces**
- **Never** on child-facing content or interactions
- Consider age-gating Fullstory initialization
- Verify parental consent before any capture
---

---

## What About Provider-Side Applications?

For EHR systems and provider-facing applications:

```javascript
// Provider applications should use VERY limited Fullstory
// Consider: Do you even need session replay?

// If you do use Fullstory on provider side:
// 1. Never capture patient data displayed on screen
// 2. Only track navigation and technical issues
// 3. Consider using it only for non-patient screens (admin, scheduling UI)

FS('setProperties', {
  type: 'page',
  properties: {
    page_type: 'ehr_dashboard',
    // Only track application name, not patient context
    ehr_module: 'scheduling',  // or "charting", "orders"
    // NEVER: patient MRN, patient name, visit type
  }
});
```

---

## Consent Considerations in Healthcare

```javascript
// Healthcare consent is complex - consult your compliance team

// Option 1: Don't use Fullstory on authenticated patient pages
// Safest option - use only on public pages

// Option 2: Get explicit consent
function initializeFullstoryWithConsent() {
  // Check if patient has consented to analytics
  if (patient.hasConsentedToAnalytics) {
    FS('setIdentity', {
      uid: sha256(patient.id),
      consent: true
    });
  } else {
    // Don't capture this session
    FS('shutdown');
  }
}

// Option 3: Capture no identifying data at all
// Anonymous sessions only, no linking
```

---

## BAA and Compliance Checklist

### Before Going Live

- [ ] **BAA signed** with Fullstory
- [ ] **Privacy by Default** mode enabled
- [ ] **All PHI screens excluded**
- [ ] **No patient identifiers** in user properties
- [ ] **No health data** in events
- [ ] **Images excluded** (could contain PHI)
- [ ] **Search excluded** (queries could contain symptoms)
- [ ] **Messages excluded** entirely
- [ ] **Test results excluded** entirely
- [ ] **Appointment details excluded**
- [ ] **Provider names excluded** (when with patient context)
- [ ] **Billing details excluded** (reveal services received)
- [ ] **Legal/compliance review** completed
- [ ] **Security team review** completed
- [ ] **Prescription data excluded** (medication names, dosages)
- [ ] **Device/wearable health data excluded**
- [ ] **Clinical trial portals** reviewed (consider not using FS)
- [ ] **Insurance claims excluded** (diagnoses, procedures, EOBs)
- [ ] **Provider searches excluded** (specialty searches reveal health needs)

---

## KEY TAKEAWAYS FOR AGENT

When helping healthcare clients with Fullstory:

1. **Default to exclusion**: In healthcare, fs-exclude is the default, not fs-unmask
2. **Masking is NOT sufficient**: Even masked text can reveal PHI through structure
3. **Everything is potentially PHI**: When in doubt, exclude it
4. **BAA is required**: Don't implement until legal has BAA in place
5. **Consider anonymous sessions**: May not need user identification at all
6. **Public vs. authenticated**: Very different rules apply
7. **Provider applications**: May not be appropriate for Fullstory at all

### What You CAN Track (Limited)

- Page navigation (not what's on the page)
- Button clicks (not the data submitted)
- Form completion rates (not form content)
- Error occurrence (not error details)
- Technical issues (connection, loading)
- Generic UI interactions

### What You CANNOT Track

- Any patient information
- Any health condition information
- Any provider information (in patient context)
- Appointment details
- Test results
- Messages
- Medications
- Insurance information
- Billing details
- Images of any kind

### Questions to Ask Healthcare Clients

1. "Do you have a BAA with Fullstory?"
2. "Is Fullstory in your HIPAA security assessment?"
3. "Are you using Private by Default mode?"
4. "Have you audited session replays for PHI exposure?"
5. "Is your implementation scoped to only non-PHI screens?"

### Red Flags

- Using fs-mask instead of fs-exclude for PHI
- Tracking appointment types or services
- Including provider names in events
- Capturing search queries
- Not having a BAA in place
- Using Fullstory on EHR screens

---

## REFERENCE LINKS

### Fullstory Official Documentation
- **HIPAA & Compliance Overview**: https://help.fullstory.com/hc/en-us/articles/360020624254-Fullstory-s-Comprehensive-Compliance-Program
- **Business Associate Agreement (BAA)**: https://help.fullstory.com/hc/en-us/articles/17635706856599-Business-Associate-Agreement-BAA
- **Security Overview / Security Policy**: https://www.fullstory.com/resources/security-policy/
- **Privacy by Default (Product Page)**: https://www.fullstory.com/platform/private-by-default/
- **Privacy by Default (Help Article)**: https://help.fullstory.com/hc/en-us/articles/360044349073-Fullstory-Private-by-Default

### Regulatory Resources
- **HIPAA Home**: https://www.hhs.gov/hipaa/
- **HIPAA Privacy Rule Summary**: https://www.hhs.gov/hipaa/for-professionals/privacy/laws-regulations/index.html
- **HIPAA Security Rule Overview**: https://www.hhs.gov/hipaa/for-professionals/security/
- **De-Identification Guidance**: https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/
- **FDA 21 CFR Part 11 Guidance**: https://www.fda.gov/regulatory-information/search-fda-guidance-documents/part-11-electronic-records-electronic-signatures-scope-and-application
- **COPPA Rule (Primary Text/Summary)**: https://www.ftc.gov/legal-library/browse/rules/childrens-online-privacy-protection-rule-coppa
- **COPPA Rulemaking Proceedings**: https://www.ftc.gov/enforcement/rules/rulemaking-regulatory-reform-proceedings/childrens-online-privacy-protection-rule
- **COPPA + HIPAA Intersection FAQ**: https://www.hhs.gov/hipaa/for-professionals/faq/227/does-hipaa-apply-to-an-health-app/index.html

### Related Fullstory Skills
- **Privacy Controls**: ../../core/fullstory-privacy-controls/SKILL.md
- **Privacy Strategy**: ../../meta/fullstory-privacy-strategy/SKILL.md
- **User Consent**: ../../core/fullstory-user-consent/SKILL.md
- **Identify Users**: ../../core/fullstory-identify-users/SKILL.md
- **Analytics Events**: ../../core/fullstory-analytics-events/SKILL.md
- **Capture Control**: ../../core/fullstory-capture-control/SKILL.md

### Fullstory Developer Documentation
- **Browser API Overview**: https://developer.fullstory.com/browser/getting-started/
- **Browser API Reference**: https://developer.fullstory.com/browser/
- **Privacy Controls API**: https://developer.fullstory.com/browser/privacy-controls/
- **Custom Properties**: https://developer.fullstory.com/browser/custom-properties/
- **Analytics Events**: https://developer.fullstory.com/browser/capture-events/analytics-events/

### Healthcare-Specific Resources
- **HIPAA Breach Notification Rule**: https://www.hhs.gov/hipaa/for-professionals/breach-notification/
- **HITECH Act Enforcement Rule**: https://www.hhs.gov/hipaa/for-professionals/special-topics/hitech-act-enforcement-interim-final-rule/
- **Health App Developer Guidance**: https://www.hhs.gov/hipaa/for-professionals/special-topics/health-apps/

---

*This skill document is specific to healthcare implementations. Always consult your HIPAA compliance officer and legal counsel before implementing Fullstory in a healthcare context. This guide does not constitute legal advice.*