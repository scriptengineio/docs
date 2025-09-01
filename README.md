# Squid SDK

## Introduction

Squid SDK is a real-time event streaming library designed to capture user interactions such as clicks, scrolls, and form submissions while also collecting browser performance metrics. The SDK automatically de-anonymizes users and enriches their identities. All of this happens in real-time through WebSockets, enabling seamless integration with your analytics and customer engagement platforms.

## Install
In order to deploy squid is required to add this snippet in your website's HTML

```html
<script>
  ;(function(squid){
    (window.$quid) || (window.$quid = {});
    document.head.appendChild((function(s){ s.src='https://app.asksquid.ai/tfs/'+squid+'/sdk';s.async=1; return s; })(document.createElement('script')));
  })('SquidID');
</script>
```

## Options 
 - logger: [Optional] Change squid's sdk logging 
 - - level: 3 **Defaul**. All logs ( Info, Warning, Error )
   - level: 2. Only Info and Warning
   - level: 1. Only Info
   - level: 0. None
 - connection.options: [Optional] Squid connection
 - - simple: `Boolean` **Default is false** By turnign this flag on Squid will connect but ignore traffic events. Squid will still capture visitor's identification.
   - ready: `Function` Squid will execute this function as soon as it connects.

### Example 1: No logging
```html
<script>
  ;(function(squid){
    window.$quid = { logger : { level : 0 } };
    document.head.appendChild((function(s){ s.src='https://app.asksquid.ai/tfs/'+squid+'/sdk';s.async=1; return s; })(document.createElement('script')));
  })('SquidID');
</script>
```

### Example 2: Do not trace traffic
```html
<script>
  ;(function(squid){
    window.$quid = { 
      connection : { options : { simple : true } } 
    };
    document.head.appendChild((function(s){ s.src='https://app.asksquid.ai/tfs/'+squid+'/sdk';s.async=1; return s; })(document.createElement('script')));
  })('SquidID');
</script>
```

### Example 3: Execute function on connection
```html
<script>
   function onSquidReady(connection){
     // Do something when squid is ready.
   }
   
  ;(function(squid){
    window.$quid = { 
      connection : { ready : onSquidReady }
    };
    document.head.appendChild((function(s){ s.src='https://app.asksquid.ai/tfs/'+squid+'/sdk';s.async=1; return s; })(document.createElement('script')));
  })('SquidID');
</script>
```

### Example 4: Execute function on connection and no logging
```html
<script>
   function onSquidReady(connection){
     // Do something when squid is ready.
   }
   
  ;(function(squid){
    window.$quid = { 
      logger     : { level : 0 },
      connection : { ready : onSquidReady }
    };
    document.head.appendChild((function(s){ s.src='https://app.asksquid.ai/tfs/'+squid+'/sdk';s.async=1; return s; })(document.createElement('script')));
  })('SquidID');
</script>
```

# API 

## `squid.VERSION`
Provides the current SDK version

### Usage
```javascript
squid.VERSION
```

## `squid.connection();`
Retrieves the current connection details.

### Usage
```javascript
await squid.connection();
```

### Response format
```javascript
  {
    id       : String,
    online   : Boolean,
    device   : String,
    identity : {
      id         : String,
      identified : Boolean
    }
  }
```
### Description
- id: Unique connection identifier.
- online: Connection status.
- device: Device fingerprint.
- identity.id : User's unique identifier. 
- identity.identified: Indicates whether the current user is identified.

## `squid.identify(\<{ email : 'foo@example.com' }\>);`
Manually binds a visitor's identification to known traits. This method will also enrich the user with **VisitorID**.

### Usage
```javascript
await squid.identify({ 
     id           : 'my-id',           // Optional: String
     firstName    : 'Foo',             // Optional: String
     lastName     : 'Bar',             // Optional: String
     name         : 'Foo Bar',         // Optional: String
     age          : 20,                // Optional: String
     gender       : 'male',            // Optional: String
     email        : 'foo@example.com', // Required: String
     birthday     : '2023-05-03T17:22:38.574+00:00', // Optional: Date
     phone        : '1111111111',      // Optional: String
     company      : 'Squid',           // Optional: String
     company_role : 'Developer'        // Optional: String,
     linkedin_url : 'linkedin.com/in/abc' // Optional: URL
});
```

### Description
- id: Unique identifier for the visitor in your database. Squid will provide one if not set.
- firstName: Visitor's firstname.
- lastName: Visitor's  lastname.
- name: Visitor's name.
- age: Visitor's age.
- gender: Visitor's gender.
- email: Visistor's email.
- birthday: Visistor's birthday.
- phone: Visistor's phone number.
- company: Visistor's company.
- company_role: Visistor' company role.
- linkedin_url: Visistor's linkeding url.

## `squid.labelClicks(\<{ matches, label, type }\>);`
Tracks user click interactions by tagging specific DOM elements with custom labels. This allows you to define meaningful user actions like "sign up clicked" or "product added to cart" and stream them in real-time.

### Options
 - `matches` (**Required**, *String*): A CSS selector to match the element(s) you want to track.
 - `label` (**Required**, *String*): A custom label that identifies this interaction.
 - `type` (**Required**, *String*): The event type, either:
 - - `key_event`: for important but non-final user actions (e.g. clicking a tab, opening a modal).
   - `conversion`: for final actions (e.g. form submission, purchase).

## Usage
 
```javascript
squid.labelClicks({
  matches : 'li .item',
  label   : 'SOME_LABEL_NAME',
  type    : 'key_event'
});
```

## `squid.engine([{ sp : 'https://myserver.com' }]);`
Connect to squid's realtime engine. Do more then just stream, react and enrich visitor's identity.

### Options
 - sp:  **Optional**. Define your own service provider. As soon as Squid's SDK attempts to provide details of an `event`, you can replace its content with any other payload coming from your server.
   - `visitor.identified`: **POST /visitor_details** `{visitorID}`
 
## Usage
 
```javascript
let engine = new squid.engine();
```

### `.on(`visitor.identified`, \<handler\>);`
This event will dispatch as soon as Squid syncs the visitor and visitor is identified. 

```javascript
let engine = new squid.engine();
engine.on('visitor.identified', event => {
  // event.detail holds Visisot's identification
});
```

Use your own service provider. This is usefull if you need to enrich visitor's traits with your own database information.
```javascript
let engine = new squid.engine({ sp : 'https://myserver.com' });

// POST 'https://myserver.com/visitor_details'
// Body: {Visitor's identification}
engine.on('visitor.identified', event => {
  // event.detail holds 'https://myserver.com' response
});
```

### `.on(`visitor.unknown`, \<handler\>);`
This event will dispatch as soon as Squid syncs and the visitor is unknown or anonymous.

```javascript
let engine = new squid.engine();
engine.on('visitor.unknown', event => {
  // your code
});
```

### `.on(`visitor.synched`, \<handler\>);`
This event will dispatch as soon as Squid syncs. this event provides the current visitor regarthless if its identified or not.

```javascript
let engine = new squid.engine();
engine.on('visitor.synched', event => {
  // your code
});
```

### `.on(`error`, \<handler\>);`
Debug if something went worng while wiring your service provider.

```javascript
let engine = new squid.engine({ sp : 'https://myserver.com' });
engine.on('error', console.error);
engine.on('visitor.identified', console.log);
```
