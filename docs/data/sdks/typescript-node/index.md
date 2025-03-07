---
title: Node.js SDK
description: The Amplitude Typescript SDK Installation & Quick Start guide.
icon: simple/nodedotjs
---


![npm version](https://badge.fury.io/js/@amplitude%2Fanalytics-node.svg)

The Node.js SDK lets you send events to Amplitude. This library is open-source, check it out on [GitHub](https://github.com/amplitude/Amplitude-TypeScript).

!!!beta "Beta SDK Resources"
    - [Node.js SDk Reference :material-book:](https://amplitude.github.io/Amplitude-TypeScript/)
    - [Node.js SDk Repository :material-github:](https://github.com/amplitude/Amplitude-TypeScript/tree/main/packages/analytics-node)
    - [Node.js SDk Releases :material-code-tags-check:](https://github.com/amplitude/Amplitude-TypeScript/releases)

--8<-- "includes/ampli-vs-amplitude.md"
    Click here for more documentation on [Ampli for Node](../typescript-node/ampli.md).

## Getting Started

### Installation

To get started with using Node SDK, install the package to your project via NPM or script loader.

#### Installing as Node package

This package is published on NPM registry and can be installed using npm and yarn.

=== "npm"

    ```bash
    npm install @amplitude/analytics-node
    ```

=== "yarn"

    ```bash
    yarn add @amplitude/analytics-node
    ```

## Usage

### Initializing SDK

Initialization is necessary before any instrumentation is done. The API key for your Amplitude project is required. The SDK can be used anywhere after it is initialized anywhere in an application.

```ts
import { init } from '@amplitude/analytics-node';

// Option 1, initialize with API_KEY only
init(API_KEY);

// Option 2, initialize including configuration
init(API_KEY, {
  flushIntervalMillis: 30 * 1000, // Sets request interval to 30s
});
```

#### EU Data Residency

You can configure the server zone when initializing the client for sending data to Amplitude's EU servers. The SDK sends data based on the server zone if it's set.

!!!note
    For EU data residency, the project must be set up inside Amplitude EU. You must initialize the SDK with the API key from Amplitude EU.

```ts
import * as amplitude from '@amplitude/analytics-node';

amplitude.init(API_KEY, OPTIONAL_USER_ID, {
  serverZone: amplitude.Types.ServerZone.EU,
});
```

### Tracking an event

--8<-- "includes/sdk-httpv2-notice.md"

Events represent how users interact with your application. For example, "Button Clicked" may be an action you want to note.

```ts
import { track } from '@amplitude/analytics-node';

// Track a basic event
track('Button Clicked', undefined, {
  user_id: 'user@amplitude.com',
});

// Track events with optional properties
const eventProperties = {
  buttonColor: 'primary',
};
track('Button Clicked', eventProperties, {
  user_id: 'user@amplitude.com',
});
```

### User properties

User properties help you understand your users at the time they performed some action within your app such as their device details, their preferences, or language.

Identify is for setting the user properties of a particular user without sending any event. The SDK supports the operations `set`, `setOnce`, `unset`, `add`, `append`, `prepend`, `preInsert`, `postInsert`, and `remove` on individual user properties. The operations are declared via a provided Identify interface. Multiple operations can be chained together in a single Identify object. The Identify object is then passed to the Amplitude client to send to the server.

!!!note 
    If the Identify call is sent after the event, the results of operations will be visible immediately in the dashboard user’s profile area, but it will not appear in chart result until another event is sent after the Identify call. So the identify call only affects events going forward. More details [here](https://amplitude.zendesk.com/hc/en-us/articles/115002380567-User-Properties-Event-Properties#applying-user-properties-to-events).

#### Setting a user property

The Identify object provides controls over setting user properties. An Identify object must first be instantiated, then Identify methods can be called on it, and finally the client will make a call with the Identify object.

```ts
import { identify, Identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identify(identifyObj, {
  user_id: 'user@amplitude.com',
});
```

### Identify.set

This method sets the value of a user property. For example, you can set a role property of a user.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.set('location', 'LAX');

identify(identifyObj);
```

#### Identify.setOnce

This method sets the value of a user property only once. Subsequent calls using setOnce() will be ignored. For example, you can set an initial login method for a user and since only the initial value is tracked, setOnce() ignores subsequent calls.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.setOnce('initial-location', 'SFO');

identify(identifyObj);
```

#### Identify.add

This method increments a user property by some numerical value. If the user property does not have a value set yet, it will be initialized to 0 before being incremented. For example, you can track a user's travel count.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.add('travel-count', 1);

identify(identifyObj);
```

#### Arrays in user properties

Arrays can be used as user properties. You can directly set arrays or use prepend, append, preInsert and postInsert to generate an array.

#### Identify.prepend

This method prepends a value or values to a user property array. If the user property does not have a value set yet, it will be initialized to an empty list before the new values are prepended.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.prepend('visited-locations', 'LAX');

identify(identifyObj);
```

#### Identify.append

This method appends a value or values to a user property array. If the user property does not have a value set yet, it will be initialized to an empty list before the new values are prepended.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.append('visited-locations', 'SFO');

identify(identifyObj);
```

#### Identify.preInsert

This method pre-inserts a value or values to a user property, if it does not exist in the user property yet. Pre-insert means inserting the value(s) at the beginning of a given list. If the user property does not have a value set yet, it will be initialized to an empty list before the new values are pre-inserted. If the user property has an existing value, it will be no operation.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.preInsert('unique-locations', 'LAX');

identify(identifyObj);
```

#### Identify.postInsert

This method post-inserts a value or values to a user property, if it does not exist in the user property yet. Post-insert means inserting the value(s) at the end of a given list. If the user property does not have a value set yet, it will be initialized to an empty list before the new values are post-inserted. If the user property has an existing value, it will be no operation.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.postInsert('unique-locations', 'SFO');

identify(identifyObj);
```

#### Identify.remove

This method removes a value or values to a user property, if it exists in the user property. Remove means remove the existing value(s) from the given list. If the item does not exist in the user property, it will be no operation.

```ts
import { Identify, identify } from '@amplitude/analytics-node';

const identifyObj = new Identify();
identifyObj.remove('unique-locations', 'JFK')

identify(identifyObj);
```

### User groups

--8<-- "includes/editions-growth-enterprise-with-accounts.md"

--8<-- "includes/groups-intro-paragraph.md"

```ts
import { setGroup } from '@amplitude/analytics-node';

// set group with single group name
setGroup('orgId', '15');

// set group with multiple group names
setGroup('sport', ['soccer', 'tennis']);
```

### Group properties

--8<-- "includes/editions-growth-enterprise-with-accounts.md"

Use the Group Identify API to set or update properties of particular groups. These updates only affect events going forward.

The `groupIdentify()` method accepts a group type and group name string parameter, as well as an Identify object that will be applied to the group.

```ts
import { Identify, groupIdentify } from '@amplitude/analytics-node';

const groupType = 'plan';
const groupName = 'enterprise';
const event = new Identify()
event.set('key1', 'value1');

groupIdentify(groupType, groupName, identify, {
  user_id: 'user@amplitude.com',
});
```

### Revenue tracking

The preferred method of tracking revenue for a user is to use `revenue()` in conjunction with the provided Revenue interface. Revenue instances will store each revenue transaction and allow you to define several special revenue properties (such as 'revenueType', 'productIdentifier', etc.) that are used in Amplitude's Event Segmentation and Revenue LTV charts. These Revenue instance objects are then passed into `revenue()` to send as revenue events to Amplitude. This lets automatically display data relevant to revenue in the platform. You can use this to track both in-app and non-in-app purchases.

To track revenue from a user, call revenue each time a user generates revenue. For example, 3 units of a product was purchased at $3.99.

```ts
import { Revenue, revenue } from '@amplitude/analytics-node';

const event = new Revenue()
  .setProductId('com.company.productId')
  .setPrice(3.99)
  .setQuantity(3);

revenue(event, {
  user_id: 'user@amplitude.com',
});
```

#### Revenue interface

|Name | Description |
|-----|-------|
|`product_id` | Optional. String. An identifier for the product. We recommend something like the Google Play Store product ID. Defaults to null. |
|`quantity` | Required. Int. The quantity of products purchased. Note: revenue = quantity * price. Defaults to 1|
|`price` | Required. Double. The price of the products purchased, and this can be negative. Note: revenue = quantity * price. Defaults to null. |
|`revenue_type` | Optional, but required for revenue verification. String. The type of revenue (e.g. tax, refund, income).  Defaults to null.|
|`receipt`| Optional. String. The receipt identifier of the revenue. Defaults to null|
|`receipt_sig`| Optional, but required for revenue verification. String. The receipt signature of the revenue. Defaults to null.|
|`properties`| Optional. JSONObject. An object of event properties to include in the revenue event. Defaults to null.

### Flushing event buffer

The `flush` method triggers the client to send buffered events.

```typescript
import { flush } from '@amplitude/analytics-node';

flush();
```

### Opt users out of tracking

You can turn off logging for a given user by setting `setOptOut` to `true`.

```ts
import { setOptOut } from '@amplitude/analytics-node';

setOptOut(true);
```

No events are saved or sent to the server while `setOptOut` is enabled, and the setting persists across page loads. 

Reenable logging by setting `setOptOut` to `false`.

```ts
import { setOptOut } from '@amplitude/analytics-node';

setOptOut(false);
```

### Callback

All asynchronous API are optionally awaitable through a Promise interface. This also serves as callback interface.

```ts
import { track } from '@amplitude/analytics-node';

// Using async/await
const results = await track('Button Clicked').promise;
result.event; // {...} (The final event object sent to Amplitude)
result.code; // 200 (The HTTP response status code of the request.
result.message; // "Event tracked successfully" (The response message)

// Using promises
track('Button Clicked').promise.then((result) => {
  result.event; // {...} (The final event object sent to Amplitude)
  result.code; // 200 (The HTTP response status code of the request.
  result.message; // "Event tracked successfully" (The response message)
});
```

### Plugins

Plugins allow you to extend Amplitude SDK's behavior by, for example, modifying event properties (enrichment type) or sending to a third-party APIs (destination type). A plugin is an object with methods `setup()` and `execute()`.

#### `add`

The `add` method adds a plugin to Amplitude client instance. Plugins can help processing and sending events.

```typescript
import { add } from '@amplitude/analytics-node';

add(new Plugin());
```

#### `remove`

The `remove` method removes the given plugin name from the client instance if it exists.

```typescript
import { remove } from '@amplitude/analytics-node';

remove(plugin.name);
```

#### Creating your custom plugin

#### Plugin.setup

This method contains logic for preparing the plugin for use and has config as a parameter. The expected return value is undefined. A typical use for this method, is to copy configuration from config or instantiate plugin dependencies. This method is called when the plugin is registered to the client via `client.add()`.

#### Plugin.execute

This method contains the logic for processing events and has event as parameter. If used as enrichment type plugin, the expected return value is the modified/enriched event; while if used as a destination type plugin, the expected return value is undefined. This method is called for each event, including Identify, GroupIdentify and Revenue events, that is instrumented using the client interface.

#### Plugin examples

##### Enrichment type plugin

Here's an example of a plugin that modifies each event that is instrumented by adding an increment integer to `event_id` property of an event starting from 100.

```ts
import { init, add } from '@amplitude/analytics-node';
import { NodeConfig, EnrichmentPlugin, Event, PluginType } from '@amplitude/analytics-types';

export class AddEventIdPlugin implements EnrichmentPlugin {
  name = 'add-event-id';
  type = PluginType.ENRICHMENT as const;
  currentId = 100;
  config?: NodeConfig;
  
  /**
   * setup() is called on plugin installation
   * example: client.add(new AddEventIdPlugin());
   */
  async setup(config: NodeConfig): Promise<undefined> {
     this.config = config;
     return;
  }
   
  /**
   * execute() is called on each event instrumented
   * example: client.track('New Event');
   */
  async execute(event: Event): Promise<Event> {
    event.event_id = this.currentId++;
    return event;
  }
}

init('API_KEY');
add(new AddEventIdPlugin());
```

#### Destination Type Plugin

Here's an example of a plugin that sends each event that is instrumented to a target server URL using your preferred HTTP client.

```ts
import { init, add } from '@amplitude/analytics-node';
import { NodeConfig, DestinationPlugin, Event, PluginType, Result } from '@amplitude/analytics-types';
import fetch from 'node-fetch';

export class MyDestinationPlugin implements DestinationPlugin {
  name = 'my-destination-plugin';
  type = PluginType.DESTINATION as const;
  serverUrl: string;
  config?: NodeConfig;

  constructor(serverUrl: string) {
    this.serverUrl = serverUrl;
  }

  /**
   * setup() is called on plugin installation
   * example: client.add(new MyDestinationPlugin());
   */
  async setup(config: NodeConfig): Promise<undefined> {
    this.config = config;
    return;
  }

  /**
   * execute() is called on each event instrumented
   * example: client.track('New Event');
   */
  async execute(event: Event): Promise<Result> {
    const payload = { key: 'secret', data: event };
    const response = await fetch(this.serverUrl, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        Accept: '*/*',
      },
      body: JSON.stringify(payload),
    });
    return {
      code: response.status,
      event: event,
      message: response.statusText,
    };
  }
}
    
init('API_KEY');
add(new MyDestinationPlugin('https://custom.domain.com'));
```

--8<-- "includes/abbreviations.md"
