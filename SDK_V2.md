# Squid SDK

## Introduction

Squid is a real-time website intelligence SDK that helps you understand who is visiting your site and how they interact with it.

It captures meaningful visitor activity such as:

- **Page engagement** - Track user attention and time on page
- **Click behavior** - Understand where users click and interact
- **Scroll depth** - Measure content engagement and readability
- **Form submissions** - Capture lead generation events
- **Site performance** - Monitor real-world page speed and loading

Squid can also **identify visitors** (when enabled), turning anonymous traffic into actionable insights.

All events are streamed instantly, giving you live visibility into user behavior and engagement — ready to power analytics, sales, marketing, and customer experience tools.

---

## Installation

To deploy Squid, add this snippet to your website's HTML `<head>` section:

```html
<script>
  ;(function(squid){
    window.$quid = {};
    document.head.appendChild((function(s){
      s.src='https://app.asksquid.ai/tfs/'+squid+'/v2/sdk';
      s.async=1;
      return s;
    })(document.createElement('script')));
  })('YOUR_SQUID_ID');
</script>
```

**Replace `'YOUR_SQUID_ID'`** with your actual Squid account identifier.

---

## Configuration Options

All options are **optional** and can be configured via the `window.$quid` object before initialization or passed to the `squid.init()` method.

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| [logger](#logger) | Object | `{ level: 0 }` | Controls SDK console logging verbosity |
| [lazy](#lazy) | Boolean | `true` | When `false`, SDK auto-connects on load |
| [performance](#performance) | Boolean | `false` | Enables browser performance tracking |
| [visitorID](#visitorid) | Boolean | `false` | Enables visitor identification and enrichment |
| [submits](#submits) | Boolean | `false` | Monitors form submissions |
| [scrolls](#scrolls) | Boolean | `false` | Tracks scroll depth |
| [clicks](#clicks) | Boolean | `false` | Records click events |

---

### logger

Controls the level of console logging output from Squid's SDK.

**Default:** `{ level: 0 }` (no logs)

#### Configuration

```javascript
window.$quid = {
  logger: { level: 2 }  // Info and Warning logs
};
```

#### Log Levels

| Level | Output | Use Case |
|-------|--------|----------|
| `0` | **None** | Production (default) - no console output |
| `1` | **Info** | Basic debugging - shows SDK lifecycle events |
| `2` | **Info + Warning** | Development - catch potential issues |
| `3` | **Info + Warning + Error** | Full debugging - all SDK messages |

#### When to Use

**Enable logging (`level: 1-3`) if:**
- You're integrating Squid for the first time
- You need to debug SDK behavior
- You want visibility into SDK lifecycle events
- You're troubleshooting connection issues

**Disable logging (`level: 0`) if:**
- You're running in production
- You want clean browser consoles
- You've confirmed everything works correctly

**Example:**

```javascript
// Development: See everything
window.$quid = {
  logger: { level: 3 }
};

// Production: Silent operation
window.$quid = {
  logger: { level: 0 }
};
```

---

### lazy

Controls whether Squid connects automatically when the SDK loads.

**Default:** `true` (manual initialization required)

#### What It Does

- **`lazy: true`** (default) → Squid waits for you to call `squid.init()` manually
- **`lazy: false`** → Squid connects automatically as soon as it loads

#### When to Use

**Set `lazy: false` (auto-connect) if:**
- You have a simple, static website
- You want Squid to start tracking immediately
- You don't need to wait for user authentication
- You want minimal setup

**Keep `lazy: true` (manual start) if:**
- You want full control over when tracking begins
- You need to wait for user login or consent
- You're using a single-page application (React, Vue, Angular, etc.)
- You need to configure options dynamically

#### Example

```javascript
// Auto-connect immediately
window.$quid = {
  lazy: false,
  performance: true,
  clicks: true
};

// Manual initialization (default)
window.$quid = {
  lazy: true  // or omit this line
};

// Later in your code, when ready:
await squid.init({
  performance: true,
  clicks: true
});
```

---

### performance

Enables browser performance metric collection.

**Default:** `false`

#### What It Does

Collects page performance data such as:

- **Page load time** - Total time to fully load the page
- **Time to interactive** - When the page becomes usable
- **Resource loading** - Script and stylesheet performance
- **Core Web Vitals** - LCP, FID, CLS metrics

This provides real-world performance insights from actual user sessions.

#### When to Use

**Enable if:**
- You care about site speed and user experience
- You want performance visibility across real users
- You're optimizing page load times
- You need to track Core Web Vitals
- You want to identify performance regressions

**Disable if:**
- You don't need performance data
- You already have performance monitoring
- You want minimal SDK overhead

#### Example

```javascript
window.$quid = {
  performance: true
};
```

---

### visitorID

Enables visitor identification and enrichment.

**Default:** `false`

#### What It Does

Allows Squid to identify and enrich visitor profiles:

- **`visitorID: false`** (default) → Visitors remain anonymous
- **`visitorID: true`** → Visitors can be identified and enriched with additional data

When enabled, Squid can match visitors to known profiles and enrich them with firmographic data (company, industry, size, etc.).

#### When to Use

**Enable if:**
- You want to know **who** is visiting (not just **what** they do)
- You run a B2B or SaaS business
- Sales teams need visibility into prospect activity
- You want to de-anonymize website traffic
- You want to trigger workflows based on visitor identity

**Disable if:**
- You prefer fully anonymous tracking
- You don't need visitor identification
- Privacy regulations restrict identification

#### Example

```javascript
window.$quid = {
  visitorID: true
};

// Later, when you know who the user is:
await squid.identify({
  email: 'user@company.com',
  firstName: 'John',
  lastName: 'Doe'
});
```

---

### submits

Enables form submission monitoring.

**Default:** `false`

#### What It Does

- Automatically detects standard HTML forms on your site
- Watches for form submission events
- **Only captures email fields** from submitted forms
- Does **not** collect other form fields (passwords, text, etc.)

#### When to Use

**Enable if:**
- You want to track inbound leads automatically
- You rely on contact forms for lead generation
- You want visibility into form submission activity
- You want form events connected to visitor profiles

**Disable if:**
- You don't want automatic form monitoring
- You prefer manual lead tracking
- You don't need email capture

#### Example

```javascript
window.$quid = {
  submits: true,
  visitorID: true  // Connect form submits to visitor profiles
};
```

---

### scrolls

Enables scroll depth tracking.

**Default:** `false`

#### What It Does

- Measures how far users scroll down a page
- Records scroll percentage milestones
- Enables scroll depth heatmaps
- Helps understand content engagement

#### When to Use

**Enable if:**
- You want scroll depth heatmaps
- You have long landing pages or blog posts
- You want to see where users drop off
- You want to measure content engagement
- You're optimizing page layout and content flow

**Disable if:**
- You don't need engagement depth insights
- You want minimal tracking overhead

#### Example

```javascript
window.$quid = {
  scrolls: true
};
```

---

### clicks

Enables click event tracking.

**Default:** `false`

#### What It Does

- Records all user click events
- Captures element information (tag, class, ID, text)
- Tracks click coordinates
- Enables click heatmaps
- Helps improve UX and conversions

#### When to Use

**Enable if:**
- You want click heatmaps
- You want to optimize buttons, CTAs, and navigation
- You need deeper interaction insights
- You care about user behavior analysis
- You're running A/B tests or conversion optimization

**Disable if:**
- You don't need click tracking
- You want minimal behavior tracking

#### Example

```javascript
window.$quid = {
  clicks: true
};
```

---

## Initialization

Squid can be initialized **automatically** or **manually**, depending on your needs.

### Automatic Initialization

Set `lazy: false` to auto-connect when the SDK loads. You **must** provide initial options.

```html
<script>
  ;(function(squid){
    window.$quid = {
      lazy: false,         // Auto-initialize when SDK loads
      performance: true,   // Collect performance metrics
      visitorID: true,     // Enable visitor identification
      submits: true,       // Monitor form submissions
      scrolls: true,       // Track scroll depth
      clicks: true,        // Record click events
      logger: { level: 1 } // Enable info logs
    };

    document.head.appendChild((function(s){
      s.src='https://app.asksquid.ai/tfs/'+squid+'/v2/sdk';
      s.async=1;
      return s;
    })(document.createElement('script')));
  })('YOUR_SQUID_ID');
</script>
```

---

### Manual Initialization

After [installing](#installation) the SDK, wait for it to load, then call `squid.init()` with your desired [options](#configuration-options).

```javascript
// Wait for SDK to load
if (typeof squid !== 'undefined') {
  await squid.init({
    performance: true,
    visitorID: true,
    submits: true,
    scrolls: true,
    clicks: true,
    logger: { level: 2 }
  });
}
```

---

## API Reference

### `squid.VERSION`

Returns the current SDK version as a string.

**Usage:**

```javascript
console.log(squid.VERSION);

```

---

### `squid.connection()`

Retrieves the current connection details and status.

**Usage:**

```javascript
const connection = await squid.connection();
console.log(connection);
```

**Response:**

```javascript
{
  id: "conn_abc123xyz",           // Unique connection identifier
  online: true,                    // Connection status
  device: "fp_device_hash",        // Device fingerprint
  identity: {
    id: "visitor_12345",           // Visitor's unique identifier
    identified: true               // Whether the visitor is identified
  }
}
```

**Fields:**

- **`id`** (string) - Unique connection identifier for this session
- **`online`** (boolean) - Whether the connection is currently active
- **`device`** (string) - Device fingerprint hash for this browser
- **`identity.id`** (string) - Unique visitor identifier
- **`identity.identified`** (boolean) - `true` if visitor has been identified, `false` for anonymous

---

### `squid.identify()`

Manually identifies a visitor and enriches their profile with known traits. This method also enriches the user with **VisitorID** data when enabled.

**Usage:**

```javascript
await squid.identify({
  id: 'user_12345',  // Your internal user ID
  firstName: 'John',
  lastName: 'Doe',
  name: 'John Doe',
  age: 32,
  gender: 'male',
  email: 'john.doe@example.com',
  birthday: '1992-05-15',
  phone: '+1-555-123-4567',
  company: 'Acme Corp',
  company_role: 'Product Manager',
  linkedin_url: 'https://linkedin.com/in/johndoe'
});
```

**Parameters:**

All parameters are **optional**, but at minimum you should provide either `id` or `email`.

| Parameter | Type | Description |
|-----------|------|-------------|
| `id` | String | Unique identifier for the visitor in your database. If not provided, Squid will generate one. |
| `firstName` | String | Visitor's first name |
| `lastName` | String | Visitor's last name |
| `name` | String | Visitor's full name |
| `age` | Number | Visitor's age |
| `gender` | String | Visitor's gender |
| `email` | String | Visitor's email address |
| `birthday` | String (ISO Date) | Visitor's birthday in ISO format |
| `phone` | String | Visitor's phone number |
| `company` | String | Visitor's company name |
| `company_role` | String | Visitor's role/title at their company |
| `linkedin_url` | String | Visitor's LinkedIn profile URL |

**Returns:** `Promise<void>`

**Example - Minimal Identification:**

```javascript
// Just identify by email
await squid.identify({
  email: 'user@example.com'
});
```

**Example - After User Login:**

```javascript
// After user logs in
async function onUserLogin(user) {
  await squid.identify({
    id: user.id,
    email: user.email,
    firstName: user.firstName,
    lastName: user.lastName
  });
}
```

---

### `squid.labelClicks()`

Tracks user click interactions by tagging specific DOM elements with custom labels. This allows you to define meaningful user actions like "Sign Up Clicked" or "Product Added to Cart" and stream them in real-time.

**Usage:**

```javascript
squid.labelClicks({
  matches: 'button.signup',           // CSS selector
  label: 'SIGNUP_BUTTON_CLICKED',     // Custom label
  type: 'conversion'                  // Event type
});
```

**Parameters:**

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `matches` | String | **Required** | CSS selector to match the element(s) you want to track |
| `label` | String | **Required** | Custom label that identifies this interaction |
| `type` | String | **Required** | Event type: `'key_event'` or `'conversion'` |

**Event Types:**

- **`key_event`** - Important but non-final user actions
  - Examples: Clicking a tab, opening a modal, viewing pricing
- **`conversion`** - Final, goal-oriented actions
  - Examples: Form submission, purchase, signup, download

**Examples:**

```javascript
// Track signup button clicks as a conversion
squid.labelClicks({
  matches: 'button#signup, .btn-signup',
  label: 'SIGNUP_CLICKED',
  type: 'conversion'
});

// Track pricing tab clicks as a key event
squid.labelClicks({
  matches: '.pricing-tab',
  label: 'PRICING_TAB_VIEWED',
  type: 'key_event'
});

// Track product add-to-cart as conversion
squid.labelClicks({
  matches: '.add-to-cart',
  label: 'PRODUCT_ADDED_TO_CART',
  type: 'conversion'
});

// Track navigation menu interactions
squid.labelClicks({
  matches: 'nav .menu-item',
  label: 'NAV_MENU_CLICKED',
  type: 'key_event'
});
```

---

### `squid.engine()`

Connects to Squid's real-time event engine. This allows you to react to visitor events and enrich visitor identity with your own data.

**Usage:**

```javascript
const engine = new squid.engine();

// Listen for events
engine.on('visitor.identified', (event) => {
  console.log('Visitor identified:', event.detail);
});
```

**Constructor Options:**

```javascript
const engine = new squid.engine({
  sp: 'https://myserver.com'  // Optional: Your service provider URL
});
```

| Option | Type | Description |
|--------|------|-------------|
| `sp` | String | Optional service provider URL. When set, Squid will POST to your server to enrich event data. |

---

#### Event: `visitor.identified`

Fires when Squid syncs a visitor **and** the visitor is identified.

**Usage:**

```javascript
const engine = new squid.engine();

engine.on('visitor.identified', (event) => {
  const visitor = event.detail;
  console.log('Identified visitor:', visitor);

  // Example: Show personalized content
  document.querySelector('#welcome').textContent =
    `Welcome back, ${visitor.firstName}!`;
});
```

**Using a Custom Service Provider:**

When you provide a service provider URL (`sp`), Squid will POST visitor data to your endpoint, allowing you to enrich it with your own database information.

```javascript
const engine = new squid.engine({
  sp: 'https://api.mycompany.com'
});

// Squid will POST to: https://api.mycompany.com/visitor_details
// Request body: { visitorID, email, firstName, ... }

engine.on('visitor.identified', (event) => {
  // event.detail now contains YOUR enriched response
  const enrichedData = event.detail;
  console.log('Enriched visitor data:', enrichedData);
});
```

**Your Service Provider Endpoint:**

```javascript
// POST /visitor_details
// Receives: { visitorID, email, firstName, lastName, ... }

app.post('/visitor_details', async (req, res) => {
  const { email } = req.body;

  // Fetch additional data from your database
  const userData = await db.users.findOne({ email });

  // Return enriched data
  res.json({
    ...req.body,
    accountType: userData.accountType,
    lifetimeValue: userData.lifetimeValue,
    lastPurchase: userData.lastPurchaseDate
  });
});
```

---

#### Event: `visitor.unknown`

Fires when Squid syncs a visitor **and** the visitor is unknown or anonymous.

**Usage:**

```javascript
const engine = new squid.engine();

engine.on('visitor.unknown', (event) => {
  console.log('Unknown visitor detected');

  // Example: Show signup prompt
  showSignupModal();
});
```

---

#### Event: `visitor.synced`

Fires when Squid syncs a visitor, **regardless** of whether they are identified or not.

**Usage:**

```javascript
const engine = new squid.engine();

engine.on('visitor.synced', (event) => {
  const visitor = event.detail;
  console.log('Visitor synced:', visitor);

  // Always fires on sync, whether identified or not
  if (visitor.identified) {
    console.log('Known visitor:', visitor.id);
  } else {
    console.log('Anonymous visitor');
  }
});
```

---

#### Event: `error`

Fires when an error occurs with your service provider integration.

**Usage:**

```javascript
const engine = new squid.engine({
  sp: 'https://api.mycompany.com'
});

engine.on('error', (event) => {
  console.error('Service provider error:', event.detail);
});

engine.on('visitor.identified', (event) => {
  console.log('Visitor:', event.detail);
});
```

---

### Basic Setup (Simple Website)

```html
<script>
  ;(function(squid){
    window.$quid = {
      lazy: false,
      clicks: true,
      scrolls: true,
      performance: true,
      visitorID: true
    };

    document.head.appendChild((function(s){
      s.src='https://app.asksquid.ai/tfs/'+squid+'/v2/sdk';
      s.async=1;
      return s;
    })(document.createElement('script')));
  })('YOUR_SQUID_ID');
</script>
```

## Support

For questions, issues, or feature requests, please contact:

- **Documentation:** [https://docs.asksquid.ai](https://docs.asksquid.ai)
- **Support:** support@asksquid.ai
- **Dashboard:** [https://app.asksquid.ai](https://app.asksquid.ai)
