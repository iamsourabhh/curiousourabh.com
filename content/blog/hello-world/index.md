---
title: Custom event listeners and subscribers in Javascript.
date: "2020-12-15T22:12:03.284Z"
description: "An attempt to do a custom implementation of the Event service in javascript"
---

I have been working on Node for quite some time and have extensively used Events. But never knew how it works internally until very recently.

### What are Events?
So basically, when something happens in your app like a user gets logged in, you fire an event to notify everybody listening to that event, that a user has logged in.
```javascript

function login(data) {
    // fires an event
    CustomEvent.emit('user_logged_in', data);
}

function someOtherFunc() {
    // event listeners
    CustomEvent.subscribe('user_logged_in', function(data) {
        // do some operation
    })
}

```

### Events Implementation
The Event class that we are designing will have the following methods:
- emit - It fires an event, with an event name and data.
- subscribe - It subscribes to the event and gets executed every time the given event is fired.
- unsubscribe - It will unsubscribe the function from the event.
- unsubscribeAll - It will unsubscribe all the functions listening to that event.

```javascript
/**
 * Disclaimer: This is not a production-quality code, lots of checks are not in place.
 */
function CustomEvent() {
    // listers is an object
    // it will be a map of event_name
    // and all the functions subscribing to it
    this.listeners = {};
}

CustomEvent.prototype.subscribe = function(eventName, someFunction) {
    // checks if eventName exists
    // if doesn't initialise a new array
    // else push the function to the existing array
    if(!this.listeners[eventName]) {
        this.listeners[eventName] = [someFunction];
        return;
    }
    this.listeners[eventName].push(someFunction);
}

CustomEvent.prototype.emit = function(eventName, data) {
    // gets the event name
    // and loops over all the functions that it has to call
    const listeners = this.listeners[eventName];
    listeners && Array.isArray(listeners) && listeners.map(listenr => {
        listenr.call(null, data);
    })
    
}

CustomEvent.prototype.unsubscribeAll = function(eventName) {
    this.listeners[eventName] = undefined;
}

CustomEvent.prototype.unsubscribe = function(eventName, someFunction) {
    
    const funcs = this.listeners[eventName];
    
    const filteredFuncs = funcs.filter(func => func.toString() !== someFunction.toString());
    
    this.listeners[eventName] = filteredFuncs;
    
}
```


If you guys have a better implementation for unsubscribing in mind, please suggest.
Keep coding. Keep learning.
Thanks.
