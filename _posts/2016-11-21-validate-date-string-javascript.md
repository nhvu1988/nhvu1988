---
title: Validate date string in javascript
author: nhvu1998
date: 2016-11-21
category: Coding
tags: [javascript,moment]

---
## Use case

Today I got a question from my colleague for validating a date string in javascript.

After quick search on Google, I still don't see have any solution for this so I write here to take note for reuse later :)

We can use [moment] library and standard format [ISO_8601] to check

```javascript
moment(date_str, moment.ISO_8601).isValid();
```

## Examples

```javascript
moment('2013-02-04T10:35:24-08:00', moment.ISO_8601).isValid(); // -> true
moment('2013-02-04', moment.ISO_8601).isValid();                // -> true
moment('2013-02-04 10:35', moment.ISO_8601).isValid();          // -> true
moment('2013-02-30 10:35', moment.ISO_8601).isValid();          // -> false, no 30 in Feb :)
moment('x', moment.ISO_8601).isValid();                         // -> false
moment('x 1', moment.ISO_8601).isValid();                       // -> false
```

[moment]: http://momentjs.com/
[ISO_8601]: http://en.wikipedia.org/wiki/ISO_8601