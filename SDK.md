# Squid SDK

## Introduction

Squid SDK is a real-time event streaming library designed to capture user interactions such as clicks, scrolls, and form submissions while also collecting browser performance metrics. The SDK automatically de-anonymizes users and enriches their identities. All of this happens in real-time through WebSockets, enabling seamless integration with your analytics and customer engagement platforms.

# API 

## squid.VERSION
Provides the current SDK version

### Usage
```javascript
squid.VERSION
```

## squid.connection();
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

## squid.identify({ email : 'foo@example.com' });
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

## squid.engine([{ sp : 'https://myserver.com' }]);
Connect to squid's realtime engine. Do more then just stream, react and enrich visitor's identity.

### Options
 - sp:  **Optional**. Define your own service provider. As soon as Squid's SDK attempts to provide details of the visitor it will replace the event's detail with whatever your service provider resolves. Squid will make a `POST` request and send visitor's identificationin the`body`.
 

## Usage
 
```javascript
let engine = new squid.engine();
```

### .on(`visitor.identified`, \<handler\>);
This event will dispatch as soon as the visitor gets identified. If visitor is all ready de anonymised it will provide visitor's identity as soon as it registers the event listener.

```javascript
let engine = new squid.engine();
engine.on('visitor.identified', event => {
  // event.detail holds Visisot's identification
});
```

Use your own service provider. This is usefull if you need to enrich visitor's traits with your own database information.
```javascript
let engine = new squid.engine({ sp : 'https://myserver.com' });

// POST 'https://myserver.com'  
// Body: {Visitor's identification}
engine.on('visitor.identified', event => {
  // event.detail holds 'https://myserver.com' response
});
```

### .on(`error`, \<handler\>);
Debug if something went worng while wiring your service provider.

```javascript
let engine = new squid.engine({ sp : 'https://myserver.com' });
engine.on('error', console.error);
engine.on('visitor.identified', console.log);
```

