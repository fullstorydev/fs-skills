---
name: fullstory-gambling
version: v2
description: Industry-specific guide for implementing Fullstory in gambling, betting, and gaming applications. Covers regulatory requirements (responsible gambling, KYC/AML), privacy controls for sensitive betting data, deposit/withdrawal flows, player protection features, and self-exclusion compliance. Includes detailed examples for sportsbooks, casinos, poker, and lottery applications.
related_skills:
  - fullstory-privacy-controls
  - fullstory-privacy-strategy
  - fullstory-user-consent
  - fullstory-identify-users
  - fullstory-analytics-events
---

# Fullstory for Gambling & Gaming

> ⚠️ **LEGAL DISCLAIMER**: This guidance is for educational purposes only and does not constitute legal, compliance, or regulatory advice. Gambling regulations vary significantly by jurisdiction and are subject to strict licensing requirements. Always consult with your legal, compliance, and responsible gambling teams before implementing any data capture solution. Your organization is responsible for ensuring compliance with all applicable gaming regulations and licensing requirements.

## Industry Overview

Gambling and gaming platforms have unique requirements due to:

- **Heavy regulation**: Jurisdiction-specific licensing requirements
- **Responsible gambling**: Mandatory player protection features
- **KYC/AML compliance**: Identity verification requirements
- **Financial sensitivity**: Deposits, withdrawals, bet amounts
- **Addiction concerns**: Behavior patterns may indicate problem gambling
- **Age verification**: Must ensure 18+/21+ compliance

### Key Goals for Gambling Implementations

1. **Optimize conversion funnels** from registration to first bet/deposit
2. **Improve user experience** across betting flows
3. **Monitor responsible gambling features** (limits, timeouts, self-exclusion)
4. **Ensure compliance** with regulatory requirements
5. **Reduce friction** in deposit/withdrawal processes
6. **Understand player engagement** without enabling surveillance

---

## Regulatory Framework

### Key Regulations by Jurisdiction

| Jurisdiction | Key Regulations | FullStory Implications |
|--------------|-----------------|------------------------|
| **UK** | UKGC License Conditions | Responsible gambling tracking, self-exclusion |
| **Malta** | MGA Requirements | Player protection, data localization |
| **US (NJ, PA, etc.)** | State Gaming Commissions | KYC data handling, geolocation |
| **EU** | GDPR + Local Gaming Laws | Consent, data minimization |
| **Australia** | Interactive Gambling Act | Responsible gambling features |

### Data That MUST Be Handled Carefully

| Data Type | Sensitivity | Implementation |
|-----------|-------------|----------------|
| Identity documents | HIGH | Never capture images |
| SSN/Tax ID | HIGH | fs-exclude |
| Bank account details | HIGH | fs-exclude |
| Card details | HIGH | fs-exclude (PCI) |
| Bet amounts | MEDIUM | Consider ranges |
| Win/loss amounts | HIGH | fs-exclude or ranges |
| Deposit/withdrawal amounts | HIGH | fs-exclude or ranges |
| Account balance | HIGH | fs-exclude |
| Betting history | MEDIUM | Consider implications |
| Self-exclusion status | HIGH | Never expose |
| Responsible gambling limits | HIGH | Handle carefully |
| Problem gambling indicators | HIGH | Never analyze in FS |

---

## Implementation Architecture

### Privacy Zones for Gambling

```
┌─────────────────────────────────────────────────────────────────┐
│                    GAMBLING APPLICATION                          │
├─────────────────────────────────────────────────────────────────┤
│  FULLY VISIBLE (fs-unmask)                                       │
│  • Sports events, odds display                                   │
│  • Casino game selection                                         │
│  • Navigation and UI                                            │
│  • Promotional banners                                          │
│  • General error messages                                       │
│  • Feature buttons (not amounts)                                │
├─────────────────────────────────────────────────────────────────┤
│  MASKED (fs-mask)                                                │
│  • Player name                                                  │
│  • Email address                                                │
│  • Phone number                                                 │
│  • Partial address (city/state OK)                              │
│  • Username (if not full name)                                  │
├─────────────────────────────────────────────────────────────────┤
│  EXCLUDED (fs-exclude)                                           │
│  • Account balance                                              │
│  • Bet slips (amounts)                                          │
│  • Transaction history amounts                                  │
│  • Deposit/withdrawal amounts                                   │
│  • Win/loss totals                                              │
│  • ID verification screens                                      │
│  • Payment card details                                         │
│  • SSN/Tax ID                                                   │
│  • Bank account details                                         │
│  • Self-exclusion settings                                      │
│  • Responsible gambling limits                                  │
│  • Problem gambling questionnaires                              │
└─────────────────────────────────────────────────────────────────┘
```

### User Identification Pattern

```javascript
// Gambling: Use player ID, never use PII
function onLogin(player) {
  FS('setIdentity', {
    uid: player.playerId,  // e.g., "PLR-789456"
    displayName: `Player ${player.playerId.slice(-4)}`
  });
  
  FS('setProperties', {
    type: 'user',
    properties: {
      // Segmentation (non-sensitive)
      account_type: player.accountType,  // "standard", "vip", "high_roller"
      account_age_days: daysSince(player.registeredAt),
      verification_status: player.kycStatus,  // "pending", "verified", "restricted"
      preferred_product: player.topProduct,  // "sports", "casino", "poker"
      
      // Engagement (generic)
      days_since_last_bet: daysSince(player.lastBetAt),
      active_days_30d: player.activeDays30d,
      
      // Jurisdiction
      jurisdiction: player.jurisdiction,  // "UK", "NJ", "Malta"
      currency: player.currency,
      
      // Feature access
      has_deposit_limit: player.limits.depositLimit !== null,
      has_loss_limit: player.limits.lossLimit !== null,
      has_session_limit: player.limits.sessionLimit !== null,
      
      // Marketing
      bonus_eligible: player.bonusEligible,
      email_opted_in: player.emailOptIn,
      
      // NEVER include:
      // balance, lifetime_deposits, lifetime_losses, betting_history
    }
  });
}
```

---

## Page-Specific Implementations

### Registration / KYC Flow

```html
<!-- Registration Page -->
<div class="registration-page">
  <h1 class="fs-unmask">Create Your Account</h1>
  
  <!-- Step indicator - visible -->
  <div class="progress fs-unmask">
    <span class="active">1. Details</span>
    <span>2. Verify</span>
    <span>3. Deposit</span>
  </div>
  
  <!-- Personal details - MASK -->
  <section class="personal-details">
    <h2 class="fs-unmask">Personal Information</h2>
    
    <div class="form-fields fs-mask">
      <input type="text" name="firstName" placeholder="First Name" />
      <input type="text" name="lastName" placeholder="Last Name" />
      <input type="email" name="email" placeholder="Email" />
      <input type="tel" name="phone" placeholder="Phone" />
      <input type="date" name="dob" placeholder="Date of Birth" />
    </div>
  </section>
  
  <!-- Address - MASK -->
  <section class="address-details fs-mask">
    <h2 class="fs-unmask">Address</h2>
    <input type="text" name="address1" placeholder="Street Address" />
    <input type="text" name="city" placeholder="City" />
    <select name="state"><!-- States --></select>
    <input type="text" name="zip" placeholder="ZIP Code" />
  </section>
  
  <!-- ID verification - EXCLUDE completely -->
  <section class="id-verification">
    <h2 class="fs-unmask">Identity Verification</h2>
    
    <div class="id-upload fs-exclude">
      <!-- ID document upload - NEVER capture -->
      <p>Upload a photo of your ID</p>
      <input type="file" name="idDocument" />
      <img id="id-preview" />
    </div>
    
    <div class="ssn-field fs-exclude">
      <label>Last 4 of SSN (US only)</label>
      <input type="text" name="ssn4" maxlength="4" />
    </div>
  </section>
  
  <!-- Terms - visible -->
  <section class="terms fs-unmask">
    <label>
      <input type="checkbox" name="terms" />
      I am 21+ and agree to the Terms of Service
    </label>
    <label>
      <input type="checkbox" name="gambling_aware" />
      I acknowledge the responsible gambling information
    </label>
  </section>
  
  <!-- Actions - visible -->
  <div class="form-actions fs-unmask">
    <button type="submit">Create Account</button>
  </div>
</div>
```

```javascript
// Registration tracking
function trackRegistrationStep(step, data) {
  FS('trackEvent', {
    name: 'registration_step_completed',
    properties: {
      step_number: step,
      step_name: data.stepName,  // "details", "verification", "deposit"
      jurisdiction: data.jurisdiction,
      // Never include PII
    }
  });
}

function onRegistrationComplete(player) {
  FS('trackEvent', {
    name: 'registration_completed',
    properties: {
      registration_method: 'email',  // "email", "social", "sso"
      jurisdiction: player.jurisdiction,
      verification_method: player.kycMethod,  // "document", "instant"
      time_to_complete_minutes: getRegistrationDuration()
    }
  });
}
```

### Sports Betting - Event Browsing

```html
<!-- Sportsbook - Event List -->
<div class="sportsbook">
  <!-- Navigation - visible -->
  <nav class="sport-nav fs-unmask">
    <a href="/football">Football</a>
    <a href="/basketball">Basketball</a>
    <a href="/tennis">Tennis</a>
    <a href="/soccer">Soccer</a>
  </nav>
  
  <!-- Live/upcoming toggle - visible -->
  <div class="event-filter fs-unmask">
    <button class="active">Live</button>
    <button>Today</button>
    <button>This Week</button>
  </div>
  
  <!-- Event list - visible (public sports data) -->
  <div class="events-list fs-unmask">
    <div class="event-card"
         data-fs-element="Event Card"
         data-fs-properties-schema='{"event_id":"string","sport":"string","competition":"string","is_live":"bool"}'>
      <div class="event-header">
        <span class="sport-icon">🏈</span>
        <span class="competition">NFL - Week 14</span>
        <span class="live-badge">LIVE</span>
      </div>
      
      <div class="event-matchup">
        <div class="team home">
          <span class="name">Kansas City Chiefs</span>
          <span class="score">21</span>
        </div>
        <div class="team away">
          <span class="name">Buffalo Bills</span>
          <span class="score">17</span>
        </div>
      </div>
      
      <!-- Odds display - visible (public data) -->
      <div class="odds-display">
        <button class="odd" data-selection="home_ml">
          Chiefs -150
        </button>
        <button class="odd" data-selection="spread">
          -3.5 (-110)
        </button>
        <button class="odd" data-selection="away_ml">
          Bills +130
        </button>
      </div>
    </div>
    <!-- More events... -->
  </div>
</div>
```

```javascript
// Sports browsing tracking
FS('setProperties', {
  type: 'page',
  properties: {
    page_type: 'sportsbook',
    sport: 'football',
    competition: 'NFL',
    event_view: 'live',
    event_count: 12
  }
});

// Event view
function onEventView(event) {
  FS('trackEvent', {
    name: 'event_viewed',
    properties: {
      event_id: event.id,
      sport: event.sport,
      competition: event.competition,
      is_live: event.isLive,
      market_count: event.markets.length
    }
  });
}

// Odds click (selection added to bet slip)
function onOddsClick(selection) {
  FS('trackEvent', {
    name: 'selection_added',
    properties: {
      event_id: selection.eventId,
      sport: selection.sport,
      market_type: selection.marketType,  // "moneyline", "spread", "total"
      selection_type: selection.type,  // "home", "away", "over", "under"
      // Don't include odds values - they change
    }
  });
}
```

### Bet Slip

```html
<!-- Bet Slip -->
<div class="bet-slip">
  <h2 class="fs-unmask">Bet Slip</h2>
  
  <!-- Selections - visible (public event data) -->
  <div class="selections fs-unmask">
    <div class="selection">
      <button class="remove">×</button>
      <div class="selection-info">
        <span class="event">Chiefs vs Bills</span>
        <span class="pick">Kansas City Chiefs -3.5</span>
        <span class="odds">-110</span>
      </div>
    </div>
    <!-- More selections... -->
  </div>
  
  <!-- Bet type - visible -->
  <div class="bet-type fs-unmask">
    <button class="active">Single</button>
    <button>Parlay</button>
  </div>
  
  <!-- Stake input - EXCLUDE (financial) -->
  <div class="stake-section fs-exclude">
    <label>Stake</label>
    <input type="number" name="stake" placeholder="$0.00" />
    
    <!-- Quick stake buttons - visible pattern, not amounts -->
    <div class="quick-stakes fs-unmask">
      <button data-amount="10">$10</button>
      <button data-amount="25">$25</button>
      <button data-amount="50">$50</button>
      <button data-amount="100">$100</button>
    </div>
  </div>
  
  <!-- Potential payout - EXCLUDE -->
  <div class="payout-display fs-exclude">
    <span class="label">To Win:</span>
    <span class="amount">$90.91</span>
    <span class="label">Total Payout:</span>
    <span class="amount">$190.91</span>
  </div>
  
  <!-- Place bet button - visible for funnel -->
  <button class="place-bet fs-unmask">Place Bet</button>
</div>
```

```javascript
// Bet slip tracking
FS('trackEvent', {
  name: 'bet_slip_viewed',
  properties: {
    selection_count: selections.length,
    bet_type: 'single',  // "single", "parlay", "teaser"
    sports: [...new Set(selections.map(s => s.sport))],
    // Never: stake amount, potential payout
  }
});

// Bet placement (use ranges for amounts)
function onBetPlaced(bet) {
  FS('trackEvent', {
    name: 'bet_placed',
    properties: {
      bet_id: bet.id,
      bet_type: bet.type,
      selection_count: bet.selections.length,
      sports: bet.sports,
      stake_range: getStakeRange(bet.stake),  // "$1-$10", "$10-$50", etc.
      is_live_bet: bet.isLive,
      // Never: exact stake, potential win
    }
  });
}

function getStakeRange(stake) {
  if (stake <= 10) return '$1-$10';
  if (stake <= 50) return '$10-$50';
  if (stake <= 100) return '$50-$100';
  if (stake <= 500) return '$100-$500';
  return '$500+';
}
```

### Casino Game Lobby

```html
<!-- Casino Lobby -->
<div class="casino-lobby">
  <h1 class="fs-unmask">Casino Games</h1>
  
  <!-- Category nav - visible -->
  <nav class="game-categories fs-unmask">
    <a href="#slots">Slots</a>
    <a href="#table">Table Games</a>
    <a href="#live">Live Casino</a>
    <a href="#jackpots">Jackpots</a>
  </nav>
  
  <!-- Game grid - visible (game names are public) -->
  <div class="game-grid fs-unmask">
    <div class="game-card"
         data-fs-element="Game Card"
         data-fs-properties-schema='{"game_id":"string","game_name":"string","game_type":"string","provider":"string"}'>
      <img src="slot-thumbnail.jpg" alt="Starburst" />
      <h3>Starburst</h3>
      <p class="provider">NetEnt</p>
      <button class="play-btn">Play Now</button>
      <button class="demo-btn">Demo</button>
    </div>
    <!-- More games... -->
  </div>
  
  <!-- Jackpot displays - values can be visible (they're promotional) -->
  <div class="jackpot-tickers fs-unmask">
    <div class="jackpot">
      <span class="name">Mega Moolah</span>
      <span class="amount">$15,234,567.89</span>
    </div>
  </div>
</div>
```

```javascript
// Casino lobby tracking
FS('setProperties', {
  type: 'page',
  properties: {
    page_type: 'casino_lobby',
    game_category: 'all',
    game_count: 250,
    featured_game_count: 12
  }
});

// Game launch
function onGameLaunch(game, mode) {
  FS('trackEvent', {
    name: 'game_launched',
    properties: {
      game_id: game.id,
      game_name: game.name,
      game_type: game.type,  // "slot", "table", "live"
      provider: game.provider,
      play_mode: mode,  // "real", "demo"
      is_mobile: isMobile()
    }
  });
}
```

### Deposit Flow

```html
<!-- Deposit Page -->
<div class="deposit-page">
  <h1 class="fs-unmask">Deposit</h1>
  
  <!-- Current balance - EXCLUDE -->
  <div class="current-balance fs-exclude">
    <span>Current Balance: $250.00</span>
  </div>
  
  <!-- Payment method selection - visible -->
  <div class="payment-methods fs-unmask">
    <h2>Select Payment Method</h2>
    <button class="method" data-method="card">
      <img src="card-icon.svg" alt="" />
      Credit/Debit Card
    </button>
    <button class="method" data-method="paypal">
      <img src="paypal-icon.svg" alt="" />
      PayPal
    </button>
    <button class="method" data-method="skrill">
      <img src="skrill-icon.svg" alt="" />
      Skrill
    </button>
    <button class="method" data-method="bank">
      <img src="bank-icon.svg" alt="" />
      Bank Transfer
    </button>
  </div>
  
  <!-- Amount selection - EXCLUDE specific amounts -->
  <div class="amount-section">
    <h2 class="fs-unmask">Select Amount</h2>
    
    <!-- Quick amounts - can show buttons, not selections -->
    <div class="quick-amounts fs-unmask">
      <button data-amount="20">$20</button>
      <button data-amount="50">$50</button>
      <button data-amount="100">$100</button>
      <button data-amount="200">$200</button>
    </div>
    
    <!-- Custom amount - EXCLUDE -->
    <div class="custom-amount fs-exclude">
      <input type="number" name="amount" placeholder="Custom amount" />
    </div>
    
    <!-- Deposit limits display - EXCLUDE (sensitive) -->
    <div class="limit-info fs-exclude">
      <p>Daily limit: $500 (remaining: $300)</p>
    </div>
  </div>
  
  <!-- Card form - EXCLUDE -->
  <div class="card-form fs-exclude">
    <input type="text" name="cardNumber" placeholder="Card Number" />
    <input type="text" name="expiry" placeholder="MM/YY" />
    <input type="text" name="cvv" placeholder="CVV" />
  </div>
  
  <!-- Submit - visible -->
  <button class="submit-deposit fs-unmask">Deposit</button>
</div>
```

```javascript
// Deposit tracking
function onDepositInitiated(method) {
  FS('trackEvent', {
    name: 'deposit_initiated',
    properties: {
      payment_method: method,  // "card", "paypal", "skrill", "bank"
      is_first_deposit: player.depositCount === 0,
      // Never: amount
    }
  });
}

function onDepositCompleted(deposit) {
  FS('trackEvent', {
    name: 'deposit_completed',
    properties: {
      payment_method: deposit.method,
      amount_range: getAmountRange(deposit.amount),
      is_first_deposit: deposit.isFirst,
      time_to_complete_seconds: deposit.processingTime
      // Never: exact amount
    }
  });
}

function onDepositFailed(error) {
  FS('trackEvent', {
    name: 'deposit_failed',
    properties: {
      payment_method: error.method,
      error_category: categorizeDepositError(error),  // "declined", "limit_exceeded", "technical"
      // Never: exact amount, card details
    }
  });
}
```

### Responsible Gambling Features

This is extremely sensitive - track usage patterns for UX but NEVER track specific limits or self-exclusion details.

```html
<!-- Responsible Gambling Page - VERY SENSITIVE -->
<div class="responsible-gambling">
  <h1 class="fs-unmask">Responsible Gambling Tools</h1>
  
  <!-- Feature overview - visible -->
  <div class="rg-tools-overview fs-unmask">
    <p>We provide tools to help you stay in control.</p>
  </div>
  
  <!-- All limit settings - EXCLUDE completely -->
  <section class="limits-section fs-exclude">
    <h2>Deposit Limits</h2>
    <div class="limit-setting">
      <label>Daily Limit</label>
      <input type="number" name="dailyLimit" />
    </div>
    <div class="limit-setting">
      <label>Weekly Limit</label>
      <input type="number" name="weeklyLimit" />
    </div>
    <div class="limit-setting">
      <label>Monthly Limit</label>
      <input type="number" name="monthlyLimit" />
    </div>
  </section>
  
  <!-- Session limits - EXCLUDE -->
  <section class="session-limits fs-exclude">
    <h2>Session Limits</h2>
    <div class="limit-setting">
      <label>Session Time Limit</label>
      <select name="sessionLimit">
        <option>No limit</option>
        <option>1 hour</option>
        <option>2 hours</option>
      </select>
    </div>
  </section>
  
  <!-- Self-exclusion - EXCLUDE completely -->
  <section class="self-exclusion fs-exclude">
    <h2>Take a Break</h2>
    <button>24-hour cool-off</button>
    <button>7-day break</button>
    <button>30-day break</button>
    <button>Self-exclude (6+ months)</button>
  </section>
  
  <!-- Reality check settings - EXCLUDE -->
  <section class="reality-check fs-exclude">
    <h2>Reality Check</h2>
    <p>Get reminders about your session length</p>
    <select name="realityCheck">
      <option>Every 30 minutes</option>
      <option>Every 60 minutes</option>
    </select>
  </section>
  
  <!-- Help resources - visible (important for users) -->
  <section class="help-resources fs-unmask">
    <h2>Get Help</h2>
    <a href="tel:1-800-522-4700">National Problem Gambling Helpline</a>
    <a href="https://www.gamblersanonymous.org">Gamblers Anonymous</a>
  </section>
</div>
```

```javascript
// Responsible gambling page tracking - BE VERY CAREFUL
FS('setProperties', {
  type: 'page',
  properties: {
    page_type: 'responsible_gambling',
    // Only track that they visited, not what they set
  }
});

// Track only that RG features were accessed (for UX research on feature discoverability)
// NEVER track specific limits, self-exclusion requests, or problem gambling indicators

function onRGPageView() {
  FS('trackEvent', {
    name: 'responsible_gambling_page_viewed',
    properties: {
      referrer_type: getReferrerType()  // "menu", "footer", "prompt"
      // NEVER: what limits they have, what they're changing
    }
  });
}

// You might track if help resources were clicked (to ensure they work)
function onHelpResourceClick(resource) {
  FS('trackEvent', {
    name: 'help_resource_clicked',
    properties: {
      resource_type: resource.type  // "helpline", "external_site"
      // Don't track which specific resource
    }
  });
}
```

### Account / Transaction History

```html
<!-- Transaction History -->
<div class="transaction-history">
  <h1 class="fs-unmask">Transaction History</h1>
  
  <!-- Filters - visible -->
  <div class="filters fs-unmask">
    <select name="type">
      <option>All</option>
      <option>Deposits</option>
      <option>Withdrawals</option>
      <option>Bets</option>
      <option>Wins</option>
    </select>
    <select name="period">
      <option>Last 7 days</option>
      <option>Last 30 days</option>
      <option>Last 90 days</option>
    </select>
    <button>Apply</button>
  </div>
  
  <!-- Transaction list - EXCLUDE amounts -->
  <div class="transaction-list fs-exclude">
    <!-- All transaction details are sensitive:
         - Amounts reveal gambling activity
         - Win/loss patterns
         - Deposit frequency
    -->
    <div class="transaction">
      <span class="date">Dec 1, 2024</span>
      <span class="type">Deposit</span>
      <span class="method">Visa ****1234</span>
      <span class="amount">+$100.00</span>
    </div>
    <div class="transaction">
      <span class="date">Dec 1, 2024</span>
      <span class="type">Bet - NFL</span>
      <span class="details">Chiefs vs Bills</span>
      <span class="amount">-$25.00</span>
    </div>
  </div>
  
  <!-- Pagination - visible -->
  <div class="pagination fs-unmask">
    <button>Previous</button>
    <button>Next</button>
  </div>
</div>
```

---

## Player Behavior Considerations

### What NOT to Analyze in FullStory

Some analyses could facilitate problem gambling identification or discrimination:

❌ **Never analyze or segment by:**
- Session duration patterns (could indicate addiction)
- Loss chasing behavior
- Deposit frequency
- Late-night gambling patterns
- Betting amount trends
- Win/loss ratios

✅ **OK to analyze:**
- Feature usability (did they find the bet slip?)
- Navigation paths (how do users find games?)
- Error rates (are deposits failing?)
- Mobile vs desktop usage
- A/B test results for UX changes

---

## Jurisdiction-Specific Considerations

### UK (UKGC)

```javascript
// UK-specific: Track interaction with affordability checks
// But NEVER track the actual amounts or outcomes
FS('trackEvent', {
  name: 'affordability_check_shown',
  properties: {
    trigger: 'deposit_threshold',  // Why it was shown
    // Never: deposit amount, check result
  }
});
```

### US State-by-State

```javascript
// Track geolocation verification
FS('trackEvent', {
  name: 'geolocation_verification',
  properties: {
    state: geoResult.state,
    success: geoResult.success,
    method: geoResult.method  // "gps", "ip", "manual"
    // Never: exact coordinates
  }
});
```

---

## Common Gambling Patterns

### Amount Range Helper

```javascript
function getAmountRange(amount) {
  if (amount <= 10) return '$1-$10';
  if (amount <= 25) return '$10-$25';
  if (amount <= 50) return '$25-$50';
  if (amount <= 100) return '$50-$100';
  if (amount <= 250) return '$100-$250';
  if (amount <= 500) return '$250-$500';
  if (amount <= 1000) return '$500-$1000';
  return '$1000+';
}
```

### Session Duration (Generic)

```javascript
// Track session duration in bands, not exact times
function getSessionDurationBand(minutes) {
  if (minutes < 15) return '<15min';
  if (minutes < 30) return '15-30min';
  if (minutes < 60) return '30-60min';
  if (minutes < 120) return '1-2hours';
  return '2hours+';  // Don't get more specific - could indicate problem gambling
}
```

---

## KEY TAKEAWAYS FOR CLAUDE

When helping gambling clients with FullStory:

1. **Regulatory awareness**: Different jurisdictions have different requirements
2. **Financial data is highly sensitive**: Always exclude or use ranges
3. **Responsible gambling features**: NEVER analyze specific limit settings or self-exclusion
4. **Behavior patterns**: Be careful about what patterns you enable - could indicate addiction
5. **KYC/AML data**: ID documents, SSN must never be captured
6. **Public data is OK**: Odds, game names, sports events are fine

### Questions to Ask Gambling Clients

1. "What jurisdictions do you operate in?"
2. "How do you handle responsible gambling feature tracking?"
3. "Is your session replay access audited?"
4. "How do you ensure self-exclusion data isn't exposed?"
5. "Are ID verification screens properly excluded?"

### Red Flags to Watch For

- Tracking specific bet/deposit amounts
- Analyzing session duration patterns
- Segmenting users by gambling frequency
- Capturing ID verification screens
- Exposing self-exclusion or limit settings
- Tracking reality check dismissals

### Ethical Considerations

Remember: FullStory in gambling should help improve UX, NOT enable:
- Problem gambling identification for marketing
- Surveillance of player behavior
- Discrimination based on gambling patterns

The goal is better UX for all players, not optimizing revenue extraction.

---

## REFERENCE LINKS

- **UKGC Guidance**: https://www.gamblingcommission.gov.uk/
- **Responsible Gambling Council**: https://www.responsiblegambling.org/
- **FullStory Privacy Controls**: ../core/fullstory-privacy-controls/SKILL.md
- **FullStory Privacy Strategy**: ../meta/fullstory-privacy-strategy/SKILL.md

---

*This skill document is specific to gambling and gaming implementations. Always consult your compliance team and legal counsel regarding jurisdiction-specific requirements.*

